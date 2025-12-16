
# Branch Protection Investigation Checklist

**Repository:** testDevOps0777/branch-protection-test  
**Date Started:** [Add date]  
**Investigator:** [Your name]

---

## Phase 1: Initial Setup ✓
- [x] Create test repository
- [x] Create test branches
- [x] Verify branches exist on GitHub

## Phase 2: Permission Check
- [ ] Check my repository permissions
```bash
  gh api repos/testDevOps0777/branch-protection-test --jq '.permissions'
```
  **Result:** ___________

- [ ] Check my organization role
```bash
  gh api orgs/testDevOps0777/memberships/$(gh api user --jq .login)
```
  **Result:** ___________

- [ ] Can I access org settings via web UI?
  - URL: https://github.com/organizations/testDevOps0777/settings/profile
  - **Result:** YES / NO

## Phase 3: Org-Level Rulesets Investigation

### 3.1: Check Existing Rulesets
- [ ] Navigate to: https://github.com/organizations/testDevOps0777/settings/rules
- [ ] Do any rulesets exist? YES / NO
- [ ] If yes, list them:
  - ___________
  - ___________

### 3.2: Create Test Org Ruleset
- [ ] Click "New ruleset" → "New branch ruleset"
- [ ] Configure:
  - **Ruleset Name:** `Test Service Branch Protection`
  - **Enforcement status:** Active
  - **Target branches:** Add target → Include by pattern → `service/**`
  - **Branch protections:**
    - ✅ Require a pull request before merging
    - Required approvals: `1`
    - ✅ Dismiss stale pull request approvals when new commits are pushed
  - Click **"Create"**
- [ ] **Time created:** ___________

### 3.3: Test Synchronization
- [ ] Go to branch: https://github.com/testDevOps0777/branch-protection-test/tree/service/CY25/10.31
- [ ] Click "Settings" → "Branches"
- [ ] Does `service/CY25/10.31` show protection rules? YES / NO
- [ ] **Wait 2 minutes**
- [ ] Check again - any changes? ___________
- [ ] Go back to org ruleset and modify:
  - Change required approvals from `1` to `2`
  - Save changes
  - **Time modified:** ___________
- [ ] **Wait 5 minutes**
- [ ] Check branch protection again
- [ ] Did it update automatically? YES / NO / PARTIAL

**KEY FINDING - Org Ruleset Sync Behavior:**
___________________________________________________________________________

## Phase 4: Repo-Level Branch Protection

### 4.1: Run Investigation Script
```bash
python gh_protection_test.py --repo testDevOps0777/branch-protection-test --branch test-protection
```
- [ ] Script executed successfully
- [ ] Review report: `branch_protection_investigation_*.md`
- [ ] **Key findings from script:** ___________

### 4.2: Manual Branch Protection (Via Web UI)
- [ ] Go to: https://github.com/testDevOps0777/branch-protection-test/settings/branches
- [ ] Click "Add branch protection rule"
- [ ] Configure for `test-protection`:
  - Branch name pattern: `test-protection`
  - ✅ Require a pull request before merging (1 approval)
  - ✅ Dismiss stale pull request approvals when new commits are pushed
  - Save changes
- [ ] Protection created successfully? YES / NO

### 4.3: Verify via API
```bash
gh api repos/testDevOps0777/branch-protection-test/branches/test-protection/protection
```
- [ ] Command succeeded
- [ ] **Output shows:** ___________

## Phase 5: Override Testing

### 5.1: Repo Rule vs Org Ruleset
- [ ] Ensure org ruleset is active for `service/**` branches (requires 2 approvals)
- [ ] Try to create repo-level protection for `service/CY25/10.31`:
  - Go to Settings → Branches → Add rule
  - Pattern: `service/CY25/10.31`
  - Try to set required approvals to `1` (less restrictive than org)
  - Save
- [ ] **Result:** ___________
- [ ] Can repo-level make it LESS strict? YES / NO
- [ ] Can repo-level make it MORE strict (e.g., 3 approvals)? YES / NO

### 5.2: Which Takes Precedence?
- [ ] Create a test PR to `service/CY25/10.31`
- [ ] How many approvals are required? ___________
- [ ] **Conclusion - What wins?**
  - [ ] Org ruleset
  - [ ] Repo-level protection
  - [ ] Most restrictive of both
  - [ ] Other: ___________

## Phase 6: Permission Requirements

### 6.1: What Permissions Did I Need?
- **For creating repo:** ___________
- **For repo-level branch protection:** ___________
- **For org-level rulesets:** ___________

### 6.2: Test with Limited Permissions (Optional)
- [ ] Create another GitHub account or use a teammate
- [ ] Give them "Write" access to repo
- [ ] Can they modify branch protection? YES / NO
- [ ] Can they modify org rulesets? YES / NO

---

## Summary of Findings

### Question 1: Do org-level rulesets sync continuously?
- [ ] Yes - changes propagate immediately/quickly to existing branches
- [ ] No - rulesets only apply at branch creation time
- [ ] Partial - ___________

**Evidence:** ___________

### Question 2: Can repo-level protection override org-level rulesets?
- [ ] Yes - repo-level takes full precedence
- [ ] No - org-level cannot be overridden
- [ ] Partial - most restrictive wins
- [ ] Other: ___________

**Evidence:** ___________

### Question 3: What's the best approach for Logik release locking?

Recommended approach:
- [ ] **Option A:** Repo-level protection only (simpler, direct control)
- [ ] **Option B:** Org-level rulesets as base + repo-level for specific branches
- [ ] **Option C:** Org-level rulesets only (if sync works well)
- [ ] **Option D:** Other: ___________

**Rationale:** ___________

---

## Next Steps

Based on findings:
1. ___________
2. ___________
3. ___________

## Notes & Observations

Test by Monica
