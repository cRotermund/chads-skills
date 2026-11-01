---
name: go-container-repo-bootstrap
version: 1.0.0
description: Bootstrap a new GitHub repository for containerized Go services with a go.work multi-module layout, Kustomize overlays, GHCR publishing, and standard contributor and agent documentation, producing the same structure every time.
triggers:
  - bootstrap a Go repo
  - scaffold a containerized Go application
  - start a new Go service repository
  - set up a new Go project with Kubernetes and Kustomize
  - create a new repo like gameserver-manager
---

# Go Container Repo Bootstrap

Scaffold a new repository for one or more containerized Go services deployed to
Kubernetes with Kustomize. The goal is repeatability: every bootstrapped
repository has the same layout, tooling, CI, and contributor documentation.
Produce that result by instantiating the templates in this skill, not by
improvising files from memory.

## Fixed Decisions

These are not questions. Do not ask about them or vary them unless the user
explicitly overrides one.

| Area | Decision |
| --- | --- |
| Language | Go only. Each service is its own module, all listed in `go.work`. |
| Layout | `src/services/<name>/`, `src/pkg/`, `infra/k8s/{base,local,prod}`, `docs/`, `tests/`, `.github/workflows/`. |
| Config | Environment variables, with `.env` loaded by `godotenv` for local runs. Real environment wins over `.env`. |
| Containers | Multi-stage build to `gcr.io/distroless/static-debian12:nonroot`, `CGO_ENABLED=0`, user `65532`. Build context is the repository root. |
| Deployment | Kubernetes with Kustomize: `base`, `local`, `prod` overlays. |
| CI/CD | GitHub Actions: `ci.yml` (checks on PRs) and `build.yml` (publish to GHCR on push to the default branch, `linux/amd64` and `linux/arm64`). |
| Branches | `<type>/<issue-number>/<short-description>` branched from the default branch. |
| Commits | Conventional Commits that each reference an issue: `feat(resolves #1): ...`, `fix(#12): ...`. |
| Out of scope | A development harness CLI, TypeScript or other languages, cloud infrastructure provisioning, Argo CD Application definitions. |

## Inputs

Gather these before writing anything. Detect what you can; ask one concise
question for the rest, and only for values you cannot detect or default.

