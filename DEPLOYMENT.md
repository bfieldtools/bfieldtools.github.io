# Publishing the documentation

The workflow in `.github/workflows/pages.yml` builds Sphinx documentation from
a selected public source repository and deploys a Pages artifact. Generated HTML
does not need to be committed or rsynced into this repository. The workflow must
run in `bfieldtools/bfieldtools.github.io` to retain `https://bfieldtools.github.io/`.

## One-time setup

1. Merge the workflow and this guide into this repository's default branch
   (`master`). A manually triggered workflow must exist on the default branch
   before GitHub shows its **Run workflow** button.
2. In **Settings → Pages → Build and deployment**, select **GitHub Actions**.
3. Check the `github-pages` environment's deployment rules allow `master`.

This workflow uses the repository's automatic `GITHUB_TOKEN`; no personal access
token or cross-repository write access is needed. Only the deployment job receives
Pages write permission. A selected source revision executes Python during the
build, so choose trusted source repositories and revisions.

## Deploy an update

Open **Actions → Build and deploy documentation → Run workflow** on `master`.
Select the source repository and branch, tag, or commit SHA. Prefer a commit SHA
for a reproducible release. The default is `bfieldtools/bfieldtools` at `master`.
That source must contain `pyproject.toml`, `uv.lock`, and the new PyVista docs setup.

Before the PyVista changes are merged upstream, select:

- Source repository: `anttimc/bfieldtools`
- Source ref: `feature/pyvista-examples` (or its exact commit SHA)

Leave **full_gallery** disabled for the usual bounded gallery. Enabling it also
runs five expensive optimization examples; external-data examples remain
source-only. The build runs on Linux with software rendering and Xvfb, using the
locked Python dependencies. An example failure prevents deployment. Sphinx
warnings are reported but do not by themselves fail the build.

The run summary records the source commit and output size. Successful builds
upload `docs/_build/html` and deploy it through the `github-pages` environment.
Check the deployment URL and a few interactive examples after the first run.
To roll back, rerun the workflow with a previously working source commit SHA.

## Repository size and existing HTML

Existing generated files can remain during migration; artifact deployments ignore
them. Once the first Actions deployment works, remove the old generated files
from the current branch in a separate commit, retaining `.github/`, this guide,
and any hosting configuration you still need. This does not shrink existing Git
history. Do not rsync with `--delete` into this repository after migration: that
can delete the deployment workflow.

Pages still limits the published site to 1 GB. The workflow checks output size
before uploading. Artifact deployment avoids adding future HTML and scene files
to Git history; reducing viewer sizes is a separate change.
