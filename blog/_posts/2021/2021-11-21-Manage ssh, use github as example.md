---
layout: post
title: "Manage ssh, github as client"
excerpt:
last_modified_at: 2021-11-21
tags:
  - ssh
  - git
---

# SSH commands

List keys for ssh client
`ssh-add -l`

Add key for ssh client
`ssh-add ~/.ssh/robertrino`

Delete key for ssh client
`ssh-add -d ~/.ssh/robertrino`

---

According to `man ssh`
> ssh default will use ~/.ssh/id_dsa, ~/.ssh/id_ecdsa, ~/.ssh/id_ed25519 and ~/.ssh/id_rsa as identity file.

---

Identity file directory `~/.ssh/`
```shell
tree ~/.ssh
├── config
├── id_ed25519
├── id_ed25519.pub
├── known_hosts
├── robertrino
└── robertrino.pub
```

I have 2 identity file
- id_ed25519: company github account
- robertrino: personal github account

# Todos
- make it easier to switch ssh key for different github account.
  - ssh config file
  - GIT_SSH_COMMAND environment variable

