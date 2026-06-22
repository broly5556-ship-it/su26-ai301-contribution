# Contribution [#988]: [Allow multiple admins on campaigns]

**Contribution Number:** [1]  
**Student:** [Youssouf Baradji]  
**Issue:** [[GitHub issue link](https://github.com/gyrinx-app/gyrinx/issues/988)]  
**Status:** [Phase III] [In Progress]

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
- `gyrinx/core/forms/campaign.py` (Settings Form)
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
2. Run `manage makemigrations` and `manage migrate`.
3. Update settings UI by adding the "admins" field to `EditCampaignForm`.
4. Add `is_admin(user)` helper method and update existing logic checks.
5. Display admins on the main campaign dashboard.

**Implement:** [Link to your branch/commits]

**Review:** Check against `CONTRIBUTING.md` and run pre-commit styling hooks.

**Evaluate:** Write `pytest` unit tests to verify adding/removing admins and testing permission boundaries.

---

## Testing Strategy

### Unit Tests(Planned for week 2, as this is not a BUG, but a modifier)

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

- Reviewed `CONTRIBUTING.md` to understand commit and PR standards.
- Successfully transitioned development environment to GitHub Codespaces after identifying compatibility issues between the project's macOS-focused bash scripts and local Windows standard PowerShell/Docker setups.
- Added `admins = models.ManyToManyField(settings.AUTH_USER_MODEL, related_name="administered_campaigns", blank=True)` to the `Campaign` model.
- Generated and applied the database migrations (`makemigrations` and `migrate`).
- Added the `"admins"` field to the `EditCampaignForm` in `gyrinx/core/forms/campaign.py` so Django automatically renders the backend UI for it.
- Saved the "known good" database state by committing changes to GitHub.

### Week [2] Progress

[To be completed next week: Frontend UI updates, permission logic, and pytest integration]

### Code Changes

- **Files modified:** `gyrinx/core/models/campaign.py`, `gyrinx/core/migrations/0146_campaign_admins.py` (Auto-generated), `gyrinx/core/forms/campaign.py`
- **Key commits:** [`git commit -m "Add many-to-many admins field to Campaign model for #988"`]
- **Approach decisions:** Chose to use a standard Django `ManyToManyField` with `blank=True` so campaigns aren't required to have secondary admins, and leveraged `forms.ModelForm` to automatically generate the settings UI.

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

**Environment Configuration:** The project relies heavily on macOS-specific shell scripts (`dev.sh`, `setup-local-postgres.sh`) requiring `brew`. Running these locally on Windows resulted in execution policy errors, missing `nodeenv` paths, and Docker Desktop freezing. **Solution:** Abandoned the local Windows/Docker approach and successfully spun up a GitHub Codespace, which provided a native Linux/Ubuntu environment where the database and node dependencies resolved correctly.

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
