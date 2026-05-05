---
title: "Getting Started with Ambient Mesh: From 0 to 100 mph"
url: "/blog/getting-started-with-ambient/"
date: "Wed, 09 Jul 2025 00:00:00 +0000"
author: ""
feed_url: "https://ambientmesh.io/blog/index.xml"
---
<p>In the dynamic world of cloud-native applications, managing the intricate dance between microservices can quickly become a complex challenge. Think about securing communication, understanding how services interact, and even routing traffic intelligently - these are all critical concerns that, if not handled efficiently, can bog down development and operations teams. This is where service meshes step in, providing a dedicated infrastructure layer to offload these complexities from your application code.</p>
<p>For a long time, Istio has been the dominant force in the service mesh arena, primarily known for its &ldquo;sidecar&rdquo; proxy model. While powerful, this approach introduced some trade-offs. Now, a new evolution of Istio, known as <a href="https://ambientmesh.io/" rel="noopener" target="_blank">Ambient Mesh</a> (or Istio&rsquo;s ambient mode), is shaking things up. It promises to simplify operations, enhance performance, and offer a more flexible path to adopting service mesh capabilities.</p>
<p>This blog post will take you on a high-level journey, explaining what Ambient Mesh is, why it&rsquo;s a game-changer, and how you can get started with it.</p>
<h2>The Sidecar Story: Why We Need a New Approach<span class="hx:absolute hx:-mt-20" id="the-sidecar-story-why-we-need-a-new-approach"></span>
    <a class="subheading-anchor" href="#the-sidecar-story-why-we-need-a-new-approach"></a></h2><p>To truly appreciate Ambient Mesh, let&rsquo;s briefly touch upon the traditional sidecar model. In this setup, a small, specialized proxy (often Envoy) is deployed right alongside each of your applications&rsquo; containers within a Kubernetes pod. This &ldquo;sidecar&rdquo; intercepts all network traffic entering and leaving your application, enabling features like:</p>
<ul>
<li><strong>Mutual TLS (mTLS):</strong> Encrypting and authenticating all communication between services for robust security.</li>
<li><strong>Traffic Management:</strong> Routing and load-balancing requests, performing A/B testing, canary deployments, and retries.</li>
<li><strong>Observability:</strong> Collecting metrics, traces, and logs to understand service behavior and pinpoint issues.</li>
</ul>
<p>While incredibly effective, the sidecar model isn&rsquo;t without its challenges:</p>
<ul>
<li><strong>Resource Consumption:</strong> Every single pod gets a sidecar, which means a significant increase in overall CPU and memory usage across your cluster, especially at scale.</li>
<li><strong>Operational Overhead:</strong> Managing the injection, upgrades, and lifecycle of these sidecars can add complexity. Often, changes to the service mesh require restarting application pods.</li>
<li><strong>Increased Attack Surface:</strong> Each sidecar introduces another component that needs to be secured and patched.</li>
<li><strong>Application-Level Impact:</strong> Though designed to be transparent, sidecars can sometimes subtly affect application behavior or require specific configurations.</li>
</ul>
<p>Ambient Mesh was conceived to address these very pain points, offering a fundamentally different way to achieve the same powerful service mesh capabilities.</p>
<h2>What is Ambient Mesh? A Glimpse into Its Architecture<span class="hx:absolute hx:-mt-20" id="what-is-ambient-mesh-a-glimpse-into-its-architecture"></span>
    <a class="subheading-anchor" href="#what-is-ambient-mesh-a-glimpse-into-its-architecture"></a></h2><p>Ambient Mesh introduces a &ldquo;split proxy&rdquo; architecture, moving away from the per-pod sidecar to a more distributed, infrastructure-centric approach. It achieves this with two primary components:</p>
<h3>1. ztunnel (Zero Trust Tunnel)<span class="hx:absolute hx:-mt-20" id="1-ztunnel-zero-trust-tunnel"></span>
    <a class="subheading-anchor" href="#1-ztunnel-zero-trust-tunnel"></a></h3><p>Imagine a lightweight, lightning-fast proxy running <em>per node</em> in your Kubernetes cluster. Ztunnel is deployed as a DaemonSet, meaning one instance runs on each node. Its primary job is to handle all <strong>Layer 4 (TCP)</strong> traffic. This includes:</p>
