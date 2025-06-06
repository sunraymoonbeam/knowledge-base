---
title: 2. Commit Guidelines
---
<div style="text-align: center;">
    <img src="https://cdn.hashnode.com/res/hashnode/image/upload/v1684174278610/5bfe372b-73a3-4385-a373-4eee0867e7c8.png" style="transform: scale(0.8);">
</div>

# Resources
1. [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)
1. [AISG 100E Playbook Commit Messages](https://aisg.gitlab.aisingapore.net/100e-projects/shared/boilerplate-and-processes/book/02-swe/7-git-comms/commit-messages/guideline.html#commit-messages)


# Table of Contents
1. [Git Commits](#git-commit-best-practices)
   1. [Atomic Commits](#atomic-commits)
   1. [Commit Frequency](#commit-often)
   1. [Issue Number](#mention-the-issue-number)
   1. [Don't Commit Half Done Work](#dont-commit-half-done-work)
   1. [Testing](#tested-and-verified)
1. [Commit Messages](#git-commit-message-guidelines)
   1. [Good Commit Message](#write-good-commit-messages)
   1. [Conventional Commits](#conventional-commits)


## Git Commit Best Practices
| S/N | Practice                  | Description                                                                 |
|-----|---------------------------|-----------------------------------------------------------------------------|
| 1   | Atomic Commits             | Each commit should represent one logical change, making debugging easier.    |
| 2   | Commit Often               | Frequent commits ensure manageable, incremental changes that are easy to review. |
| 3   | Mention the Issue Number   | Reference the issue number in commit messages to link code with the task or bug. |
| 4   | Don't Commit Half-Done Work| Only commit completed logical components, using `stash` for temporary work.  |
| 5   | Tested and Verified        | Ensure the code is thoroughly tested before committing, especially when sharing. |
| 6   | Write Good Commit Messages | Use clear, imperative, and descriptive commit messages to maintain a clean history. |



### Atomic Commits
An atomic commit means that a single commit represents only one particular change. This doesn’t mean only one file or even one line changed, but rather one “logical” change. Atomic commits make it easier to understand changes, debug issues, and identify potential problems. Developers can easily roll them back if something went wrong.

```bash
git commit -m "Add user authentication" ✅

git commit -m "Add user authentication AND update UI styles" ❌
```

### Commit Often
Committing regularly is important. It ensures that changes are incremental and manageable, allowing for easier reviews, debugging, and rollbacks if necessary. Having large commits and sharing them infrequently, in contrast, makes it hard to solve conflicts.

```bash
git commit -m "Update project" ❌
```

### Mention the Issue Number
It’s helpful to reference the issue number in the commit message. This creates a clear connection between the commit and the associated task or bug.

```bash
# Good commits
git commit -m "Fixed page loading issue on mobile devices, closes #456" ✅
git commit -m "Added input validation for email format, resolves #123" ✅

# Bad commits
git commit -m "fix the bug" ❌
git commit -m "improved UI" ❌
```

### Don't Commit Half-Done Work
You should only commit code when a logical component is completed.

Split a feature‘s implementation into logical chunks that can be completed quickly so that you can commit often. 

If you're tempted to commit just because you need a clean working copy (to check out a branch, pull in changes, etc.) consider using Git‘s ```stash``` feature instead.

### Tested and Verified
Resist the temptation to commit something that you «think» is completed. Test it thoroughly to make sure it really is completed and has no side effects (as far as one can tell). While committing half-baked things in your local repository only requires you to forgive yourself, having your code tested is even more important when it comes to pushing/sharing your code with others.


## Git Commit Message Guidelines
### Write Good Commit Messages
Descriptive Commit Message: A descriptive commit message clearly explains what the commit does and why the change was made. It should provide enough context for others (and your future self) to understand the change without reading the code.

```bash
git commit -m "Fix Correct null pointer exception in user login" ✅

git commit -m "Fix bug" ❌
```

- Capitalized, short __(50 chars or less)__ summary

- More detailed explanatory text, if necessary.  __Wrap it to about 72 characters__.  In some contexts, the first line is treated as the subject of an email and the rest of the text as the body.  The blank line separating the summary from the body is critical (unless you omit the body entirely); tools like rebase can get confused if you run the two together.

- Write your commit message in the imperative: "Fix bug" and not "Fixed bug" or "Fixes bug."  This convention matches up with commit messages generated by commands like git merge and git revert.

### Conventional Commits
Use the standard commit guidelines to keep your git history clean, consistent and easy to read. Usually these guidelines interpret in the form of type (feat, fix, chore, refactor docs), and short summary plus occasionally a long explanation or REF to other relative issues.

```bash
git commit -m "feat: add JWT-based authentication"
git commit -m "fix: resolve race condition in login flow"
```

| Component | Description | Example |
| ------ | ------ | ------ |
| fix  | a commit of the type fix patches a bug in your codebase (this correlates with PATCH in Semantic Versioning). |
|  feat  | a commit of the type feat introduces a new feature to the codebase (this correlates with MINOR in Semantic Versioning).|  feat: allow provided config object to extend other configs      |
| chore |  a commit of the type chore updates the build process, auxiliary tools, libraries, etc |  chore!: drop support for Node 6  |
| ci  |  a commit of the type ci updates the continuous integration process. |        |
| docs  |  a commit of the type docs updates the documentation. | docs: correct spelling of CHANGELOG |
| refactor | a commit of the type refactors the codebase  |        |
| revert       | Reverts a previous commit       |        |
| perf       | A code change that improves performance    |        |
| style  | a commit of the type style updates the style of the codebase. |        |
| test | a commit of the type test adds or updates tests. |        |