# Plugin Version Management

When modifying any plugin content, you MUST update the plugin version.

## Version Update Checklist

1. **Update `plugin.json`** in the plugin's `.claude-plugin/` directory

The version in each plugin's `plugin.json` is authoritative. The marketplace.json does not need version fields for each plugin.

## When to Increment Versions

- **Patch (0.0.X)**: Bug fixes, typo corrections, minor documentation updates
- **Minor (0.X.0)**: New skills, new commands, new features, significant content additions
- **Major (X.0.0)**: Breaking changes, major restructuring, incompatible updates

## File to Update

For a plugin named `{plugin-name}`:

```
plugins/{plugin-name}/.claude-plugin/plugin.json  → update "version" field
```

## Example

If you add a new skill to the `oscal` plugin:

Edit `plugins/oscal/.claude-plugin/plugin.json`:
```json
"version": "0.2.0"  // was "0.1.0"
```

## Commit Message

Include the version bump in your commit message:
```
Add new feature to {plugin-name}

- Description of changes
- Bump {plugin-name} version to X.Y.Z
```
