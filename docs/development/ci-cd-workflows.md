# CI/CD Workflows

OpenAEV uses [GitHub Actions](https://docs.github.com/en/actions) for continuous integration and deployment. This page documents the workflows available to contributors and how to use them.

## Available workflows

| Workflow | File | Trigger | Purpose |
|---|---|---|---|
| **Deploy feature branch on staging** | `test-feature-branch.yml` | Manual (`workflow_dispatch`) | Build and deploy a feature branch to a staging environment for testing |

## Deploy feature branch on staging

The `test-feature-branch` workflow allows you to deploy any branch of the OpenAEV repository to a dedicated staging environment. This is useful for testing changes in a production-like setup before merging.

### How to trigger

1. Navigate to the [Actions tab](https://github.com/OpenAEV-Platform/openaev/actions) of the `openaev` repository.
2. Select the **"Deploy feature branch on staging"** workflow in the left sidebar.
3. Click **"Run workflow"**.
4. Select the branch you want to deploy.
5. Optionally customize the `openaev_config` input (see below).
6. Click **"Run workflow"** to start the deployment.

### Input parameters

| Parameter | Type | Required | Default | Description |
|---|---|---|---|---|
| `openaev_config` | `string` | No | `{ "SPRING_PROFILES_ACTIVE": "ci" }` | JSON object containing Spring configuration overrides for the deployed instance |

!!! warning "The `openaev_config` input must be valid JSON"

    The value you provide for `openaev_config` is injected directly into the AWX deployment
    command as part of `--extra_vars`. It **must** be a valid JSON object (e.g.
    `{ "SPRING_PROFILES_ACTIVE": "ci" }`).

    Common mistakes that cause a **"not valid JSON or YAML"** error:

    - Using single quotes instead of double quotes inside the JSON: `{ 'key': 'value' }` ❌
    - Adding trailing commas: `{ "key": "value", }` ❌
    - Wrapping the value in extra quotes in the GitHub UI (the UI already treats it as a string)
    - Using YAML syntax instead of JSON: `key: value` ❌

### What the workflow does

The workflow performs the following steps:

1. **Build** — Checks out the selected branch, sets up an Alpine Linux environment, builds the frontend (`yarn install && yarn build`) and backend (`mvn install`).
2. **Containerize** — Builds a Docker image tagged with the branch name and pushes it to DockerHub (`filigran/openaev-platform`).
3. **Deploy** — Triggers an AWX job template to deploy the built image to the staging infrastructure.

### Accessing the staging environment

After a successful deployment, the workflow outputs a URL for the feature environment. The URL follows this pattern:

```
https://feat-<branch-slug>.oaev.staging.filigran.io
```

Where `<branch-slug>` is derived from the branch name (first 15 characters, with underscores and dots replaced by hyphens, trailing hyphens removed).

!!! tip "Example"

    For a branch named `feature/my-new-feature`, the staging URL would be:
    `https://feat-feature-my-new.oaev.staging.filigran.io`

## Troubleshooting

### "Not valid JSON or YAML" error in the deploy step

This error occurs when the AWX `--extra_vars` payload is malformed. The most common causes are:

1. **Invalid `openaev_config` input** — Ensure the value is strict JSON with double quotes. See the [warning above](#input-parameters).
2. **Branch name with special characters** — Branch names containing characters like `"`, `'`, or `$` can break the shell quoting in the deploy step. Use simple alphanumeric branch names with hyphens and slashes (e.g. `feature/my-feature`).
3. **Empty Docker image digest** — If the build step fails silently, the image digest output may be empty, producing invalid JSON in `extra_vars`. Check the build step logs first.

!!! info "Inspecting the error"

    In the GitHub Actions job log, expand the **"Deploy feature environment via AWX"** step.
    Look at the full `awx` command being executed — this will show you the exact `--extra_vars`
    value that was rejected. Verify it is valid JSON by pasting it into a JSON validator.

### Build failures in the Alpine environment

If the build fails during `yarn install`, `yarn build`, or `mvn install`:

- Ensure your branch has no merge conflicts with the base branch.
- Check that `openaev-front/package.json` and `pom.xml` are valid.
- Verify that all submodules are properly initialized (the workflow uses `submodules: recursive`).
