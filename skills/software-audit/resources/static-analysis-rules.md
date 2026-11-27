# Static Analysis Rules (Sonar/Lint-Style)

> Loaded by `software-audit` for **implementation** and **comprehensive** audits
> only. The auditor emulates a high-quality static analyzer (SonarQube, ESLint,
> CodeQL, Semgrep) while applying human architectural judgment.

## Examination Scope

- **All production source files** in the audit scope (exclude generated, vendor,
  and test files unless explicitly included).
- **Configuration files** (TSConfig, ESLint, Prettier, Dockerfile, CI/CD, k8s
  manifests, Terraform).
- **Dependency manifests** (`package.json`, `go.mod`, `pom.xml`, `Cargo.toml`,
  `requirements.txt`, `pyproject.toml`).
- **Infrastructure-as-Code** files.

## 1. Security Vulnerabilities (OWASP Top 10 / CWE/SANS 25)

| Category | What to Detect | Example Patterns |
|---|---|---|
| **Injection** | SQL/NoSQL/Command/LDAP injection via unsanitized input | Raw string interpolation in queries, `exec()` with user input, `eval()` |
| **Broken Auth** | Weak password policy, session fixation, token exposure in URL/logs | JWT without expiry, hardcoded secrets, session ID in query string |
| **Sensitive Data Exposure** | PII/secrets in logs, error messages, client-side storage | `console.log(password)`, localStorage tokens, stack traces in prod |
| **XXE** | XML parser with external entity processing enabled | `DocumentBuilderFactory` without `FEATURE_SECURE_PROCESSING` |
| **Broken Access Control** | Missing authorization checks, IDOR, forced browsing | Direct object reference without ownership check, missing `@PreAuthorize` |
| **Security Misconfig** | Debug enabled, default creds, open CORS, missing security headers | `app.debug = true`, `Access-Control-Allow-Origin: *`, no HSTS/CSP |
| **XSS** | Unescaped output in HTML/JS/CSS contexts | `innerHTML = userInput`, `dangerouslySetInnerHTML`, template without auto-escape |
| **Insecure Deserialization** | Deserializing untrusted data without validation | `ObjectInputStream.readObject()`, `pickle.load()` |
| **Known Vulnerabilities** | Vulnerable dependencies (see SCA) | CVE-2021-44228 (Log4Shell), prototype pollution |
| **Insufficient Logging** | Missing audit trails for security events | No login/logout logs, no failed-auth alerts, no admin action logs |

## 2. Code Smells & Maintainability (SonarQube Categories)

| Category | Rule | Threshold |
|---|---|---|
| **Cognitive Complexity** | Per function/method | > 15 (warn), > 25 (critical) |
| **Cyclomatic Complexity** | Per function/method | > 10 (warn), > 20 (critical) |
| **Function Length** | Lines per function | > 50 (warn), > 100 (critical) |
| **Parameter Count** | Parameters per function | > 4 (warn), > 7 (critical) |
| **Nesting Depth** | Max nesting level | > 3 (warn), > 5 (critical) |
| **Duplicate Code** | Blocks ≥ 10 lines / 100 tokens | > 3% (warn), > 5% (critical) |
| **Dead Code** | Unused functions, variables, imports, parameters | Any (warn) |
| **Magic Numbers** | Numeric literals in logic (except 0, 1, -1) | Any non-constant (warn) |
| **Large Classes/Files** | Lines per class/file | > 500 (warn), > 1000 (critical) |
| **God Classes** | Too many responsibilities | > 20 methods, low cohesion (warn) |
| **Feature Envy** | Method uses more data from another class | Qualitative (warn) |
| **Data Classes** | Only getters/setters, no behavior | Qualitative (warn) |
| **Switch Statements** | Long switch on type codes | > 5 cases (warn) |
| **Speculative Generality** | Unused abstraction, YAGNI code | Any (warn) |

## 3. Reliability & Correctness

