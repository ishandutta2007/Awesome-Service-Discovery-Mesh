# Awesome-Service-Discovery-Mesh

# Awesome-Service-Discovery-Mesh

## Top Service Discovery & Mesh Tools Ecosystem

**Curated List of SaaS Products & Open-Source GitHub Projects**  
*Focused on Dynamic Service Registry, Health Checking & Cross-Platform Networking*  
**Last updated: March 2026**

This repository tracks notable **SaaS platforms** and **open-source projects** for **Service Discovery & Mesh**. These tools help applications find each other without hardcoded addresses — maintaining a real-time registry of healthy instances, propagating changes to clients, and in mesh deployments, securing and observing all service-to-service traffic.

**Examples** include AWS Cloud Map, HashiCorp Consul, Eureka, ZooKeeper, CoreDNS, Istio, Linkerd, Kuma, Traefik Mesh, and NGINX Service Mesh (the category leaders).

**Open-source emphasis**: This section is heavily expanded with every major active project for self-hosting, multi-platform service registry, and transparent mesh architecture — ideal for platform engineers prioritizing control, avoiding vendor lock-in, and building networking that spans Kubernetes, VMs, and bare metal.

Contributions welcome! Open a PR to add/update entries. Keep descriptions factual and link to official sites.

## Table of Contents
- [SaaS/Hosted Platforms](#saas-hosted-platforms)
- [Open-Source GitHub Projects](#open-source-github-projects)
- [How to Contribute](#how-to-contribute)
- [Disclaimer](#disclaimer)

## SaaS/Hosted Platforms

- **[AWS Cloud Map](https://aws.amazon.com/cloud-map/)**  
  **Fully managed service discovery for AWS resources.** Maps logical names to backend services including DynamoDB tables, SQS queues, EC2 instances, and ECS tasks. Create a **namespace** to group services, then a **service** for each resource type. Applications discover resources via **`DiscoverInstances` API calls or DNS queries** depending on namespace configuration. Health checking integrates with Route 53 or third-party checkers. Pricing is per registered resource and discovery API call. **Best for AWS-native applications** wanting managed service discovery without operating Consul or etcd.

- **[HashiCorp Consul (HCP)](https://www.hashicorp.com/cloud)**  
  Managed Consul service mesh on HashiCorp Cloud Platform with automated upgrades, scaling, and multi-cloud peering.

- **[Google Cloud Service Directory](https://cloud.google.com/service-directory)**  
  Managed service registry for GCP with DNS and API-based discovery, health checking, and integration with GKE.

- **[Azure Private DNS](https://azure.microsoft.com/en-us/products/private-dns/)**  
  Managed DNS zones for service discovery within Azure VNets, with autoregistration for VMs and Private Link integration.

- **[Google Anthos Service Mesh](https://cloud.google.com/anthos/service-mesh)**  
  Managed Istio distribution on GCP with centralized multi-cluster management, automatic upgrades, and integration with Anthos Config Management.

- **[Buoyant Cloud](https://buoyant.io/cloud)**  
  Commercial management plane for Linkerd with automated deployment, health monitoring, and lifecycle management across multiple clusters.

## Open-Source GitHub Projects

- **[HashiCorp Consul](https://github.com/hashicorp/consul)**  
  **The reference multi-platform service discovery and mesh solution** — MPL 2.0 licensed. **Built-in service discovery with health checking, DNS interface, and HTTP API** — services register themselves and Consul maintains a real-time service catalog. **Native multi-datacenter support** via WAN federation or cluster peering with mesh gateways — services in one datacenter discover and communicate with services in another, **regardless of whether datacenters run Kubernetes, VMs, or a mix**. **Four gateway types**: API gateway, ingress gateway, terminating gateway (mesh-to-external), and mesh gateway (datacenter-to-datacenter). **Intentions control traffic at L4 and L7** with allow/deny rules between services. **Consul Dataplane (v1.14+)** removes client agents for lower latency. **Consul allows users to choose consistency level (strong, weak, or eventual)** — the only tool in this category with that flexibility. **The clear choice for hybrid-cloud enterprises** with significant VM and bare-metal estates alongside Kubernetes.

- **[Eureka](https://github.com/Netflix/eureka)**  
  **Netflix's REST-based service registry optimized for AWS availability** — Apache 2.0 licensed. **AP system (availability over consistency)** — designed for cross-region, large-scale deployments where brief registry inconsistency is acceptable. **Client-server model**: services register with the Eureka server and send periodic heartbeats (default 30s); the server removes instances that miss heartbeats. **Eureka clients cache the registry locally** — if the Eureka server goes down, clients continue using cached data. **Zone-aware load balancing**: `preferSameZoneEureka` routes traffic to same-zone instances first. **Spring Cloud Netflix integration** is deep and mature — the default for Java/Spring microservices. **Default port 8761**. **Trade-offs**: Weak consistency means clients may briefly see stale data. Health check propagation requires explicit configuration (`eureka.client.healthcheck.enabled=true`). **Best for Java/Spring Cloud microservices** wanting Netflix-proven availability-first discovery.

- **[ZooKeeper](https://github.com/apache/zookeeper)**  
  **The mature distributed coordination service with strong consistency** — Apache 2.0 licensed. **CP system using ZAB (Paxos variant)** — guarantees strong consistency at the cost of availability during partitions. **Service discovery via ephemeral znodes**: services create znodes under `/services/{service-name}/`, and when a session terminates (crash, network loss, or timeout), ZooKeeper atomically deletes all ephemeral nodes owned by that session. **Session timeout negotiated in 4–40s window** (default tickTime 2s, min 4s, max 40s). **Watch and poll discovery patterns**: watches notify immediately but can cause herd effect with many clients; polls are safer but slower. **Critical failure mode**: a hung process (GC stall, blocked event loop) can survive session timeout without dropping TCP — the ephemeral node stays, and clients keep routing to a zombie instance. **Best for applications already using ZooKeeper** for leader election and distributed locks, or requiring strong consistency.

- **[CoreDNS](https://github.com/coredns/coredns)**  
  **The general-purpose DNS server that is the default for Kubernetes clusters** — Apache 2.0 licensed. **GA for Kubernetes cluster DNS since 1.11** — replaced kube-dns as the default. **Single Go process instead of three containers** (kubedns, dnsmasq, sidecar) — eliminates the dnsmasq security vulnerability surface. **Plugin-based architecture** — the Corefile configures everything from Kubernetes service resolution to stub domains, caching, and Prometheus metrics. **Resolves `service.namespace.svc.cluster.local`** for in-cluster discovery. **The foundation for Kubernetes DNS-based service discovery** — used in GKE, AKS, EKS, and most distributions.

- **[Istio](https://github.com/istio/istio)**  
  **The industry-standard service mesh with the richest traffic management** — CNCF Graduated, Apache 2.0 licensed. **Two data plane modes**: sidecar (Envoy per pod) and **ambient** (per-node ztunnel with optional waypoint proxies, no pod restarts required). **Kubernetes-first** — relies on Kubernetes service discovery; VM support exists but is secondary. **VirtualService and DestinationRule** provide fine-grained routing, retries, timeouts, circuit breaking, fault injection, and traffic mirroring. **AuthorizationPolicy resources** with rich matching on headers, paths, methods, and source identities. **Best for Kubernetes-centric environments** needing advanced L7 traffic management and a large API ecosystem.

- **[Linkerd](https://github.com/linkerd/linkerd2)**  
  **The simplicity-first, ultralight service mesh** — CNCF Graduated, Apache 2.0 licensed. **Built-in service discovery via destination service** distributing updates through long-lived gRPC streams. **Identity service** issues 24-hour proxy certificates tied to Kubernetes ServiceAccounts with TLS 1.3 hybrid post-quantum key exchange. **EWMA load balancing** shifts traffic toward endpoints that are fast right now rather than round-robin. **Automatic mTLS** with certificate rotation handled for you. **Gartner Peer Insights reviewers** consistently cite operational simplicity and low latency as primary selection reasons. **Best for teams that want a service mesh that stays out of the way**.

- **[Kuma](https://github.com/kumahq/kuma)**  
  **Platform-agnostic service mesh from Kong** — Apache 2.0 licensed. **Runs on Kubernetes and VM/Bare Metal** with the same control plane. **Multi-Mesh** for isolated meshes in one cluster. **Single and Multi-Zone** for cross-platform, cross-cloud, cross-cluster deployments. **Built-in service discovery, mTLS, traffic permissions, routing, fault injection, tracing, and GUI**. **Kong Mesh** is the enterprise distribution. **Best for teams wanting one mesh across Kubernetes and VMs** with a clean GUI.

- **[Traefik Mesh](https://github.com/traefik/mesh)**  
  **Lightweight, simpler service mesh** — **opt-in by default**, existing services unaffected until explicitly added. **Kubernetes-only** (not OpenShift-compatible). **No mTLS** — a significant security limitation. **Configuration via Kubernetes annotations and SMI objects**. **Best for teams already using Traefik** wanting basic mesh features without Istio or Linkerd complexity.

- **[etcd](https://github.com/etcd-io/etcd)**  
  **The cloud-native key-value store with Raft consensus** — Apache 2.0 licensed. **Strong consistency (CP)** like ZooKeeper but simpler to operate. **Common for gRPC service discovery** via custom `NameResolver` implementations using etcd v3 API with Watch for instance changes. **Best for gRPC-heavy environments** needing a simple, reliable registry.

### Additional Strong Open-Source Options

- **Serf** — Decentralized cluster membership and failure detection from HashiCorp. Gossip-based, eventual consistency.
- **Netflix Ribbon** — Client-side load balancer that pairs with Eureka for service discovery.
- **Spring Cloud LoadBalancer** — Replaces Ribbon in modern Spring Cloud, with zone preference via Eureka metadata.
- **go-zero** — Go microservices framework with built-in service discovery via etcd.
- **Dapr** — Distributed application runtime with service invocation building block for discovery.

**Frameworks for building custom systems**: Combine **Consul** for cross-platform service discovery spanning Kubernetes, VMs, and bare metal with built-in health checking, **Eureka** for Java/Spring Cloud microservices prioritizing availability, **CoreDNS** for Kubernetes DNS-based discovery, **Istio** for advanced L7 traffic management on Kubernetes, **Linkerd** for simplicity-first mesh with automatic mTLS, and **etcd** for gRPC-native registry — to build a service discovery and mesh layer tailored to your platform mix and consistency requirements.

## How to Contribute

1. Fork the repo.
2. Add/edit entries in `README.md` (follow existing format).
3. Include: name, link, 1–2 sentence description, and whether it's SaaS or open-source.
4. Submit PR with a short explanation.

Star the repo if you find it useful!

## Disclaimer

- This is a **community-curated** list — not exhaustive and not an endorsement.
- Service discovery and mesh tools differ significantly in consistency model, platform support, and operational complexity. **Consul is the only solution with built-in service discovery and native multi-datacenter support** — Eureka and ZooKeeper require custom configuration for multi-DC. **Eureka is AP (availability over consistency); Consul, ZooKeeper, and etcd are CP** — choose based on whether brief registry inconsistency is acceptable. **ZooKeeper's session timeout mechanism can fail when a process hangs without dropping TCP** — the ephemeral node survives, and clients route to zombies. **Istio's ambient mode eliminates pod restarts** when adding workloads to the mesh, a significant operational improvement over sidecar mode. **Traefik Mesh has no mTLS** — a critical security gap. **Service mesh adds latency overhead** — estimated +0.5-1ms for mesh-based discovery vs no overhead for client-side.
- Self-hosted open-source solutions require proper cluster sizing, health check configuration, and monitoring of the registry itself.

---

**Made for platform engineers, SREs, DevOps teams, and developers building reliable service networking.**  
Let's make service discovery and mesh more open, resilient, and platform-agnostic.
