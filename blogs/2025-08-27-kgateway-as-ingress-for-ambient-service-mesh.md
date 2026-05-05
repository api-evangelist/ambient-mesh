---
title: "kgateway as Ingress for Ambient Service Mesh"
url: "/blog/kgateway-as-ingress-for-ambient-service-mesh/"
date: "Wed, 27 Aug 2025 00:00:00 +0000"
author: ""
feed_url: "https://ambientmesh.io/blog/index.xml"
---
<p>The Kubernetes ecosystem continues its rapid evolution, introducing innovations that fundamentally alter how applications are deployed and managed. Among the most significant advancements is Istio&rsquo;s <a href="https://www.solo.io/blog/getting-started-with-ambient-mesh-from-0-to-100-mph" rel="noopener" target="_blank">Ambient Mesh</a>, a transformative approach to service mesh. Ambient Mesh promises the full power of a service mesh, but notably, without the architectural complexities of sidecar proxies. However, integrating external traffic into this streamlined mesh, particularly at Layer 7, demands a robust and intelligent ingress solution capable of seamlessly bridging the traditional network edge with the mesh&rsquo;s inherent security and advanced capabilities.</p>
<p>This post will detail how <a href="https://kgateway.dev/" rel="noopener" target="_blank">kgateway</a>, an open-source gateway solution aligned with the Kubernetes Gateway API, can serve as the ideal ingress for Ambient Service Mesh deployments. This combination delivers efficient traffic management and the powerful security features of Istio&rsquo;s latest architecture.</p>
<h2>But Wait! Doesn’t Istio Have a Built-in Ingress Gateway?<span class="hx:absolute hx:-mt-20" id="but-wait-doesnt-istio-have-a-built-in-ingress-gateway"></span>
    <a class="subheading-anchor" href="#but-wait-doesnt-istio-have-a-built-in-ingress-gateway"></a></h2><p>It does! And for many use cases, the default <a href="https://istio.io/latest/docs/tasks/traffic-management/ingress/ingress-control/" rel="noopener" target="_blank">Istio Ingress Gateway</a> is sufficient. In many ways, the Istio Ingress Gateway and kgateway are similar. Both are built on Envoy, a lightweight, highly performant distributed proxy designed for services and applications. Both can leverage the universal Kubernetes Gateway API, providing a consistent API interface for deploying and managing gateway deployments. The goal of kgateway is to build on the robust features of the Istio Ingress Gateway and provide a rich set of API management features designed for the full API lifecycle.</p>
<p>The standard Ingress Gateway provides powerful features such as:</p>
<ul>
<li><strong>Traffic Routing</strong> : Directing traffic to different services based on headers, URI paths, and methods</li>
<li><strong>Load Balancing</strong> : Distributing traffic across service instances using various algorithms</li>
<li><strong>Security</strong> : Providing features like TLS termination and identity-based authentication</li>
<li><strong>Observability</strong> : Generating detailed metrics, logs, and traces for monitoring and troubleshooting</li>
<li><strong>Resiliency</strong> : Implementing advanced resiliency patterns such as circuit breaking, timeouts, and retries</li>
<li><strong>Deployment Strategies</strong> : Enabling modern deployment strategies like canary releases and weighted traffic splitting.</li>
</ul>
<p>Kgateway offers all the features of Istio Ingress Gateway, plus additional capabilities tailored for modern application architectures.</p>
<ul>
<li><strong>Advanced Security Capabilities</strong> : Enabling advanced security tools to help protect your APIs, such as External auth to integrate with your existing authorization services, built-in rate limiting, and native support for CORS and CSRF.</li>
<li><strong>Data Transformation</strong> : Modify requests and responses on the fly, for example, by converting between different data formats like XML and JSON. This is crucial for integrating disparate services without requiring backend applications to handle data format conversions.</li>
<li><strong>Extended Traffic Control</strong> : Kgateway goes beyond basic load balancing with sophisticated traffic management capabilities like <strong>canary releases</strong> , <strong>A/B testing</strong> , and <strong>circuit breaking</strong>. While some of these are available in Istio, Kgateway’s implementation is more streamlined and geared towards a pure API gateway use case.</li>
<li><strong>Route Delegation</strong> : Large, complex routing configurations can be broken down into smaller, more manageable units. Ownership of these simplified configurations can then be delegated to the respective application or domain teams.</li>
<li><strong>External Processing Servers</strong> : Extend kgateway by developing and integrating an external gRPC processing server capable of reading and modifying all aspects of an HTTP request or response.</li>
</ul>
<p>…and more! For robust and complex ingress needs,<a href="https://www.solo.io/products/gloo-gateway" rel="noopener" target="_blank"> Gloo Gateway</a> extends the open-source <strong>kgateway</strong> project to provide a comprehensive enterprise experience.</p>
<h2>Ingress within Ambient Mesh<span class="hx:absolute hx:-mt-20" id="ingress-within-ambient-mesh"></span>
    <a class="subheading-anchor" href="#ingress-within-ambient-mesh"></a></h2><p>Even with the inherent elegance and efficiency of Ambient Mesh, the fundamental problem of ingress persists. External traffic must gain entry into the mesh, be routed accurately to its intended destination, and rigorously adhere to defined security and traffic management policies. Traditional ingress controllers primarily focus on routing plaintext or TLS-terminated HTTP traffic directly to Kubernetes Services. However, an Ambient Mesh workload, secured at Layer 4 by ztunnel, fundamentally expects mTLS traffic encapsulated within HBONE (HTTP-Based Overlay Network Encapsulation). For a deeper dive on the fundamentals of Ambient Mesh and a quick primer to get you started, check <a href="https://www.solo.io/blog/getting-started-with-ambient-mesh-from-0-to-100-mph" rel="noopener" target="_blank">this</a> blog post out.</p>
