# An Introduction to MATLAB metaprogramming

[Original URL](http://daniel-levin.github.io/2015/05/16/intro-to-matlab-metaprog.html)

> This post will provide a brief introduction to metaprogramming in MATLAB. MATLAB is both an old and highly specialised language. For this reason, it lacks many of the features found in more modern...

This post will provide a brief introduction to metaprogramming in MATLAB. MATLAB is both an old and highly specialised language. For this reason, it lacks many of the features found in more modern and general purpose programming languages. However, with a few simple tools, we will find that MATLAB is flexible enough to allow for very simple metaprogramming.

Roughly speaking, metaprogramming is the process of treating a program as data - meaning that a program can be generated or manipulated as though it were a normal piece of data. Languages such as Ruby expose a number of metaprogramming features. MATLAB offers no such suite of tools whose express purpose is metaprogramming. With a little bit of creativity, this can actually be done, using the symbolic toolbox.

Note that it might be a _bit_ of a stretch to call the content of this post metaprogramming, but I feel that it is sufficiently similar to justify the label.

## Dependencies

The code in this post works because of the following:

- Functions are first class objects
- Functions can be declared anonymously
- Symbolic expressions can be mutated programatically.

In broad strokes, this lets us declare symbolic expressions, modify them, and turn them into functions. This is the core idea in this post.

## Using and modifying symbolic expressions

First, let's declare a symbolic variable

MATLAB will now treat `x` not as a numerical variable but rather a symbol. This lets us build up more complicated expressions. For example:

```
>> x + x
ans =
2*x
>> x * x
ans =
x^2
>> sin(x)^2 + cos(x)^2
ans =
sin(x)^2 + cos(x)^2
```

Of course, the last expression is just . MATLAB knows this:

```
>> simplify(sin(x)^2 + cos(x)^2)
ans =
1
```

We can also do calculus:

```
>> diff(3*x^2 + sin(x))
ans =
6*x + cos(x)
>> int(6*x + cos(x))
ans =
sin(x) + 3*x^2
>> taylor(exp(x), x, 0, 'Order', 10)
ans =
x^9/362880 + x^8/40320 + x^7/5040 + x^6/720 + x^5/120 + x^4/24 + x^3/6 + x^2/2 + x + 1
```

The symbolic toolbox comes with a function called `matlabFunction` that will turn a symbolic expression into a function:

```
>> p = matlabFunction(x^2 + 2*x + sin(x))
p =
@(x)x.*2.0+sin(x)+x.^2
>> p(0.2)
ans =
0.6387
```

With these tools in hand, we can start writing programs that write programs.

## Fourier Series Approximations

A Fourier series is an approximation of a function using a linear combination of sines and cosines of different periods. A real function can be approximated by such a series as follows:

where:

The approximation is exact for certain classes of functions, but we will write a program to approximate arbitrary real functions using a truncated version of the series.

Now, we will implement a function that takes a function, and returns another function that is its (truncated) Fourier series approximation. There are better ways to implement a Fourier series, but this is a neat demonstration of metaprogramming in MATLAB.

The function should work as follows

```
>> g = fourierapprox(f, N);
```

where `f` is a function and `N` is the highest term to approximate `f` to, with `g` being the approximating function.

First, we create a symbolic expression that will eventually be converted to a MATLAB function, and call the independent variable . Then, we add the first term to the series:

```
function[fn] =fourierapprox(f, N)
series = sym();
 x = sym('x');
 a_0 = (1/pi) * integral(f, -pi, pi);
 series = series + (1/2)*a_0;
 ...
end
```

(Notice that we are using MATLAB's built-in numerical routines to compute the necessary integrals.)

Next, we need to add a finite (`N`) linear combination of sines and cosines to `series`:

```
function[fn] =fourierapprox(f, N)
series = sym();
 x = sym('x');
 a_0 = (1/pi) * integral(f, -pi, pi);
 series = series + (1/2)*a_0;
 for n = 1:N
 a_n = (1/pi) * integral(@(x) f(x) .* cos(n*x), -pi, pi);
 series = series + a_n * cos(n*x);
 b_n = (1/pi) * integral(@(x) f(x) .* sin(n*x), -pi, pi);
 series = series + b_n * sin(n*x);
 end
 fn = matlabFunction(series);
end
```

The last line is key: it converts the purely symbolic variable `series` into an executable function. This means that `fn` behaves as though it was hard coded! Calling this 'metaprogramming' is correct, because we generated this function programatically.

## Running this function

Let's use `fourierapprox` to generate some approximating functions.

The following function will plot the original function and the Fourier approximation on the same axes, over the specified interval.

```
function[ output_args ] =plotfourierapprox(f, N, a, b)
fplot(f, [a b], 'r');
 hold on;
 pn = fourierapprox(f, N);
 fplot(pn, [a b], 'b');
end
```

### Square Wave

```
>> plotfourierapprox(@square, 5, -5, 5)
```

![Square](http://daniel-levin.github.io/square_5.png)

```
>> plotfourierapprox(@square, 15, -5, 5)
```

![Square](http://daniel-levin.github.io/square_15.png)

### 

```
>> plotfourierapprox(@(x) x.^3, 10, -4, 4)
```

![Cube](http://daniel-levin.github.io/cube_10.png)

Metaprogramming is not a programming paradigm typically used in MATLAB. I wanted to demonstrate that it is not only possible but also useful.
