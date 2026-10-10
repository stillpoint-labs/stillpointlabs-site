FUSE blocks: cannot remove .git/*.lock; cannot run git operations on the host repo from inside the session.

To create the branch and commit on the host machine:

```
cd ~/dev/stillpointlabs-site
# Clean up any stuck git locks (these were left over from a previous session):
rm -f .git/index.lock .git/HEAD.lock

# Switch to main and branch:
git checkout main
git checkout -b research/cold-start-trust-2026-05-25

# The files are already on disk at docs/research/2026-05-25-cold-start-trust/.
# Add and commit:
git add docs/research/2026-05-25-cold-start-trust/
git commit -m 'research(cold-start-trust): May 2026 deep-research deliverable'
```
