---
title: "Observing the ambient mesh"
url: "/blog/sidecar-migration-part-5/"
date: "Tue, 08 Jul 2025 00:00:00 +0000"
author: ""
feed_url: "https://ambientmesh.io/blog/index.xml"
---
Every component in a service mesh generates telemetry data that can be used for the observability of your environment. In sidecar mode, you had full Layer 7 processing at every step, which gave you the opportunity for full observability at Layer 7 (by way of configurable generation of metrics, traces and logs). Ambient mode, by virtue of having Layer 7 processing be opt-in through waypoints, changes the observability picture a little.
