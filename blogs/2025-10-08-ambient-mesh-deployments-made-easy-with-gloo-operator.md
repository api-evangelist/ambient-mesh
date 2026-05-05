---
title: "Ambient mesh deployments made easy with Gloo Operator"
url: "/blog/ambient-mesh-deployments-made-easy-with-gloo-operator/"
date: "Wed, 08 Oct 2025 00:00:00 +0000"
author: ""
feed_url: "https://ambientmesh.io/blog/index.xml"
---
<h2>Installation methods<span class="hx:absolute hx:-mt-20" id="installation-methods"></span>
    <a class="subheading-anchor" href="#installation-methods"></a></h2><p>Istio has traditionally supported installation either with the Istio CLI or with Helm.</p>
<p>The Istio CLI was popular on account of providing a straightforward and easy way of getting Istio up and running on a Kubernetes cluster.</p>
<p>Helm has become the preferred and recommended method to install Istio.</p>
<p>Historically Istio also had the Istio in-cluster Operator, a mechanism for installing the service mesh using the Kubernetes Operator pattern. A controller would watch for the application of the IstioOperator resource, and react to it by installing Istio. This method was deprecated and ultimately removed, primarily due security concerns having to do with the operator controller requiring broad permissions to manage Istio resources.</p>
<p>For more details on the history of Istio installation and its nuances, see John Howard&rsquo;s blog posts <a href="https://blog.howardjohn.info/posts/past-present-future-istio-install/" rel="noopener" target="_blank">The Past, Present, and Future of Istio Installation</a> and <a href="https://blog.howardjohn.info/posts/istio-install/" rel="noopener" target="_blank">Everything you need to know about Istio installation</a>.</p>
<p>Today, in ambient mode, to install Istio with Helm, <a href="https://istio.io/latest/docs/ambient/install/helm/" rel="noopener" target="_blank">the process</a> is as follows:</p>
<ul>
<li>Install the Kubernetes Gateway API CRDs - the Gateway API is a dependency, so must be installed as a prerequisite step.</li>
<li>Deploy the base Helm chart, which applies Istio&rsquo;s CRDs.</li>
<li>Istiod, Istio&rsquo;s control plane, has its own dedicated chart.</li>
<li>Apply the Helm chart for the Istio CNI agent, which is required for ambient.</li>
<li>Finally, apply the Helm chart for the ztunnel component, Istio ambient&rsquo;s layer 4 proxy.</li>
</ul>
<p>Thanks to the <a href="https://gateway-api.sigs.k8s.io/" rel="noopener" target="_blank">Kubernetes Gateway API</a>, we can now deploy gateways on-demand in a Kubernetes-native manner using its Gateway resource.</p>
<p>There are good reasons to like Helm:</p>
<ul>
<li>Helm is a de facto standard for provisioning systems to Kubernetes, is well-established and approved in enterprise environments.</li>
<li>Helm is consequently also familiar to many devops engineers.</li>
<li>The installation is modular, and easily customized.</li>
<li>Each Helm chart maps to a specific component of the ambient mesh, providing control over the installation process.</li>
</ul>
<p>This then leads us to the subject of how we install Istio in <a href="https://docs.solo.io/gloo-mesh/latest/" rel="noopener" target="_blank">Solo Enterprise for Istio</a>. Solo Enterprise for Istio supports Istio installation with Helm, but it also provides an alternative: the <a href="https://docs.solo.io/gloo-mesh/latest/ambient/setup/install/operator/" rel="noopener" target="_blank">Gloo Operator</a>.</p>
<h2>The Gloo Operator<span class="hx:absolute hx:-mt-20" id="the-gloo-operator"></span>
    <a class="subheading-anchor" href="#the-gloo-operator"></a></h2><p>The Gloo Operator represents a higher level abstraction for installing Istio. With the Gloo Operator, platform operators do not need to be aware of, or to reference specific components which today make up Istio. The idea is not new, it is basically the operator pattern again.</p>
