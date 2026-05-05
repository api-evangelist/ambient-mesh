---
title: "How Ambient Mesh Delivers Advanced Resource and Cost Savings"
url: "/blog/advanced-cost-savings/"
date: "Thu, 10 Jul 2025 00:00:00 +0000"
author: ""
feed_url: "https://ambientmesh.io/blog/index.xml"
---
<h2>Introduction<span class="hx:absolute hx:-mt-20" id="introduction"></span>
    <a class="subheading-anchor" href="#introduction"></a></h2><p>A service mesh is an infrastructure layer that controls and secures network traffic between distributed applications. With the explosion of microservices and cloud-native workloads, service meshes have become crucial to run these applications securely and efficiently. A service mesh can offload a lot of the complexities of distributed computing and allow developers to focus on creating valuable business outcomes. Some of these concerns include:</p>
<ul>
<li>Service discovery</li>
<li>End-to-end encryption</li>
<li>Workload identity</li>
<li>Load balancing and complex routing</li>
<li>Observability</li>
<li>Fault tolerance and resiliency</li>
</ul>
<p><a href="https://istio.io/" rel="noopener" target="_blank">Istio</a> has become the de facto standard in cloud-native service meshes. Introduced as an open-source project in 2017, Istio has garnered widespread adoption by both large enterprises and high-tech companies. It offloads the encryption, service discovery, observability, and other network functions from application teams, and is supported by a large and active community.</p>
<p>Istio uses a well-known and proven pattern to implement service mesh functionality, known as the sidecar pattern. The sidecar pattern is an effective approach to implementing service mesh, but also brings significant overhead and costs with it. To address these issues, the Istio community has released a new <a href="https://ambientmesh.io/" rel="noopener" target="_blank">Ambient</a>, or node proxy, solution.</p>
<p>In this post, we will introduce Istio&rsquo;s new ambient mode, and how it drastically reduces the cost and resources required to run Istio at scale.</p>
<h2>Sidecar Pattern vs. Node Proxy<span class="hx:absolute hx:-mt-20" id="sidecar-pattern-vs-node-proxy"></span>
    <a class="subheading-anchor" href="#sidecar-pattern-vs-node-proxy"></a></h2><p>First, let&rsquo;s take a look at the differences between the original Istio sidecar pattern and the new Ambient mode in Istio.</p>
<p>In a sidecar-based mesh, each workload that is deployed (typically a pod in Kubernetes or a virtual machine) has a proxy process deployed alongside it that intercepts all inbound and outbound traffic. This process, known as a sidecar, can encrypt and decrypt traffic, apply policies, and emit telemetry without the need to modify the original workload. In Istio, this sidecar is an Envoy proxy, and in Kubernetes, this Envoy proxy is automatically injected into a pod as a separate container.</p>
<p>A typical sidecar-based mesh would look something like this:</p>
<figure><img alt="Sidecar-based service mesh architecture" src="/blog/advanced-cost-savings/sidecar-mesh-architecture.png" /><figcaption>
      <p>Sidecar-based service mesh architecture</p>
    </figcaption>
</figure>

<p>The sidecar pattern has proven to be an effective approach to service meshes over the years, but it also comes with several downsides:</p>
<ul>
<li>Operating sidecars quickly becomes complex in a large-scale environment.</li>
<li>Availability of workloads becomes dependent on the sidecar. For example, an upgrade to Istio requires each pod to be restarted to get an upgraded Envoy proxy.</li>
<li>Support for workloads outside of Kubernetes and Linux virtual machines is limited. For example, joining serverless functions to a sidecar mesh is difficult and often requires bespoke solutions.</li>
<li>Envoy proxy was originally designed to be an OSI Layer 7 reverse proxy. But many service mesh users only need Layer 3/4 capabilities, such as encryption and network telemetry. The overhead of a full Layer 7 solution is overkill in these scenarios.</li>
<li>Most glaringly, there is a significant infrastructure &ldquo;tax&rdquo; for running sidecars in every pod or virtual machine.</li>
</ul>
<p>Istio&rsquo;s Ambient mode addresses these issues in two ways: it implements a proxy-per-node model, and it splits Layer 4 and Layer 7 concerns into separate processes. In Ambient, two components make up the service mesh&rsquo;s data plane:</p>
<ul>
<li><strong>ztunnel</strong>, which handles Layer 4 functions such as mTLS encryption, traffic redirection, and network telemetry, and</li>
<li><strong>Waypoint</strong>, which provides Layer 7 features such as HTTP traffic management and header manipulation.</li>
</ul>
<p>An Istio service mesh using Ambient mode looks like this:</p>
<figure><img alt="Ambient mesh architecture" src="/blog/advanced-cost-savings/ambient-mesh-architecture.png" /><figcaption>
      <p>Ambient mesh architecture with ztunnel and Waypoint components</p>
    </figcaption>
</figure>

