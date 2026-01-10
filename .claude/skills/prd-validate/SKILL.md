---
name: prd-validate
description: Validate PRD scope completeness before implementation
---

# PRD Validation Protocol

Run before implementation to catch scope gaps and design issues.

## Phase 1: Consumer & Design Validation

### Consumer Questions
1. What frontend screens consume this?
2. How does this appear in list vs. detail view?
3. How do users search/filter/navigate?
4. What agent workflows touch this?
5. How does an agent discover this resource?
6. Can an agent reason about this from OpenAPI alone?

### Abstraction Design Questions
7. What are the key abstractions being introduced?
8. Where do boundaries live? (service vs. route vs. model)
9. What internal interfaces are needed?
10. What existing abstractions does this extend vs. create new?

## Phase 2: Quality Review

### Abstraction Smells
- [ ] No god classes (single responsibility)
- [ ] Proper separation of concerns
- [ ] Loose coupling between components

### Code Smells
- [ ] No excessive data transforms between layers
- [ ] No magic strings (use enums)
- [ ] No untyped dicts (use models)
- [ ] No duplication (extract helpers)

### Workaround Detector
- [ ] Is this building on a previous workaround?
- [ ] If patching a patch, STOP and reassess foundation

## Phase 3: Domain Pattern Checklist

### Entity Patterns (new tables/models)
- [ ] Human identifier (name/nickname/title) if user-facing
- [ ] Money fields use Decimal, not float
- [ ] Timestamps: created_at, updated_at, deleted_at
- [ ] Status/state as enum, not string

### Endpoint Patterns (new routes)
- [ ] HAL `_links` with self, related, actions
- [ ] List endpoints: pagination + relevant filters
- [ ] RFC 7807 errors with domain codes
- [ ] OpenAPI descriptions and examples

### Agent Patterns (schema as ontology)
- [ ] Resource understandable from schema alone
- [ ] Relationships explicit and navigable
- [ ] Actions discoverable via links
