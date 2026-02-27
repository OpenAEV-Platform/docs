# Development Troubleshooting

This guide provides solutions to common issues encountered during development, building, and CI/CD pipelines for OpenAEV.

## Yarn / Corepack issues

### HTTP 500 error from repo.yarnpkg.com

**Symptom:**

Build fails with an error similar to:

```
Error: Server answered with HTTP 500 when performing the request to
https://repo.yarnpkg.com/4.11.0/packages/yarnpkg-cli/bin/yarn.js
```

This error occurs when the Yarn repository (`repo.yarnpkg.com`) returns a server error (HTTP 500) while Corepack tries to download the Yarn binary. This is typically a **transient upstream issue** — the Yarn package registry is temporarily unavailable.

**Impact:**

This affects any CI/CD pipeline (CircleCI, Drone, GitHub Actions, etc.) that uses Corepack to manage Yarn versions, as well as local development setups relying on Corepack.

**Solutions:**

#### 1. Retry the build

Since this is usually a transient server-side issue, simply **re-running the failed CI/CD job** is often sufficient. Most CI platforms support manual or automatic retries:

- **CircleCI**: Click "Rerun" on the failed workflow.
- **Drone**: Click "Restart" on the failed build.
- **GitHub Actions**: Click "Re-run failed jobs" on the workflow run.

#### 2. Pin and cache the Yarn binary

To avoid downloading Yarn from the registry on every build, you can enable Corepack to use a locally cached version:

```bash
# Download and cache Yarn binary ahead of time
corepack prepare yarn@4.11.0 --activate
```

In your CI configuration, cache the Corepack directory to prevent repeated downloads:

- **Corepack cache location**: `~/.cache/node/corepack` (Linux) or the path defined by `COREPACK_HOME`.

Example for **GitHub Actions**:
```yaml
- name: Cache Corepack
  uses: actions/cache@v4
  with:
    path: ~/.cache/node/corepack
    key: corepack-yarn-${{ hashFiles('package.json') }}

- name: Enable Corepack
  run: corepack enable

- name: Install dependencies
  run: yarn install
```

Example for **CircleCI**:
```yaml
- restore_cache:
    keys:
      - corepack-yarn-v1-{{ checksum "package.json" }}
- run:
    name: Enable Corepack and install
    command: |
      corepack enable
      yarn install
- save_cache:
    key: corepack-yarn-v1-{{ checksum "package.json" }}
    paths:
      - ~/.cache/node/corepack
```

#### 3. Use COREPACK_NPM_REGISTRY as a fallback

If `repo.yarnpkg.com` is persistently unavailable, you can configure Corepack to use an alternative npm registry to download the Yarn package:

```bash
export COREPACK_NPM_REGISTRY=https://registry.npmjs.org
corepack enable
yarn install
```

This tells Corepack to fetch the `yarn` package from the npm registry instead of the default Yarn repository.

#### 4. Install Yarn without Corepack

As a last resort, you can bypass Corepack entirely and install Yarn directly:

```bash
# Using npm
npm install -g yarn@4.11.0

# Or download directly
curl -fsSL https://yarnpkg.com/install.sh | bash
```

!!! warning "Version consistency"
    When bypassing Corepack, ensure the Yarn version you install matches the version specified in your project's `package.json` (`packageManager` field) to avoid inconsistencies.

### Corepack not found

**Symptom:**

```
corepack: command not found
```

**Solution:**

Corepack is included with Node.js >= 16.10 but may not be enabled by default. Enable it with:

```bash
corepack enable
```

If Corepack is not available, ensure you are using a supported Node.js version (>= 20 recommended for OpenAEV):

```bash
node --version  # Should be >= 20
```

## Network and proxy issues

### Builds failing behind a corporate proxy

If your CI/CD environment or development machine is behind a proxy, package downloads may fail.

**Solution:**

Configure the proxy for both npm/Yarn and Corepack:

```bash
# For Yarn
yarn config set httpProxy http://proxy.example.com:8080
yarn config set httpsProxy http://proxy.example.com:8080

# For Corepack / Node.js
export HTTP_PROXY=http://proxy.example.com:8080
export HTTPS_PROXY=http://proxy.example.com:8080
export NO_PROXY=localhost,127.0.0.1
```

### SSL certificate errors during package install

**Symptom:**

```
Error: unable to verify the first certificate
```

**Solution:**

If your environment uses a custom CA or self-signed certificate:

```bash
# Point Node.js to your CA bundle
export NODE_EXTRA_CA_CERTS=/path/to/your/ca-bundle.crt
```

!!! warning "Security"
    Avoid disabling SSL verification (`NODE_TLS_REJECT_UNAUTHORIZED=0`) in production or CI environments. Always prefer adding the correct CA certificate.

## Getting help

If you continue to experience build or development issues:

1. **Check the logs** for specific error messages.
2. **Search the [Filigran Community on Slack](https://community.filigran.io)** for similar issues.
3. **Open an issue** on the [OpenAEV documentation repository](https://github.com/OpenAEV-Platform/docs/issues) if you believe documentation is missing or incorrect.
