---
name: external-secrets-gitops-authoring
version: 1.0.0
description: Author declarative ExternalSecret manifests for GitOps repositories and prevent Argo CD drift caused by implicit defaults or generated fields.
triggers:
  - create an ExternalSecret manifest
  - add an ExternalSecret to GitOps
  - configure External Secrets for Argo CD
  - fix ExternalSecret Argo drift
  - make an ExternalSecret fully declarative
---

# External Secrets GitOps Authoring

Create and review Kubernetes `ExternalSecret` manifests for repositories whose
source-controlled YAML is reconciled by a GitOps controller such as Argo CD.
The desired manifest must describe the complete intended resource specification,
not merely the minimum accepted by the API server. Minimize the difference
between the rendered source and the deployed object without copying controller-
managed state into Git.

## Goals

- Keep secret values out of source control and out of rendered manifests.
- Make the resource specification explicit and reproducible.
- Prevent persistent GitOps drift caused by API or controller defaults.
- Preserve the separation between application secrets and the secret-store
  infrastructure that reads them.
- Produce manifests that can be rendered and reviewed before deployment.

## Before Authoring

1. Identify the installed External Secrets Operator API version and CRD schema.
   Use the version supported by the target cluster rather than assuming that
   examples from another operator release have identical defaults.
2. Inspect the repository's existing conventions for namespaces, Kustomize
   overlays, `SecretStore` references, labels, and resource ownership.
3. Determine whether the referenced `SecretStore` or `ClusterSecretStore`
   already exists. Do not create a second store in an application manifest
   unless that is explicitly part of the design.
4. Identify the intended target Secret name, ownership behavior, refresh
   behavior, and source keys. Treat those as part of the contract.
5. Confirm that the source repository does not already contain a resource with
   the same Kubernetes identity.

## Authoring Rules

### Keep Values External

Use `remoteRef` or an approved `dataFrom` source to reference the external
secret store. Never put secret values, access tokens, private keys, generated
Docker configuration, or local environment-file contents in the manifest.

The manifest may contain non-sensitive identifiers such as remote parameter
paths, but those paths should not expose credentials through names or comments.
Use the repository's secret naming convention and avoid inventing alternate
paths for an existing secret.

### Declare The Resource Specification

Declare every behaviorally relevant field, including fields that currently have
operator or CRD defaults. At minimum, make these decisions explicit when they
are supported by the installed schema:

```yaml
apiVersion: external-secrets.io/v1
kind: ExternalSecret
metadata:
  name: application-secret
  namespace: application-namespace
spec:
  refreshInterval: 1h
  secretStoreRef:
    name: secret-store
    kind: ClusterSecretStore
  target:
    name: application-secret
    creationPolicy: Owner
    deletionPolicy: Retain
    template:
      engineVersion: v2
      mergePolicy: Replace
      type: Opaque
  data:
    - secretKey: EXAMPLE_VALUE
      remoteRef:
        key: /path/to/example-value
        conversionStrategy: Default
        decodingStrategy: None
        metadataPolicy: None
```

Adjust values and fields to the installed CRD and the intended behavior. The
example is a shape, not a universal policy. In particular, choose deletion and
creation policies deliberately because they control the lifecycle of the
generated Kubernetes Secret.

For each `remoteRef`, explicitly declare the operator's default conversion,
decoding, and metadata policies when the schema supports them. Apply the same
rule to every entry rather than only the first entry. If using `dataFrom`, make
its extraction, find, or rewrite behavior equally explicit.

Do not add fields merely because they appear in a live object. Exclude
`status`, `metadata.resourceVersion`, `metadata.uid`, `metadata.generation`,
managed fields, controller timestamps, and other fields owned by Kubernetes or
the operator. Those are observed state, not desired configuration.

### Make Namespace Ownership Clear

Ensure the rendered resource receives the intended namespace exactly once.
Either declare `metadata.namespace` in a standalone manifest or use a clearly
owned Kustomize overlay namespace transformation. Render the overlay that Argo
CD actually tracks; do not validate only the unrendered base when namespace,
labels, names, or patches are applied by Kustomize.

### Keep Store Configuration Separate

An `ExternalSecret` references a store; it does not normally define provider
credentials or provider configuration. Keep cloud region, provider roles,
authentication configuration, and store lifecycle in the platform or
infrastructure repository that owns the `SecretStore` or `ClusterSecretStore`.
Keep application-specific remote keys and target mappings in the application
GitOps resource.

If a provider region or other non-secret setting is changed, update the owning
store configuration and verify all consumers. Do not put that setting into the
generated Secret simply to make it available to the application.

## GitOps Drift Workflow

Use this order when creating or repairing a resource:

1. Render the exact Kustomize, Jsonnet, or other source path consumed by the
   GitOps application.
2. Inspect the rendered `ExternalSecret` and confirm identity, namespace, store
   reference, target behavior, refresh interval, mappings, and explicit
   defaultable fields.
3. Compare the rendered output with the live object using the GitOps tool or a
   server-side dry-run where available.
4. If a field is present in the live `spec` because the API or operator
   defaulted it, add that field to source control with the intended value.
5. If a field is only generated runtime state, do not add it to source. Check
   whether the GitOps tool is incorrectly comparing status or managed metadata.
6. Reconcile and inspect both sync status and resource health. Confirm that a
   second reconciliation produces no new spec diff.

Do not solve a spec mismatch first by adding broad Argo CD ignore rules. Ignore
rules can conceal real configuration drift. Use them only for known
controller-owned fields after the desired specification has been made explicit
and the remaining difference has been identified.

## Review Checklist

- [ ] The manifest uses the External Secrets API version supported by the target
      cluster.
- [ ] The resource has a unique name and an unambiguous namespace.
- [ ] The referenced store exists and its `kind` is explicit.
- [ ] No secret values or local environment files are present.
- [ ] `refreshInterval`, target name, creation policy, and deletion policy are
      explicit.
- [ ] Target template behavior is explicit where supported by the CRD.
- [ ] Every remote reference declares conversion, decoding, and metadata
      policies where supported.
- [ ] Remote keys and generated Secret keys are mapped intentionally.
- [ ] The rendered GitOps output has been inspected, not only the source file.
- [ ] The live-vs-source diff contains no avoidable default-field differences.
- [ ] Runtime status and generated metadata have not been copied into Git.
- [ ] Argo CD ignore rules are not being used to conceal a spec mismatch.

## Expected Output

Return or commit:

- The focused `ExternalSecret` manifest or overlay change.
- Any required update to the owning GitOps application path.
- The rendered-manifest validation performed.
- The live-diff or reconciliation result, when cluster access is available.
- Any schema, operator-version, or access limitation that prevented validation.

Keep the change limited to the ExternalSecret contract and its required
GitOps wiring. Do not add credentials, provider infrastructure, unrelated
application resources, or environment-specific operational instructions.