<p>An effective L7 ingress for Ambient Mesh must therefore perform several critical functions:</p>
<ol>
<li>Terminate External TLS: It must securely terminate the TLS connections initiated by external clients.</li>
<li>Apply Initial L7 Routing and Policies: It should be capable of applying initial Layer 7 routing rules (e.g., path-based, host-based) and preliminary policies (e.g., rate limiting, basic authentication).</li>
<li>mTLS Connection to Mesh: Communication must be mTLS from the gateway into the service mesh, specifically targeting the ztunnel on the node hosting the destination workload. This ensures secure communication from the very edge of the mesh all the way to the workload.</li>
<li>Ensure Mesh Policy Adherence: It must ensure that L7 policies defined deeper within the mesh are respected and, where necessary, enhanced or chained from the ingress point.</li>
</ol>
<p>It is precisely here that kgateway, when integrated with Ambient Mesh, truly shines.</p>
<h2>Getting Started<span class="hx:absolute hx:-mt-20" id="getting-started"></span>
    <a class="subheading-anchor" href="#getting-started"></a></h2><p>Integrating kgateway with Ambient Mesh is a straightforward process. For a guided, hands-on experience, a free lab, <a href="https://www.solo.io/resources/lab/kgateway-lab-integrating-kgateway-with-istio-at-ingress" rel="noopener" target="_blank">Integrating kgateway with Istio at Ingress</a>, is available. Additionally, <a href="https://www.youtube.com/watch?v=4G_zNX2nuZ0" rel="noopener" target="_blank">this</a> video provides a complete walkthrough of the integration.</p>
<p>1. Prerequisites:</p>
<ul>
<li>A Kubernetes cluster.</li>
<li><a href="https://istio.io/latest/docs/ambient/getting-started/#download-the-istio-cli" rel="noopener" target="_blank">Istioctl</a>: The command-line utility for Istio</li>
</ul>
<p>2. Deploy Istio Ambient. This will deploy Istio in ambient mode.</p>
<pre><code>istioctl install --set profile=ambient
</code></pre>
<p>‍</p>
<p>3. Deploy the Kubernetes Gateway API CRDs. This is a prerequisite for kgateway.</p>
<pre><code>kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.3.0/standard-install.yaml
</code></pre>
<p>‍</p>
<p>4. <a href="https://kgateway.dev/docs/quickstart/" rel="noopener" target="_blank">Deploy kgateway</a>: Install kgateway into your cluster. This process will deploy the kgateway CRDs, create a GatewayClass resource, and deploy the necessary controller and associated pods.</p>
<pre><code>helm upgrade -i --create-namespace --namespace kgateway-system --version v2.0.4 kgateway-crds oci://cr.kgateway.dev/kgateway-dev/charts/kgateway-crds

helm upgrade -i --namespace kgateway-system --version v2.0.4 kgateway oci://cr.kgateway.dev/kgateway-dev/charts/kgateway
</code></pre>
<p>After creating the CRDs and deploying kgateway, ensure the kgateway pod has been created and is running:</p>
<pre><code>kubectl get pods -n kgateway-system
</code></pre>
<p>Example output:</p>
<pre><code>NAME                        READY   STATUS    RESTARTS   AGE
kgateway-5495d98459-46dpk   1/1     Running   0          19s
</code></pre>
<p>5. Enroll the default namespace in the Ambient Mesh. This will tell Istio that any applications deployed to this namespace should be part of the Ambient Service Mesh.</p>
<pre><code>kubectl label namespace default istio.io/dataplane-mode=ambient
</code></pre>
<p>6. Deploy the <strong>httpbin</strong> sample application from the Istio Github repository</p>
<pre><code>kubectl create -f https://github.com/istio/istio/raw/master/samples/httpbin/httpbin.yaml
</code></pre>
<p>7. Define a Gateway Resource: Create a Gateway object in the default namespace. This resource represents the ingress&rsquo;s network listener, defining which ports (e.g., 80/443) and hosts it will expose to external traffic.</p>
<pre><code>apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: ambient-ingress-gateway
  namespace: default
