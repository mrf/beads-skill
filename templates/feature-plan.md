# Feature Planning Template

Use this template with `bd create -f feature-plan.md` to create multiple issues at once.

## Epic: [Feature Name]

**Type**: epic
**Priority**: 1
**Labels**: feature
**Description**: [Overall description of the feature]

---

## Design: [Feature Name] Architecture

**Type**: task
**Priority**: 1
**Labels**: design, architecture
**Description**: Design the architecture and API for [feature name]
**Depends on**: [epic-id]

---

## Implementation: [Component 1]

**Type**: task
**Priority**: 2
**Labels**: feature, backend
**Description**: Implement [component 1 details]
**Depends on**: [design-task-id]

---

## Implementation: [Component 2]

**Type**: task
**Priority**: 2
**Labels**: feature, frontend
**Description**: Implement [component 2 details]
**Depends on**: [design-task-id]

---

## Testing: [Feature Name]

**Type**: task
**Priority**: 2
**Labels**: testing
**Description**: Write integration tests for [feature name]
**Depends on**: [impl-task-1-id], [impl-task-2-id]

---

## Documentation: [Feature Name]

**Type**: task
**Priority**: 3
**Labels**: docs
**Description**: Update documentation for [feature name]
**Depends on**: [impl-task-1-id], [impl-task-2-id]

---

## Instructions

1. Replace [Feature Name] with your actual feature name
2. Customize the tasks for your specific needs
3. Fill in the dependency IDs after creating the epic
4. Use `bd create -f feature-plan.md` to create all issues
5. Link dependencies with `bd dep add [dependent] [dependency]`
6. View the plan with `bd dep tree [epic-id]`
