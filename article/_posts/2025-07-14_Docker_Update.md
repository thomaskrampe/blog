---
layout: post
title: Update Docker Container and Images
description: >
  Update Docker Container and Images from command line
image: 
  path: https://picsur.myctx.net/i/40384e2c-cb85-4f70-b98e-d9f8d633da19.jpg
sitemap: true
hide_last_modified: true
categories: [article]
tag: [Docker]
comments: false
---

Go to the folder where the docker compose.yaml file is stored and download the latest image with the following command:

```bash
sudo docker compose pull
```

After downloading, start the container with the new image:

```bash
sudo docker compose up -d
```

**[Update Docker Images & Containers To Latest Version](https://www.mend.io/blog/update-docker-images-and-containers-to-the-latest-version-easily-and-quickly/)**

Container neu starten mit folgenden Kommando:

```bash
sudo docker restart <container>
```
