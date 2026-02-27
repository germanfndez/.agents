# Commit message template (Conventional Commits)

```text
<type>(<scope>): <summary>

<What changed.>
<Why it changed.>
```

## Types
| Type       | When to use                                      |
|------------|--------------------------------------------------|
| `feat`     | New feature or behavior                          |
| `fix`      | Bug fix                                          |
| `refactor` | Code restructure without behavior change         |
| `perf`     | Performance improvement                          |
| `test`     | Adding or fixing tests                           |
| `docs`     | Documentation only                               |
| `style`    | Formatting, whitespace (no logic change)         |
| `chore`    | Tooling, deps, config (no production code change)|
| `build`    | Build system or external dependency changes      |
| `ci`       | CI configuration                                 |
| `revert`   | Reverting a previous commit                      |

## Rules
- Summary: imperative mood, specific ("Add", "Fix", "Remove", "Refactor") — max ~72 chars
- Body: focus on behavior and intent, not implementation minutiae
- Breaking change: use `!` in header (`feat!:`) and/or add `BREAKING CHANGE:` footer
- **No** `Co-Authored-By`, `Signed-off-by`, or AI attribution — ever
