# Tree mapping with ω

Looking through the code for the solvers, you may notice a "ω" that keeps popping up, in expressions like:
```python
(ω(x) + ω(y)).ω
```
or the equivalent
```python
(x**ω + y**ω).ω
```
which is just a different (fewer-bracket-using) syntax.

## Usage

The above are equivalent to just:
```python
jax.tree_util.tree_map(lambda a, b: a + b, x, y)
```
and are designed just to be a convenient syntax for broadcasting operations over a PyTree.

Other operations are of course defined: `ω` understands several of the built-in Python operators, including addition, subtraction, matrix multiplication etc.

!!! tip

    As a convention, we both structure and destructure `ω` on a single line; we never assign a variable that is `ω`-wrapped. Passing `ω`-variables around starts to feel a bit too magic.

!!! warning

    Note that when doing e.g. `a + ω(b)`, with the `ω` on the right, then things will probably break if `a` is a NumPy array. This is because the overload `a.__add__(ω(b))` is checked before `ω(b).__radd__(a)`, and NumPy will accept pretty much anything. The fix is to wrap `a` in a `jnp.ndarray` (which correctly raises `NotImplemented` instead).

## Commentary

### Non-goals

Making anything like `jax.numpy.maximum(x**ω, y**ω)` work is not a goal for `ω`. Just use a regular `jax.tree_util.tree_map` in these situtions. `ω` only aims to support overloadable Python operations, and as a convenience single-argument functions via e.g. `ω(x).call(jax.numpy.abs)`.

### On syntax

The syntax might look a little bit odd. The rationale is as follows:

- A single letter `ω` is used to avoid taking up too much space, so as to keep the terse syntax that e.g. `x + y` provides.
- We use a Greek letter, instead of the more typical Latin characters, to aid visual identification and minimise visual noise.
    - Set up an alternate Greek keyboard if you haven't already. (The author is a mathematician and therefore already has this configured...)
- We support the `... ** ω` operation, as well as `ω(...)`, to minimise the number of brackets. For some expressions this reduces visual noise.
- Specifically the `**` operation is used as it has a high precedence -- in particular higher than arithmetic operations. It also pairs visually conveniently with `.ω` (the unwrapping operation): `**` is two high dots, and `.` is one low dot.

### See also

See also [tree-math](https://github.com/google/tree-math) for a similar project with a similar idea. One key difference is that `ω` will broadcast leaves together, whilst `tree-math` will not (and is instead meant to feel like a one-dimensional vector in its usage).