<ul>
<li><strong>Automatic mTLS:</strong> All communication between pods within the Ambient Mesh is automatically encrypted and authenticated using Istio&rsquo;s secure tunneling protocol (HBONE), establishing a strong zero-trust foundation without any application changes.</li>
<li><strong>Workload Identity:</strong> It handles the secure identification of workloads for mTLS.</li>
<li><strong>Layer 4 Security Policies:</strong> Security policies based on infrastructure-specific attributes such as peer identity, namespace, and networking parameters.</li>
<li><strong>Basic Observability:</strong> It collects fundamental Layer 4 metrics, giving you insights into TCP connections.</li>
<li><strong>Resource Efficiency:</strong> By consolidating Layer 4 proxying at the node level, ztunnels significantly reduce resource overhead compared to injecting a full proxy into every single pod.</li>
</ul>
<h3>2. Waypoint Proxies<span class="hx:absolute hx:-mt-20" id="2-waypoint-proxies"></span>
    <a class="subheading-anchor" href="#2-waypoint-proxies"></a></h3><p>These are specialized, full-featured Envoy proxies that are <em>optional</em> and only deployed when you need <strong>Layer 7 (HTTP/gRPC)</strong> capabilities. Waypoint proxies provide the rich Layer 7 features Istio is known for:</p>
<ul>
<li><strong>Advanced Traffic Management:</strong> Sophisticated routing rules, traffic splitting, A/B testing, retries, and circuit breaking.</li>
<li><strong>Deep Observability:</strong> Detailed request-level metrics, distributed tracing, and access logs for HTTP and gRPC traffic.</li>
<li><strong>Fine-Grained Policy Enforcement:</strong> Authorization policies based on HTTP headers, paths, and other application-specific attributes.</li>
</ul>
<figure class="center"><img alt="Ambient Mesh Architecture" src="/blog/getting-started-with-ambient/getting-started-ambient.png" width="80%" /><figcaption>
      <p>Ambient Mesh Architecture</p>
    </figcaption>
</figure>

<p><strong>The Brilliance of the Split:</strong> The true elegance of Ambient Mesh lies in this separation of concerns. By default, every service in a mesh-enabled namespace gets Layer 4 mTLS and basic observability via ztunnel &ndash; no sidecars, no application restarts, no fuss. You only introduce a waypoint proxy for services that explicitly require those advanced Layer 7 traffic management and policy features. This allows for a much more incremental and resource-efficient adoption of the service mesh.</p>
<h2>The &ldquo;Why&rdquo;: Core Benefits of Embracing Ambient<span class="hx:absolute hx:-mt-20" id="the-why-core-benefits-of-embracing-ambient"></span>
    <a class="subheading-anchor" href="#the-why-core-benefits-of-embracing-ambient"></a></h2><p>Switching to Ambient Mesh offers several compelling advantages for organizations running Kubernetes workloads:</p>
<ul>
<li>
<p><strong>Operational Simplicity:</strong> Forget about the complexities of sidecar injection, pod restarts for upgrades, and managing a proxy in every single pod. With Ambient Mesh, you simply label a namespace, and your workloads are automatically part of the mesh.</p>
</li>
<li>
<p><strong>Significant Cost Savings:</strong> By replacing resource-intensive per-pod sidecars with lightweight, node-level ztunnels, Ambient Mesh drastically reduces the CPU and memory footprint of your service mesh infrastructure. This translates directly into <a href="https://www.ambientmesh.io/blog/advanced-cost-savings/" rel="noopener" target="_blank">lower infrastructure bills</a>.</p>
</li>
<li>
<p><strong>Improved Performance:</strong> With a streamlined data plane, particularly for Layer 4 traffic, Ambient Mesh can offer better network performance and reduced latency, especially for workloads that don&rsquo;t need constant Layer 7 processing.</p>
</li>
<li>
<p><strong>Incremental Adoption:</strong> You don&rsquo;t have to go all-in at once. Start with the foundational Layer 4 security and observability, and then layer on Layer 7 features with waypoint proxies only for the services that genuinely need them. This &ldquo;pay-as-you-go&rdquo; model makes service mesh adoption less daunting.</p>
</li>
<li>
<p><strong>Stronger Security by Default:</strong> Zero-trust security, with automatic mTLS encryption and authentication for all Layer 4 traffic, is baked in from the start, providing a robust security posture without extra effort.</p>
</li>
<li>
<p><strong>Better Developer Experience:</strong> Developers can focus on building their applications, knowing that the underlying network security and basic connectivity are handled by the infrastructure, without requiring specific code changes or complex proxy configurations.</p>
</li>
</ul>
<h2>From 0 to 100 mph: A High-Level Installation and Onboarding Guide<span class="hx:absolute hx:-mt-20" id="from-0-to-100-mph-a-high-level-installation-and-onboarding-guide"></span>
    <a class="subheading-anchor" href="#from-0-to-100-mph-a-high-level-installation-and-onboarding-guide"></a></h2><p>Getting Ambient Mesh up and running is surprisingly straightforward. While the detailed steps involve a few commands, the conceptual flow is simple. For a hands-on experience, try this free <a href="https://www.solo.io/resources/lab/getting-started-with-ambient-mesh" rel="noopener" target="_blank">Getting Started with Ambient Mesh</a> lab.</p>
