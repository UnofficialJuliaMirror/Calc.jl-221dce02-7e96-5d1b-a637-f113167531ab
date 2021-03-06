## Calc - an RPN calculator for the Julia REPL

This Julia package implements an RPN calculator for use at the Julia command
line (the REPL). The reverse-polish notation is popular with some scientific
calculators. See the [HP 48](http://www.ces.clemson.edu/ge/staff/park/Class/ENGR130/Handouts/BasicSkills/Calculators/HP48G/HP48G.html)
for example.

![calc](calc1.gif)

This package enables a new REPL mode. Use the equals key (`=`) at the start of a
line to start the calculator. RPN commands operate on a stack that is
redisplayed after every operation. Use Backspace at a blank prompt to return to
the normal Julia prompt.

The calculator keys tend to match that of [Emacs
Calc](https://www.gnu.org/software/emacs/manual/html_mono/calc.html). See the
following for a handy cheat sheet in several formats:

* https://github.com/SueDNymme/emacs-calc-qref/releases

Not all of the Emacs Calc operations are supported, but many basic arithmetic
operations are supported.

By default, trig operations are entered and displayed in degrees. This setting
can be changed with `m r` for radians and `m d` for degrees. Another setting
controls display of complex numbers. The default display is in rectangular
coordinates. Use `m p` to toggle between polar and rectangular coordinates.

This calculator also supports algebraic entry (normal Julia syntax). Within the
`calc> ` prompt, use `=` to switch to algebraic entry. This is useful for
evaluating expressions that are difficult with the default Calc.jl keys. Examples
include:

- Using a function that doesn't have a key defined.
- Entering a Julia variable onto the stack.
- Entering a negative number (`=` `-23` is an alternative to `23` `n`).

With algebraic entry, you can refer to stack variables with `_1`, `_2`, `_3`,
and so on. 

## Predefined Keys

In the following, `x` is the top value on the stack, and `y` is the second value
on the stack.

Many multi-key sequences start with prefixes with the following meanings:

| prefix | meaning                     |
| -------| --------------------------- |
| `I`    | Inverse                     |
| `H`    | Hyperbolic (other uses too) |
| `f`    | Function                    |
| `m`    | Mode                        |
| `u`    | Statistics                  |
| `s`    | Store                       |

#### General

| key           | operation                   |
| ---------     | --------------------------- |
| `<space>`     | Enter value on the stack    |
| `<enter>`     | Enter value on the stack    |
| `<del>`       | Delete `x` from the stack   |
| `<tab>`       | Swap `x` & `y` on the stack |
| `U`           | Undo                        |
| `D`           | Redo                        |
| `=`           | Trigger algebraic entry     |
| `<backspace>` | Exit the calculator         |

#### Arithmetic
| key   | operation          |
| ----- | -------------      |
| `+`   | `y + x`            |
| `-`   | `y - x`            |
| `n`   | `-x`,  negate      |
| `*`   | `y * x`            |
| `/`   | `y / x`            |
| `&`   | `1/x`              |
| `%`   | `y % x`, remainder |
| `A`   | `abs(x)`           |
| `fs`  | `sign(x)`          |
| `fn`  | `min(y, x)`        |
| `fx`  | `max(y, x)`        |
| `f[`  | `x - 1`            |
| `f]`  | `x + 1`            |
#### Algebraic
| key    | operation         |
| ------ | ----------------- |
| `Q`    | `sqrt(x)`         |
| `IQ`   | `x^2`             |
| `L`    | `log(x)`          |
| `E`    | `exp(x)`          |
| `IL`   | `exp(x)`          |
| `HL`   | `log10(x)`        |
| `IHL`  | `10^x`            |
| `B`    | `log(x, y)`       |
| `^`    | `y^x`             |
| `I^`   | `y^(1/x)`         |
| `fh`   | `sqrt(x^2 + y^2)` |

#### Trig
These trig functions use radian equivalents when in radian mode.

| key   | operation  |
| ----- | ---------- |
| `S`   | `sind(x)`  |
| `C`   | `cosd(x)`  |
| `T`   | `tand(x)`  |
| `IS`  | `asind(x)` |
| `IC`  | `acosd(x)` |
| `IT`  | `atand(x)` |
| `P`   | Insert π   |
#### Settings
| key   | operation                                        |
| ----- | ----------                                       |
| `mr`  | Use radians                                      |
| `md`  | Using degrees                                    |
| `mp`  | Toggle between polar and rectangular coordinates |
#### Complex numbers
| key   | operation                              |
| ----- | ----------                             |
| `X`   | `complex(y, x)`                        |
| `IX`  | the real and imaginary parts of `x`    |
| `Z`   | `y∠x`, polar entry with `x` in degrees |
| `IZ`  | the magnitude and angle of `x`         |
| `J`   | `conj(x)`                              |
| `G`   | `angle(x)`                             |
| `fr`  | `real(x)`                              |
| `fi`  | `imag(x)`                              |
#### Percentages
| key        | operation                                    |
| -----      | ----------                                   |
| `<meta-%>` | `x/100`, convert from a percentage           |
| `c%`       | `100x`, convert to a percentage              |
| `b%`       | `100(x-y)/y`, percent change from `y` to `x` |
#### Vectors
| key    | operation                    |
| -----  | ----------                   |
| &#124; | `vcat(y, x)`                 |
| `Vu`   | Unpack `x` to the stack      |
| `Vp`   | Pack the stack into a vector |
#### Statistics
| key    | operation    |
| -----  | ----------   |
| `u#`   | `length(x)`  |
| `u+`   | `sum(x)`     |
| `u*`   | `prod(x)`    |
| `uX`   | `maximum(x)` |
| `uN`   | `minimum(x)` |
| `uM`   | `mean(x)`    |
| `HuM`  | `median(x)`  |
| `uS`   | `std(x)`     |
| `HuS`  | `var(x)`     |
#### Storing
| key        | operation                                      |
| -----      | ----------                                     |
| `ss`       | Store `x` in the prompted variable             |
| `sS`       | Store the whole stack in the prompted variable |
| `<meta-k>` | Copy `x` to the clipboard                      |
| `<ctrl-k>` | Pop `x` to the clipboard                       |

## Defining keys

Keys can be defined or redefined with `Calc.setkeys` by passing a keymap
dictionary. Here is an example to map the key sequence `fp` to an operation
that finds the parallel combination of impedances of the first two arguments on
the stack:

```julia
Calc.setkeys(Dict("fp" => Calc.calcfun((y, x) -> 1 / (1/y + 1/x), 2)))
```

`Calc.calcfun` is the main function for defining operations. The first argument
is the function that performs the operation, and the second argument is the
number of arguments to that function. Neither `Calc.setkeys` nor `Calc.calcfun`
are exported. 
