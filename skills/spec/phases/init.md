# Phase: Init

Initialize a new feature spec from a natural-language description.

## Inputs

- **Description**: The user's feature description (free-form text)
- **Template files**:
  - `templates/spec.json`
  - `templates/spec.md`
  - `templates/requirements.md`

## Procedure

### 1. Parse Feature Name

Convert the description into a kebab-case slug for the directory name.

- Strip filler words ("Build a", "Create the", "Add support for")
- Use 2-4 meaningful words: `user-notifications`, `payment-refunds`, `csv-export`
- All lowercase, hyphens only

### 2. Check Uniqueness

Look for an existing directory at `.agents/specs/<feature-slug>/`.

- If it already exists, tell the user and stop. Suggest they use a different name or work with the existing spec.
- If the `.agents/specs/` directory doesn't exist yet, that's fine — create it.

### 3. Create Directory Structure

Create the following tree:

```
.agents/specs/<feature-slug>/
  spec.json
  spec.md
  requirements.md
  tasks/
    .gitkeep
```

### 4. Populate Files

**spec.json** — Copy from `templates/spec.json` and fill in:
- `feature_name`: the kebab-case slug
- `description`: the user's original description (trimmed)
- `created_at` / `updated_at`: today's date (YYYY-MM-DD)
- All other fields keep their template defaults (`phase: "initialized"`, booleans `false`, `branch: null`)

**spec.md** — Copy from `templates/spec.md`:
- Replace `{{FEATURE_NAME}}` with the slug
- Fill the **Problem** section with what's broken or missing (extract from the user's description)
- Fill the **Goal** section with the desired end state (one sentence)
- Replace `{{DATE}}` with today's date (YYYY-MM-DD)
- Leave task index empty — it gets populated during the tasks phase

**requirements.md** — Copy from `templates/requirements.md`:
- Replace `{{FEATURE_NAME}}` with the slug
- Add a stub section with the description so the requirements phase has context

**tasks/.gitkeep** — Empty file to track the directory in git.

### 5. Report

Print a summary of what was created:

```
Spec initialized: <feature-slug>
  .agents/specs/<feature-slug>/
    spec.json        — phase tracking
    spec.md          — feature overview
    requirements.md  — requirements stub
    tasks/           — task files (empty)
```

## Next Step

If `.agents/reference/` doesn't exist or is empty, mention that steering docs can improve spec quality:

```
Tip: Run /steering to set up project context (product overview, tech stack).
This helps the spec workflow produce more accurate requirements and designs.
```

Then suggest:

```
Ready for requirements. Run: /spec requirements <feature-slug>
```

## Rules

- Never overwrite an existing spec directory
- Always use kebab-case for the feature slug
- The description in spec.json should be the user's words, not a rewrite
- Do not create any files outside the `.agents/specs/<feature-slug>/` directory
- Do not run any code or modify project source files during init