spec:
  gatewayClassName: kgateway
  listeners:
  - name: http
    protocol: HTTP
    port: 80
</code></pre>
<p>8. Define HTTPRoutes: Create HTTPRoute resources to define how specific external hostnames and URL paths map to your services that are running within the Ambient Mesh.</p>
<pre><code>apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: httpbin-route
  namespace: default
spec:
  parentRefs:
  - name: ambient-ingress-gateway # This links the route to your Gateway
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: httpbin
      port: 8000
</code></pre>
<p>9. Verify Ambient Workloads: Verify that your gateway and httpbin are correctly enrolled in Ambient Mesh (e.g., its namespace is in Ambient mode, and ztunnel has successfully picked it up). Traffic originating from kgateway will then flow securely and efficiently through ztunnel.</p>
<pre><code>istioctl ztunnel-config workload
</code></pre>
<p>You should see output similar to this. Note that the PROTOCOL is HBONE (vs TCP for other workloads). This indicates that the kgateway POD and httpbin are both using mTLS through the Ambient Mesh.</p>
<pre><code>NAMESPACE          POD NAME                                 IP           NODE                WAYPOINT PROTOCOL
default            ambient-ingress-gateway-5d59765d48-92xdd 10.104.93.74 kind4-control-plane None     HBONE
default            httpbin-787cdcc9df-v8npz                 10.104.93.73 kind4-control-plane None     HBONE
</code></pre>
<p>10. Note the service IP address of your kgateway POD and send traffic to it</p>
<pre><code>kubectl get svc|grep ambient-ingress-gateway


NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)        AGE
ambient-ingress-gateway   LoadBalancer   10.4.208.214   172.19.104.1   80:32757/TCP   140m
</code></pre>
<p>11. Curl the IP address and port of the service to test connectivity</p>
<pre><code>curl 172.19.104.1:80/ip
</code></pre>
<p>You should see output similar to the following</p>
<pre><code>{
  &quot;origin&quot;: &quot;10.104.93.74&quot;
}
</code></pre>
<p>In a few short steps, you’ve deployed Istio in Ambient mode, deployed an application, onboarded it to the service mesh, deployed kgateway, configured kgateway to route traffic securely, and verified everything. Pretty cool!</p>
<h2>Advanced Considerations<span class="hx:absolute hx:-mt-20" id="advanced-considerations"></span>
    <a class="subheading-anchor" href="#advanced-considerations"></a></h2><ul>
<li><strong>TLS Termination</strong> : kgateway proficiently handles external TLS termination, allowing your HTTPRoutes to define secure ingress while offloading TLS processing from your application.</li>
<li><strong>Authentication &amp; Authorization</strong>: kgateway can integrate with external authentication systems or apply fundamental authorization policies before traffic enters the mesh. For more complex, workload-specific authorization requirements, Istio&rsquo;s comprehensive authorization policies applied on waypoints (or directly on ztunnel for L4 policies) take over.</li>
<li><strong>Observability Deep Dive</strong> : Leverage powerful observability tools such as Grafana for metrics visualization, Kiali for mesh topology and traffic flow, and Jaeger for distributed tracing. These tools, integrated with Istio&rsquo;s telemetry, provide an unparalleled view of the request lifecycle from kgateway through ztunnel and any waypoints to your final application.</li>
</ul>
<h2>Conclusion<span class="hx:absolute hx:-mt-20" id="conclusion"></span>
    <a class="subheading-anchor" href="#conclusion"></a></h2><p>The combination of kgateway with Istio&rsquo;s Ambient Mesh represents a significant and decisive leap forward in Kubernetes networking. This synergy offers a powerful, flexible, and exceptionally efficient solution for ingress, extending the profound benefits of Ambient&rsquo;s sidecar-less architecture directly to your cluster&rsquo;s edge. By providing a unified API for all traffic management, ensuring robust security from the very ingress point, and delivering comprehensive observability across the entire data path, this architecture empowers organizations to simplify complex operations, substantially reduce resource consumption, and build more resilient, performant, and secure microservices applications. Kgateway and Ambient Mesh are not merely tools; they are shaping the very future of cloud-native infrastructure.</p>
<p>To learn more or get involved with the kgateway and Ambient Mesh projects, check these out:</p>
<ul>
<li>Free, hands-on <a href="https://kgateway.dev/resources/labs/" rel="noopener" target="_blank">kgateway labs</a>‍</li>
<li>Join the <a href="https://kgateway.dev/slack" rel="noopener" target="_blank">kgateway community on Slack</a></li>
<li><a href="https://ambientmesh.io/get-started/" rel="noopener" target="_blank">Ambient Mesh quickstart</a></li>
<li><a href="https://ambientmesh.io/cost-savings-estimator/" rel="noopener" target="_blank">Cost savings estimator for Ambient Mesh</a></li>
</ul>
