# Contribution [#]: [Allow multiple admins on campaigns]

**Contribution Number:** [1]  
**Student:** [Youssouf Baradji]  
**Issue:** [[GitHub issue link](https://github.com/gyrinx-app/gyrinx/issues/988)]  
**Status:** [Phase II] [Complete]

---

## Why I Chose This Issue: 

I chose the "Campaign Admins" issue because it aligns perfectly with my background in Python and HTML frontend UI work. The issue requests a new feature to allow campaign owners to grant administrative rights to other users. I'm interested in this because I have experience building frontend UI and can immediately apply that to building the permissions management screen. It provides a highly scoped opportunity for me to learn and practice backend database architecture (setting up a many-to-many relationship). The maintainer is actively engaged and provided a clear, bulleted list of requirements to follow.

## Understanding the Issue
From reading the issue thread, I understand that currently, only a campaign owner can manage a campaign. My contribution will solve this by enabling shared admin rights, making the application much more collaborative for teams. I've left a comment on the issue introducing myself and proposing a scoped first step for the pull request.

### Problem Description

Currently, campaigns only support a single administrator (the owner). The lack of shared admin rights creates a bottleneck for group-managed tabletop campaigns.

### Expected Behavior

Campaign owners should have a UI in settings to grant/revoke admin rights to other users, and the app logic must respect these shared admins for permissions.

### Current Behavior

The `Campaign` database model only supports a single `owner`. There is no database field, permission logic, or user interface to support secondary administrators.

### Affected Components

- `gyrinx/core/models/campaign.py` (Database model)
- Campaign permission checks in views
- Campaign dashboard and settings HTML templates

---

## Reproduction Process

### Environment Setup

Followed README Quick Start: Set up Python venv, configured `.env`, installed Node dependencies, and started local server using `./scripts/dev.sh`.

### Steps to Reproduce

1. Launch local dev server and log in as a user.
2. Create or open an existing Campaign where you are the owner.
3. Navigate to the Campaign settings and dashboard.
4. **Observed result:** No UI exists to add another admin. Inspecting `gyrinx/core/models/campaign.py` confirms no database field exists for multiple admins.

### Reproduction Evidence

- **Commit showing reproduction:** [[](https://github.com/broly5556-ship-it/gyrinx/tree/fix-issue-988)]
- **Screenshots/logs:** N/A 
- **My findings:** Verified feature absence. Maintainer comments confirm a many-to-many relationship and UI updates are needed.

---

## Solution Approach

### Analysis

The `Campaign` model inherits from `Owned` with a single `ForeignKey` to `User`. The database physically cannot store additional admins, meaning the UI and logic cannot support them either.

### Proposed Solution

Add a `ManyToManyField` for admins to the `Campaign` model, update permission logic to check this field, and build the management UI for the owner.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** Update DB to store shared admins, update permission logic, and build UI for assigning them.

**Match:** Use a `ManyToManyField` similar to other Django user relationships in the codebase.

**Plan:** 1. Add `admins = models.ManyToManyField(...)` to `Campaign` in `gyrinx/core/models/campaign.py`.
2. Add `is_admin(user)` helper method and update existing logic checks.
3. Update settings UI with multi-select input for admins.
4. Display admins on the main campaign dashboard.
5. Run `manage makemigrations`.

**Implement:** [Link to your branch/commits]

**Review:** Check against `CONTRIBUTING.md` and run pre-commit styling hooks.

**Evaluate:** Write `pytest` unit tests to verify adding/removing admins and testing permission boundaries.

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [1] Progress

For week 1. these are my testings/.//

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
