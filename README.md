# Ambient Mesh (ambient-mesh)

Ambient Mesh is a sidecar-less service mesh architecture built on Istio that simplifies microservices communication, enhances zero-trust security, and improves observability without requiring sidecar proxy injection. It uses a shared per-node proxy (ztunnel) for zero-trust security and optional waypoint proxies for advanced Layer 7 policies, enabling seamless migration from sidecar-based meshes with zero downtime.

**URL:** [https://ambientmesh.io/](https://ambientmesh.io/)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags

 - Service Mesh, Istio, Kubernetes, Zero Trust, Observability, Traffic Management, Microservices

## Timestamps

- **Created:** 2026-04-19
- **Modified:** 2026-04-19

## APIs

### Ambient Mesh

Ambient Mesh provides a sidecar-less service mesh via the Kubernetes Gateway API and Istio ambient mode. It exposes configuration APIs for traffic management, security policies, resilience settings, and observability through standard Kubernetes CRDs including HTTPRoute, Gateway, AuthorizationPolicy, and WaypointProxy resources.

**Human URL:** [https://ambientmesh.io/](https://ambientmesh.io/)

#### Tags

 - Service Mesh, Kubernetes, Istio, Zero Trust

#### Properties

- [Documentation](https://ambientmesh.io/docs/)
- [GettingStarted](https://ambientmesh.io/docs/quickstart/)
- [APIReference](https://ambientmesh.io/docs/)

## Common Properties

- [Website](https://ambientmesh.io/)
- [Documentation](https://ambientmesh.io/docs/)
- [GettingStarted](https://ambientmesh.io/docs/quickstart/)
- [Blog](https://ambientmesh.io/blog/)
- [GitHubOrganization](https://github.com/istio)
- [GitHubRepository](https://github.com/istio/istio)

## Features

| Name | Description |
|------|-------------|
| Sidecar-Less Architecture | Operates at the platform layer without sidecar proxy injection, reducing resource overhead and operational complexity while maintaining full service mesh capabilities. |
| Zero-Trust Security | SPIFFE-based workload identity with automatic mutual TLS encryption between workloads, certificate management, and zero-trust network policies enforced by ztunnel. |
| Traffic Management | Advanced traffic routing, load balancing, traffic splitting, mirroring, blue-green deployments, and gateway management via Kubernetes Gateway API HTTPRoute resources. |
| Resilience | Zone-aware load balancing, circuit breaking, outlier detection, fault injection, timeouts, and retry budgets for high-availability workloads. |
| Observability | Distributed tracing, performance metrics via Prometheus, Kiali observability console, and HTTP observability for traffic visualization and security verification. |
| Zero-Downtime Migration | Free migration tooling for upgrading from sidecar-based architectures with automated workload analysis and risk mitigation for waypoint proxy requirements. |
| Waypoint Proxies | Optional per-namespace or per-workload Layer 7 proxies that provide advanced policy enforcement without requiring per-pod sidecar containers. |

## Use Cases

| Name | Description |
|------|-------------|
| Microservices Security | Enforce mutual TLS and zero-trust policies across microservices without modifying application code or injecting sidecar proxies. |
| Traffic Management | Implement advanced traffic routing, A/B testing, canary deployments, and traffic mirroring across Kubernetes workloads. |
| Istio Migration | Migrate existing Istio sidecar-based deployments to ambient mode with zero downtime using the free migration tooling. |
| Kubernetes Observability | Gain full visibility into service-to-service communication with metrics, tracing, and traffic visualization via Kiali and Prometheus. |
| Multi-Cluster Networking | Extend ambient mesh policies and security across multiple Kubernetes clusters for hybrid and multi-cloud architectures. |

## Integrations

| Name | Description |
|------|-------------|
| Istio | Ambient Mesh is built on Istio ambient mode, using its control plane and CRDs for configuration. |
| Kubernetes Gateway API | Uses the standard Kubernetes Gateway API with HTTPRoute, Gateway, and GRPCRoute resources for traffic management. |
| Prometheus | Integrates with Prometheus for metrics collection and monitoring of mesh traffic and performance. |
| Kiali | Integrates with Kiali for service mesh observability, traffic visualization, and security verification. |
| Gloo Mesh | Solo.io's Gloo Mesh provides enterprise-grade ambient mesh management for scaling across enterprise workloads. |
| OpenShift | Red Hat OpenShift Service Mesh 3.x supports Istio ambient mode for OpenShift deployments. |

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
