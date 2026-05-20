---
title: "Operational differences between sidecar and ambient mode"
url: "/blog/sidecar-migration-part-2/"
date: "Wed, 11 Jun 2025 00:00:00 +0000"
author: ""
feed_url: "https://ambientmesh.io/blog/index.xml"
---
If you’re used to running Istio in sidecar mode, here are a few things you need to know about how ambient mode differs from it. The istio-cni component is now required The Istio CNI node agent was an optional component for sidecar mode. It was used to configure traffic redirection in one central, privileged place, without having to grant that privilege to every pod in the init container .
