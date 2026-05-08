# GitHub Workflow

One-time setup on GitHub

1. Push the repo to GitHub. The workflow runs in whatever repo it's pushed to. Make sure the URL matches what your README/installers expect (your README references createra-robotics/dreambo_servo_wizard).
2. Enable workflow write permissions (this is the one click most people miss):

- Repo → Settings → Actions → General → Workflow permissions
- Select "Read and write permissions"
- Save

This is required because the host job calls gh release create, which needs contents: write. The YAML already declares the permission, but org/repo defaults can override it.

3. (If under an organization) Allow Actions to run. Org-level Actions policies can disable workflows. Check Org Settings → Actions → General → Allow all actions (or at least allow GitHub-owned + axodotdev actions).

4. No secrets to add. GITHUB_TOKEN is provisioned automatically. There are no API keys, no signing keys, no Cargo registry tokens in this workflow.

How releases get triggered

The workflow triggers on:
- pull_request — runs build/plan as a smoke test, no release published.
- push of a tag matching **[0-9]+.[0-9]+.[0-9]+* — e.g., v0.1.0, 0.2.3, v1.0.0-rc.1. This is what publishes a GitHub Release with prebuilt binaries for all 5 targets in dist-workspace.toml.

So your release flow is:

# 1. Bump version in Cargo.toml to match the tag you're about to push
# 2. Commit
git commit -am "release v0.1.0"                                                                                                                                                                                                                                                                          
git push

# 3. Tag and push the tag
git tag v0.1.0                                                                                                                                                                                                                                                                                           
git push origin v0.1.0

Pushing the tag is what kicks off the Release workflow. ~5–10 minutes later you'll have a GitHub Release with .tar.gz/.zip archives, an installer shell script, and an installer PowerShell script — exactly the URLs your README points at. 