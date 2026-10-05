---
layout: post
title: How to Use JCasC - Jenkins Configuration as Code
date: 2026-10-05 15:09:00
description: Step by step guide to use Jenkins configuration as code
tags: DevOps, Jenkins, Docker
categories: DevOps
featured: false
---

<p align="center">
  <img src="https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/14-JCasC/cover.png?raw=true" alt="CoverImage">
</p>


## How to Use JCasC (Jenkins Configuration as Code)

If you've ever set up Jenkins the "normal" way, you know the drill. You install Jenkins, log in, and then spend the next two hours clicking through menus. Set the system message here. Add a user there. Configure the SonarQube server. Add a credential. Set up the Docker cloud. And then, a month later, you need to do it all again on a fresh instance because something broke — and you have no idea what you clicked the first time.

That's the problem JCasC (Jenkins Configuration as Code) solves. You describe your Jenkins configuration in a YAML file, commit it to git, and let Jenkins configure itself at startup. No clicking. No "what did I set the executors to last time?" No drift between environments.

In this post, I'll show you how JCasC works, what a real config file looks like, and how to run it — whether you're using Docker or not.

### What Is JCasC?

JCasC is a Jenkins plugin called `configuration-as-code`. You write a YAML file (usually `jenkins.yaml` or `casc.yaml`), tell Jenkins where to find it, and the plugin applies the configuration on startup. If something in the file changes, you restart Jenkins (or trigger a reload) and the new config is applied.

The YAML maps directly to Jenkins' internal object model. Every setting you can click in the UI has a corresponding field in the YAML. It's verbose, but it's predictable — and once you get the hang of the structure, it becomes the fastest way to manage Jenkins.

### The Basic Shape

A JCasC file is organized into a few top-level sections:

- **`jenkins`** — the core controller settings: executors, security, cloud agents, system message.
- **`unclassified`** — things that don't fit elsewhere, like the Jenkins URL, SonarQube servers, and other global tool integrations.
- **`credentials`** — the credential store.
- **`tool`** — build tool installations (Git, Maven, JDK, etc.).

Here's the skeleton:

```yaml
jenkins:
  systemMessage: "..."
  numExecutors: 2
  securityRealm: ...
  authorizationStrategy: ...
  clouds: ...

unclassified:
  location: ...
  sonarGlobalConfiguration: ...

credentials:
  system:
    domainCredentials: ...

tool:
  git: ...
```

Not every section is required. Start with what you need and grow from there.

### A Real Example

Let me walk through a working config. This is from a real Jenkins setup — a CI server for a small app with a SonarQube integration and some credentials.

**Basic controller settings:**

```yaml
jenkins:
  systemMessage: "Trading App CI — managed by JCasC"
  numExecutors: 2
  mode: NORMAL
```

`systemMessage` is the banner at the top of the Jenkins dashboard. That "managed by JCasC" hint is intentional — it tells anyone who logs in not to bother changing things through the UI, because the config file will overwrite them on the next reload.

**Security:**

```yaml
  securityRealm:
    local:
      allowsSignup: false
      users:
        - id: "admin"
          password: "my_password"

  authorizationStrategy:
    globalMatrix:
      permissions:
        - "Overall/Administer:admin"
        - "Overall/Read:authenticated"
```

This sets up a local user database with one admin user, disables self-signup, and gives the admin full control while letting any authenticated user read. For anything more serious than a personal setup, you'd probably swap `local` for LDAP or OIDC — but the structure is the same.

One warning: **don't commit real passwords to git.** The `my_password` above is a placeholder. For real secrets, use environment variable substitution (I'll cover that in a minute).

**Docker cloud:**

```yaml
  clouds:
    - docker:
        name: "docker"
        dockerApi:
          dockerHost:
            uri: "unix:///var/run/docker.sock"
        templates: []
```

This registers the local Docker daemon as a cloud provider, which lets your pipelines use `agent { docker { ... } }`. The `templates: []` bit means no pre-baked agent templates — you define them per-pipeline instead.

