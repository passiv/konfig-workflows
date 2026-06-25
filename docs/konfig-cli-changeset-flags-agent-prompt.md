# Agent prompt: implement `konfig changeset --level` enum option

You are working in the Konfig CLI repository. Please update the `konfig changeset` command so automation callers can explicitly choose the semver bump level used in generated changeset files through a named enum option.

## Context

The reusable workflow in `passiv/konfig-workflows` now calls:

```bash
konfig changeset -a -m "$CHANGESET_MESSAGE" --level="$CHANGESET_TYPE"
```

`CHANGESET_TYPE` is supplied by the workflow caller and defaults to `patch`. Supported values should be:

- `patch`
- `minor`
- `major`

The CLI should own validation for this enum option. The workflow should not duplicate custom validation logic for the accepted values.

## Requested behavior

1. Add a named enum option to the `konfig changeset` command:
   - `--level=<patch|minor|major>`
2. Validate `--level` in the CLI parser/command layer, using the CLI framework's normal enum or choices support if available.
3. Preserve existing behavior when `--level` is omitted. If the command currently defaults generated changesets to `patch`, keep that default.
4. Keep existing options working, especially:
   - `-a` / all package or all SDK selection behavior.
   - `-m` / changeset message.
   - Any existing interactive behavior.
5. Add or update tests for:
   - `konfig changeset -a -m "Regenerate SDKs" --level="patch"`
   - `konfig changeset -a -m "Regenerate SDKs" --level="minor"`
   - `konfig changeset -a -m "Regenerate SDKs" --level="major"`
   - no `--level` option still using the existing default.
   - an invalid level, for example `--level="invalid"`, failing with a clear validation error.
6. Update CLI help text/docs so users can discover the new `--level` option and accepted values.

## Acceptance criteria

- Generated changeset files contain the selected bump level for `patch`, `minor`, and `major`.
- Existing callers that omit `--level` continue to behave exactly as before.
- The CLI fails fast and clearly if `--level` is not one of `patch`, `minor`, or `major`.
- Automated tests cover the new enum option and the default/no-option path.
