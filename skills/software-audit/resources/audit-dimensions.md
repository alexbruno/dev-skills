# Audit Dimensions

Apply **all nine dimensions** to every audit scope:

| Dimension | Key Questions |
|---|---|
| **Correctness** | Does the code behave as specified? Are edge cases handled? |
| **Security** | Are threats modeled and mitigations verified? (STRIDE/PASTA, SAST/SCA, secrets, authZ/authN, crypto) |
| **Architecture** | Are boundaries respected? Is coupling controlled? Is evolution enabled? |
| **Reliability** | Are failure modes understood? Is observability sufficient? Are SLOs defined? |
| **Performance** | Are bottlenecks identified? Is capacity planned? Are regressions caught? |
| **Maintainability** | Is code readable? Are tests trustworthy? Is onboarding feasible? |
| **Compliance & Supply Chain** | Are licenses compatible? Dependencies current? SBOM? Provenance? |
| **KISS** | Simplest solution that works; any over-engineering? |
| **SOLID** | SRP/OCP/LSP/ISP/DIP where the paradigm supports them; see `code-simplification`. |

> **Calibration**: KISS and SOLID are **contextual heuristics**, not universal gates. In non-OOP or idiomatic code, a "SOLID violation" is only a finding when it causes concrete harm (testability, coupling, change amplification). KISS/YAGNI win ties.

Canonical doctrine: `code-simplification`.
