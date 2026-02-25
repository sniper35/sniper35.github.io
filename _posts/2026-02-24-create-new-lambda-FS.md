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

### Run once you setup a new server and re-use the FS

```
bash /lambda/nfs/dev-env/setup/bootstrap.sh && source ~/.bashrc
```

## Set up a fS step by step

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

### setup vllm

sync repo

```
cd /lambda/nfs/dev-env/repos/vllm
git fetch upstream main
git pull upstream main
git push
```

### uv pip sync envs

```
uv venv --python 3.12 --seed
source .venv/bin/activate

#incremental compilation using ccache
CCACHE_NOHASHDIR="true" VLLM_USE_PRECOMPILED=1 uv pip install -U -e . --torch-backend=auto

which nvcc

uv pip install -r requirements/build.txt --torch-backend=auto

uv pip install pytest tblib
```

if we want to setup the incremental compilation workflow

```
python tools/generate_cmake_presets.py

cmake --preset release

cmake --build --preset release --target install

# Make changes and repeat
cmake --build --preset release --target install
```
