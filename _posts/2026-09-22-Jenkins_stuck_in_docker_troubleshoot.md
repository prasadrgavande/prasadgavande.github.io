---
layout: post
title: Jenkins Stuck on "Getting Ready to Work" - Troubleshooting Startup Hangs in Docker
date: 2026-09-22 15:09:00
description: Jenkins Stuck on "Getting Ready to Work" - Troubleshooting Startup Hangs in Docker
tags: devops
categories: devops
featured: false
---
![CoverImage](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/12-Jenkins-stuck/JenkinsStuckCoverImage.png?raw=true)

# Jenkins Is Stuck on "Getting Ready to Work" — Here's How I Fixed It 

> [!NOTE]
> I am running Jenkins in Docker, below post have steps which I used to troubleshoot issue. 

If you've ever stared at a Jenkins screen that says *"Jenkins is getting ready to work"* for what feels like forever, you're not alone.

I've been there. You spin up Jenkins in Docker, feeling good about your setup, and then... nothing. The page just sits there. No error, no progress, just that loading spinner mocking you.

After troubleshooting this multiple times (and pulling my hair out), I've figured out the usual suspects. Let me walk you through what's actually happening and how to fix it.

## What This Message Actually Means

When Jenkins shows "getting ready to work," it's in the middle of its initialization process. On a fresh install or after a restart, it's doing things like:

- Loading plugins
- Checking for updates
- Initializing the JVM

Usually, this takes 30 seconds to a couple of minutes. But if it's been more than 5 minutes with no progress? Something's blocking it.

## The Usual Suspect: Plugin Update Center

Here's the thing nobody tells you upfront — **Jenkins tries to phone home to `updates.jenkins.io` during startup.**

If you're on a network where that endpoint is slow, blocked, or you just having a bad day, Jenkins will hang. And it won't always tell you why.

This is especially common if you're:
- Running Jenkins in a corporate network with strict firewall rules
- Hosting in a region with poor connectivity to Jenkins' servers
- Using cloud providers that throttle certain outbound connections

### How to Confirm This

First, check the logs:

```bash
docker logs -f your_jenkins_container_name
```

Look for lines mentioning `updates.jenkins.io` or repeated connection attempts. If the logs seem stuck after something like "Listed all plugins," that's your smoking gun.

## The Fix: Point Jenkins to a Faster Mirror

The simplest solution? Tell Jenkins to use a mirror that's actually reachable.

If you're in Asia (or anywhere far from Jenkins' default servers), the Tsinghua mirror is a lifesaver:

```yaml
environment:
  JENKINS_UPDATE_CENTER_URL: "https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json"
```

For other regions, you can try:
- `http://mirror.xmission.com/jenkins/updates/update-center.json` (US)

Add this to your `docker-compose.yml` under the Jenkins service, then restart:

```bash
docker compose restart jenkins
```

This alone fixes the problem more often. 

## Don't Forget the Healthcheck Timeout

Here's something that bit me early on — Docker's healthcheck settings.

If your `start_period` and `retries` are too aggressive, Docker will mark Jenkins as unhealthy before it even finishes starting. This can cause restart loops or just confusing behavior.

Here's what I use now:

```yaml
healthcheck:
  test: ["CMD-SHELL", "curl -f http://localhost:8080/login || exit 1"]
  interval: 30s
  timeout: 10s
  retries: 10
  start_period: 300s
```

That `start_period: 300s` gives Jenkins 5 full minutes before Docker even starts checking. On first boot with plugin downloads, you need that breathing room.

## Memory: The Silent Killer

Jenkins is a memory-hungry beast. If your container is starving for RAM, it'll hang during startup without any obvious error.

Check current usage:

```bash
docker stats your_jenkins_container_name --no-stream
```

If memory looks tight, set explicit JVM limits:

```yaml
environment:
  JAVA_OPTS: "-Xms512m -Xmx2048m"
```

**2GB is the minimum recommend** for a Jenkins master running plugins like SonarQube. 4GB is more comfortable if you have the resources.

## When It's a Plugin Problem

Sometimes the hang is caused by a plugin that's incompatible with your Jenkins version. The logs will show something like:

- `NoSuchMethodError`
- `ClassNotFoundException`
- A specific plugin name appearing repeatedly

In that case, disable the plugin temporarily:

```bash
docker exec -it your_jenkins_container bash
cd /var/jenkins_home/plugins
mv problematic-plugin /tmp/
exit
docker compose restart jenkins
```

Once Jenkins starts, you can reinstall a compatible version from the UI.

## Quick Checklist

Before you go nuclear and wipe everything, run through this:

1. **Check the logs** — `docker logs -f container_name`
2. **Add a plugin mirror** — Tsinghua or XMission
3. **Increase healthcheck timeout** — `start_period: 300s`
4. **Set JVM memory limits** — `-Xms512m -Xmx2048m`
5. **Check for plugin errors** — disable the culprit
6. **Try the right URL** — `http://localhost:8080/login`

## The Nuclear Option (Use With Caution)

If nothing works and you're okay losing your Jenkins config:

```bash
docker compose down
docker volume rm your_jenkins_volume_name
docker compose up -d
```

This wipes everything — jobs, credentials, plugins. Only do this on a fresh install or if you have backups.

## Remember 

The "getting ready to work" hang is almost always one of three things: network issues reaching the update center, insufficient startup time, or memory constraints.

Start with the mirror fix. It's the quickest win and solves most cases.

If you're still stuck after trying these steps, grab the last 30-50 lines of your logs and drop them in a comment.

---
