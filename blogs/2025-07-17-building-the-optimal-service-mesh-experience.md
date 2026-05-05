---
title: "Building the Optimal Service Mesh Experience"
url: "/blog/optimal-mesh-experience-1/"
date: "Thu, 17 Jul 2025 00:00:00 +0000"
author: ""
feed_url: "https://ambientmesh.io/blog/index.xml"
---
<p>Microservices and distributed application architectures have been proven to have many benefits over monolithic applications and traditional infrastructure. Some of these benefits include:</p>
<ul>
<li>Faster time to market for new features and upgrades</li>
<li>Independently scalable components and better resource utilization</li>
<li>Geographically distributed applications</li>
<li>Highly resilient applications for better customer experience</li>
<li>Flexibility in deployment - on-premises, cloud, and hybrid</li>
</ul>
<p>These benefits don’t come without costs though. There is inherent complexity introduced with microservices and distributed architectures. One of the challenges introduced is that what used to be in-memory communications with monolithic applications are now network communications between services. This presents a number of new concerns:</p>
<ul>
<li>Increased security blast radius</li>
<li>Service discovery and routing</li>
<li>Fault tolerance</li>
<li>Observability</li>
<li>Workload identification</li>
</ul>
<div class="flex flex-row justify-center gap-12">
<figure><img alt="Depiction of a simple service graph" src="/blog/optimal-mesh-experience-1/service-graph.png" width="400" /><figcaption>
      <p>Simple Service Graph</p>
    </figcaption>
</figure>

<figure><img alt="Depiction of a Netflix service graph" src="/blog/optimal-mesh-experience-1/netflix-graph.png" width="400" /><figcaption>
      <p>Netflix Service Graph</p>
    </figcaption>
</figure>

</div>
<p>A service mesh aims to address these challenges. A service mesh provides encryption, service discovery, observability, and complex routing to free up application development teams to focus on solving business problems. A service mesh moves these concerns to the infrastructure layer as a platform component, and abstracts the implementation from application teams.</p>
<p>In this post, we will look at challenges associated with introducing a service mesh and how to provide a best-in-class experience for developers and platform operators.</p>
<h1>The Optimal Service Mesh</h1><p>So what does an optimal service mesh look like? There are two different personas that need to be considered in the life of a service mesh:</p>
<ol>
<li>Platform Engineer - The persona responsible for providing a high-quality platform product to support application development and operations.</li>
<li>Application Developer - The persona that consumes platform services to enable high-velocity application development and frequent releases.</li>
</ol>
<p>Each of these personas have different needs.</p>
<h2>Platform Engineer<span class="hx:absolute hx:-mt-20" id="platform-engineer"></span>
    <a class="subheading-anchor" href="#platform-engineer"></a></h2><p>The platform engineer needs to provide a secure, performant, resilient, and scalable platform to their customers, the application developers. The platform needs to provide value to the developers and is ever-evolving. A service mesh should contribute to the following in the platform:</p>
<ul>
<li>Security</li>
<li>Observability</li>
<li>Resiliency</li>
<li>Scalability</li>
<li>Multi-tenancy</li>
<li>Policy, control, and auditability</li>
</ul>
<h2>Application Developer<span class="hx:absolute hx:-mt-20" id="application-developer"></span>
    <a class="subheading-anchor" href="#application-developer"></a></h2><p>The application developer is responsible for creating valuable software for the business. The developer is constantly working through a backlog of features to continually innovate the business (their customers). Application developers leverage the platform to reduce undifferentiated work that doesn’t contribute directly to business value. A service mesh should provide these features to the application developer:</p>
