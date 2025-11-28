The Netlify deploy errored, with the following guidance provided:

Diagnosis

- Relevant log lines: [line 8](#L8) and [line 9](#L9).
- Error type and cause: Netlify failed while reading the build configuration because the configured "base directory" points to an absolute path that does not exist in the repo: "Base directory does not exist: /opt/build" ([line 8](#L8)). This indicates your Netlify build settings (in netlify.toml or the Site settings UI) are set to an invalid/absolute path (/opt/build) instead of a path relative to the repository, or to a directory that isn't present in the repo.

Solution

1. Verify the configured base directory actually exists in the repo
   - Inspect your repository root for the folder you intend to use as the base (e.g., the project root, a frontend folder, or a build output folder like dist). If you configured a subfolder, confirm it is committed to the repo.

2. Fix the Netlify base directory setting
   - If you used netlify.toml, open it and remove or correct any absolute path. The value must be a path relative to the repository (or omitted). Example fixes:
```toml
# If your site is built from the repository root, remove base or set to "."
[build]
  # remove base line entirely OR
  base = "."
  publish = "dist"    # set to your publish/build output folder if needed
  command = "npm run build"
```
```toml
# If your site lives in a subfolder called "client"
[build]
  base = "client"
  publish = "client/dist"
  command = "npm run build"
```
   - Commit and push the corrected netlify.toml.

   - OR, if you configured the base directory in the Netlify UI:
     - Go to Site settings → Build & deploy → Continuous Deployment → Build settings (or Settings → Build & deploy → Environment) and set Base directory to the correct relative path or clear it if the repo root is used. Do NOT use an absolute path like /opt/build.

3. Re-deploy
   - After correcting the base directory (and committing netlify.toml if you changed it), trigger a new deploy from Netlify.

Notes
- The important check is that the base path in Netlify configuration is relative and points to a folder that exists and is committed to the repo. The current error shows an absolute, non-existent path (/opt/build) causing the parse failure.

The relevant error logs are:

Line 0: build-image version: cc7730f6f332688234781c0852e824dedc2337ca (noble)
Line 1: buildbot version: cc7730f6f332688234781c0852e824dedc2337ca
Line 2: Fetching cached dependencies
Line 3: Failed to fetch cache, continuing with build
Line 4: Starting to prepare the repo for build
Line 5: No cached dependencies found. Cloning fresh repo
Line 6: git clone --filter=blob:none https://github.com/jigneshdudhrejiya24-cloud/gujaratinulax
Line 7: Preparing Git Reference refs/heads/main
Line 8: Failed during stage 'Reading and parsing configuration files': 
When resolving config:
Base directory does not exist: /opt/build
Line 9: Failing build: Failed to parse configuration
Line 10: Finished processing build request in 1.532s
