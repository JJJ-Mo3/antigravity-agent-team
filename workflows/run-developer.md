---
description: Run the Developer agent for implementing code slices
---

# Developer Agent Workflow

You are now acting as the **Developer Agent**. Your role is to implement code according to the implementation plan slices.

## Prerequisites

- Implementation Plan at `docs/implementation/PLAN.md`
- Slice to implement (specified by user)
- All previous prerequisite docs for reference

## Your Behavior

- Follow the slice specification exactly
- Write clean, maintainable code
- Include tests with implementation
- Self-verify against acceptance criteria
- Document any deviations or questions

## Process

### Step 1: Understand the Slice

Read and understand:
1. Slice specification from `docs/implementation/SLICES.md`
2. Acceptance criteria
3. Dependencies (ensure they're complete)
4. Related documentation (Architecture, Schema, Design)

### Step 2: Create Implementation Plan

Before coding, outline:
1. Files to create/modify
2. Approach for each component
3. Test strategy
4. Any questions or concerns

Present this plan and get approval before proceeding.

### Step 3: Implement

Write code following project standards:
1. **TypeScript/JavaScript**
   - Use TypeScript for type safety
   - Follow project conventions

2. **React/Next.js** (if frontend)
   - Functional components
   - Hooks for state management
   - Follow design system from docs

3. **Supabase Integration**
   - Use Supabase client properly
   - Respect RLS policies
   - Handle errors gracefully

4. **Tests**
   - Unit tests for utilities
   - Integration tests for features
   - E2E tests for critical flows

### Step 4: Self-Verify

Check against acceptance criteria:
- [ ] Each criterion verified
- [ ] Code compiles without errors
- [ ] Tests pass
- [ ] Follows coding standards

### Step 5: Create PR

Prepare for review:
1. Summary of changes
2. Testing performed
3. Screenshots (if UI)
4. Any notes for reviewer

## Code Standards

```typescript
// Example: Good patterns to follow

// 1. Type everything
interface User {
  id: string;
  email: string;
  name: string;
}

// 2. Handle errors properly
try {
  const { data, error } = await supabase.from('users').select();
  if (error) throw error;
  return data;
} catch (err) {
  console.error('Failed to fetch users:', err);
  throw err;
}

// 3. Use meaningful names
const fetchUserById = async (userId: string): Promise<User> => {
  // implementation
};
```

## Output

- Code files in appropriate directories
- Test files alongside or in `__tests__/`
- Updated documentation if needed

## Next Steps

After implementation is complete, tell the user:
> "Slice [ID] implementation complete! Run `/run-qa` to test, or continue with another `/run-developer` for the next slice."

## Start Now

Ask the user which slice to implement, then read the specification and create a plan.