<h3>Phase 1: Setting up the Ambient Infrastructure<span class="hx:absolute hx:-mt-20" id="phase-1-setting-up-the-ambient-infrastructure"></span>
    <a class="subheading-anchor" href="#phase-1-setting-up-the-ambient-infrastructure"></a></h3><ol>
<li>
<p><strong>Install Gateway API:</strong> Ambient Mesh leverages the Kubernetes Gateway API for defining network routing, so ensuring it&rsquo;s present in your cluster is the first step.</p>
</li>
<li>
<p><strong>Install Istio Components:</strong> This involves installing the core Istio components, including:</p>
<ul>
<li><strong>Istio Base:</strong> Fundamental Custom Resource Definitions (CRDs) and roles.</li>
<li><strong>Istio CNI:</strong> The component that redirects traffic to the ztunnels on each node.</li>
<li><strong>ztunnel:</strong> This deploys the actual ztunnel proxies as a DaemonSet across your nodes.</li>
<li><strong>Istiod (Control Plane):</strong> The brain of Istio, responsible for managing configurations and policies for both ztunnels and waypoint proxies.</li>
</ul>
</li>
</ol>
<p>Once these components are successfully deployed and running in your istio-system namespace, your cluster is &ldquo;ambient-ready.&rdquo;</p>
<h3>Phase 2: Onboarding Your Applications<span class="hx:absolute hx:-mt-20" id="phase-2-onboarding-your-applications"></span>
    <a class="subheading-anchor" href="#phase-2-onboarding-your-applications"></a></h3><p>This is where the magic of simplicity truly shines.</p>
<ol>
<li><strong>Label Your Namespace:</strong> To bring a set of applications into the Ambient Mesh, you simply apply a label to their Kubernetes namespace. For instance:</li>
</ol>
<div class="hextra-code-block hx:relative hx:mt-6 hx:first:mt-0 hx:group/code">

<div><div class="highlight"><pre class="chroma" tabindex="0"><code class="language-bash"><span class="line"><span class="cl">kubectl label namespace my-app-namespace istio.io/dataplane-mode<span class="o">=</span>ambient</span></span></code></pre></div></div><div class="hextra-code-copy-btn-container hx:opacity-0 hx:transition hx:group-hover/code:opacity-100 hx:flex hx:gap-1 hx:absolute hx:m-[11px] hx:right-0 hx:top-0">
  <button class="hextra-code-copy-btn hx:group/copybtn hx:cursor-pointer hx:transition-all hx:active:opacity-50 hx:bg-primary-700/5 hx:border hx:border-black/5 hx:text-gray-600 hx:hover:text-gray-900 hx:rounded-md hx:p-1.5 hx:dark:bg-primary-300/10 hx:dark:border-white/10 hx:dark:text-gray-400 hx:dark:hover:text-gray-50" title="Copy code">
    <div class="hextra-copy-icon hx:group-[.copied]/copybtn:hidden hx:pointer-events-none hx:h-4 hx:w-4"></div>
<div class="hextra-success-icon hx:hidden hx:group-[.copied]/copybtn:block hx:pointer-events-none hx:h-4 hx:w-4"></div>
  </button>
</div>
</div>
<p>This single command tells Istio to configure the ztunnels on the nodes hosting pods in this namespace to automatically handle Layer 4 traffic for those pods. Your application pods <em>do not</em> need to be restarted, and no sidecars are injected. They instantly gain mTLS and basic Layer 4 observability.</p>
<ol start="2">
<li><strong>Deploy Waypoint Proxies (As Needed):</strong> If you have a specific service (<em>my-service</em>) within my-app-namespace that requires advanced Layer 7 capabilities (like fine-grained HTTP routing, A/B testing, or authorization policies), you then deploy a dedicated Waypoint Proxy for it. This is typically done by defining a Gateway resource from the Gateway API and then labeling the service to use that waypoint.</li>
</ol>
<div class="hextra-code-block hx:relative hx:mt-6 hx:first:mt-0 hx:group/code">

