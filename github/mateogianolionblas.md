# mateogianolio/nblas

[Original URL](https://github.com/mateogianolio/nblas)

> C++ bindings for CBLAS (Basic Linear Algebra Subprograms). Developed in parallel with vectorious, where it will eventually be included. $ npm install mateogianolio/nblas $ npm test $ npm...

C++ bindings for CBLAS (Basic Linear Algebra Subprograms). Developed in parallel with [vectorious](https://github.com/mateogianolio/vectorious), where it will eventually be included.

```
$ npm install mateogianolio/nblas
$ npm test
$ npm run benchmark

var nblas = require('nblas');

var f64a = new Float64Array([1, 2, 3]),
 f64b = new Float64Array([4, 5, 6]);

nblas.ddot(3, f64a, 1, f64b, 1); // 32

var f32a = new Float32Array([1, 2, 3]),
 f32b = new Float32Array([4, 5, 6]);

nblas.sdot(3, f32a, 1, f32b, 1); // 32
```

Double precision functions expect `Float64Array` vectors, single precision functions expect `Float32Array` vectors.

## [](https://github.com/mateogianolio/nblas#dot)`dot`

- Produces a single precision dot product from two single precision vectors `x` and `y` of size `n`.

  ```
  float sdot (
   const int n,
   const float *x, const int inc_x,
   const float *y, const int inc_y
  );
  ```

- Produces a double precision dot product from two double precision vectors `x` and `y`.

  ```
  double ddot (
   const int n,
   const double *x, const int inc_x,
   const double *y, const int inc_y
  );
  ```

## [](https://github.com/mateogianolio/nblas#axpy)`axpy`

- Adds a double precision vector `x` scaled with a double precision scalar `alpha` to another double precision vector `y`.

  ```
  void daxpy (
   const int n,
   const double alpha,
   const double *x, const int inc_x,
   const double *y, const int inc_y
  );
  ```

- Adds a single precision vector `x` scaled with a single precision scalar `alpha` to another single precision vector `y`.

  ```
  void saxpy (
   const int n,
   const float alpha,
   const float *x, const int inc_x,
   const float *y, const int inc_y
  );
  ```

## [](https://github.com/mateogianolio/nblas#scal)`scal`

- Scales a double precision vector `x` with a double precision scalar `alpha`.

  ```
  void dscal (
   const int n,
   const double alpha,
   const double *x, const int inc_x
  );
  ```

- Scales a single precision vector `x` with a single precision scalar `alpha`.

  ```
  void sscal(
   const int n,
   const float alpha,
   const float *x, const int inc_x
  );
  ```

## [](https://github.com/mateogianolio/nblas#copy)`copy`

- Copies a double precision vector `x` to a double precision vector `y`.

  ```
  void dcopy (
   const int n,
   const double *x,
   const int inc_x,
   const double *y,
   const int inc_y
  );
  ```

- Scales a single precision vector `x` to a single precision vector `y`.

  ```
  void scopy (
   const int n,
   const float *x,
   const int inc_x,
   const float *y,
   const int inc_y
  );
  ```

## [](https://github.com/mateogianolio/nblas#swap)`swap`

- Swaps the elements of a double precision vector `x` with a double precision vector `y`.

  ```
  void dswap (
   const int n,
   const double *x,
   const int inc_x,
   const double *y,
   const int inc_y
  );
  ```

- Swaps a single precision vector `x` with a single precision vector `y`.

  ```
  void sswap (
   const int n,
   const float *x,
   const int inc_x,
   const float *y,
   const int inc_y
  );
  ```
