---
title: "Reducing the costs and complexity of your cloud native architecture with Ambient Mesh"
url: "/blog/reducing-the-costs-and-complexity-of-your-cloud-native-architecture-with-ambient-mesh/"
date: "Sat, 25 Oct 2025 00:00:00 +0000"
author: ""
feed_url: "https://ambientmesh.io/blog/index.xml"
---
<p>Organizations have the need to connect, secure and observe the services running in their environments, whether they be on-premises, or running in the cloud, or hybrid environments.</p>
<p>More specifically, &ldquo;connect, secure, and observe&rdquo; means having:</p>
<ul>
<li>Control over how traffic is routed.</li>
<li>Secure, authenticated, and authorized network communications.</li>
<li>The ability to observe and monitor, and analyze traffic, and the behavior of a system.</li>
</ul>
<p>Furthermore, organizations need standard methods for implementing policy control, where policies can be reviewed, updated, and enforced. Second, they need the flexibility to modify policies at any time; policies cannot, for example, be coupled to the configuration of running applications. Finally, organizations require the ability for application teams to self-service, for example, the configuration of traffic rules, and of authorization rules pertaining to the services they maintain.</p>
<h2>Cloud native connectivity anti-patterns<span class="hx:absolute hx:-mt-20" id="cloud-native-connectivity-anti-patterns"></span>
    <a class="subheading-anchor" href="#cloud-native-connectivity-anti-patterns"></a></h2><p>In his six-part series titled <a href="https://youtu.be/O8kNL-C0UkA" rel="noopener" target="_blank"><em>Common patterns for solving cloud-native connectivity</em></a>, Solo.io CTO Louis Ryan walks through a list of ways in which organizations have dealt with these needs, by employing certain anti-patterns, from &ldquo;DIY&rdquo; solutions to L3 networking solutions, to a practice called &ldquo;hair-pinning.&rdquo;</p>
<p>To summarize briefly, the &ldquo;do it yourself&rdquo; anti-pattern involves application teams fending for themselves to connect, secure and observe their applications by means of introducing software libraries that provide traffic routing, telemetry, and secure communications capabilities. This approach has many problems, including language diversity, library consistency, library maintenance, and dynamism – the inability to effect a policy change in a timely fashion.</p>
<p>The Layer 3 networking solutions anti-pattern involves attempting to secure network communications through firewall rules and other layer 3 attributes that associate workloads to network identity (e.g. IP addresses). Problems include:</p>
<ul>
<li>IP churn – the fact that identity coupled to network identity doesn&rsquo;t work in environments where the assignment of IP addresses to workloads is dynamic,</li>
<li>Ticket ops – the need to submit tickets to the platform or infrastructure team to effect a policy change,</li>
<li>Being limited to Layer 3/4 networking (not Layer 7), and</li>
<li>Hybrid impedance mismatch – inconsistencies in terms of how rules are applied across environments (cloud vs on-prem, for example).</li>
</ul>
<p>The &ldquo;hair-pinning&rdquo; anti-pattern involves routing internal traffic through the API gateway as a means of obtaining policy enforcement. Problems with hair-pinning include:</p>
<ul>
<li>The gateway becomes a single point of failure,</li>
<li>Increased latency in calls between services having to be routed through a gateway,</li>
<li>Locality outage when services are unable to reach the API gateway in a different environment due to a network communication issue, and</li>
<li>Cost explosion due to the way that many API gateway solutions are priced.</li>
</ul>
<h2>Sidecar-based service mesh<span class="hx:absolute hx:-mt-20" id="sidecar-based-service-mesh"></span>
    <a class="subheading-anchor" href="#sidecar-based-service-mesh"></a></h2><p>Louis goes on to describe how service meshes have helped organizations achieve their cloud native connectivity objectives by introducing proxies as sidecars alongside workloads. But sidecar-based service meshes introduce problems of their own, including:</p>
<ul>
<li>Users have no choice but to deploy a proxy alongside every single workload, whether their layer 7 capabilities are required or not. Adopters almost always deploy more proxies than they need. This increases resource utilization and cost.</li>
<li>From a security standpoint, the layer 7 sidecars represent a larger attack surface.</li>
<li>Sidecars are part of the mesh infrastructure, and are coupled at deployment time to applications. In Kubernetes, every pod has a sidecar injected alongside the application workload. The implication is that upgrading or patching the sidecar disrupts the operation of the running application, for example.</li>
<li>The coupling of application workload and sidecar complicates operations. Patching a CVE in Envoy requires restarting your entire fleet of applications.</li>
<li>As the number of services in the mesh grows, the control plane has to do more and more work to keep all of the proxies&rsquo; configurations up to date.</li>
</ul>
<h2>Istio Ambient Mesh<span class="hx:absolute hx:-mt-20" id="istio-ambient-mesh"></span>
    <a class="subheading-anchor" href="#istio-ambient-mesh"></a></h2><p>Service meshes continue to evolve, and the Istio project today offers two modes: the older sidecar-based implementation, and a newer approach: ambient mode.</p>
<p>Istio&rsquo;s ambient mesh was developed as a response to the problems encountered with sidecar based installations. In ambient mode:</p>
<ul>
<li>Mesh infrastructure is separate from running applications.</li>
<li>We can enable mutual TLS &amp; zero-trust without deploying any Layer 7 proxies (no Envoys). Ambient mesh&rsquo;s ztunnel component acts as a per-node Layer 4 proxy that efficiently and securely routes traffic between services.</li>
<li>The Kubernetes Gateway API supports the ability to deploy waypoints on-demand where we need them, and scaled to the volume of traffic, not the number of pods being proxied.</li>
</ul>
<p>When using Istio in ambient mode, cost is consequently dramatically lower, and operations are dramatically simpler. In his blog <a href="https://www.solo.io/blog/how-ambient-mesh-delivers-advanced-resource-and-cost-savings" rel="noopener" target="_blank"><em>How Ambient Mesh Delivers Advanced Resource and Cost Savings</em></a>, Brian Jimerson does a deep dive into those cost savings by providing several cost comparisons between running a sidecar-based service mesh and Istio ambient mode.</p>
<h2>Summary<span class="hx:absolute hx:-mt-20" id="summary"></span>
    <a class="subheading-anchor" href="#summary"></a></h2><p>If today your organization is running Istio in sidecar mode, or if your organization is in a situation where it employs one of the above-referenced anti-patterns to implement cloud native connectivity, ambient promises to dramatically improve both the costs and complexity of your architecture.</p>
<p>To facilitate migration from Istio sidecar mode to ambient mode, Solo.io publishes a free migration tool, which can be downloaded from <a href="https://ambientmesh.io/#migrate-from-sidecars" rel="noopener" target="_blank">ambientmesh.io</a>. Louis Ryan &amp; James Ilse (principal solutions architect @ Solo.io) demonstrate this migration tool in a recently conducted webinar titled <a href="https://www.solo.io/resources/webinar/from-sidecars-to-ambient-a-practical-guide-to-migrating-your-service-mesh" rel="noopener" target="_blank"><em>From sidecars to Ambient: A practical guide to migrating your service mesh</em></a>. Separately, <a href="https://www.solo.io/resources/video/migrating-from-sidecars-to-ambient-mesh" rel="noopener" target="_blank"><em>How to migrate workloads from sidecar to ambient mesh with zero downtime</em></a> provides yet another demonstration of the use of the migration tool.</p>