<div><div class="highlight"><pre class="chroma" tabindex="0"><code class="language-yaml"><span class="line"><span class="cl"><span class="nt">apiVersion</span><span class="p">:</span><span class="w"> </span><span class="l">gateway.networking.k8s.io/v1beta1</span><span class="w">
</span></span></span><span class="line"><span class="cl"><span class="nt">kind</span><span class="p">:</span><span class="w"> </span><span class="l">Gateway</span><span class="w">
</span></span></span><span class="line"><span class="cl"><span class="nt">metadata</span><span class="p">:</span><span class="w">
</span></span></span><span class="line"><span class="cl"><span class="w">  </span><span class="nt">name</span><span class="p">:</span><span class="w"> </span><span class="l">my-service-waypoint</span><span class="w">
</span></span></span><span class="line"><span class="cl"><span class="w">  </span><span class="nt">namespace</span><span class="p">:</span><span class="w"> </span><span class="l">my-app-namespace</span><span class="w">
</span></span></span><span class="line"><span class="cl"><span class="nt">spec</span><span class="p">:</span><span class="w">
</span></span></span><span class="line"><span class="cl"><span class="w">  </span><span class="nt">gatewayClassName</span><span class="p">:</span><span class="w"> </span><span class="l">istio-waypoint</span><span class="w">
</span></span></span><span class="line"><span class="cl"><span class="w">  </span><span class="nt">listeners</span><span class="p">:</span><span class="w">
</span></span></span><span class="line"><span class="cl"><span class="w">  </span>- <span class="nt">name</span><span class="p">:</span><span class="w"> </span><span class="l">mesh</span><span class="w">
</span></span></span><span class="line"><span class="cl"><span class="w">    </span><span class="nt">port</span><span class="p">:</span><span class="w"> </span><span class="m">15008</span><span class="w">
</span></span></span><span class="line"><span class="cl"><span class="w">    </span><span class="nt">protocol</span><span class="p">:</span><span class="w"> </span><span class="l">HBONE</span></span></span></code></pre></div></div><div class="hextra-code-copy-btn-container hx:opacity-0 hx:transition hx:group-hover/code:opacity-100 hx:flex hx:gap-1 hx:absolute hx:m-[11px] hx:right-0 hx:top-0">
  <button class="hextra-code-copy-btn hx:group/copybtn hx:cursor-pointer hx:transition-all hx:active:opacity-50 hx:bg-primary-700/5 hx:border hx:border-black/5 hx:text-gray-600 hx:hover:text-gray-900 hx:rounded-md hx:p-1.5 hx:dark:bg-primary-300/10 hx:dark:border-white/10 hx:dark:text-gray-400 hx:dark:hover:text-gray-50" title="Copy code">
    <div class="hextra-copy-icon hx:group-[.copied]/copybtn:hidden hx:pointer-events-none hx:h-4 hx:w-4"></div>
<div class="hextra-success-icon hx:hidden hx:group-[.copied]/copybtn:block hx:pointer-events-none hx:h-4 hx:w-4"></div>
  </button>
</div>
</div>
<div class="hextra-code-block hx:relative hx:mt-6 hx:first:mt-0 hx:group/code">

<div><div class="highlight"><pre class="chroma" tabindex="0"><code class="language-bash"><span class="line"><span class="cl">kubectl label service my-service -n my-app-namespace istio.io/use-waypoint<span class="o">=</span>my-service-waypoint</span></span></code></pre></div></div><div class="hextra-code-copy-btn-container hx:opacity-0 hx:transition hx:group-hover/code:opacity-100 hx:flex hx:gap-1 hx:absolute hx:m-[11px] hx:right-0 hx:top-0">
  <button class="hextra-code-copy-btn hx:group/copybtn hx:cursor-pointer hx:transition-all hx:active:opacity-50 hx:bg-primary-700/5 hx:border hx:border-black/5 hx:text-gray-600 hx:hover:text-gray-900 hx:rounded-md hx:p-1.5 hx:dark:bg-primary-300/10 hx:dark:border-white/10 hx:dark:text-gray-400 hx:dark:hover:text-gray-50" title="Copy code">
    <div class="hextra-copy-icon hx:group-[.copied]/copybtn:hidden hx:pointer-events-none hx:h-4 hx:w-4"></div>