| Category | What to Detect |
|---|---|
| **Null/Undefined Dereference** | Accessing properties on potentially null values without guards |
| **Resource Leaks** | Unclosed streams, connections, file handles, listeners, timers |
| **Exception Swallowing** | Empty catch blocks, `catch {}` without logging/rethrowing |
| **Unhandled Promises/Rejections** | Async functions without try/catch, missing `.catch()` |
| **Race Conditions** | Non-atomic read-modify-write, TOCTOU, shared mutable state |
| **Infinite Loops/Recursion** | Missing base case, unbounded iteration |
| **Off-by-One Errors** | Array/index boundary mistakes |
| **Type Safety Violations** | `any` types, unsafe casts, `// @ts-ignore` |
| **Floating Point Precision** | Equality checks on floats, currency without a decimal library |
| **Timezone/Date Bugs** | `Date` without timezone, mutable date objects, parsing assumptions |

## 4. Performance Anti-Patterns

| Category | What to Detect |
|---|---|
| **N+1 Queries** | Loop executing DB queries, missing eager loading/batching |
| **Synchronous I/O in Async Context** | `fs.readFileSync` in an async handler, blocking the event loop |
| **Unnecessary Allocations** | Object creation in hot loops, string concatenation in loops |
| **Missing Pagination** | Unbounded `SELECT *`, `fetchAll()` without limits |
| **Inefficient Algorithms** | O(n²) where O(n log n) is possible, redundant computations |
| **Memory Leaks** | Growing caches without eviction, unclosed observers, global accumulators |
| **Blocking Operations** | Heavy computation on the main thread, missing worker offload |
| **Connection Pool Exhaustion** | Missing pool sizing, leaked connections |

## 5. Architecture & Design (SOLID Mapping)

> SOLID is a **contextual heuristic**. Flag a principle only when the violation
> causes concrete harm; do not force OOP abstractions onto idiomatic code.

| Category | What to Detect | Principle |
|---|---|---|
| **Layer Violations** | Domain calling infrastructure directly, UI calling DB | DIP / Architecture |
| **Circular Dependencies** | Module A → B → A | OCP / Architecture |
| **Missing Abstractions** | Concrete dependencies where a seam is genuinely needed | DIP |
| **Violated Boundaries** | Cross-module imports forbidden by architecture | DIP / Architecture |
| **Anemic Domain Models** | Entities with only getters/setters, logic in services | SRP / DDD |
| **Primitive Obsession** | Primitives for domain concepts (use Value Objects) | SRP / DDD |
| **Inappropriate Intimacy** | Classes knowing too much about each other's internals | SRP / ISP |
| **Missing ADRs** | Significant decisions without documented rationale | OCP / Architecture |
| **God Classes** | Classes with > 20 methods, low cohesion | SRP |
| **Feature Envy** | Method uses more data from another class than its own | SRP |
| **Fat Interfaces** | Interfaces with > 5 methods, clients use < 50% | ISP |
| **Concrete Dependencies in Constructors** | High-level modules constructing low-level concretions | DIP |
| **Subtype Contract Violations** | Subtype strengthens preconditions / weakens postconditions | LSP |

## 6. Testing Quality (Beyond Coverage)

| Category | What to Detect |
|---|---|
| **Coverage Gaps** | Uncovered critical paths, security-sensitive code < 90% |
| **Mutation Survivors** | Mutants not killed (weak assertions) |
| **Flaky Tests** | Non-deterministic, time-dependent, shared state |
| **Test Pyramid Imbalance** | Too many E2E, too few unit tests |
| **Brittle Tests** | Over-mocking, testing implementation instead of behavior |
| **Missing Edge Cases** | Empty inputs, nulls, boundaries, concurrency, errors |
| **Slow Tests** | Unit tests > 100ms, integration > 5s |
| **No Contract Tests** | APIs consumed by other teams/services |

## 7. Configuration & Infrastructure