<ul>
<li>Advanced routing and traffic management</li>
<li>Simplified authentication and authorization</li>
<li>Self-service with low friction</li>
<li>Observability</li>
<li>Service registration and discovery</li>
<li>Simple controls for progressive delivery practices</li>
</ul>
<h1>Istio Ambient Mesh</h1><p>Istio is the most well known and widely used service mesh. Launched in 2017, Istio is now part of the Cloud Native Computing Foundation (CNCF) and broadly adopted by organizations of all sizes.</p>
<p>Istio operates with two layers: a data plane that controls all communication between services, and a control plane that configures and manages the data plane. Historically the data plane was implemented by injecting ‘sidecar’ proxies into each running workload. The pod’s <code>iptables</code> is modified to redirect all inbound and outbound traffic through the proxy.</p>
<figure><img alt="Depiction of the Istio sidecar architecture" src="/blog/optimal-mesh-experience-1/sidecar-arch.png" /><figcaption>
      <p>Istio Sidecar Architecture</p>
    </figcaption>
</figure>

<p>The sidecar architecture is a proven solution, but comes at a cost. Since every workload needs a proxy injected into it, it adds a lot of overhead in terms of infrastructure, operations, and complexity.</p>
<p>To address this a new Ambient mode was recently introduced. With Ambient mode, the proxy responsibility is split into two components: OSI Layer 4 functions such as encryption and peer authorization are handled by a node-level proxy called ztunnel, and OSI Layer 7 features like HTTP routing and header manipulation are performed by a proxy called a Waypoint. Waypoints are typically deployed per namespace.</p>
<figure><img alt="Depiction of Istio waypoint architecture" src="/blog/optimal-mesh-experience-1/waypoint-arch.png" /><figcaption>
      <p>Istio Ambient Mode</p>
    </figcaption>
</figure>

<p>Ambient mode drastically reduces the infrastructure and operations required for Istio. By splitting Layer 4 and Layer 7 functions, you can selectively deploy Waypoints as needed. And deploying ztunnel proxies per node instead of per workload significantly reduces the infrastructure required for the data plane.</p>
<h1>Ambient Mesh for Platform Engineers</h1><p>Ambient Mesh helps Platform Engineers meet their goals in many ways.</p>
<h2>Security and Policy Enforcement<span class="hx:absolute hx:-mt-20" id="security-and-policy-enforcement"></span>
    <a class="subheading-anchor" href="#security-and-policy-enforcement"></a></h2><p>Ambient Mesh automatically enables mutual TLS (mTLS) encryption between workloads, and mTLS includes the workload identity. mTLS prevents man-in-the-middle attacks and allows for fine-grained access control policies. Ambient Mesh can also enforce Layer 7 policies, such as OAuth flows and limiting HTTP methods. Ambient Mesh’s ztunnel and Waypoint proxy act as a Policy Enforcement Point for the service mesh.</p>
<h2>Observability<span class="hx:absolute hx:-mt-20" id="observability"></span>
    <a class="subheading-anchor" href="#observability"></a></h2><p>Since all traffic is controlled and monitored by Ambient Mesh, it has access to a rich set of telemetry data for your workloads. This telemetry data can then be ingested by observability tools. Distributed tracing tools can also give insight into issues in service graphs by integrating with Ambient Mesh.</p>
<h2>Multi-tenancy<span class="hx:absolute hx:-mt-20" id="multi-tenancy"></span>
    <a class="subheading-anchor" href="#multi-tenancy"></a></h2><p>Ambient Mesh provides a rich set of policy controls to manage and isolate multiple tenants within a service mesh. You can control access to resources with APIs such as authorization policies. Peer identities can be inspected, validated, and authorized. And for enterprise-grade multi-tenancy, <a href="https://www.solo.io/products/gloo-mesh" rel="noopener" target="_blank">Solo Enterprise for Istio</a> allows you to span multiple Kubernetes clusters to implement a cluster-per-tenant model.</p>
<h2>Resilience<span class="hx:absolute hx:-mt-20" id="resilience"></span>
    <a class="subheading-anchor" href="#resilience"></a></h2><p>Ambient Mesh enables workloads to become more resilient to network latencies and upstream service failures. Ambient Mesh provides features such as retries, timeouts, and circuit breaking to enable applications to gracefully handle upstream issues and network problems.</p>
