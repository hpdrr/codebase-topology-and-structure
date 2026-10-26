# Contributing to Codebase Topology & Structure

Contributions to expand ecosystem standards, enhance architectural decision trees, and refine anti-pattern checklists are welcome.

## Guidelines

1. **Focus on Production Conventions**:
   - Align layout proposals with authoritative language guidelines (e.g., standard Go project layout, Python `src` layout PEPs, Cargo Workspaces).
   - Prefer compiler-enforced visibility rules over unenforced human conventions.

2. **File Structure Consistency**:
   - Maintain the standard frontmatter format in `SKILL.md` (`name`, `description`, `triggers`, `version`).
   - Keep examples concise and focused on structural tree representations and boundary guarantees.

3. **Submitting Changes**:
   - Fork the repository.
   - Create a feature branch (`git checkout -b feature/elixir-umbrella-layout`).
   - Ensure `SKILL.md` passes YAML frontmatter verification.
   - Open a Pull Request referencing the rationale and industry sources for the pattern.