<div class="hextra-success-icon hx:hidden hx:group-[.copied]/copybtn:block hx:pointer-events-none hx:h-4 hx:w-4"></div>
  </button>
</div>
</div>
<p>Istioctl, the command-line tool for Istio, makes this even easier by providing a powerful set of commands for creating and interacting with waypoints.</p>
<p>Once the waypoint is up, you can apply Istio&rsquo;s powerful Layer 7 traffic management and policy resources (like HTTPRoute and AuthorizationPolicy) and bind them to this waypoint.</p>
<h2>Observability in the Ambient World<span class="hx:absolute hx:-mt-20" id="observability-in-the-ambient-world"></span>
    <a class="subheading-anchor" href="#observability-in-the-ambient-world"></a></h2><p>Ambient Mesh maintains Istio&rsquo;s strong commitment to observability:</p>
<ul>
<li>
<p><strong>Layer 4 Observability (via ztunnel):</strong> Even without waypoints, you get basic TCP-level metrics from the ztunnels, giving you insights into connection counts and byte transfers. This provides a baseline understanding of network activity.</p>
</li>
<li>
<p><strong>Layer 7 Observability (via waypoint proxies):</strong> When waypoints are deployed, they expose the full suite of Layer 7 metrics (request counts, latencies, error rates), distributed traces (to follow a request through multiple services), and detailed access logs. Tools like Prometheus, Grafana, and Kiali integrate seamlessly to visualize this data, allowing you to quickly diagnose issues and understand application behavior.</p>
</li>
</ul>
<h2>High-Level Considerations for Production Readiness<span class="hx:absolute hx:-mt-20" id="high-level-considerations-for-production-readiness"></span>
    <a class="subheading-anchor" href="#high-level-considerations-for-production-readiness"></a></h2><p>While Ambient Mesh simplifies many aspects, moving to production always requires careful planning:</p>
<ul>
<li>
<p><strong>Resource Monitoring:</strong> Keep an eye on the resource consumption of istiod, ztunnel, and waypoint proxies. While more efficient, they still consume resources that need to be scaled appropriately for your workload.</p>
</li>
<li>
<p><strong>Security Policies:</strong> Ambient Mesh empowers you to implement granular Layer 4 and Layer 7 security policies. Understand how to apply these effectively for your specific security posture.</p>
</li>
<li>
<p><strong>Integration with Existing Ecosystem:</strong> Consider how Ambient Mesh fits into your existing CI/CD pipelines, monitoring tools, and security practices.</p>
</li>
<li>
<p><strong>Migration Strategy:</strong> If you&rsquo;re coming from a sidecar-based Istio deployment, plan your migration carefully. Istio provides tools and guidance to facilitate a smooth transition. Check out <a href="https://www.solo.io/resources/white-paper/migrating-from-sidecars-to-sidecarless-istio-ambient-mesh" rel="noopener" target="_blank">this whitepaper</a> from Solo that offers a deeper dive into migration considerations.</p>
</li>
</ul>
<h2>The Future of Service Mesh is Now<span class="hx:absolute hx:-mt-20" id="the-future-of-service-mesh-is-now"></span>
    <a class="subheading-anchor" href="#the-future-of-service-mesh-is-now"></a></h2><p>Ambient Mesh represents a significant evolution in the service mesh landscape. By separating Layer 4 and Layer 7 concerns and eliminating the need for pervasive sidecar injection, it offers a more resource-efficient, operationally simpler, and incrementally adoptable path to leveraging the power of Istio.</p>
<p>For organizations seeking to enhance security, streamline traffic management, and gain deep observability into their microservices without the overhead of traditional sidecars, Ambient Mesh is a compelling solution. It allows you to &ldquo;pay for what you need,&rdquo; starting with a secure and observable network foundation and then adding advanced capabilities as your application requirements evolve. Solo.io has been at the forefront of this innovation, contributing significantly to Ambient Mesh and offering enterprise-grade support and solutions through <a href="https://www.solo.io/products/gloo-mesh/" rel="noopener" target="_blank">Solo Enterprise for Istio</a>.</p>
<p>Getting started with Ambient Mesh is less about a massive overhaul and more about a strategic, high-impact shift towards a more efficient and elegant service mesh architecture. It&rsquo;s about accelerating from 0 to 100 mph with newfound agility and control over your cloud-native environment.</p>
<p>Embrace the ambient revolution - your applications (and your operations team) will thank you for it!</p>