<h1>Ambient Mesh for Application Developers</h1><p>Ambient Mesh also assists Application Developers in meeting their goals, by providing a number of features that would otherwise require developers to build by themselves, and do not directly contribute to the creation of business value.</p>
<h2>Traffic Management<span class="hx:absolute hx:-mt-20" id="traffic-management"></span>
    <a class="subheading-anchor" href="#traffic-management"></a></h2><p>Ambient Mesh has a large set of APIs to route, split, authorize, and modify traffic to services within the mesh. Developers can control routing and load balancing of traffic. They can do more advanced traffic management such as splitting traffic between versions of services or mirroring traffic to different services. HTTP header values can be inspected, validated, and modified with Ambient Mesh. And resilience features such as circuit breaking can be controlled by development teams.</p>
<h2>Service Registration and Discovery<span class="hx:absolute hx:-mt-20" id="service-registration-and-discovery"></span>
    <a class="subheading-anchor" href="#service-registration-and-discovery"></a></h2><p>Service registration and discovery can be a tedious task to implement and maintain for developers. Ambient Mesh automatically maintains a registry of services and endpoints for services in the mesh. Services outside of the service mesh can be managed using the Service Entry API, allowing developers to augment the service registry.</p>
<h2>Progressive Delivery<span class="hx:absolute hx:-mt-20" id="progressive-delivery"></span>
    <a class="subheading-anchor" href="#progressive-delivery"></a></h2><p>Progressive delivery is a practice of delivering software to subsets of users, incrementally delivering features in a controlled, low-risk manner. Techniques such as canary deployments, percentage-based rollouts, A/B testing, and feature flags are all common techniques that require the inspection and manipulation of routes and headers in an automated fashion. Ambient Mesh’s traffic management APIs can be easily integrated with popular tools such as <a href="https://argoproj.github.io/rollouts/" rel="noopener" target="_blank">Argo Rollouts</a> to drastically reduce the effort of adopting progressive delivery methods.</p>
<h2>Self-service<span class="hx:absolute hx:-mt-20" id="self-service"></span>
    <a class="subheading-anchor" href="#self-service"></a></h2><p>The <a href="https://gateway-api.sigs.k8s.io/" rel="noopener" target="_blank">Kubernetes Gateway API</a> was designed to be deeply role-oriented; organizational roles are expected to use and configure different resources that are in their domain. It is designed so that developers manage the routing logic for their applications, and associate these routes with gateways managed by platform teams.</p>
<figure><img alt="Depiction of the Kubernetes Gateway API model" src="/blog/optimal-mesh-experience-1/gw-api-model.png" width="550" /><figcaption>
      <p>Kubernetes Gateway API model</p>
    </figcaption>
</figure>

<p>Ambient Mesh is based on the Kubernetes Gateway API and its extensibility framework. Because of this, developers are able to manage traffic routing and manipulation in a declarative, self-service model, without relying on ticket based systems to implement routing logic.</p>
<h1>Conclusion</h1><p>As you can see, Ambient Mesh represents a monumental shift in how a service mesh can be adopted by organizations. When designed correctly Ambient Mesh brings a host of benefits to users:</p>
<ul>
<li>Drastically reduced operational burden for platform teams, compared to sidecar-based meshes.</li>
<li>Significant reduction in infrastructure requirements for a service mesh.</li>
<li>Secure-by-default model, providing mTLS, workload identity, and authorization policies out of the box.</li>
<li>Complex traffic management for application developers, with declarative, self-service configuration.</li>
</ul>
<p>We invite you to get started with Ambient Mesh today! Here are ways that you can get involved:</p>
<ol>
<li>Check out the <a href="https://ambientmesh.io/docs/" rel="noopener" target="_blank">documentation</a> for Ambient Mesh.</li>
<li>Follow one of our <a href="https://ambientmesh.io/labs/" rel="noopener" target="_blank">hands-on labs</a>.</li>
<li><a href="https://ambientmesh.io/docs/setup/installation/" rel="noopener" target="_blank">Install Ambient Mesh</a> in a test cluster.</li>
<li>Check out the enterprise features in <a href="https://www.solo.io/products/gloo-mesh" rel="noopener" target="_blank">Solo Enterprise for Istio</a>.</li>
</ol>