<p>Istio&rsquo;s new Ambient architecture addresses many of the concerns with sidecar-based Istio:</p>
<ul>
<li>Operations become much simpler, as there is only a proxy per node, not per pod, and little to no configuration is required for ztunnel&rsquo;s Layer 4 functions.</li>
<li>Upgrades and patches do not require restarts of the workloads and are done in a zero-downtime fashion.</li>
<li>Ztunnel runs on many platforms, expanding the reach of a service mesh.</li>
<li>Splitting Layer 4 and Layer 7 into 2 processes allows users to selectively deploy Waypoints as needed but still get mTLS, authorization, and telemetry across workloads. Ztunnel is designed specifically for Layer 4 concerns.</li>
<li>The infrastructure required to run a proxy per node is drastically less than a proxy per pod, especially in large, high-density environments.</li>
</ul>
<p>As you can see, Istio&rsquo;s Ambient mode aims to address many of the shortcomings of sidecar mode, while still providing the security, control, and observability required of a service mesh.</p>
<h2>Savings with Ambient Mesh<span class="hx:absolute hx:-mt-20" id="savings-with-ambient-mesh"></span>
    <a class="subheading-anchor" href="#savings-with-ambient-mesh"></a></h2><h3>Infrastructure Savings<span class="hx:absolute hx:-mt-20" id="infrastructure-savings"></span>
    <a class="subheading-anchor" href="#infrastructure-savings"></a></h3><p>To give an idea of the kind of savings that can be realized for cloud compute costs by migrating to Ambient mode from sidecar-based Istio, let&rsquo;s look at the infrastructure costs associated with sidecars and with Ambient.</p>
<p>First, the average publicly available price for each vCPU in the major cloud providers&rsquo; node instance types is between $22 and $30 a month. For our calculations, let&rsquo;s be conservative and use $22 per month per vCPU in standard cloud instances.</p>
<p>Let&rsquo;s assume we have 3 Kubernetes clusters with 15,000 pods that we want to be part of a service mesh. Let&rsquo;s also assume that these clusters have a total of 200 nodes and 1000 namespaces.</p>
<p>The Istio community has regularly benchmarked Envoy proxy using 0.6 vCPU for 1000 requests per second, so let&rsquo;s use that figure. For a sidecar-based service mesh, we would use: <strong>9000 vCPUs</strong> (15,000 pod proxies x 0.6 vCPU) for proxies, which would cost us <strong>$2,376,000 per year</strong> ($22 x 12 months x 9000 vCPU) in cloud compute resources.</p>
<p>In contrast, Ambient Mesh&rsquo;s ztunnel typically uses 0.3 vCPU per node instance. If we deployed a Waypoint to each of our namespaces, our vCPU utilization would be <strong>60</strong> (200 nodes x 0.3 vCPU) for ztunnel and an additional <strong>600 vCPUs</strong> (1000 namespaces x 0.6 vCPU) for Waypoints. This means that an equivalent Ambient Mesh would cost us <strong>$174,240 per year</strong> ($22 x 660 vCPUs x 12 months), a savings of <strong>$2,201,760 per year!</strong> Even if we deployed Waypoints with 3 replicas for availability, our annual cost would be <strong>$491,040</strong>, for a savings of <strong>$1,884,960 per year</strong>.</p>
<h4>Cost Comparison of Envoy Sidecars and Ambient<span class="hx:absolute hx:-mt-20" id="cost-comparison-of-envoy-sidecars-and-ambient"></span>
    <a class="subheading-anchor" href="#cost-comparison-of-envoy-sidecars-and-ambient"></a></h4><table>
  <thead>
      <tr>
          <th>Mode</th>
          <th>Sidecars</th>
          <th>Ambient</th>
          <th>Ambient</th>
      </tr>
  </thead>
  <tbody>
      <tr>
          <td>Price / vCPU / month</td>
          <td>$22.00</td>
          <td>$22.00</td>
          <td>$22.00</td>
      </tr>
      <tr>
          <td>Kubernetes clusters</td>
          <td>3</td>
          <td>3</td>
          <td>3</td>
      </tr>
      <tr>
          <td>Nodes</td>
          <td>200</td>
          <td>200</td>
          <td>200</td>
      </tr>
      <tr>
          <td>Namespaces</td>
          <td>1000</td>
          <td>1000</td>
          <td>1000</td>
      </tr>
      <tr>
          <td>Pods</td>
          <td>15,000</td>
          <td>15,000</td>
          <td>15,000</td>
      </tr>
      <tr>
          <td>Waypoint replicas</td>
          <td>N/A</td>
          <td>1</td>
          <td>3</td>
      </tr>
      <tr>
          <td>Envoy vCPU</td>
          <td>0.6</td>
          <td>0.6</td>
          <td>0.6</td>
      </tr>
      <tr>
          <td>Ztunnel vCPU</td>
          <td>N/A</td>
          <td>0.3</td>
          <td>0.3</td>
      </tr>
      <tr>
          <td>vCPUs for Mesh</td>
          <td>9000</td>
          <td>660</td>
          <td>1860</td>
      </tr>
      <tr>
          <td>Cost / year</td>
          <td>$2,376,000.00</td>
          <td>$174,240.00</td>
          <td>$491,040.00</td>
      </tr>
      <tr>
          <td><strong>Annual Savings</strong></td>
          <td></td>
          <td><strong>$2,201,760.00</strong></td>
          <td><strong>$1,884,960.00</strong></td>
      </tr>
  </tbody>