| Category | What to Detect |
|---|---|
| **Hardcoded Config** | URLs, ports, credentials in code (use env vars/config) |
| **Missing .dockerignore** | Bloated images, secrets in build context |
| **Insecure Docker** | Running as root, `--privileged`, `latest` tags, no `HEALTHCHECK` |
| **K8s Misconfig** | No resource limits/requests, no liveness/readiness probes, `hostNetwork` |
| **Secrets in CI/CD** | Hardcoded tokens in pipelines, unprotected variables |
| **Missing SBOM** | No Software Bill of Materials generation |

## 8. KISS Violations (Simplicity & Directness)

| Category | What to Detect | Example Patterns |
|---|---|---|
| **Speculative Generality** | Abstractions, interfaces, factories with 1 consumer or 0 tests | `StrategyFactory` for a single strategy |
| **Premature Abstraction** | Base classes, generics, plugin architectures before need | `AbstractBaseService` with no subclasses |
| **Unnecessary Indirection** | Pass-through wrappers, single-method delegates, proxy layers | `UserServiceWrapper` → `UserService` → `UserRepo` |
| **YAGNI Code** | Features, config, endpoints not in the current spec | `exportCsv()` never called, unused feature flags |
| **Clever Code** | Cryptic one-liners, nested ternaries, dense chains | Code golf in a hot path |
| **Over-Parameterization** | > 4 params where a config object would clarify | `createUser(name, email, role, active, locale, tz, opts)` |
| **Premature Optimization** | Caching, pooling, async patterns without a measured bottleneck | `LRUCache` on a cold path |
| **Pattern Overuse** | Patterns where simple code suffices | `Visitor` for 2 node types, `Builder` for 3 fields |

## Rule Reference Mappings

| Ecosystem | Key Rule Sets |
|---|---|
| **JavaScript/TypeScript** | ESLint (recommended, security, typescript-eslint), SonarJS, `eslint-plugin-security`, `eslint-plugin-sonarjs` |
| **Python** | Ruff, Bandit, Flake8, Pylint, MyPy (strict), Semgrep Python rules |
| **Go** | `go vet`, `staticcheck`, `golangci-lint`, `gosec`, `revive` |
| **Java/Kotlin** | SpotBugs, Error Prone, SonarJava, Detekt, ArchUnit |
| **C#/.NET** | SonarC#, Roslyn Analyzers, StyleCop, SecurityCodeScan |
| **Rust** | Clippy, `cargo audit`, `cargo deny`, Rudra |
| **Multi-language** | SonarQube rules, CodeQL queries, Semgrep Registry, OWASP Code Pulse |

**Agent directive**: when examining code in a specific language, **mentally apply**
the strict/recommended rule sets for that ecosystem. Flag violations even if no
linter is configured in the project.

## Output Requirements for Code Examination Findings

````markdown
#### AUDIT-<YYYYMMDD>-<NNN> — [<Dimension>] — <Rule/Category> — <Concise Title>
- **Location**: `<file/path.ext:L<start>-L<end>>`
- **Severity**: Critical | High | Medium | Low | Informational
- **Discovery Method**: `<CodeGraph query | Repomix pattern | Manual review | SAST/SCA/Linter: <tool>>`
- **Rule**: `<ESLINT_RULE|SONAR_RULE|CWE|CUSTOM|KISS|SOLID>` — `<rule-id-or-name>`
- **Evidence**:
  ```<lang>
  <exact code snippet with 3-5 lines of context>
  ```
- **Why It Matters**: <security/reliability/maintainability impact in this context>
- **Recommendation**: <specific fix with corrected code example>
- **References**: [Rule Doc](url), [CWE-XXX](url), [OWASP](url)
- **Status**: Open
````

## Sampling Strategy for Large Codebases (> 500 files)

- **100% CodeGraph queries**: architecture, security call chains, cycles, dead
  code, impact analysis, KISS/SOLID structural queries.
- **100% Repomix scan**: full-pattern search across the snapshot.
- **100% file reads**: security-critical paths, modified files in scope,
  config/CI/CD/infra files.
- **Targeted reads**: files surfaced by CodeGraph/Repomix — not random sampling.
- **Full automated scan**: SAST, linters, SCA on 100%; the auditor reviews output.
