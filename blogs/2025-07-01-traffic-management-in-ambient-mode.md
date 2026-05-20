---
title: "Traffic management in ambient mode"
url: "/blog/sidecar-migration-part-4/"
date: "Tue, 01 Jul 2025 00:00:00 +0000"
author: ""
feed_url: "https://ambientmesh.io/blog/index.xml"
---
Once you have services enrolled in a mesh, you can use Istio’s traffic management features to perform countless useful functions — A/B testing, canary rollouts, reliability management; the list goes on. As with sidecar mode, ambient mode involves the use of gateways (proxies to get traffic into and out of the mesh) and introduces waypoints (a type of gateway used to manage traffic between services, which replaces the function of the sidecar directly). Configuration There are gateways, and then there are Gateways Sometimes the English language fails us in spectacular ways.