</table>
<p>Using these calculations, real Istio users have created business cases to justify migrating to Ambient Mesh. The following table shows what some organizations have projected in savings.</p>
<h4>Real-World Savings Projections<span class="hx:absolute hx:-mt-20" id="real-world-savings-projections"></span>
    <a class="subheading-anchor" href="#real-world-savings-projections"></a></h4><table>
  <thead>
      <tr>
          <th>Industry</th>
          <th>Technology Services</th>
          <th>Financial Services</th>
          <th>Healthcare</th>
          <th>Federal Government</th>
      </tr>
  </thead>
  <tbody>
      <tr>
          <td>Clusters</td>
          <td>36</td>
          <td>71</td>
          <td>4</td>
          <td>3</td>
      </tr>
      <tr>
          <td>Nodes</td>
          <td>391</td>
          <td>432</td>
          <td>38</td>
          <td>816</td>
      </tr>
      <tr>
          <td>Mesh Pods</td>
          <td>21,000</td>
          <td>28,800</td>
          <td>3,855</td>
          <td>16,316</td>
      </tr>
      <tr>
          <td>Mesh vCPUs</td>
          <td>12,000</td>
          <td>17,280</td>
          <td>2,300</td>
          <td>9,800</td>
      </tr>
      <tr>
          <td><strong>Annual Savings w/ Ambient</strong></td>
          <td><strong>$2.0M-$2.8M</strong></td>
          <td><strong>$1.9M</strong></td>
          <td><strong>$400K</strong></td>
          <td><strong>$2.0M-$3.1M</strong></td>
      </tr>
  </tbody>
</table>
<p>As you can see, real users are excited about the savings in infrastructure costs that are possible by migrating to Ambient from sidecars.</p>
<h3>Other Areas of Savings<span class="hx:absolute hx:-mt-20" id="other-areas-of-savings"></span>
    <a class="subheading-anchor" href="#other-areas-of-savings"></a></h3><p>Users switching to Ambient Mesh from sidecars can realize savings across other areas in addition to infrastructure costs:</p>
<ul>
<li>
<p>The operational burden is drastically reduced. Monitoring and troubleshooting are greatly simplified with the reduction in proxies. This reduces operating costs and the time to recovery when there are issues.</p>
</li>
<li>
<p>Responsibilities of configuration can be split across parties. Platform operators can implement encryption, zero-trust configuration, and observability with ztunnel. Developers can implement their specific routing logic with a Waypoint, without the need to touch ztunnel. This reduces the time to resolution for application teams.</p>
</li>
<li>
<p>For users who primarily use service mesh for network encryption, observability, and basic authorization, ztunnel can be used as a replacement for the sidecar proxy, without the need for corresponding Waypoint proxies. Ztunnel is lightweight and purpose-built in Rust for Layer 4 processing.</p>
</li>
<li>
<p>Ztunnel allows many types of platforms to be joined to a service mesh. For example, ztunnel can be integrated with serverless functions, tightly managed container platforms, and non-Linux virtual machines. This greatly reduces the need for bespoke solutions for these platforms.</p>
</li>
</ul>
<p>These savings also apply to companies that avoided implementing Istio in sidecar mode. This simplified, lighter-weight model drastically reduces the friction to adopt Istio.</p>
<h2>Conclusion<span class="hx:absolute hx:-mt-20" id="conclusion"></span>
    <a class="subheading-anchor" href="#conclusion"></a></h2><p>As you can see, Istio&rsquo;s ambient mode greatly improves upon the original sidecar architecture. One of the areas of improvement is the overall cost of using sidecars at scale. Switching to ambient mode can help organizations of all sizes drastically reduce their service mesh footprint and improve overall user experience and customer satisfaction.</p>
<p>Ambient also reduces the barrier to entry for new service mesh users. Many companies have held off on implementing an Istio-based service mesh because of the cost and complexity of sidecar-based solutions. With Istio ambient mode, many of these concerns are lowered or eliminated.</p>
<p>Istio has long been regarded as the most reliable and complete service mesh solution. With the advent of Ambient Mesh, the Istio community continues to innovate and set itself apart from other solutions, while improving the user experience.</p>
<p>To get an idea of how much you could save with Ambient Mesh, try the new <a href="https://ambientmesh.io/cost-savings-estimator/" rel="noopener" target="_blank">Cost Savings Estimator</a>.</p>
