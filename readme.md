# FinOps Webhook Template Chart

This chart is a template that can be imported into a composition chart to include the webhook for the FinOps optimization components. See the composition definition [krateo-v2-template-finops-example-pricing-vm-azure](https://github.com/krateoplatformops/krateo-v2-template-finops-example-pricing-vm-azure) for more details.

## Usage
Include the chart as a dependency, then configure the webhook through the following fields in the values file:
```yaml
# Webhook configuration, the certificare and the service should match the OPA install
# Default values match the krateo-installer
webhook:
  fullnameOverride: mutating-webhook-finopsexamplepricingvmazures
  # To set annotations on all admissionController resources (Secret/Certificate/Issuer/AdmissionController)
  # annotations:
  #   example: value

  # Adds a namespace selector to the admission controller webhook
  namespaceSelector:
    matchExpressions:
    - key: openpolicyagent.org/webhook
      operator: NotIn
      values: 
        - ignore

  # SideEffectClass for the webhook, setting to NoneOnDryRun enables dry-run.
  # Only None and NoneOnDryRun are permitted for admissionregistration.k8s.io/v1.
  sideEffect: None

  useHttps: true
  generatedCerts: true # If this is set to true, the chart will look for the certsSecretRef secret
  # The webhook lookup will look for the field "caBundle"
  certsSecretRef:
    name: opa-kube-mgmt-cert
    namespace: opa-system
  # Otherwise it will use the CA Bundle specified in CA
  CA: ""

  service:
    name: opa-kube-mgmt
    namespace: opa-system
    port: 8181
```

This webhook relies on the certificates created by the [opa-chart](https://github.com/krateoplatformops/opa-chart). If you have external certificates, configure the `certsSecretRef`.