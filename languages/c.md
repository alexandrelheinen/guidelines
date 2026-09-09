# C

Layered on top of [cpp.md](cpp.md): the same `.clang-format` baseline,
naming conventions, and error-handling-by-layer principles apply. This
file only covers what is specifically different for C.

## No RAII

C has no destructors, so resource cleanup is manual: pair every
acquire with an explicit release on every exit path, including error
paths. `goto cleanup;` patterns are acceptable and often clearer than
duplicating cleanup code at each early return.

```c
int result = -1;
FILE *f = fopen(path, "r");
if (!f) {
    goto cleanup;
}
/* ... */
result = 0;
cleanup:
if (f) {
    fclose(f);
}
return result;
```

## No exceptions

Every fallible function returns an error code or uses an out-parameter for
the result, checked at every call site: there is no alternative error
channel. See
[style/errors.md](../style/errors.md#error-strategy-by-layer-cc-embedded-and-similarly-latency-sensitive-code).

## Naming

Same as C++: `snake_case` functions and variables, `UPPER_CASE`
constants/macros, `snake_case` file names. No classes, so the
"file matches the class" rule becomes "file matches the module's primary
struct or subsystem" (`gpio.c`/`gpio.h` for the GPIO subsystem).

## Testing

Fewer C-specific unit-test frameworks are in active use here than for
C++; where a project mixes C and C++, GTest (from a `.cpp` test file
calling into the C API via `extern "C"`) is an acceptable way to reuse the
same test infrastructure rather than adopting a second framework.