**SonarQube integration:**

```yaml
unclassified:
  location:
    url: "http://localhost:8080/"
    adminAddress: "admin@example.com"

  sonarGlobalConfiguration:
    buildWrapperEnabled: false
    installations:
      - name: "SonarQube"
        serverUrl: "http://sonarqube:9000"
        credentialsId: "sonar-token"
```

The `name: "SonarQube"` matters here — it has to match exactly what your Jenkinsfile references in `withSonarQubeEnv('SonarQube')`. If you rename it in one place and not the other, the build fails with a confusing error. Keep them in sync.

**Tool installations:**

```yaml
tool:
  git:
    installations:
      - name: "Default"
        home: "git"
```

This tells Jenkins where to find Git. `home: "git"` means "look it up on the PATH," which works inside a container. On a VM, you'd point it at `/usr/bin/git` instead.

### Credentials — And Why They're Tricky

The `credentials` section is where JCasC earns its keep. Instead of clicking through the UI to add each credential, you declare them:

```yaml
credentials:
  system:
    domainCredentials:
      - credentials:
          - string:
              scope: GLOBAL
              id: "sonar-token"
              secret: "${SONAR_TOKEN}"
              description: "SonarQube user token"

          - usernamePassword:
              scope: GLOBAL
              id: "github-pat"
              username: "${GITHUB_USER}"
              password: "${GITHUB_PAT}"
              description: "GitHub Personal Access Token for CI"
```

Two things to notice:

1. **`${SONAR_TOKEN}` syntax.** JCasC supports environment variable substitution. You don't commit secrets to git — you commit the variable name and let the environment provide the value at startup. In Docker, that's an `env_file` or `-e` flags. On a VM, it's whatever your process manager uses to inject environment variables.

2. **`string` vs `usernamePassword` vs `sshUsernamePrivateKey`.** Each credential type has a different shape. A `string` credential is just a secret. A `usernamePassword` has a username and a password. An SSH key credential has a username and a private key. Match the type to what the consuming plugin expects.

### A Clever Trick: String Credentials for Optional Values

Here's a design pattern worth stealing. Imagine your Jenkins has an optional "Deploy" stage. Only a few people ever use it, and everyone else doesn't need it. You could declare the deploy credentials as an SSH key credential — but if the private key isn't configured, JCasC fails to build the credential, and that failure takes down the *entire* configuration load. Suddenly an optional feature becomes a Jenkins-wide outage.

A safer approach: declare the deploy credential as a `string` (a plain secret), even if it holds a private key. An empty string is a perfectly valid `string` credential, so JCasC loads fine even when nothing's configured. The Deploy stage itself is what fails — loudly, and only when someone actually tries to deploy.

```yaml
          # Optional: only used by the Deploy stage. Kept as a `string`
          # credential (not an SSH key) so that an empty value is still
          # valid and JCasC can load without it.
          - string:
              scope: GLOBAL
              id: "deploy-ssh-key"
              secret: "${DEPLOY_SSH_PRIVATE_KEY}"
              description: "Private key for the deploy target (optional)"
```

The comment in the file explains the "why" so nobody "fixes" it later by converting it to a proper SSH key credential and accidentally breaking Jenkins for everyone.

### Two Ways to Run It

Like `plugins.txt`, JCasC works whether or not you're using Docker.

**Docker path.** You install the `configuration-as-code` plugin (add it to your `plugins.txt`), copy your YAML into the image, and set the `CASC_JENKINS_CONFIG` environment variable:

```dockerfile
FROM jenkins/jenkins:2.504.1-lts-jdk21
USER root
COPY plugins.txt /usr/share/jenkins/plugins.txt
RUN jenkins-plugin-cli --plugin-file /usr/share/jenkins/plugins.txt

COPY jenkins.yaml /var/jenkins_home/casc_configs/jenkins.yaml
ENV CASC_JENKINS_CONFIG=/var/jenkins_home/casc_configs/jenkins.yaml

USER jenkins
```

