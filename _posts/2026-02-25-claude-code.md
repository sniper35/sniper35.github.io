---
layout: post
title: Claude Code Practice
date: 2026-02-25 10:14:00-0400
description: create FS for a new lambda region
tags: infra tools oss cloud GPU
categories: ai-infra
giscus_comments: true
related_posts: false
toc:
  sidebar: left
---

This post documents how I use/config Claude Code and other coding agents(Codex)

### Claude Code configs

.claude/settings.json

```
{
  "includeCoAuthoredBy": false,
  "skipDangerousModePermissionPrompt": true,
  "permissions": {
    "allow": [
      "WebSearch",
      "Glob",
      "Grep",
      "Read",
      "Bash(git status*)",
      "Bash(git log*)",
      "Bash(git diff*)",
      "Bash(git show*)",
      "Bash(git branch --list*)",
      "Bash(git branch -a*)",
      "Bash(git branch -r*)",
      "Bash(git remote -v*)",
      "Bash(git tag*)",
      "Bash(git stash list*)",
      "Bash(git rev-parse*)",
      "Bash(git ls-files*)",
      "Bash(git shortlog*)",
      "Bash(git blame*)",
      "Bash(git config --get*)",
      "Bash(git config --list*)",
      "Bash(nvidia-smi*)",
      "Bash(tail:*)"
    ]
  }
}
```
