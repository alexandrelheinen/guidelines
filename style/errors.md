# Error handling

## Validate at boundaries, trust internals

Validate input where it enters the system — API request bodies, CLI
arguments, config file parsing, deserialized messages. Once data has
crossed that boundary and been validated, don't re-validate it defensively
throughout the internal call chain; trust the type system and the
boundary check.

- Web/API layers: validate request bodies with a schema library (e.g. Zod)
  before they reach business logic.
- CLI/daemon startup: validate config fully at load time; fail with a clear
  message and non-zero exit code before doing any work, rather than failing
  midway through.

## Fail fast, no silent defaults

A function that receives an invalid argument should raise/return an error
immediately, not substitute a default value and continue. Silent fallbacks
turn configuration mistakes into hard-to-trace runtime behavior.

## Error strategy by layer (C/C++, embedded, and similarly latency-sensitive code)

Different layers of the same system warrant different error-handling
strategies — this is not an inconsistency, it's a deliberate tradeoff
between safety and hot-path cost:

| Layer | Strategy |
|---|---|
| Startup / config loading | Result types or return codes, checked explicitly (`result.ok()`); log and exit non-zero on failure |
| Main loop / orchestration | Return codes, bounded retry with backoff |
| Driver / hot path | Return codes or sentinel values (e.g. a `Quality::kBad` enum); no exceptions, no heap allocation |
| Signal handlers | `volatile sig_atomic_t` flag assignment only — nothing else is safe |

RAII (or the language's equivalent — Python context managers, Rust `Drop`)
handles resource cleanup; destructors/`__exit__`/`Drop::drop` must not
themselves throw or raise.

## Exceptions vs. result types (application code)

For application-level Python, TypeScript, and similar: use exceptions for
truly exceptional conditions, and build a small domain-specific exception
hierarchy rather than raising bare `Exception`/`Error`.

```python
class LuthierError(Exception):
    """Base exception for this package."""

class InvalidInputError(LuthierError):
    """Raised when caller-provided data fails validation."""

class ReconstructionError(LuthierError):
    """Raised when a reconstruction stage cannot produce a result."""
```

Constructors and factory functions validate and raise immediately
(`ArgumentOutOfRangeException`, `ValueError`, etc.) rather than constructing
an object in an invalid state.

## Logging

- Never log secrets, tokens, or credentials — check this on every new log
  statement, not just at review time.
- Daemons/services log through the platform's logging facility (`syslog`,
  a structured logger), never raw `print`/`std::cout`, so log level and
  routing are controlled centrally.
- A caught-and-swallowed error is a bug unless it's logged at a level that
  makes it discoverable.

## Stubs and not-yet-implemented code

Mark an intentionally unimplemented function by raising `NotImplementedError`
(Python) and pairing it with `@pytest.mark.xfail(strict=True,
raises=NotImplementedError)` on its test, or `GTEST_SKIP()` with a
descriptive reason (C++) — never a bare `pass`/empty body that silently
returns success. Remove the stub marker in the same commit that lands the
real implementation.

## Avoid speculative error handling

Don't add error handling, retries, or fallbacks for a failure mode that
can't actually occur given the code's own guarantees. Handle the failure
modes that exist; don't design for hypothetical ones.
