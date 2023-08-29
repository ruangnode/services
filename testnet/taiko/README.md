---
description: Run with docker
---

# Taiko

<figure><img src="../../.gitbook/assets/project1.jpg" alt="" width="100"><figcaption></figcaption></figure>

## Run a Taiko node

### Overview

This guide will walk you through the process of operating a Taiko node via [simple-taiko-node(opens in a new tab)](https://github.com/taikoxyz/simple-taiko-node). You will be able to:

* Run a Taiko node easily from the command line on Windows, Mac, and Linux.
* View a [Grafana(opens in a new tab)](https://grafana.com/) dashboard which displays the node's status.

### Prerequisites

* [Docker(opens in a new tab)](https://docs.docker.com/engine/install/) is installed and **running**.
* [Git(opens in a new tab)](https://github.com/git-guides/install-git/) is installed.
* Consult the [Geth minimum hardware requirements(opens in a new tab)](https://github.com/ethereum/go-ethereum#hardware-requirements), but ignore the storage requirement as Taiko nodes will require less storage. Around 50GB should be more than enough initially, but over time it could become insufficient as the chain grows. As of `2023-06-21T16:59:11+00:00` a Taiko node sync takes **17.3 GB**.
