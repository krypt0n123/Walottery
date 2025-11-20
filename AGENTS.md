# Repository Guidelines

## Project Structure & Module Organization
- Root README describes the lottery concept; contract code lives in `walottery/sources/walottery.move`.
- `Move.toml` (edition 2024.beta) defines the `walottery` named address; keep package metadata in sync with deployments.
- Tests belong in `walottery/tests`; the template file is currently commented out and can be expanded with `#[test_only]` helpers.
- Assets or docs should sit beside the package; avoid introducing extra top-level Move packages without a clear reason.

## Build, Test, and Development Commands
Use the Move CLI inside the `walottery` directory:
```bash
cd walottery
sui move build            # type-check the package
sui move test             # run Move unit tests
sui move publish --gas-budget 100000000   # publish (adjust budget/network as needed)
```
Run `sui move prove` only when verification is configured; keep CI-friendly flags minimal.

## Coding Style & Naming Conventions
- Follow Move 2024.beta defaults; use 4-space indentation and keep lines concise.
- Modules, structs, and functions use snake_case (e.g., `physical_lottery`, `create_lottery`); constants stay in SCREAMING_SNAKE_CASE.
- Prefer straightforward control flow over cleverness; validate inputs early with clear abort codes.
- Comments can be bilingual, but favor short English summaries; keep user-facing strings stable for front-end expectations.

## Testing Guidelines
- Write focused unit tests under `walottery/tests`, covering deadline checks, duplicate participation, deterministic shuffles (mock randomness when possible), and claim eligibility.
- Name tests after behavior (`test_join_rejects_duplicates`) and isolate mutable state per test.
- Assert on emitted abort codes for negative cases to lock down invariants.
- When adding helpers, gate them with `#[test_only]` to avoid bloating on-chain bytecode.

## Commit & Pull Request Guidelines
- History is minimal; adopt concise, imperative commit subjects (e.g., `add claim validation`, `refine draw randomness`).
- For PRs, include: purpose, key changes, testing performed (`sui move test`), and any deployment considerations (addresses, gas budgets).
- Link issues when relevant and attach screenshots or logs for user-facing changes (e.g., claim flow UI).
- Keep diffs small and scoped; note any breaking changes to storage layout or abort codes.

## Security & Configuration Tips
- Deadlines and quantities are user-supplied; validate them rigorously and avoid bypasses that could settle twice.
- `seal_approve` intentionally restricts decryption to the creator—review all changes that touch this guard.
- Do not store plaintext shipping data; rely on encrypted payloads and document expected serialization/encryption schemes in code comments when they change.
