# code_of_the_damned();

[Original URL](http://codeofthedamned.com/index.php/introduction-to-the-math-of)

> Three-dimensional computer graphics is an exciting aspect of computing because of the amazing visual effects that can be created for display. All of this is created from an enormous number of...

Three-dimensional computer graphics is an exciting aspect of computing because of the amazing visual effects that can be created for display. All of this is created from an enormous number of calculations that manipulate virtual models, which are constructed from some form of geometric definition. While the math involved for some aspects of computer graphics and animation can become quite complex, the fundamental mathematics that is required is very accessible. Beyond learning some new mathematic notation, a basic three-dimensional view can be constructed with algorithms that only require basic arithmetic and a little bit of trigonometry.

I demonstrate how to perform some basic operations with two key constructs for 3D graphics, without the rigorous mathematic introduction. Hopefully the level of detail that I use is enough for anyone that doesn't have a strong background in math, but would very much like to play with 3D APIs. I introduce the math that you _must_ be familiar with in this entry, and in two future posts I demonstrate how to manipulate geometric models and apply the math towards the display of a 3D image.

## Linear Algebra

The type of math that forms the basis of 3D graphics is called linear algebra. In general, linear algebra is quite useful for solving systems of linear equations. Rather than go into the details of linear algebra, and all of its capabilities, we are going to simply focus on two related constructs that are used heavily within the topic, the _matrix_ and the _vertex_.

### Matrix

(Add matrix introduction)

These are all examples of valid matrices:

\( \left\lbrack \matrix{a & b & c \cr d & e & f} \right\rbrack, \left\lbrack \matrix{10 & -27 \cr 0 & 13 \cr 7 & 17} \right\rbrack, \left\lbrack \matrix{x^2 & 2x \cr 0 & e^x} \right\rbrack\)

Square matrices are particularly important, that is a matrix with the same number of columns and rows. There are some operations that can only be performed on a square matrix, which I will introduce shortly. The notation for matrices, in general, uses capital letters as the variable names. Matrix dimensions can be specified as a shorthand notation, and to also identify an indexed position within the matrix. As far as I am aware, row-major indexing is _always_ used for consistency, that is, the first index represents the row, and the second represents the column.

\( A= [a_{ij}]= \left\lbrack \matrix{a_{11} & a_{12} & \ldots & a_{1n} \cr a_{21} & a_{22} & \ldots & a_{2n} \cr \vdots & \vdots & \ddots & \vdots \cr a_{m1} & a_{m2} & \ldots & a_{mn} } \right\rbrack \)

### Vector

The vector is a special case of a matrix, where there is only a single row _or_ a single column; also a common practice is to use lowercase letters to represent vectors:

\( u= \left\lbrack \matrix{u_1 & u_2 & u_3} \right\rbrack \), \( v= \left\lbrack \matrix{v_1\cr v_2\cr v_3} \right\rbrack\)

## Operations

The mathematical shorthand notation for matrices is very elegant. It simplifies these equations that would be quite cumbersome, otherwise. There are only a few operations that we are concerned with to allow us to get started. Each operation has a basic algorithm to follow to perform a calculation, and the algorithm easily scales with the dimensions of a matrix.

Furthermore, the relationship between math and programming is not always clear. I have a number of colleagues that are excellent programmers and yet they do not consider their math skills very strong. I think that it could be helpful to many to see the conversion of the matrix operations that I just described from mathematical notation into code form. Once I demonstrate how to perform an operation with the mathematical notation and algorithmic steps, I will also show a basic C++ implementation of the concept to help you understand how these concepts map to actual code.

The operations that I implement below assume a Matrix class with the following interface:

C++

 | `class Matrix`
 | ---------------------------------------------------------------
 | `{`
 | `public:`
 | `// Ctor and Dtor omitted`
 | ``
 | `// Calculate and return a reference to the specified element.`
 | `double& element(size_t row, size_t column);`
 | ``
 | `// Resizes this Matrix to have the specified size.`
 | `void resize(size_t row, size_t column);`
 | ``
 | `// Returns the number rows.`
 | `size_t rows();`
 | ``
 | `// Returns the number of columns.`
 | `size_t columns();`
 | `private:`
 | `std::vector<double> data;`
 | `};`

### Transpose

_Transpose_ is a unary operation that is performed on a single matrix, and it is represented by adding a _superscript_ T to target matrix.

For example, the transpose of a matrix A is represented with A

<sup>T</sup>

.

The transpose "flips" the orientation of the matrix so that each row becomes a column, and each original column is transformed into a row. I think that a few examples will make this concept more clear.

\( A= \left\lbrack \matrix{a & b & c \cr d & e & f \cr g & h & i} \right\rbrack \)

\(A^T= \left\lbrack \matrix{a & d & g \cr b & e & h \cr c & f & i} \right\rbrack \)

The resulting matrix will contain the same set of values as the original matrix, only their position in the matrix changes.

\( B= \left\lbrack \matrix{1 & 25 & 75 & 100\cr 0 & 5 & -50 & -25\cr 0 & 0 & 10 & 22} \right\rbrack \)

\(B^T= \left\lbrack \matrix{1 & 0 & 0 \cr 25 & 5 & 0 \cr 75 & -50 & 10 \cr 100 & -25 & 22} \right\rbrack \)

It is very common to transpose a matrix, including vertices, before performing other operations. The reason will become clear for _matrix multiplication_.

\( u= \left\lbrack \matrix{u_1 & u_2 & u_3} \right\rbrack \)

\(u^T= \left\lbrack \matrix{u_1 \cr u_2 \cr u_3} \right\rbrack \)

### Matrix Addition

Addition can only be performed between two matrices that are the same size. By size, we mean that the number of rows and columns are the same for each matrix. Addition is performed by adding the values of the corresponding positions of both matrices. The sum of the values creates a new matrix that is the same size as the original two matrices provided to the add operation.

\( A= \left\lbrack \matrix{0 & -2 & 4 \cr -6 & 8 & 0 \cr 2 & -4 & 6} \right\rbrack, B= \left\lbrack \matrix{1 & 3 & 5 \cr 7 & 9 & 11 \cr 13 & 15 & 17} \right\rbrack \)

Then

\(A+B=\left\lbrack \matrix{1 & 1 & 9 \cr 1 & 17 & 11 \cr 15 & 11 & 23} \right\rbrack \)

The size of these matrices do not match in their current form.

\(U= \left\lbrack \matrix{4 & -8 & 5}\right\rbrack, V= \left\lbrack \matrix{-1 \\ 5 \\ 4}\right\rbrack \)

However, if we take the transpose of one of them, their sizes will match and they can then be added together. The size of the result matrix depends upon which matrix we perform the transpose operation on from the original expression.:

\(U^T+V= \left\lbrack \matrix{3 \\ -3 \\ 9} \right\rbrack \)

\(U+V^T= \left\lbrack \matrix{3 & -3 & 9} \right\rbrack \)

Matrix addition has the same algebraic properties as with the addition of two scalar values:

#### Commutative Property:

\( A+B=B+A \)

#### Associative Property:

\( (U+V)+W=U+(V+W) \)

#### Identity Property:

\( A+0=A \)

#### Inverse Property:

\( A+(-A)=0 \) The code required to implement _matrix addition_ is relatively simple. Here is an example for the `Matrix` class definition that I presented earlier:

C++

 | `void Matrix::operator+=(const Matrix& rhs)`
 | ------------------------------------------------------
 | `{`
 | `if (rhs.data.size() == data.size())`
 | `{`
 | `// We can simply add each corresponding element`
 | `// in the matrix element data array.`
 | `for (size_t index = 0; index < data.size(); ++index)`
 | `{`
 | `data[index] += rhs.data[index];`
 | `}`
 | `}`
 | `}`
 | ``
 | `Matrix& operator+( const Matrix& lhs,`
 | `const Matrix& rhs)`
 | `{`
 | `Matrix result(lhs);`
 | `result += rhs;`
 | ``
 | `return result;`
 | `}`

### Scalar Multiplication

Scalar multiplication allows a single scalar value to be multiplied with every entry within a matrix. The result matrix is the same size as the matrix provided to the scalar multiplication expression:

\( A= \left\lbrack \matrix{3 & 6 & -9 \cr 12 & -15 & -18} \right\rbrack \)

Then

\( \frac{1}3 A= \left\lbrack \matrix{1 & 2 & -3 \cr 4 & -5 & -6} \right\rbrack, 0A= \left\lbrack \matrix{0 & 0 & 0 \cr 0 & 0 & 0} \right\rbrack, -A= \left\lbrack \matrix{-3 & -6 & 9 \cr -12 & 15 & 18} \right\rbrack, \)

Scalar multiplication with a matrix exhibits these properties, where _c_ and _d_ are scalar values:

#### Distributive Property:

\( c(A+B)=cA+cB \)

#### Identity Property:

\( 1A=A \) The implementation for scalar multiplication is even simpler than addition.<br>
_Note: this implementation only allows the scalar value to appear before the Matrix object in multiplication expressions, which is how the operation is represented in math notation._:

C++

 | `void Matrix::operator_=(const double lhs)_`
 | ------------------------------------------------------
 | `{`
 | `for (size_t index = 0; index < data.size(); ++index)`
 | `{`
 | `data[index] = rhs;`
 | `}`
 | `}`
 | ``
 | `Matrix& operator_( const double scalar,_`
 | `const Matrix& rhs)`
 | `{`
 | `Matrix result(rhs);`
 | `result = scalar;`
 | ``
 | `return result;`
 | `}`

### Matrix Multiplication

Everything seems very simple with matrices, at least once you get used to the new structure. Then you are introduced to _matrix multiplication_. The algorithm for multiplication is not difficult, however, it is much more labor intensive compared to the other operations that I have introduced to you. There are also a few more restrictions on the parameters for multiplication to be valid. Finally, unlike the addition operator, the matrix multiplication operator does not have all of the same properties as the multiplication operator for scalar values; specifically, the order of parameters matters.

#### Input / Output

Let's first address what you need to be able to multiply matrices, and what type of matrix you can expect as output. Once we have addressed the structure, we will move on to the process.

Given an the following two matrices:

\( A= \left\lbrack \matrix{a_{11} & \ldots & a_{1n} \cr \vdots & \ddots & \vdots \cr a_{m1} & \ldots & a_{mn} } \right\rbrack, B= \left\lbrack \matrix{b_{11} & \ldots & b_{1v} \cr \vdots & \ddots & \vdots \cr b_{u1} & \ldots & b_{uv} } \right\rbrack \)

A valid product for \( AB=C \) is only possible if number of columns \( n \) in \( A \) is equal to the number of rows \( u \) in \( B \). The resulting matrix \( C \) will have the dimensions \( m \times v \).

\( AB=C= \left\lbrack \matrix{c_{11} & \ldots & c_{1v} \cr \vdots & \ddots & \vdots \cr c_{m1} & \ldots & c_{mv} } \right\rbrack, \)

Let's summarize this in a different way, hopefully this arrangement will make the concept more intuitive:

![Matrix multiplication dimensions](http://codeofthedamned.com/media/shared/global/damned/matrix_mult.png "Matrix multiplication dimensions") One last form of the rules for matrix multiplication:

- The number of columns, \(n\), in matrix \( A \) must be equal to the number of rows, \(u\), in matrix \( B \):<br>
  \( n = u \)
- The output matrix \( C \) will have the number of rows, \(m\), in \(A\), and the number of columns, \(v\), in \(B\):<br>
  \( m \times v \)
- \(m\) and \(v\) do not have to be equal. The only requirement is that they are both greater-than zero:<br>
  \( m \gt 0,\)<br>
  \(v \gt 0 \)

#### How to Multiply

To calculate a single entry in the output matrix, we must multiply the element from each column in the first matrix, with the element in the corresponding row in the second matrix, and add all of these products together. We use the same row in the first matrix, \(A\), for which we are calculating the row element in \(C\). Similarly, we use the column in the second matrix, \(B\) that corresponds with the calculating column element in \(C\).

More succinctly, we can say we are _multiplying rows into columns_.

For example:

\( A= \left\lbrack \matrix{a_{11} & a_{12} & a_{13} \cr a_{21} & a_{22} & a_{23}} \right\rbrack, B= \left\lbrack \matrix{b_{11} & b_{12} & b_{13} & b_{14} \cr b_{21} & b_{22} & b_{23} & b_{24} \cr b_{31} & b_{32} & b_{33} & b_{34} } \right\rbrack \)

The number of columns in \(A\) is \(3\) and the number of rows in \(B\) is \(3\), therefore, we can perform this operation. The size of the output matrix will be \(2 \times 4\).

This is the formula to calculate the element \(c_{11}\) in \(C\) and the marked rows used from \(A\) and the columns from \(B\):

\( \left\lbrack \matrix{\color{#B11D0A}{a_{11}} & \color{#B11D0A}{a_{12}} & \color{#B11D0A}{a_{13}} \cr a_{21} & a_{22} & a_{23}} \right\rbrack \times \left\lbrack \matrix{\color{#B11D0A}{b_{11}} & b_{12} & b_{13} & b_{14} \cr \color{#B11D0A}{b_{21}} & b_{22} & b_{23} & b_{24} \cr \color{#B11D0A}{b_{31}} & b_{32} & b_{33} & b_{34} } \right\rbrack = \left\lbrack \matrix{\color{#B11D0A}{c_{11}} & c_{12} & c_{13} & c_{14}\cr c_{21} & c_{22} & c_{23} & c_{24} } \right\rbrack \)

\( c_{11}= (a_{11}\times b_{11}) + (a_{12}\times b_{21}) + (a_{13}\times b_{31}) \)

To complete the multiplication, we need to calculate these other seven values \( c_{12}, c_{13}, c_{14}, c_{21}, c_{22}, c_{23}, c_{24}\). Here is another example for the element \(c_{23}\):

\( \left\lbrack \matrix{ a_{11} & a_{12} & a_{13} \cr \color{#B11D0A}{a_{21}} & \color{#B11D0A}{a_{22}} & \color{#B11D0A}{a_{23}} } \right\rbrack \times \left\lbrack \matrix{b_{11} & b_{12} & \color{#B11D0A}{b_{13}} & b_{14} \cr b_{21} & b_{22} & \color{#B11D0A}{b_{23}} & b_{24} \cr b_{31} & b_{32} & \color{#B11D0A}{b_{33}} & b_{34} } \right\rbrack = \left\lbrack \matrix{c_{11} & c_{12} & c_{13} & c_{14}\cr c_{21} & c_{22} & \color{#B11D0A}{c_{23}} & c_{24} } \right\rbrack \)

\( c_{23}= (a_{21}\times b_{13}) + (a_{22}\times b_{23}) + (a_{23}\times b_{33}) \)

Notice how the size of the output matrix changes. Based on this and the size of the input matrices you can end up with some interesting results:

\( \left\lbrack \matrix{a_{11} \cr a_{21} \cr a_{31} } \right\rbrack \times \left\lbrack \matrix{ b_{11} & b_{12} & b_{13} } \right\rbrack = \left\lbrack \matrix{c_{11} & c_{12} & c_{13} \cr c_{21} & c_{22} & c_{23} \cr c_{31} & c_{32} & c_{33} } \right\rbrack \)

\( \left\lbrack \matrix{ a_{11} & a_{12} & a_{13} } \right\rbrack \times \left\lbrack \matrix{b_{11} \cr b_{21} \cr b_{31} } \right\rbrack = \left\lbrack \matrix{c_{11} } \right\rbrack \)

> Tip:

> To help you keep track of which row to use from the first matrix and which column from the second matrix, create your result matrix of the proper size, then methodically calculate the value for each individual element.

The algebraic properties for the matrix multiplication operator do not match those of the scalar multiplication operator. These are the most notable:

#### Not Commutative:

The order of the factor matrices definitely matters.

\( AB \ne BA \) in general<br>
I think it is very important to illustrate this fact. Here is a simple \(2 \times 2 \) multiplication performed two times with the order of the input matrices switched. I have highlighted the only two terms that the two resulting answers have in common:

\( \left\lbrack \matrix{a & b \cr c & d } \right\rbrack \times \left\lbrack \matrix{w & x\cr y & z } \right\rbrack = \left\lbrack \matrix{(\color{red}{aw}+by) & (ax+bz)\cr (cw+dy) & (cx+\color{red}{dz}) } \right\rbrack \)

\( \left\lbrack \matrix{w & x\cr y & z } \right\rbrack \times \left\lbrack \matrix{a & b \cr c & d } \right\rbrack = \left\lbrack \matrix{(\color{red}{aw}+cx) & (bw+dx)\cr (ay+cz) & (by+\color{red}{dz}) } \right\rbrack \)

#### Product of Zero:

\( AB=0 \) does not necessarily imply \( A=0 \) or \( B=0 \)

#### Scalar Multiplication is Commutative:

\( (kA)B = k(AB) = A(kB) \)

#### Associative Property:

Multiplication is associative, however, take note that the relative order for all of the matrices remains the same.

\( (AB)C=A(BC) \)

#### Transpose of a Product:

The transpose of a product is equivalent to the product of transposed factors multiplied in reverse order

\( (AB)^T=B^TA^T \)

#### Code

We are going to use a two-step solution to create a general purpose matrix multiplication solution. The first step is to create a function that properly calculates a single element in the output matrix:

C++

 | `double Matrix::multiply_element(`
 | ---------------------------------------------------------------------
 | `const Matrix& rhs,`
 | `const Matrix& rhs,`
 | `const size_t i,`
 | `const size_t j`
 | `)`
 | `{`
 | `double product = 0;`
 | ``
 | `// Multiply across the specified row, i, for the left matrix`
 | `// and the specified column, j, for the right matrix.`
 | `// Accumulate the total of the products`
 | `// to return as the calculated result.`
 | `for (size_t col_index = 1; col_index <= lhs.columns(); ++col_index)`
 | `{`
 | `for (size_t row_index = 1; row_index <= rhs.rows(); ++row_index)`
 | `{`
 | `product += lhs.element(i, col_index)`
 | `* rhs.element(row_index, j);`
 | `}`
 | `}`
 | ``
 | `return product;`
 | `}`

Now create the outer function that performs the multiplication to populate each field of the output matrix:

C++

 | `// Because we may end up creating a matrix with`
 | --------------------------------------------------------------
 | `// an entirely new size, it does not make sense`
 | `// to have a _= operator for this general-purpose solution._`
 | `Matrix& Matrix::operator( const Matrix& lhs,`
 | `const Matrix& rhs)`
 | `{`
 | `if (lhs.columns() == rhs.rows())`
 | `{`
 | `// Resize the result matrix to the proper size.`
 | `this->resize(lhs.row(), rhs.columns());`
 | ``
 | `// Calculate the value for each element`
 | `// in the result matrix.`
 | `for (size_t i = 1; i <= this->rows(); ++i)`
 | `{`
 | `for (size_t j = 1; j <= this->columns(); ++j)`
 | `{`
 | `element(i,j) = multiply_element(lhs, rhs, i, j);`
 | `}`
 | `}`
 | `}`
 | ``
 | `return *this;`
 | `}`

## Summary

3D computer graphics relies heavily on the concepts found in the branch of math called, _Linear Algebra_. I have introduced two basic constructs from Linear Algebra that we will need to move forward and perform the fundamental calculations for rendering a three-dimensional display. At this point I have only scratched the surface as to what is possible, and I have only demonstrated _how_. I will provide context and demonstrate the _what_ and _why_, to a degree, on the path helping you begin to work with three-dimensional graphics libraries, even if math is not one of your strongest skills.

### References

Kreyszig, Erwin; "Chapter 7" from _Advanced Engineering Mathematics_, 7th ed., New York: John Wiley & Sons, 1993
