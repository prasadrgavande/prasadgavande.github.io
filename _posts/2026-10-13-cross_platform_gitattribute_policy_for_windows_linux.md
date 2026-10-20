---
layout: post
title: A Cross-Platform .gitattributes Policy for Windows and Linux
date: 2026-10-13 15:09:00
description: Stop CRLF vs LF errors in Git. how a .gitattributes line-ending policy keeps shell scripts, YAML, TOML, and CI files working across Windows and Linux.
tags: DevOps, git
categories: DevOps
featured: false
giscus_comments: true
---

<p align="center">
  <img src="https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/15-gitattributes/cover.png?raw=true" alt="CoverImage">
</p>


It usually happens on Friday EOD :D 

You push a change, the CI pipeline starts, and then it dies with something like:

```text
/bin/bash^M: bad interpreter: No such file or directory
```

Or maybe a YAML file that looks perfectly fine in your editor explodes on a Linux agent with a syntax error you can’t see. You start blaming Docker -> Then Jenkins. 

But the real culprit is usually much smaller: a carriage return.

This isn’t a Docker-specific problem. Docker just happens to be one place where it becomes visible. The same thing happens with any Linux CI runner, remote server, POSIX shell, or containerized build step. If a file is written on Windows but consumed on Linux, line endings matter.

## CRLF vs LF

Windows traditionally uses `CRLF` — carriage return plus line feed — as its line ending.

Linux and most POSIX systems use `LF` — just line feed.

Git tries to smooth this over. On Windows, a common setting is:

```bash
core.autocrlf=true
```

That tells Git to check files out with `CRLF` in your working directory, but store them with `LF` in the repository. Most of the time, that’s fine.

Until it isn’t.

Because when a shell script, a `Makefile`, or a Linux service reads a file that still contains `CRLF`, it doesn’t always fail loudly. Sometimes it fails quietly. Sometimes it fails in a way that makes you question your career choices.

## `core.autocrlf` isn’t enough

The problem with relying on `core.autocrlf` is that it’s a local setting. It depends on every developer having the right Git config. It also doesn’t know which files are safe to convert and which ones absolutely must stay `LF`.

A `Dockerfile` might be fine with `CRLF` on your machine, but not when a Linux builder reads it.

A `.gitleaksignore` file might look harmless, but if its fingerprints are matched as exact strings, a trailing `\r` can silently stop an ignore rule from working.

A `Makefile` is even less forgiving. Make treats indentation strictly. A stray carriage return can produce:

```text
missing separator
```

And then you spend an hour staring at tabs and spaces, wondering where your life went wrong.

## The fix: a `.gitattributes` policy

The solution is to stop letting Git guess. Put the rules in the repository itself, where they apply to everyone.

That’s what `.gitattributes` is for.

Here’s the policy I use. It’s a cross-platform line-ending policy for any repo that gets checked out on Windows but executed, parsed, or built on Linux.

```gitattributes
# --- Executed by a POSIX shell or a Linux agent -----------------------------
Jenkinsfile            text eol=lf
Dockerfile             text eol=lf
*.sh                   text eol=lf

# --- Parsed by Linux services -------------------------------------------------
*.yml                  text eol=lf
*.yaml                 text eol=lf
*.toml                 text eol=lf
# --- Windows-native only ------------------------------------------------------
# PowerShell/Batch are only ever run on Windows, where CRLF is expected.
*.ps1                  text eol=crlf
*.bat                  text eol=crlf
*.cmd                  text eol=crlf
```

## The three categories that matter

When I wrote this policy, I grouped files into three buckets.

### 1. Executed by a POSIX shell or Linux agent

These are the obvious ones:

- `Jenkinsfile`
- `Dockerfile`
- `*.sh`

If a Linux machine is going to execute it, it needs `LF`.


### 2. Parsed by Linux services

This is where people get surprised. It’s not just scripts. It’s configuration files too:

- `*.yml`, `*.yaml`
- `*.toml`
- `*.ini`, `*.cfg`
- `*.properties`
- `.gitleaksignore`
- `Makefile`

These files are read by tools that expect Unix-style line endings. A trailing `\r` can break parsing, suppress ignore rules, or make Make throw a fit.

The `.gitleaksignore` example is especially nasty because it fails silently. The fingerprint doesn’t match, the secret isn’t suppressed, and your security scan starts yelling about something you already dealt with.

### 3. Windows-native only

Some files should stay `CRLF`:

- `*.ps1`
- `*.bat`
- `*.cmd`

PowerShell and Batch are Windows-native. There’s no reason to force `LF` on them. Let them live in their natural habitat.

## The one-time renormalisation

Adding `.gitattributes` doesn’t magically fix files that are already committed with the wrong line endings. Git won’t rewrite history just because you asked nicely.

You need to run this once:

```bash
git add --renormalize .
git commit -m "chore: normalise line endings"
```

This tells Git to re-check every file, apply the new rules, and update the index.

The commit will look ugly. Every line in some files may appear changed, because the invisible line ending changed. That’s normal. It’s a one-time cost. After that, the repo is clean and predictable.

## Finally

Line endings are one of those problems that feel stupid until they cost you.

A `.gitattributes` file turns line endings from a local, personal Git setting into a repository-wide contract. It says: no matter who checks this out, or what operating system they use, these files will be `LF` because Linux will read them.

It’s not glamorous. It won’t make your architecture diagram prettier. But it will stop `/bin/bash^M`. 
