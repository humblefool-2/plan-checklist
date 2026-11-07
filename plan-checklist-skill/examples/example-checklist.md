# Fix Login Redirect Loop

- Created: 2026-01-15
- Source: conversation
- Source project: ~/code/my-service
- Ticket: PROJ-123
- Status: In progress

## Context

Users get a redirect loop after login when the session cookie has a stale domain. The cause is in `auth/session.py`, in `refresh_cookie()`. Staging reproduces the bug. The load balancer is not the cause.

## Steps

- [x] Reproduce the loop on staging
- [x] Rule out the load balancer config
- [ ] 1. Fix the cookie domain in `auth/session.py:refresh_cookie()`
- [ ] 2. Add a regression test in `tests/test_session.py` (after: 1)
- [ ] 3. Update the changelog (parallel)
- [ ] 4. Check the diff before you open the PR (after: 1, 2)

## Notes

- 2026-01-16: Step 1 needs a migration for old cookies. Ask the team first.
