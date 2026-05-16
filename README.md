# skills

Claude Code skills marketplace. Each skill ships as its own plugin — install only what you want.

## Install

Add the marketplace once:

```bash
/plugin marketplace add lweixw/skills
```

Then install any skill by name (plugin name matches skill name):

```bash
/plugin install grill-me-with-docs@lweixw-skills
```

List what's available:

```bash
/plugin
```

## Available skills

| Plugin | What it does |
|--------|--------------|
| [`grill-me-with-docs`](plugins/grill-me-with-docs/) | Stress-tests a plan against your project's domain model; updates `CONTEXT.md` and ADRs inline. |
| [`local-review`](plugins/local-review/) | Multi-agent review of uncommitted changes (bugs, security, CLAUDE.md compliance, types, simplification) with a confidence filter. |

## Structure

```
.
├── .claude-plugin/
│   └── marketplace.json              # Catalog — one entry per skill
└── plugins/
    └── <skill-name>/                 # One plugin per skill (name = skill name)
        ├── .claude-plugin/
        │   └── plugin.json           # Plugin manifest
        └── skills/
            └── <skill-name>/
                ├── SKILL.md          # Skill definition with frontmatter
                └── ...               # Helper docs, scripts, etc.
```

## Add a new skill

1. **Pick a name** (kebab-case). Plugin name and skill name match — this is what users install.

2. **Create the plugin layout:**

   ```bash
   SKILL=my-new-skill
   mkdir -p "plugins/$SKILL/.claude-plugin" "plugins/$SKILL/skills/$SKILL"
   ```

3. **Write `plugins/$SKILL/.claude-plugin/plugin.json`:**

   ```json
   {
     "name": "my-new-skill",
     "description": "One-line summary of what the skill does and when to use it.",
     "author": { "name": "Leo Wei", "email": "3482527+lweixw@users.noreply.github.com" },
     "homepage": "https://github.com/lweixw/skills",
     "repository": "https://github.com/lweixw/skills.git",
     "license": "MIT",
     "keywords": ["claude-code", "skill"]
   }
   ```

   Omit `version` so each git commit becomes a new version. Add `"version": "1.2.3"` if you want explicit pinning.

4. **Write `plugins/$SKILL/skills/$SKILL/SKILL.md`:**

   ```markdown
   ---
   name: my-new-skill
   description: When and why Claude should invoke this skill. Claude reads this to decide.
   ---

   Instructions for the skill go here.
   ```

5. **Register in `.claude-plugin/marketplace.json`** — add an entry to the `plugins` array:

   ```json
   {
     "name": "my-new-skill",
     "source": "./plugins/my-new-skill",
     "description": "One-line summary.",
     "category": "productivity",
     "tags": ["foo", "bar"]
   }
   ```

6. **Validate locally:**

   ```bash
   claude plugin validate .
   ```

7. **Test locally without publishing:**

   ```bash
   claude --plugin-dir ./plugins/my-new-skill
   ```

8. **Commit and push.** Users refresh with `/plugin marketplace update lweixw-skills`.

## SKILL.md frontmatter

| Field | Required | Purpose |
|-------|----------|---------|
| `name` | yes | Kebab-case identifier. Match the directory name. |
| `description` | yes | One-line trigger — Claude reads this to decide when to invoke. |
| `allowed-tools` | no | Whitelist of tools the skill may use. |
| `disable-model-invocation` | no | Set `true` for skills that should only run when explicitly invoked. |

## License

MIT — see [LICENSE](LICENSE).