<p>The installation is declarative; it is driven mainly by the <a href="https://docs.solo.io/gloo-mesh/latest/ambient/setup/install/operator/#servicemeshcontroller-reference" rel="noopener" target="_blank">ServiceMeshController</a> resource, where you specify the main configuration parameters for your installation. Applying the resource triggers a controller which then goes about to reconcile your cluster to this desired state. For <a href="https://docs.solo.io/gloo-mesh/latest/ambient/setup/install/operator/#advanced-settings-configuration" rel="noopener" target="_blank">advanced configuration</a>, there is an ancillary resource, the ConfigMap gloo-extensions-config in the gloo-mesh namespace, for specifying a myriad of additional configuration details.</p>
<p>The Gloo Operator differs significantly from the original Istio Operator both in design, functionality, and in terms of security considerations:</p>
<ul>
<li>Gloo Operator translates configuration into validated Helm chart values. It is strictly an abstraction, which minimizes installation errors.</li>
<li>The Gloo Operator does not require elevated level privileges.</li>
</ul>
<p>There are several benefits to this approach to installing Istio:</p>
<ul>
<li>The end user is shielded from low-level changes in the implementation of Istio.</li>
<li>The Gloo Operator makes upgrades simple and easy.</li>
<li>The Gloo Operator presents a well-defined contract with the platform operator.</li>
<li>This approach is much simpler, and doesn&rsquo;t require Helm expertise.</li>
</ul>
<h2>Example: Installing Istio with the Gloo Operator<span class="hx:absolute hx:-mt-20" id="example-installing-istio-with-the-gloo-operator"></span>
    <a class="subheading-anchor" href="#example-installing-istio-with-the-gloo-operator"></a></h2><p>To get a feel for how to work with the Gloo Operator, let us walk through an example.</p>
<p>General reference for installing Istio in Solo Enterprise for Istio with the Gloo Operator can be found <a href="https://docs.solo.io/gloo-mesh/latest/ambient/setup/install/operator/" rel="noopener" target="_blank">here</a> for single cluster installations, and <a href="https://docs.solo.io/gloo-mesh/latest/ambient/multicluster/install/default/operator/" rel="noopener" target="_blank">here</a> for multicluster installations.</p>
<p>Install the Gloo Operator with Helm:</p>
<pre><code>helm install gloo-operator --version 0.2.5 \
  oci://us-docker.pkg.dev/solo-public/gloo-operator-helm/gloo-operator \
  --namespace gloo-mesh --create-namespace \
  --set manager.env.SOLO_ISTIO_LICENSE_KEY=$GLOO_MESH_LICENSE_KEY
</code></pre>
<p>Wait for the gloo-operator pod to be ready:</p>
<pre><code>kubectl wait --for=condition=Ready pod --all -n gloo-mesh --timeout=300s
</code></pre>
<p>List the pods in the gloo-mesh namespace to visually verify that it is Running:</p>
<pre><code>kubectl get pods -n gloo-mesh
</code></pre>
<p>Next, review the following ServiceMeshController resource:</p>
<pre><code>---
apiVersion: operator.gloo.solo.io/v1
kind: ServiceMeshController
metadata:
  name: managed-istio
  labels:
    app.kubernetes.io/name: managed-istio
spec:
  dataplaneMode: Ambient
  installNamespace: istio-system
  version: 1.26.2
</code></pre>
<p>The ServiceMeshController resource exposes status conditions that allow you to track the status and phase of the installation, and when it has completed.</p>
<p>We can watch how the status of the ServiceMeshController resource changes as the installation progresses to completion:</p>
<pre><code>watch &quot;kubectl get servicemeshcontrollers.operator.gloo.solo.io managed-istio -o yaml | yq .status&quot;
</code></pre>
<p>Apply the ServiceMeshController resource to the cluster:</p>
<pre><code>kubectl apply -f artifacts/service-mesh-controller.yaml
</code></pre>
<p>At this point, list the pods in istio-system to see Istio&rsquo;s components istiod, istio-cni, and ztunnel running:</p>
<pre><code>kubectl get pods -n istio-system
</code></pre>
<p>The Operator took our specification and turned it into a running instance of Istio.</p>
<h2>Summary<span class="hx:absolute hx:-mt-20" id="summary"></span>
    <a class="subheading-anchor" href="#summary"></a></h2><p>Helm is the recommended method for installing Istio open-source. In Solo Enterprise for Istio we have an additional option with the Gloo Operator.</p>
<p>Whereas the Helm approach is modular, it exposes many details, which demands knowledge of and expertise with the components that make up Istio. The Gloo Operator on the other hand encapsulates those details and provides a clean interface, and an abstraction over those details.</p>
<p>To continue learning more about the Gloo Operator, check out these resources:</p>
<ul>
<li><a href="https://www.solo.io/resources/video/ambient-mesh-deployments-made-easy-gloo-operator" rel="noopener" target="_blank">Accompanying video</a></li>
<li><a href="https://docs.solo.io/gloo-mesh/latest/ambient/setup/install/operator/" rel="noopener" target="_blank">Gloo Operator documentation</a></li>
<li><a href="https://www.solo.io/resources/video/helm-gloo-operator-ambient-mesh-deployment" rel="noopener" target="_blank">Helm vs Gloo Operator</a></li>
</ul>
