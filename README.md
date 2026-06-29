# Contribution [#988]: [Allow multiple admins on campaigns]

**Contribution Number:** [1]  
**Student:** [Youssouf Baradji]  
**Issue:** [[GitHub issue link](https://github.com/gyrinx-app/gyrinx/issues/988)]  
**Status:** [Phase III] [Complete]

---

## Why I Chose This Issue

I chose the "Campaign Admins" issue because it aligns perfectly with my background in Python and HTML frontend UI work. The issue requests a new feature to allow campaign owners to grant administrative rights to other users. I'm interested in this because I have experience building frontend UI and can immediately apply that to building the permissions management screen. It provides a highly scoped opportunity for me to learn and practice backend database architecture (setting up a many-to-many relationship). The maintainer is actively engaged and provided a clear, bulleted list of requirements to follow.

---

## Understanding the Issue

From reading the issue thread, I understand that currently, only a campaign owner can manage a campaign. My contribution will solve this by enabling shared admin rights, making the application much more collaborative for teams. I've left a comment on the issue introducing myself and proposing a scoped first step for the pull request.

### Problem Description
Currently, campaigns only support a single administrator (the owner). The lack of shared admin rights creates a bottleneck for group-managed tabletop campaigns.

### Expected Behavior
Campaign owners should have a UI in settings to grant/revoke admin rights to other users, and the app logic must respect these shared admins for permissions.

### Current Behavior
The `Campaign` database model only supports a single `owner`. There is no database field, permission logic, or user interface to support secondary administrators.

### Affected Components
* `gyrinx/core/models/campaign.py` (Database model)
* `gyrinx/core/forms/campaign.py` (Settings Form)
* Campaign permission checks in views
* Campaign dashboard and settings HTML templates

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
* **Commit showing reproduction:** [[Link](https://github.com/broly5556-ship-it/gyrinx/tree/fix-issue-988)]
* **Screenshots/logs:** N/A 
* **My findings:** Verified feature absence. Maintainer comments confirm a many-to-many relationship and UI updates are needed.

---

## Solution Approach

### Analysis
The `Campaign` model inherits from `Owned` with a single `ForeignKey` to `User`. The database physically cannot store additional admins, meaning the UI and logic cannot support them either.

### Proposed Solution
Add a `ManyToManyField` for admins to the `Campaign` model, update permission logic to check this field, and build the management UI for the owner.

### Implementation Plan
Using UMPIRE framework (adapted):

* **Understand:** Update DB to store shared admins, update permission logic, and build UI for assigning them.
* **Match:** Use a `ManyToManyField` similar to other Django user relationships in the codebase.
* **Plan:** 1. Add `admins = models.ManyToManyField(...)` to `Campaign` in `gyrinx/core/models/campaign.py`.
  2. Run `manage makemigrations` and `manage migrate`.
  3. Update settings UI by adding the "admins" field to `EditCampaignForm`.
  4. Add `is_admin(user)` helper method and update existing logic checks.
  5. Display admins on the main campaign dashboard.
* **Implement:** [[Link](https://github.com/broly5556-ship-it/gyrinx/tree/fix-issue-988)]
* **Review:** Check against `CONTRIBUTING.md` and run pre-commit styling hooks.
* **Evaluate:** Write `pytest` unit tests to verify adding/removing admins and testing permission boundaries.

---

## Testing Strategy

### Unit Tests (Executed via Pytest)
* **Test case 1 (Owner Check):** Verified that `is_admin(user)` returns `True` for the original campaign owner.
* **Test case 2 (Secondary Admin Check):** Verified that `is_admin(user)` returns `True` for secondary users explicitly added to the campaign's `admins` ManyToMany list.
* **Test case 3 (Unauthorized User Check):** Verified that `is_admin(user)` returns `False` for an unrelated/random authenticated player.
* **Test case 4 (Unauthenticated User Check):** Verified that `is_admin(None)` handles anonymous/unauthenticated states safely by returning `False`.

### Integration Tests
* [ ] Integration scenario 1
* [ ] Integration scenario 2

### Manual Testing
* Verified in local browser that when logged in as a secondary admin, management buttons ("Start Campaign", "Edit Campaign") render correctly on the dashboard instead of being hidden.
* Confirmed the "Arbitrators" layout reads clearly on the dashboard UI beneath the campaign header without crowding adjacent Flexbox metadata badges.

---

## Implementation Notes

### Week [1] Progress
* Reviewed `CONTRIBUTING.md` to understand commit and PR standards.
* Successfully transitioned development environment to GitHub Codespaces after identifying compatibility issues between the project's macOS-focused bash scripts and local Windows standard PowerShell/Docker setups.
* Added `admins = models.ManyToManyField(settings.AUTH_USER_MODEL, related_name="administered_campaigns", blank=True)` to the `Campaign` model.
* Generated and applied the database migrations (`makemigrations` and `migrate`).
* Added the `"admins"` field to the `EditCampaignForm` in `gyrinx/core/forms/campaign.py` so Django automatically renders the backend UI for it.
* Saved the "known good" database state by committing changes to GitHub.

### Week [2] Progress
* **Frontend Dashboard Enhancement:** Updated the main `campaign.html` template to display a dedicated "Arbitrators" list section directly underneath the campaign title.
* **Permission Gate Refactoring:** Audited and updated logic blocks inside `campaign.html` and `campaign_lists.html` using grouped logical operators `(campaign.owner == user or user in campaign.admins.all)` to grant full dashboard and control parity to secondary administrators.
* **Backend Model Verification:** Refactored the pre-existing core `is_admin(self, user)` method inside `Campaign` to evaluate the new `admins` field relationship alongside the traditional owner check.
* **Automated Unit Testing Integration:** Created a completely new test file `test_campaign_admins.py` utilizing the local Docker-managed PostgreSQL database engine container to mathematically validate all core administrative permissions boundaries.

### Code Changes
* **Files modified:** `gyrinx/core/models/campaign.py`, `gyrinx/core/forms/campaign.py`, `gyrinx/core/templates/campaign.html`, `gyrinx/core/templates/campaign_lists.html`, `gyrinx/tests/test_campaign_admins.py`
* **Key commits:** * `git commit -m "Add many-to-many admins field to Campaign model for #988"`
  * `git commit -m "Update campaign templates and logic for multiple admins #988"`
  * `git commit -m "Add unit test to verify multiple campaign admins logic #988"`
* **Approach decisions:** Leveraged a standard Django `ManyToManyField` paired with an expanded `is_admin` method wrapper to cleanly extend permissions control while preserving old dependencies. Parentheses were intentionally introduced inside template condition tags to isolate the `or` operations from adjacent `and not` context rules, preventing authorization leakage for archived instances.

---

## Pull Request

**PR Link:** [[GitHub PR URL when submitted]
](https://github.com/gyrinx-app/gyrinx/pull/1922)]


**PR Description:** [This pull request implements shared campaign administration capabilities to allow multiple users to manage a campaign collaboratively, resolving the current limitation where only the original campaign owner has administrative privileges.

Key changes include:

Database Schema: Added an admins ManyToManyField to the Campaign model to track secondary administrators.
Form Management: Updated EditCampaignForm to expose the new admins field, allowing owners to securely delegate management permissions.

Backend Permissions Logic: Enhanced the core is_admin method within the Campaign model to check the many-to-many relationship in tandem with the traditional owner constraint.

Frontend Interface Layout: Integrated a scannable "Arbitrators" list interface directly on the main campaign template (campaign.html) and restructured conditional layout checks inside campaign.html and campaign_lists.html using grouped parenthetical logicGates to grant full control parity over UI management items.

These adjustments are backwards-compatible and preserve existing project schema patterns.

Related issues
Closes #988

Release notes
Campaigns now support multiple shared administrators ("Arbitrators"). Campaign owners can delegate full administrative rights to secondary users via the campaign settings menu, giving added managers complete access to dashboard actions, editing workflows, and campaign control parameters.

Manual testing
The permissions gating, template rendering, and model methods were verified through automated and browser-driven scenarios executed within a containerized sandbox.

Verification Steps
Spun up the project's standardized storage instance using docker compose up -d.

Initialized testing routines targeting the custom campaign authorization scope.

Successfully logged local browser operations running as an authorized secondary admin, verifying that primary dashboard management buttons (e.g., "Start Campaign", "Edit Campaign") unhide and execute as expected.

Confirmed correct placement of the "Arbitrators" metadata strings beneath the header without causing flex-badge misalignments.

The change has been manually tested on:

[ ] Windows

[ ] macOS

[x] Linux (GitHub Codespaces environment)

Checklist
[x] I am a human, I have read and understood the [project's policy], and I have acted in accordance to it.

I have:

[x] followed the project's code of conduct

[x] performed a self-review of my work

[x] commented on the particularly hard to understand areas of my code.

[x] split my work into well-defined, bisectable commits, and I named my commits well

[x] applied the appropriate labels

[x] checked that all my commits can be built.

[ ] my change has been manually tested on Windows, macOS, and Linux.

[ ] confirmed that my code does not cause performance regressions (e.g., by running the Quake benchmark).

[x] added unit tests where applicable to prove the correctness of my code and to avoid future regressions.

[x] provided the release notes draft (for significant user-facing changes).]

**Maintainer Feedback:**
* [Date]: [Summary of feedback received]
* [Date]: [How you addressed it]

**Status:** [Awaiting review]

---

## Learnings & Reflections

### Technical Skills Gained
* Writing automated backend validation tests using `pytest-django` fixtures.
* Structuring compound logical grouping inside Django HTML template engines.
* Orchestrating containerized isolation services inside cloud environments using `docker compose`.

### Challenges Overcome

**Environment Configuration:** The project relies heavily on macOS-specific shell scripts (`dev.sh`, `setup-local-postgres.sh`) requiring `brew`. Running these locally on Windows resulted in execution policy errors, missing `nodeenv` paths, and Docker Desktop freezing. **Solution:** Abandoned the local Windows/Docker approach and successfully spun up a GitHub Codespace, which provided a native Linux environment where the database and node dependencies resolved correctly.

**Port and Database Authentication Blocks:** Encountered system authentication and port collisions when trying to bind local Ubuntu PostgreSQL instances up at the same time as containerized configurations. **Solution:** Discovered the structural project preference for Docker orchestration, shut down standard background host threads using system controllers, and safely isolated port 5432 mappings using targeted Docker Compose configurations.

### What I'd Do Differently Next Time
I would carefully inspect configuration files like `docker-compose.yml` or `pyproject.toml` at the very beginning of the setup phase to identify the project's native virtualization tooling preference before spending time setting up traditional host server runtimes manually.

---

## Resources Used
* Github video that helped a lot [(https://www.youtube.com/watch?v=e9lnsKot_SQ)]
