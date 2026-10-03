# Full User E2E Contract

On the exact release candidate, run the installed `aella_cli` on an approved lab appliance.

1. Start the interactive CLI and confirm the prompt.
2. Run `show version`, `show hostname`, and at least one network/time read-only command.
3. Use contextual help for a mutating command without applying it.
4. Exit cleanly.
5. Record candidate SHA, host/lab identity, transcript/evidence, and PASS/FAIL.

Direct function calls, mocks, import checks, or unit tests alone are not Full User E2E.
