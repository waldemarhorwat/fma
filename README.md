# Fused Multiply-Add

Stage: 0

Champion: Waldemar Horwat @waldemarhorwat

[Draft spec](https://waldemarhorwat.github.io/fma/)

Fused multiply-add is a three-operand IEEE floating-point operation that performs the computation $x × y + z$ with no intermediate rounding of the product — only the final result is rounded.  It is a standard operation supported by modern floating-point hardware and mandated by IEEE 754-2019.

## Background

The IEEE 754 standard mandates, in addition to other kinds of operations, six basic arithmetic operations: +, -, ×, /, square root, and fused multiply-add.  The first five were original, while fused multiply-add was added in IEEE 754-2008.

ECMAScript has implemented the first five of these since the beginning, but has not included fused multiply-add when the IEEE 754 spec was updated in 2008.  We propose adding it to the language.

Fused multiply-add is supported with IEEE 754 semantics in other major programming languages, including:
* C/C++: [`fma`, `fmaf`, `fmal`](https://en.cppreference.com/cpp/numeric/math/fma)
* Java `Math.Fma`
* C# [`Math.FusedMultiplyAdd`](https://learn.microsoft.com/en-us/dotnet/api/system.math.fusedmultiplyadd)
* Python: [`math.fma`](https://docs.python.org/3/library/math.html#math.fma)
* Rust: [`mul_add`](https://doc.rust-lang.org/std/primitive.f64.html#method.mul_add)

## Usage

[Fused multiply-add](https://en.wikipedia.org/wiki/Multiply%E2%80%93accumulate_operation), a basic floating-point instruction supported by modern hardware, is essential for correctness and performance in many numerical algorithms. Its lack in ECMAScript presents a challenge to anyone trying to adapt code that makes use of it in other languages — fused multiply-add is difficult to implement as a library, requiring hundreds of lines of slow and tricky code.

Common uses include computing dot products, matrix multiplication, evaluating polynomials, and neural network applications.

For a simple example of how one can use fused multiply-add, here's how we can compute the exact product of Numbers $a$ and $b$ with no rounding:

`let high = a * b` (using standard IEEE 754 multiplication)

`let err = Math.fma(a, b, -high)`

At this point, barring overflow or underflow of Number's exponent range, $MV(a) × MV(b) = MV(high) + MV(err)$, with $high$ containing the most significant bits of the result and $err$ containing the least significant bits of the result.
