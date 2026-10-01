---
layout: post
title: How to Use plugins.txt for Jenkins
date: 2026-09-22 15:09:00
description: simple guide which explain How to Use `plugins.txt` for Jenkins
tags: DevOps, Jenkins, Docker
categories: DevOps
featured: false
---
<p align="center">
  <img src="https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/13-jenkins_plugins/cover.png?raw=true" alt="CoverImage">
</p>

## How to Use `plugins.txt` for Jenkins

If you've ever set up a fresh Jenkins instance and spent the next hour in the Update Center clicking "Install" on plugin after plugin, you already know why `plugins.txt` exists. It's a plain text file that tells Jenkins which plugins you need, so you don't have to install them by hand. Think of it as a shopping list for your Jenkins controller — and once you start using it, going back to manual installs feels like punishment.

In this post, we will walk through what `plugins.txt` is, why it's worth the five minutes it takes to set up, and how to use it. Whether you run Jenkins in Docker, on a VM, or your own laptop, the same file works for all of them. 

### What Exactly Is `plugins.txt`?

`plugins.txt` is just a text file. One plugin per line. That's the whole format.

What you *do* with it depends on how you run Jenkins. The idea is the same either way: instead of installing plugins manually through the UI, you hand a list to a tool and let it do the work.

**If you run Jenkins in Docker**, you copy the file into your image and let the Jenkins Plugin Installation Manager CLI (`jenkins-plugin-cli`) handle it at build time. That means plugins are baked into the image before the container ever starts, so your Jenkins is ready the moment it boots.

```dockerfile
FROM jenkins/jenkins:2.504.1-lts-jdk21
USER root
COPY plugins.txt /usr/share/jenkins/plugins.txt
RUN jenkins-plugin-cli --plugin-file /usr/share/jenkins/plugins.txt
USER jenkins
```

**If you don't use Docker** — say you're on a VM, or any server, or just testing Jenkins on your laptop — you use the same underlying tool, but standalone. It's a Java JAR called `jenkins-plugin-manager`. You point it at your `plugins.txt`, tell it where your Jenkins WAR file lives, and where to drop the plugins:

```bash
java -jar jenkins-plugin-manager-*.jar \
  --war /path/to/jenkins.war \
  --plugin-file plugins.txt \
  --plugin-download-directory /path/to/plugins
```

Then you copy the downloaded `.jpi` files into your Jenkins plugin directory (usually `$JENKINS_HOME/plugins`), restart Jenkins, and you're done. Same list, same result, no Docker required.

The `jenkins-plugin-cli` command inside the Docker image is really just a wrapper around `jenkins-plugin-manager`. They're the same tool. So whichever path you take, the `plugins.txt` you write is identical.

### Why Bother With `plugins.txt`?

Two big reasons, and they apply whether you're running containers or not:

1. **Reproducibility.** Your plugin list lives in version control alongside your Jenkins config. Anyone who sets up the same environment gets the same plugins. No "well, it works on my machine" moments.

2. **Speed.** Plugins are installed ahead of time. When Jenkins starts, it just loads them. No waiting on the Update Center, no hoping the network is up.

### The Format: Simple but Powerful

Each line in `plugins.txt` is one of four things:

- A plugin name: `workflow-aggregator`
- A plugin name with a pinned version: `git:5.2.1`
- A comment (starting with `#`)
- A blank line (ignored)

That's it. No YAML indentation drama, no JSON brackets to lose track of. Just names and optional versions.

Here's a real example — a list I've used in production Jenkins setups:

```
# Jenkins plugins required by the root Jenkinsfile.
#
# `withCredentials`, `waitForQualityGate`, `junit` and `cleanWs` all come
# from plugins that are not in the base Jenkins image.
#
# Versions are intentionally unpinned: the update centre resolves the current
# compatible release for the running Jenkins core. Pin these if you need a
# reproducible image, but re-check compatibility when Jenkins LTS bumps.

# --- Pipeline plumbing ---
workflow-aggregator
workflow-cps
workflow-job
pipeline-stage-view

# --- SCM ---
git
git-client

# --- Credentials: withCredentials(), used for POSTGRES_PASSWORD / REDIS_PASSWORD ---
credentials
credentials-binding
plain-credentials

# --- Test reporting: junit() in the post block ---
junit
coverage

# --- Workspace hygiene: cleanWs() ---
ws-cleanup

# --- Build log timestamps: timestamps() in options ---
timestamper
build-timeout

# --- Static analysis: sonarGlobalConfiguration + waitForQualityGate ---
sonar
quality-gates

# --- Docker agent/cloud support ---
docker-plugin
docker-java-api
docker-commons
docker-workflow
```

Notice the comments — they tell whoever reads this file *why* each plugin group is there. Six months from now, when you're wondering why `quality-gates` is in your list, that comment will save you a Stack Overflow rabbit hole.

### Two Ways to Use It

**Docker path.** You add `plugins.txt` to your repo, reference it in your Dockerfile with `COPY`, and run `jenkins-plugin-cli` during the build. Whenever you add or remove a plugin, you rebuild the image and redeploy. This is the most common setup today, and if you're already containerized, it's the path of least resistance.

**Non-Docker path.** You download `jenkins-plugin-manager` from its GitHub releases page. You run it against your `plugins.txt` and your Jenkins WAR file. It downloads everything into a folder. You copy those `.jpi` files into `$JENKINS_HOME/plugins` and restart Jenkins. If you do this often, wrap it in a small shell script or Ansible task so it's one command.

```bash
#!/usr/bin/env bash
set -euo pipefail

JENKINS_WAR=/opt/jenkins/jenkins.war
JENKINS_HOME=/var/lib/jenkins
PLUGIN_MANAGER=/opt/tools/jenkins-plugin-manager.jar

java -jar "$PLUGIN_MANAGER" \
  --war "$JENKINS_WAR" \
  --plugin-file plugins.txt \
  --plugin-download-directory /tmp/plugins

cp /tmp/plugins/*.jpi "$JENKINS_HOME/plugins/"
systemctl restart jenkins
```

That's it. Both paths use the same file. Pick whichever fits your environment.

### A Few Gotchas to Watch For

**Don't rely on the Update Center at runtime.** If Jenkins tries to install plugins on startup and the Update Center is unreachable — because of a proxy, a firewall, or just a bad day — your instance might fail to start. Installing plugins ahead of time avoids this entirely.

**Plugin compatibility is a moving target.** When you upgrade Jenkins LTS, some plugin versions may become incompatible. If you're pinning versions, test the upgrade in staging first. Unpinned lists are more forgiving because the resolver picks compatible releases automatically.

**Transitive dependencies matter.** If you list only `workflow-aggregator`, it pulls in `workflow-cps`, `workflow-job`, and a pile of others. If you need a specific version of one of those, list it explicitly. Otherwise you're at the mercy of the aggregator's dependency graph, which can change between releases.

**The file name doesn't actually matter.** `plugins.txt` is just a convention. The tool reads whatever you pass with `--plugin-file`. But stick with the convention — it makes your setup instantly familiar to anyone who's worked with Jenkins before.

**Order doesn't matter, but grouping does.** Jenkins doesn't care what order plugins are listed in. But grouping them by purpose — pipeline, SCM, testing, credentials — makes the file way easier to skim. Future you will appreciate it.

### Finally 

`plugins.txt` is one of those simple tools that quietly solves a real problem. It turns plugin installation from a manual, error-prone chore into a declarative, version-controlled process.



