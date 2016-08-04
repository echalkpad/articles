
[Original URL](https://www.npmjs.com/package/crocker/)

> Crank-Rocker – Design and Analysis. Crocker is a javascript namespace and function library, which has proven successful in designing and analyzing Crank-Rockers as special planar fourbar mechanisms...

[![](http://img.shields.io/:license-mit-blue.svg)](https://github.com/goessner/g2/license.txt) [![](https://img.shields.io/npm/v/crocker.svg)](https://www.npmjs.com/package/crocker/) [![](https://img.shields.io/npm/dt/crocker.svg)](https://www.npmjs.com/package/crocker) [![](https://img.shields.io/gemnasium/mathiasbynens/he.svg)](https://github.com/goessner/crocker)

## [Crank-Rocker – Design and Analysis.](https://www.npmjs.com/package/crocker/#crank-rocker----design-and-analysis)

`Crocker` is a javascript namespace and function library, which has proven successful in designing and analyzing Crank-Rockers as special planar fourbar mechanisms in my lectures. `Crocker.js` is tiny, it weights only 1.5 kB compressed and gzipped.

`Crocker` is _not_ meant to simulate mechanisms. For this and general fourbar mechanisms see [fourbar](https://github.com/goessner/fourbar).

Here is a _Crank-Rocker_ [animation](https://goessner.github.io/morphr/examples/complex.html) using the tiny [Morphr](https://github.com/goessner/morphr) javascript class and a [g2](https://github.com/goessner/g2) command queue for vector graphics.

## [Geometry](https://www.npmjs.com/package/crocker/#geometry)

A _Crank-Rocker_ is a specific _fourbar_ mechanism. It is composed of four binary links that are connected to each other by revolute joints. These links are called _crank_, _coupler_, _rocker_ and _frame_. Their lengths are named `a, b, c, d` respectively.

![](https://raw.githubusercontent.com/goessner/crocker/master/img/extremepositions.png)

The input _crank_ is fully rotatable and performs a complete 360° turn. At the same time the _rocker_ oscillates between its extreme positions `ψ_min` and `ψ_max`. Both _crank_ and _rocker_ are connected to the _fixed frame_ by pivots. The _coupler_ functions as a _connecting rod_ between that two.

![](https://raw.githubusercontent.com/goessner/crocker/master/img/extremepositions2.png)

The rocker's working angular range is named `ψ_0` and corresponds during tracking motion (crank and rocker move unidirectional - counterclockwise here) to the crank's angular range `φ_0`. On their way back they run in opposite directions. The crank's angle is `360° - φ_0` then and the rocker's `-ψ_0`. As a descriptive angle we use the difference `α = 180° - φ_0` here. So if `φ_0` is not 180° or `α` is not 0°, the crank needs different times for the rocker's _back_ and _forth_ motion. This degree of non-uniformity can be expressed by the ratio `t_f/t_b`, which is an important parameter of the engineer's point of view while designing a crank-rocker mechanism and directly relates to `α`. The other important design parameter of course is the rocker's angular range `ψ_0`.

As the four link lengths `a, b, c, d` sufficiently define a specific crank-rocker and `α` and `ψ_0` are added as important design parameters, there must be two relations between those now _six_ parameters. It can be shown that these relations are:

![](https://raw.githubusercontent.com/goessner/crocker/master/img/relations.png)

Another important value is the transmission angle `μ` between _coupler_ and _rocker_. Of special interest here is the _minimal_ transmission angle `μ_min` which occurs, when the _crank_ is collinear with the _frame_. This happens two times while the crank performs a complete rotation.

So the minimal transmission angle `μ_min` has to be calculated as `min(μ_inner,μ_outer)`. Please note, that - as a convention - the transmission angle is always considered to be in the range `0 < μ <= 90°`. So in case `μ` happens to be greater than 90°, its supplement angle (`180° - μ`) should be taken instead.

Ideally the transmission angle is always 90°, which isn't possible of course. So a specific design goal with crank-rockers is to _maximize_ the minimal transmission angle. A valuable optimization method regarding this can be applied with `crocker.js`.

See [[1]](https://www.npmjs.com/package/crocker/#fn1), [[2]](https://www.npmjs.com/package/crocker/#fn2) or [[3]](https://www.npmjs.com/package/crocker/#fn3) for further details.

## [Implementation](https://www.npmjs.com/package/crocker/#implementation)

The `crocker` library consists mostly of small functions. It is no `class`, so you cannot instantiate any Crank-Rocker objects.

The most simple way to use the `crocker` library is with `node.js`.

### [Node Example - Simple Crank-Rocker](https://www.npmjs.com/package/crocker/#node-example---simple-crank-rocker)

```
return w/180*MathPI;return w*180/MathPI;var crocker =require"../crocker.js";var a =60    d =120    psi0 = toRad80    alfa = crockeralfa_tfb10/9    b = crockerb_adadalfapsi0    c = crockerc_adadalfapsi0    muMin = crockermuMinabcd;console.log"psi0 = "+ toDegpsi0;console.log"alfa = "+ toDegalfa;console.log"b = "+ b;console.log"c = "+ c;console.log"muMin = "+ toDegmuMin;
```

Here is a table of `Crocker` functions. Please note that these functions are only loosely coupled. Usually the validity of the parameter set handed over to them is checked in the context, from where they are invoked. So please ensure to use valid parameters, as

- `a,b,c,d` are Grashof positive.
- `a` is the smallest link length.
- `ψ_0` is smaller than 180°.
- All angles are provided in radians.

just to name a few.

Function                   | Comment
-------------------------- | ---------------------------------------------------------------------------------------------------
`.psiMin(a,b,c,d)`         | Minimal Rocker angle `ψ_min`.
`.psiMax(a,b,c,d)`         | Maximal Rocker angle `ψ_max`.
`.phiMin(a,b,c,d)`         | Crank angle `ϕ_min` corresponding to rocker angle `ψ_min`.
`.phiMax(a,b,c,d)`         | Crank angle `ϕ_max` corresponding to rocker angle `ψ_max`.
`.phi0(a,b,c,d)`           | Crank angular range `ϕ_0` corresponding to `ψ_0` in unidirectional mode.
`.muInner(a,b,c,d)`        | Transmission angle `μ_inner` corresponding to crank's inner frame position.
`.muOuter(a,b,c,d)`        | Transmission angle `μ_outer` corresponding to crank's outer frame position.
`.muMin(a,b,c,d)`          | Minimal Transmission angle `μ_min`.
`.alfa_tfb(tfb)`           | Crank angular range difference `α` from time ratio `tf/tb` of rocker's forth and back moving times.
`.a_muMinMax(d,alfa,psi0)` | Crank length for maximized minimal transmission angle.

The other functions in the `crocker` library help to determine the missing parameters when four out of the six `a, b, c, d, α, ψ_0` are given. The following table shows those functions with their required arguments:

function    | `a`  | `b`  | `c`  | `d`  | `α`  | `ψ_0`
----------- | ---- | ---- | ---- | ---- | ---- | -----
`.alfa`     | `__` | ``   | `__` | ``   | `-`  | `-`
`.alfa_abc` | `__` | ``   | `__` | `-`  | `-`  | ``
`.alfa_abd` | `__` | ``   | `-`  | `__` | `-`  | ``
`.alfa_acd` | `__` | `-`  | ``   | `__` | `-`  | ``
`.alfa_bcd` | `-`  | `__` | ``   | `__` | `-`  | ``
`.psi0`     | `__` | ``   | `__` | ``   | `-`  | `-`
`.psi0_abc` | `__` | ``   | `__` | `-`  | ``   | `-`
`.psi0_abd` | `__` | ``   | `-`  | `__` | ``   | `-`
`.psi0_acd` | `__` | `-`  | ``   | `__` | ``   | `-`
`.psi0_bcd` | `-`  | `__` | ``   | `__` | ``   | `-`
`.a_bc`     | `-`  | `__` | ``   | `-`  | `__` | ``
`.a_bd`     | `-`  | `__` | `-`  | ``   | `__` | ``
`.a_cd`     | `-`  | `-`  | `__` | ``   | `__` | ``
`.b_ac`     | `__` | `-`  | ``   | `-`  | `__` | ``
`.b_ad`     | `__` | `-`  | `-`  | ``   | `__` | ``
`.b_cd`     | `-`  | `-`  | `__` | ``   | `__` | ``
`.c_ab`     | `__` | ``   | `-`  | `-`  | `__` | ``
`.c_ad`     | `__` | `-`  | `-`  | ``   | `__` | ``
`.c_bd`     | `-`  | `__` | `-`  | ``   | `__` | ``
`.d_ab`     | `__` | ``   | `-`  | `-`  | `__` | ``
`.d_ac`     | `__` | `-`  | ``   | `-`  | `__` | ``
`.d_bc`     | `-`  | `__` | ``   | `-`  | `__` | ``

### [Node Example - Optimal Crank-Rocker](https://www.npmjs.com/package/crocker/#node-example---optimal-crank-rocker)

```
return w/180*MathPI;return w*180/MathPI;var Crocker =require"./crocker.js"Crocker;var d =120    psi0 = toRad80    alfa = Crockeralfa_tfb10/9    a = Crockera_muMinMaxdalfapsi0    b = Crockerb_adadalfapsi0    c = Crockerc_adadalfapsi0    muMin = CrockermuMinabcd;console.log"psi0 = "+ toDegpsi0;console.log"alfa = "+ toDegalfa;console.log"a = "+ a;console.log"b = "+ b;console.log"c = "+ c;console.log"muMin = "+ toDegmuMin;
```

Here is a [graphics example](https://goessner.github.io/crocker/Examples/crockerdraw.html) using `crocker.js` and [g2](https://github.com/goessner/g2).

## [References](https://www.npmjs.com/package/crocker/#references)

[[1]]() S. Gössner: [Getriebelehre - Vektorielle Analyse ebener Mechanismen](http://www.amazon.de/Getriebelehre-Vektorielle-Analyse-ebener-Mechanismen/dp/3832530827)

[[2]]() J.M. McCarthy: [Geometric Design of Linkages](http://www.amazon.com/Geometric-Linkages-Interdisciplinary-Applied-Mathematics/dp/1441978917)

[[3]]() J.J. Uicker: [Theory of Machines and Mechanisms](http://www.amazon.co.uk/Theory-Machines-Mechanisms-Joseph-Uicker/dp/0195371232)

## [GitCDN](https://www.npmjs.com/package/crocker/#gitcdn)

Use the link <https://gitcdn.xyz/repo/goessner/crocker/master/crocker.min.js> for getting the latest commit as a raw file.

In HTML use ...

## [License](https://www.npmjs.com/package/crocker/#license)

`crocker.js` is licensed under the terms of the MIT License. See LICENSE-MIT for details.

## [API Reference](https://www.npmjs.com/package/crocker/#api-reference)

See the [API Reference](https://github.com/goessner/crocker/blob/master/api.md) for details.

## [Change Log](https://www.npmjs.com/package/crocker/#change-log)

All notable changes to this project will be documented here. This project adheres to Semantic Versioning.

## [0.8.6 - 2016-07-06](https://www.npmjs.com/package/crocker/#086---2016-07-06)

### [Added](https://www.npmjs.com/package/crocker/#added)

## [0.8.0 - 2016-03-25](https://www.npmjs.com/package/crocker/#080---2016-03-25)

### [First Commit to Github](https://www.npmjs.com/package/crocker/#first-commit-to-github)
