---
title: "Add Volume to Deployment"
category: Sample
version: 1.6.0
subject: Deployment, Volume
policyType: "mutate"
description: >
    Some Kubernetes applications like HashiCorp Vault must perform some modifications to resources in order to invoke their specific functionality. Often times, that functionality is controlled by the presence of a label or specific annotation. This policy, based on HashiCorp Vault, adds a volume and volumeMount to a Deployment if there is an annotation called "vault.k8s.corp.net/inject=enabled" present.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/a/add-volume-deployment/add-volume-deployment.yaml" target="-blank">/other/a/add-volume-deployment/add-volume-deployment.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-volume
  annotations:
    policies.kyverno.io/title: Add Volume to Deployment
    policies.kyverno.io/category: Sample
    policies.kyverno.io/subject: Deployment, Volume
    policies.kyverno.io/minversion: 1.6.0
    policies.kyverno.io/description: >-
      Some Kubernetes applications like HashiCorp Vault must perform some modifications
      to resources in order to invoke their specific functionality. Often times, that functionality
      is controlled by the presence of a label or specific annotation. This policy, based on HashiCorp
      Vault, adds a volume and volumeMount to a Deployment if there is an annotation called
      "vault.k8s.corp.net/inject=enabled" present.
spec:
  rules:
  - name: add-volume
    match:
      any:
      - resources:
          kinds:
          - Deployment
    preconditions:
      any:
      - key: "{{request.object.spec.template.metadata.annotations.\"vault.k8s.corp.net/inject\"}}"
        operator: Equals
        value: enabled
    mutate:
      patchesJson6902: |-
        - op: add
          path: /spec/template/spec/volumes/-
          value:
            name: vault-secret
            emptyDir:
              medium: Memory
        - op: add
          path: /spec/template/spec/containers/0/volumeMounts/-
          value:
            mountPath: /secret
            name: vault-secret
```
