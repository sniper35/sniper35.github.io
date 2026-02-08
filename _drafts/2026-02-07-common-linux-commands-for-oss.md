---
layout: post
title: a post with table of contents on a sidebar
date: 2026-02-07 10:14:00-0400
description: common linux commands when doing oss development
tags: infra tools oss
categories: ai-infra
giscus_comments: true
related_posts: false
toc:
  sidebar: left
---

This post shows how to add a table of contents as a sidebar.

## Adding a Table of Contents

To add a table of contents to a post as a sidebar, simply add

```yml
toc:
  sidebar: left
```

to the front matter of the post. The table of contents will be automatically generated from the headings in the post. If you wish to display the sidebar to the right, simply change `left` to `right`.

### Setup remote origin/upstream

Jean shorts raw denim Vice normcore, art party High Life PBR skateboard stumptown vinyl kitsch. Four loko meh 8-bit, tousled banh mi tilde forage Schlitz dreamcatcher twee 3 wolf moon. Chambray asymmetrical paleo salvia, sartorial umami four loko master cleanse drinking vinegar brunch. <a href="https://www.pinterest.com">Pinterest</a> DIY authentic Schlitz, hoodie Intelligentsia butcher trust fund brunch shabby chic Kickstarter forage flexitarian. Direct trade <a href="https://en.wikipedia.org/wiki/Cold-pressed_juice">cold-pressed</a> meggings stumptown plaid, pop-up taxidermy. Hoodie XOXO fingerstache scenester Echo Park. Plaid ugh Wes Anderson, freegan pug selvage fanny pack leggings pickled food truck DIY irony Banksy.

### sync files

```
rsync -av -vv -e ssh --exclude='*.jpg' user@host:/path/ .
```

`-a` — archive mode: preserves permissions, timestamps, symlinks, and recurses into directories. Shorthand for -rlptgoD.

`-v` — verbose: show which files are being transferred

`--exclude='*.jpg'` — skip any file matching \*.jpg anywhere in the transfer

We can add `-n` to dry run first, it will show you which files are being included/excluded and why, without actually transferring anything.

`-n` — dry run: simulate the transfer without actually copying anything. Useful for previewing what would happen.

```
rsync -avn -vv -e ssh --exclude='*.jpg' user@host:/path/ .
```

### Networking/Infra Debug

This command is to check which process is listening(serving) on port 8000 without resolving hostname(IP) and service name. Or
`List processes that currently have a TCP socket open, listening on local port 8000, without doing DNS or service-name lookups.”`

```
lsof -nP -iTCP:8000 -sTCP:LISTEN
```

- -i: Select Internet files (network sockets), not other network sockets (UDP, all ports etc).
- -TCP:8000: Show me sockets where protocol == TCP AND local port == 8000
- -n — no DNS resolution, Do NOT resolve hostnames, just show IP. It's faster
- -P — no service name resolution.
- -sTCP:LISTEN — filter by TCP state. TCP sockets have states defined by the TCP finite-state machine. Common ones: LISTEN, ESTABLISHED,TIME_WAIT,CLOSE_WAIT. So it restricts results to sockets that are accepting incoming connections.

The output will be like:

```
COMMAND   PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
python3  4217  dong    5u  IPv4 0xabc        0t0  TCP *:8000 (LISTEN)
```

- COMMAND: executable
- PID: process ID (can kill or inspect)
- USER: owner
- FD: file descriptor (e.g., 5u = fd 5, read/write)
- NAME: socket details

### Workflow of debugging a process

# 1. find PID

by port:

```
lsof -nP -iTCP:8000 -sTCP:LISTEN
```

by process name:

```
pgrep -af command
```

- -a — “show arguments”
- -f — “full command line match”
  To find the PID for the running process `java -Xmx2g -jar fis-transaction-adapter.jar`, singly use `pgrep fis-transaction-adapter` will not match but `pgrep -f fis-transaction-adapter` will match and find the PID.

# 2. inspect

ps -o pid,stat,%cpu,%mem,etime,command -p <PID>
lsof -p <PID>

# 3. terminate

kill -TERM <PID>
sleep 5
kill -9 <PID> # only if needed

```

### Git commands

To show only the filenames (added or deleted) we could use
```

git show --name-status a535897b3c7

```
The output will look like:
```

commit a535897b3c744974152eedf9cdfd48c3c85dcc2a
Author: sniper35 <dongw2019@gmail.com>
Date: Sat Feb 7 09:06:14 2026 +0000

    test scripts

A examples/diffusion_router/bench_no_router.py
A examples/diffusion_router/bench_router.py
A examples/diffusion_router/compare_router_vs_no_router.py
A examples/diffusion_router/run_router_vs_no_router_matrix.py
(END)

```
Add ` --oneline` option,
```

git show --oneline --name-status a535897b3c7

```
The output will look like:
```

a535897b test scripts
A examples/diffusion_router/bench_no_router.py
A examples/diffusion_router/bench_router.py
A examples/diffusion_router/compare_router_vs_no_router.py
A examples/diffusion_router/run_router_vs_no_router_matrix.py
(END)

```

### Lambda Cloud File System Persistence

{:data-toc-text="Customizing"}

If you want to learn more about how to customize the table of contents of your sidebar, you can check the [bootstrap-toc](https://afeld.github.io/bootstrap-toc/) documentation. Notice that you can even customize the text of the heading that will be displayed on the sidebar.

### Example of Sub-Heading 2

Jean shorts raw denim Vice normcore, art party High Life PBR skateboard stumptown vinyl kitsch. Four loko meh 8-bit, tousled banh mi tilde forage Schlitz dreamcatcher twee 3 wolf moon. Chambray asymmetrical paleo salvia, sartorial umami four loko master cleanse drinking vinegar brunch. <a href="https://www.pinterest.com">Pinterest</a> DIY authentic Schlitz, hoodie Intelligentsia butcher trust fund brunch shabby chic Kickstarter forage flexitarian. Direct trade <a href="https://en.wikipedia.org/wiki/Cold-pressed_juice">cold-pressed</a> meggings stumptown plaid, pop-up taxidermy. Hoodie XOXO fingerstache scenester Echo Park. Plaid ugh Wes Anderson, freegan pug selvage fanny pack leggings pickled food truck DIY irony Banksy.

### Example of another Sub-Heading 2

Jean shorts raw denim Vice normcore, art party High Life PBR skateboard stumptown vinyl kitsch. Four loko meh 8-bit, tousled banh mi tilde forage Schlitz dreamcatcher twee 3 wolf moon. Chambray asymmetrical paleo salvia, sartorial umami four loko master cleanse drinking vinegar brunch. <a href="https://www.pinterest.com">Pinterest</a> DIY authentic Schlitz, hoodie Intelligentsia butcher trust fund brunch shabby chic Kickstarter forage flexitarian. Direct trade <a href="https://en.wikipedia.org/wiki/Cold-pressed_juice">cold-pressed</a> meggings stumptown plaid, pop-up taxidermy. Hoodie XOXO fingerstache scenester Echo Park. Plaid ugh Wes Anderson, freegan pug selvage fanny pack leggings pickled food truck DIY irony Banksy.
```
