# kubernetes-manifest-security-scanner

Browser-based scanner for Kubernetes manifests. Paste a YAML file (Pod, Deployment, StatefulSet, DaemonSet, Job, CronJob), get findings against the Pod Security Standards plus common hardening rules.

Single HTML file. No build step, no dependencies, no network calls.

## What it checks

- **Pod Security Standards** — flags violations of the Restricted profile: privileged containers, host namespaces (`hostNetwork`, `hostPID`, `hostIPC`), `allowPrivilegeEscalation`, root user (`runAsUser: 0` or unset), unmasked `/proc`, host path mounts, sysctls outside the allowed list.
- **Capabilities** — added Linux capabilities (especially `SYS_ADMIN`, `NET_ADMIN`, `SYS_PTRACE`, `NET_RAW`) and missing `drop: [ALL]`.
- **Resource limits** — missing CPU/memory `requests` or `limits`, ratio between request and limit when both are set.
- **Image hygiene** — `:latest` tag, no tag at all, image pull policy `Always` for non-development workloads, images from public registries without a digest.
- **Probes** — missing `livenessProbe` and `readinessProbe` on long-running workloads.
- **Secrets and config** — secrets passed as environment variables instead of mounted, sensitive values in ConfigMaps.
- **Service account** — `automountServiceAccountToken: true` (default) on workloads that don't need API access, `default` service account in use.
- **NetworkPolicy** — flagged if a Deployment is in a namespace with no NetworkPolicy detected in the pasted input (informational; cluster state isn't visible).
- **Read-only root filesystem** — `readOnlyRootFilesystem: false` or unset.

## Severity scale

| Tag | Meaning |
|-----|---------|
| critical | Container breakout primitive (privileged, hostPath to /, host namespaces with capabilities) |
| high | Restricted-profile violation that materially weakens isolation |
| medium | Baseline-profile violation or significant hardening gap |
| low | Hygiene issue (missing probes, `:latest` tag, missing limits) |
| info | Suggestion, e.g. consider seccompProfile |

## What this is NOT

Not a replacement for `kube-bench`, `kubescape`, `polaris`, or admission controllers like Kyverno/OPA Gatekeeper. It does not query the cluster, expand Helm templates, or render Kustomize. It applies rules to whatever YAML you paste. Use it for fast review of manifest changes; enforce policy in cluster.

## Privacy

The YAML you paste stays in the browser. No analytics, no storage, no network requests after page load.

## Samples

Three samples are wired to header buttons: a privileged Pod, a Deployment missing security context entirely, and a manifest with mixed issues (good practices on the Pod but `:latest` image and missing limits).

## Related repositories

Part of a 10-repo security audit set.

Browser-based audit tools:
- [iam-policy-analyzer](https://github.com/0xelitesystem/iam-policy-analyzer)
- [terraform-security-linter](https://github.com/0xelitesystem/terraform-security-linter)
- [session-cookie-auditor](https://github.com/0xelitesystem/session-cookie-auditor)
- [regex-redos-checker](https://github.com/0xelitesystem/regex-redos-checker)

Reference collections:
- [incident-response-runbooks](https://github.com/0xelitesystem/incident-response-runbooks)
- [ai-llm-security-audit](https://github.com/0xelitesystem/ai-llm-security-audit)
- [api-security-audit-checklist](https://github.com/0xelitesystem/api-security-audit-checklist)
- [secrets-leak-response-runbook](https://github.com/0xelitesystem/secrets-leak-response-runbook)
- [threat-modeling-worksheets](https://github.com/0xelitesystem/threat-modeling-worksheets)

## License

MIT. See [LICENSE](LICENSE).
