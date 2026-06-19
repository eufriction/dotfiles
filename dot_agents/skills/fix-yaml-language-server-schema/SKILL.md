---
name: fix-yaml-language-server-schema
description: >
  Insert or replace a yaml-language-server schema comment in a Kubernetes
  manifest. Use when asked to add a schema hint, fix a yaml-language-server
  $schema comment, annotate a Kubernetes manifest or CRD YAML, or when the
  user pastes a manifest that is missing a schema line. Trigger terms:
  yaml-language-server, $schema, schema comment, Kubernetes manifest, CRD.
---

## Default workflow

1. Parse `apiVersion` and `kind` from the manifest.
2. If `apiVersion` contains no `/` (e.g. `v1`), the resource is a **core API
   object** — stop and return the manifest unchanged (see "Core API objects").
3. Split `apiVersion` into `<group>/<version>` (e.g. `cilium.io/v2` → group
   `cilium.io`, version `v2`).
4. Lowercase `kind` (e.g. `CiliumNetworkPolicy` → `ciliumnetworkpolicy`).
5. Build the schema comment:

   ```
   # yaml-language-server: $schema=https://raw.githubusercontent.com/datreeio/CRDs-catalog/main/<group>/<kind>_<version>.json
   ```

6. Insert the comment as the first line after the document separator (`---`) if
   one is present, or as the first line if not. If a `# yaml-language-server:`
   line already exists, replace it in place.
7. Return only the updated YAML — no explanation, no markdown fences.

## Core API objects

If `apiVersion` has no `/` (e.g. `v1`), the resource is a Kubernetes core API
object (`Pod`, `Service`, `ConfigMap`, etc.). The yaml-language-server already
have these schemas and must **not** receive a `# yaml-language-server:` comment.
Return the manifest unchanged.

## Edge cases

- **Existing schema line:** replace it, do not duplicate it.
- **Document separator:** preserve `---` and keep it as the first line; place
  the schema comment immediately after it.
- **Multi-document YAML:** apply to the first document only unless the user
  selects all documents.
- **Preserve everything else:** no other lines, comments, or blank space should
  change.

## Output contract

- Output only the updated YAML.
- No prose, no explanation, no markdown fences.

## Examples

### Input 1 — no separator, no existing schema

```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: backend-policy
```

### Output 1

```yaml
# yaml-language-server: $schema=https://raw.githubusercontent.com/datreeio/CRDs-catalog/main/cilium.io/ciliumnetworkpolicy_v2.json
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: backend-policy
```

### Input 2 — document separator present

```yaml
---
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: backend-policy
```

### Output 2

```yaml
---
# yaml-language-server: $schema=https://raw.githubusercontent.com/datreeio/CRDs-catalog/main/cilium.io/ciliumnetworkpolicy_v2.json
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: backend-policy
```

### Input 3 — replace an existing schema comment

```yaml
---
# yaml-language-server: $schema=https://example.com/old-schema.json
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: my-route
```

### Output 3

```yaml
---
# yaml-language-server: $schema=https://raw.githubusercontent.com/datreeio/CRDs-catalog/main/gateway.networking.k8s.io/httproute_v1.json
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: my-route
```

### Input 4 — core API object (no group in `apiVersion`)

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  key: value
```

### Output 4

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  key: value
```
