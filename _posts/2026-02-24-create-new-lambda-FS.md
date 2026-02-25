---
layout: post
title: a post with table of contents on a sidebar
date: 2026-02-07 10:14:00-0400
description: create FS for a new lambda region
tags: infra tools oss cloud GPU
categories: ai-infra
giscus_comments: true
related_posts: false
toc:
  sidebar: left
---

This post shows the workflow to setup a new FS for a new region so we can reuse this FS when creating new instance in this region.

### edit env.config

edit env.config to ajust `LAMBDA_REGION=`

### copy setup folder to server

from local mac

```
scp -r setup ubuntu@209.20.159.10:/lambda/nfs/dev-env/
```

### copy ssh keys

on server

```
mkdir -p  /lambda/nfs/dev-env/home/.ssh/
#copy private keys from mac
vim /lambda/nfs/dev-env/home/.ssh/id_ed25519.pub
# copy private keys from mac
vim /lambda/nfs/dev-env/home/.ssh/id_ed25519
chmod 600 /lambda/nfs/dev-env/home/.ssh/id_ed25519
chmod 644 /lambda/nfs/dev-env/home/.ssh/id_ed25519.pub
```

### add git signer

```
echo "dongw2019@gmail.com $(cat /lambda/nfs/dev-env/home/.ssh/id_ed25519.pub)" > /lambda/nfs/dev-env/home/.ssh/allowed_signers
```

### add HF token

```
echo "hf_YOUR_TOKEN" > /lambda/nfs/dev-env/home/.huggingface_token
chmod 600 /lambda/nfs/dev-env/home/.huggingface_token
```

### first-setup script

```
bash /lambda/nfs/dev-env/setup/first-time-setup.sh
```

### bootstrap

```
bash /lambda/nfs/dev-env/setup/bootstrap.sh
```

### source

```
source ~/.bashrc
```

### test Git ssh

```
ssh -T git@github.com
```

### test signed commits

```
cd /lambda/nfs/dev-env/repos && git init test-signing && cd test-signing
git commit --allow-empty -m 'test signed commit'
git log --show-signature
```