| Input | Placeholder | How to get it |
| --- | --- | --- |
| Repository name | `__REPO_NAME__` | Target directory name, or ask. Lowercase, hyphenated. |
| GitHub owner | `__OWNER__` | Ask, or read the `origin` remote if one exists. Keep the original casing. |
| Lowercase `owner/repo` | `__GHCR_PATH__` | Derive: lowercase of owner and repository name. GHCR requires lowercase. |
| Module root | `__MODULE_ROOT__` | Derive: `github.com/__OWNER__/__REPO_NAME__`. |
| Default branch | `__DEFAULT_BRANCH__` | Detect from the existing repository (`git symbolic-ref --short HEAD` or the remote's default). If there is no repository, ask: `main` or `master`. |
| Go version | `__GO_VERSION__` | The installed toolchain's `major.minor` (`go version`), for example `1.26`. Use it in `go.work`, every `go.mod`, the Dockerfile image tag, and the README prerequisites so they never drift. |
| Project summary | `__PROJECT_SUMMARY__` | One or two sentences from the user. |
| Service name(s) | `__SERVICE__` | At least one. Lowercase, hyphenated. |
| Service description | `__SERVICE_DESCRIPTION__` | One sentence per service. |
| Service port | `__PORT__` | Default `8080`. |
| Secret keys | `__SECRET_KEY__` | Environment variable names each service needs as secrets. May be none. |
| Production namespace | `__NAMESPACE__` | Ask. Required for the prod overlay. |
| SSM path prefix | `__SSM_PREFIX__`, `__SSM_PARAM__` | Only when a service has secrets. Ask for the Parameter Store prefix, for example `/kubernetes/appsecrets/<repo>`. Parameter name per key: lowercase, hyphenated version of the key. |

If the user supplies only a repo name and a one-line purpose, propose defaults
for the rest in a single message and proceed on confirmation.

## Preflight

1. Confirm the target directory. Create it if missing.
2. Check what already exists. If any destination file in the template map
   exists, list the conflicts and ask before overwriting. Never overwrite
   silently. Never touch files you were not asked to create.
3. Confirm `go` is available. Check for `docker` and `kubectl`; if absent, the
   related verification steps will be skipped and must be reported.
4. Initialize git only if the user wants it and the directory is not already a
   repository. Use the detected or chosen default branch name.

## Template Map

Templates live in this skill under `tools/templates/`. Read each template from
the skill, replace the placeholders, and write it to the destination. Template
files end in `.tmpl`; the destination does not.

| Template | Destination |
| --- | --- |
| `root/editorconfig.tmpl` | `.editorconfig` |
| `root/gitattributes.tmpl` | `.gitattributes` |
| `root/gitignore.tmpl` | `.gitignore` |
| `root/dockerignore.tmpl` | `.dockerignore` |
| `root/go.work.tmpl` | `go.work` |
| `root/AGENTS.md.tmpl` | `AGENTS.md` |
| `root/CONTRIBUTING.md.tmpl` | `CONTRIBUTING.md` |
| `root/README.md.tmpl` | `README.md` |
| `docs/architecture.md.tmpl` | `docs/architecture.md` |
| `docs/local-dev.md.tmpl` | `docs/local-dev.md` |
| `github/CODEOWNERS.tmpl` | `.github/CODEOWNERS` |
| `github/ci.yml.tmpl` | `.github/workflows/ci.yml` |
| `github/build.yml.tmpl` | `.github/workflows/build.yml` |
| `service/go.mod.tmpl` | `src/services/<name>/go.mod` |
| `service/main.go.tmpl` | `src/services/<name>/main.go` |
| `service/Dockerfile.tmpl` | `src/services/<name>/Dockerfile` |
| `service/README.md.tmpl` | `src/services/<name>/README.md` |
| `service/env.example.tmpl` | `src/services/<name>/.env.example` |
| `service/internal/config/config.go.tmpl` | `src/services/<name>/internal/config/config.go` |
| `service/internal/config/config_test.go.tmpl` | `src/services/<name>/internal/config/config_test.go` |
| `service/internal/handler/health.go.tmpl` | `src/services/<name>/internal/handler/health.go` |
| `service/internal/handler/health_test.go.tmpl` | `src/services/<name>/internal/handler/health_test.go` |
| `service/internal/middleware/logging.go.tmpl` | `src/services/<name>/internal/middleware/logging.go` |
| `k8s/base/deployment.yaml.tmpl` | `infra/k8s/base/<name>-deployment.yaml` |
| `k8s/base/service.yaml.tmpl` | `infra/k8s/base/<name>-service.yaml` |
| `k8s/base/kustomization.yaml.tmpl` | `infra/k8s/base/kustomization.yaml` |
| `k8s/local/kustomization.yaml.tmpl` | `infra/k8s/local/kustomization.yaml` |
| `k8s/local/env.example.tmpl` | `infra/k8s/local/<name>.env.example` (only if the service has secrets) |
| `k8s/prod/kustomization.yaml.tmpl` | `infra/k8s/prod/kustomization.yaml` |
| `k8s/prod/external-secret.yaml.tmpl` | `infra/k8s/prod/<name>-external-secret.yaml` (only if the service has secrets) |
| `k8s/prod/README.md.tmpl` | `infra/k8s/prod/README.md` |

Also create an empty `tests/.gitkeep` and an empty `src/pkg/.gitkeep`.

## Instantiating Templates

- Replace every `__PLACEHOLDER__` from the Inputs table. A leftover
  `__...__` in any output file is a defect; search for them before finishing.
- Keep the template content otherwise intact. Do not restyle, reorder, or
  "improve" a template while instantiating it. If a template is wrong, tell
  the user and fix the skill's template rather than diverging in one repo.
- Text between `BEGIN optional: secrets` and `END optional: secrets` markers
  (including the marker lines or comments themselves) is kept only when at
  least one service has secrets; otherwise delete the whole block. When it is
  kept, delete the marker lines.
- Repeat per-service content for each additional service. The following are
  service-specific and must be duplicated or extended, not merged:
  - the `use (...)` entries in `go.work`;
  - the matrix entries in both workflows;
  - the Deployment, Service, and `kustomization.yaml` resources in `base`;
  - the `images` entries and secret generators in the `local` and `prod`
    overlays, and the `patches` entry for each Deployment in `prod`;
  - component rows in `README.md` and `docs/architecture.md`, and the example
    commands in `CONTRIBUTING.md` and `docs/local-dev.md`, which use the first
    service as the example.
- For secrets: list one `secretKey` entry per key in the ExternalSecret, one
  line per key in the local `.env.example`, and one parameter per key in the
  prod README. Do not put secret values anywhere. Author or later change
  `ExternalSecret` manifests with the `external-secrets-gitops-authoring`
  skill.
- For a service that must import a sibling workspace module (for example from
  `src/pkg/`), extend its Dockerfile to `COPY` that module before the build and
  add a `replace` directive to the service's `go.mod`, because the image build
  runs with `GOWORK=off`.

## Procedure

1. Run Preflight and collect Inputs.
2. Write the root, docs, GitHub, service, and Kubernetes files from the
   Template Map for each service.
3. Fetch dependencies in each service module, which writes `go.mod` and
   `go.sum` (the Dockerfile requires `go.sum`):

   ```sh
   cd src/services/<name>
   go get github.com/go-chi/chi/v5 github.com/joho/godotenv
   go mod tidy
   ```

   This needs network access. If it is unavailable, say so and list the
   commands the user must run.
4. Verify (below).
5. Report. Do not stage, commit, push, create a remote repository, or open a
   pull request unless the user asks. If the repository is new, print the
   commands for the user to run, for example:

   ```sh
   git add -A
   git commit -m "chore(#1): bootstrap repository"
   gh repo create __OWNER__/__REPO_NAME__ --private --source . --push
   ```

   Also tell the user the follow-up GitHub settings that cannot come from
   files: branch protection on the default branch, requiring the `CI` checks,
   and at least one approving review.

## Verification

Run from the repository root and report each result honestly:

- `gofmt -l .` prints nothing.
- For every module from `go list -m -f '{{.Dir}}'`: `go vet ./...` and
  `go test ./...` pass.
- For every service: `docker build -f src/services/<name>/Dockerfile -t
  <name>:bootstrap-check .` succeeds (skip, and say so, if no container runtime
  is available).
- With the local `*.env.example` files copied to `*.env` temporarily or
  already present: `kubectl kustomize infra/k8s/local` and
  `kubectl kustomize infra/k8s/prod` render without error. Remove any
  `*.env` files you created for the check; they must not be left behind.
- No `__PLACEHOLDER__` strings remain: search the repository for `__[A-Z_]*__`.
- No secret values or machine-specific paths were written.

If a check cannot run, say which one and why. Do not claim a passing result for
a check that was skipped.

## Maintaining This Skill

The templates are the contract. To change a convention for every future
repository, edit the relevant template and bump this skill's `version`
(minor for additions, major for a change that alters generated layout or
contributor workflow). Do not fork a convention inside a single generated
repository without telling the user it now diverges from the skill.
