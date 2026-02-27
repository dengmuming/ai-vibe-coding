---
description: Steps for initializing a new feature development process.
---

# Initialize Feature Workflow

Follow these steps when starting work on a new feature:

1. **Understand Requirements:** Read the feature request or issue carefully. Ask clarifying questions using `notify_user` if requirements are vague.
2. **Review Environment:**
   - Check out the latest `main` or `develop` branch.
   - Look at the codebase structure to determine where new files belong.
3. **Draft a Plan:**
   - Write down an implementation plan (in your scratchpad or `implementation_plan.md` artifact).
   - Identify which files need modifying, deleting, or creating.
4. **Create a Development Branch:**
   - `git checkout -b feat/your-feature-name` (Replace `your-feature-name` with the actual short name of the feature).
// turbo
5. **Set Up the Foundation:** Create basic file skeletons and wire them up with existing components. Don't write business logic yet.
6. **Seek Feedback:** If the boilerplate or design seems complex or risky, pause and notify the user for a quick review.