The plugin reads `CASC_JENKINS_CONFIG`, finds the file, and applies it at startup.

**Non-Docker path.** You install the same plugin through the Update Center (or your `plugins.txt`), then tell Jenkins where the config file lives. There are three ways:

1. **Environment variable.** Set `CASC_JENKINS_CONFIG=/path/to/jenkins.yaml` in whatever starts Jenkins — systemd unit, init script, `JENKINS_JAVA_OPTIONS` in `/etc/default/jenkins`, wherever.

2. **Java system property.** Add `-Dcasc.jenkins.config=/path/to/jenkins.yaml` to the Jenkins startup flags.

3. **Default location.** Drop the file into `$JENKINS_HOME/casc_configs/` and JCasC finds it automatically. This is the simplest option if you don't want to fiddle with environment variables.

For a systemd-based install, option 1 looks like this in your unit file:

```ini
[Service]
Environment="CASC_JENKINS_CONFIG=/etc/jenkins/jenkins.yaml"
```

Then `systemctl restart jenkins` and the config is applied.

### Reloading Without Restarting

Once JCasC is running, you don't always need to restart Jenkins to apply changes. There's a reload button in **Manage Jenkins → Configuration as Code**. Click it, and JCasC re-reads the file. Handy when you're iterating on the YAML and don't want to wait for a full Jenkins restart every time.

Just remember: the reload replaces the current configuration with whatever's in the file. If someone made a change through the UI that isn't reflected in the YAML, that change disappears. That's the whole point — the file is the source of truth.

### Things That Will Bite You

**JCasC does not install plugins.** This is the big one. If you configure SonarQube in your YAML but the `sonar` plugin isn't installed, JCasC will quietly ignore that section. Worse, if a whole section references a missing plugin, sometimes it fails silently and you don't notice until the build breaks. Always make sure your plugins are installed before JCasC runs. If you're using `plugins.txt`, that's already handled — plugins go in at build time, JCasC runs at startup, and everything lines up.

**Secrets and environment variables are a two-step dance.** JCasC substitutes `${VAR}` at load time. If the variable isn't set, JCasC substitutes an empty string — usually without complaining. That empty string can cause confusing failures later. For instance, a `github-pat` credential with an empty password makes the pipeline fail before it starts with "Invalid username or token. Password authentication is not supported." Not exactly obvious what happened.

The fix is to always make sure the environment variable is defined, even if its value is empty. In Docker Compose, `env_file` with a default of `""` works. On a VM, set it in your systemd unit or export it before starting Jenkins.

**Multi-line values need careful quoting.** If you're passing an SSH private key through an environment variable, it has newlines. Docker Compose handles this if you quote the value in your `.env` file:

```
DEPLOY_SSH_PRIVATE_KEY="-----BEGIN OPENSSH PRIVATE KEY-----
...
-----END OPENSSH PRIVATE KEY-----
"
```

Some shells and editors mangle this. If you hit that, base64-encode the key and decode it in a wrapper script. Less elegant, but it works everywhere.

**Changing the file breaks nothing until you reload.** JCasC is read at startup (or on manual reload). Editing the YAML doesn't do anything until you apply it. This is a feature — you can prepare a change, review it in a PR, and apply it when you're ready. But it also means "I edited the file and nothing happened" is a common newbie confusion. Restart Jenkins or hit the reload button.

### Wrapping Up

JCasC turns Jenkins configuration from a series of UI clicks into a file you can version, review, and reproduce. Pair it with `plugins.txt` and you've got a Jenkins that stands itself up from two files in your repo — no manual setup, no drift, no "what did I do last time?"

Start small. Get a basic `jenkins.yaml` with just `systemMessage` and `numExecutors` working. Add security. Add one credential. Add one tool. Iterate. Every section you migrate is one less thing you have to click through the next time you set up Jenkins. And trust me — there will be a next time.

Once you're comfortable, the configuration file becomes the single source of truth for your Jenkins setup. Commit it. Review it in pull requests. Rebuild Jenkins from it in five minutes instead of five hours. That's the whole point.


