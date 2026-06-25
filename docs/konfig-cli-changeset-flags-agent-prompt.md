# Agent prompt: implement `konfig changeset` semver flags

You are working in the Konfig CLI repository. Please update the `konfig changeset` command so automation callers can explicitly choose the semver bump level used in generated changeset files.

## Context

The reusable workflow in `passiv/konfig-workflows` now calls:

```bash
konfig changeset -a -m "$CHANGESET_MESSAGE" --"$CHANGESET_TYPE"
```

`CHANGESET_TYPE` is validated before the CLI call and can only be one of:

- `patch`
- `minor`
- `major`

Existing workflow callers default to `patch`. The CLI must support these flags so the workflow can safely select the requested bump level.

## Requested behavior

1. Add explicit boolean flags to the `konfig changeset` command:
   - `--patch`
   - `--minor`
   - `--major`
2. Preserve existing behavior when no semver flag is provided. If the command currently defaults generated changesets to `patch`, keep that default.
3. Ensure exactly one semver flag is honored:
   - No flag: use the current default, expected to be `patch`.
   - One flag: use that semver type in the generated changeset.
   - More than one flag: fail with a clear validation error.
4. Keep existing options working, especially:
   - `-a` / all package or all SDK selection behavior.
   - `-m` / changeset message.
   - Any existing interactive behavior.
5. Add or update tests for:
   - `konfig changeset -a -m "Regenerate SDKs" --patch`
   - `konfig changeset -a -m "Regenerate SDKs" --minor`
   - `konfig changeset -a -m "Regenerate SDKs" --major`
   - no semver flag still using the existing default.
   - conflicting flags failing, for example `--patch --minor`.
6. Update CLI help text/docs so users can discover the new flags.

## Acceptance criteria

- Generated changeset files contain the selected bump level for `patch`, `minor`, and `major`.
- Existing callers that omit semver flags continue to behave exactly as before.
- The command fails fast and clearly if multiple semver flags are provided.
- Automated tests cover the new flags and the default/no-flag path.
