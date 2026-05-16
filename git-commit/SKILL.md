---
name: git-commit
description: Stage, review, and commit git changes. Use when the user says "commit," "commit this," "save my work," "wrap up," or gives permission to commit automatically. Also use when a task reaches a logical checkpoint and the user has asked to be proactive about committing.
---

# Git Commit

Stage, review, and commit changes to git. Every commit should be a single logical
change with a plain-English imperative subject line.

## When to commit

- The user explicitly asks to commit (any phrasing)
- The user has previously said "commit when you're done" or "you can commit
  without asking" and a task reaches completion
- A logical unit of work is finished and the user has set the expectation that
  you drive the pace

When in doubt, ask. Never commit without the user's awareness.

## Step 1: Run the project's tests

Tests must pass before committing, unless the user has explicitly said to skip
them.

Discover the test command by checking, in order:
1. What the user or the project's CLAUDE.md says about running tests
2. A `Makefile` target (e.g., `make test`, `make check`)
3. A script in `package.json` (`npm test`, `yarn test`, `pnpm test`)
4. A language-idiomatic command (`cargo test`, `zig build test`, `go test
   ./...`, `pytest`)
5. Integration tests must not be overlooked.
6. Ask the user if nothing is discoverable

Run the tests. If they fail, stop and tell the user -- do not commit.

## Step 2: Understand the commit voice

Use the commit voice demonstrated by the example commits below,
even when working in a different repository. Do not infer a different
voice from the current repo's recent history unless the user explicitly
asks you to.

The following examples are canonical. Match this style.

```
commit 64ca983c3a1d6021adf1c448554c333cc09f6722
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Aug 13 15:09:19 2017 -0400

    Initial commit

commit c7a43798a335785864991122b0bb223ee0b1d4a8
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Aug 13 15:18:56 2017 -0400

    Make it clear this is an Ansible role

commit ac3105a14c219fbcecb34ab21a30eb3fd47a196b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 28 16:34:00 2017 -0400

    Convert to Packer/Ansible build
    
    This now builds a Debian AMI using packer with Ansible as the
    provisioner.
    
    The primary components of this AMI are a lightweight Debian system
    with cloud-init, docker, and hyperkube. Hyperkube is a single
    binary containing multiple kubernetes components.

commit 9eb9b264b7e99dd9a796d1bfffd2b371e4e51c40
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 5 12:48:56 2017 -0500

    Add minimal README

commit 47b74995d7b0d7d4a6c0a18875b03be6807a80ad
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 5 12:49:53 2017 -0500

    Add `dep` config and dependencies in `vendor/`

commit c7da8924e5f6c30269bab4be035da2eece6aa456
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 5 12:51:33 2017 -0500

    Add main command with subcommands
    
    This project is inspired by the `protokube` service that is
    included with https://github.com/kubernetes/kops. Whereas
    `protokube` works as a continuously running service in a
    container, `keights` is a set of cli commands to be managed by
    systemd using timers and path units.
    
    The package contains three subcommands:
    
    `collect`: This collects network information about an autoscaling
    group and writes it to a file.
    
    `volumize`: This attaches an EBS volume with a specified tag to
    the EC2 instance the command is running on, and creates a
    filesystem on the volume if it does not exist.
    
    `share`: This writes entries to `/etc/hosts` so that hosts within
    the cluster can find each other.

commit 6ac71f68c38046284ff17f1688401e859c929d9f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 5 13:18:47 2017 -0500

    Add goreleaser config with .gitignore

commit 69081f0877d552dabe9140cfa507cf4bdfa588c5
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 5 15:20:38 2017 -0500

    Fix file renaming
    
    Files cannot be renamed across filesystem boundaries, so this
    puts the temporary file into the same directory as the file to be
    renamed, using a '.' prefix so that it is hidden.

commit d0045d6e31143e1d991f827bb12e19673083f720
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 5 15:22:15 2017 -0500

    Fix order in /etc/hosts; the IP comes before hostname

commit 1c3e464a800e6847fc7b1d93cd61dff55b68cc0c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 5 16:15:07 2017 -0500

    Fix file permissions issue caused by using `ioutil.TempFile()`
    
    Using `ioutil.TempFile()` creates a temporary file with a mode of
    0600. Instead, temporary file will be written to a temporary
    directory, so that permissions may be set on that file.

commit 9db77a567d7a3ffe34b1d769490b207d53c77e60
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Nov 6 13:19:51 2017 +0000

    Add fpm configuration to create a deb package
    
    Included with the package are systemd units for running keights
    commands on a schedule and when paths change.

commit 98e6d616ac3579e9648cbd636b54ea82751bc8c8
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Nov 9 04:45:35 2017 +0000

    Make autoscaling group name autodiscoverable
    
    Putting a reference to the autoscaling group name in the launch
    configuration of a CloudFormation template causes a circular
    reference, since the autoscaling group depends on the launch
    configuration. The keights commands are being changed here to get
    the name of the autoscaling group that they belong to at runtime
    instead.

commit 936c289d13b4377157bbe0fde6c5a8db328fa885
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Nov 9 06:18:22 2017 +0000

    Update systemd unit to go with change in argument
    
    The `-a` argument to `keights collect` has been removed, so needs
    to be removed in the systemd service as well.

commit e056b5065e341186f7fe4a371228cd22cace9700
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 11 15:23:37 2017 -0500

    Remove `WaitFor()` function into `helpers` package

commit aa67c5e9d9225aa8f7b0416d13b1ada3584ea969
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 11 15:38:10 2017 -0500

    Refactor `WaitForDevice()` to use `WaitFor()` helper

commit ce31cd922e41ac4265041bbd8835409b084a566f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 11 23:57:40 2017 -0500

    Change volume wait logic
    
    Sometimes `FindVolume()` fails because the volume hasn't been
    created or tagged yet. Change it instead to be `WaitForVolume()`,
    and remove the old `WaitForVolume()`, which is not really needed
    since `WaitForDevice()` is a good enough wait to know when the
    volume is ready.

commit 6b1bb5928ae43b4e684b7b7473b294467990ee84
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 12 10:04:13 2017 -0500

    Add Travis CI config, including publish with goreleaser

commit ba1b800db0d766d39e0387560a578860c738c2a2
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 12 10:20:05 2017 -0500

    Add Travis build icon

commit 33f3892da7f1da1747bd0da81a130d83979ccf2d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 12 10:31:23 2017 -0500

    Fix shell conditional logic causing build failure on tagged release

commit b674710af67dd0e21323fc7291cb320c61e27fca
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 12 10:36:16 2017 -0500

    Clean previous build before running goreleaser
    
    The goreleaser command will not publish if the repo is in a dirty
    state, so the leftover binary from previous step needs to be
    removed first.

commit ecee9c443ec9e0fb582c1077126af8aca4235a35
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 12 11:25:33 2017 -0500

    Add encrypted environment variable used for publishing

commit 3ecbdcd839c9ffd9f0e09dcc2c98ca2c2e26147b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 12 16:53:20 2017 +0000

    Fix shadowing of `output` variable in callback
    
    The `output` variable should have been filled in after callback
    was finished, but it was shadowed by using type inference to
    define a new variable of the same name.

commit 1cbc60062a27727d67e8f371ea7fd7d8b4ebe530
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 12 14:08:10 2017 -0500

    Move `InputToMapping()` into `helpers` package for reuse elsewhere

commit 8dd2d447e615fffb5f459d0b8c30a3724c1e5f79
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Nov 13 00:05:36 2017 -0500

    Add command to render Go templates
    
    The templates will be rendered based on variables passed on the
    command line using the `--var` argument, as well as some builtins:
    the host's own IP address, its index in an autoscaling group, and
    the mapping of autoscaling host indexes to IP addresses, as
    collected by the `keights collect` command.
```

Make a note of **forbidden words and phrases**:
  - wire/wired (as in "wired up" or "wired in")
  - surface (as a verb)
  - load-bearing
  - land/lands/landed
  - shape/shaped
  - phases -- Do not discuss project "phases".

## Step 3: Review the diff

Run `git diff --staged` and `git diff` to see what will be committed. Read the
changes and assess:

- **One concept?** A commit should be one logical change. If the diff spans
  multiple unrelated things, tell the user and suggest splitting.
- **No sweep-up?** Untracked files or unrelated modifications should not be
  bundled in. Flag anything that looks accidental.
- **Forbidden words?** Check for the forbidden words from Step 2 in the diff.
  They must never appear in committed code, comments, or messages.

If you find issues, tell the user what you found and ask how they want to
handle it before proceeding.

## Step 4: Decide amend vs. new commit

Prefer a new commit. Only amend when:

- The user says "amend" or "squash this into the last commit"
- The staged changes are **clearly** a continuation of the previous commit's
  topic (same file, same function, same bug description) and the previous
  commit has not been pushed

Never amend a pushed commit or a merge commit. Never amend without telling the
user.

If amending, use `git commit --amend`. Do not use `--no-edit` -- always show
the user the resulting message.

## Step 5: Write the commit message

Model the message on this voice (from the user's own commit history):

Rules:

- Follow the Linux kernel style for line length (summary 50 chars, body 72).
- Is a summary alone sufficient? Ask yourself if the subject line is enough
  to know what changed when investigating later, or if there is a significant
  "why" that can't be answered from the subject line alone.
- **Subject line**: imperative, present tense, plain English. Explain *what*
  changed in terms a new teammate would understand. No conventional-commit
  prefixes (`feat:`, `fix:`). No trailing period.
- **Body** (optional): the *details* if the subject line alone cannot cover it.
  Does it need this at all? This isn't the documentation; the log is mainly to
  determine what changed later and when. If a body is provided, optimize for
  clear readability, avoiding lots of punctuation and noise. And **ELI5**.
- **No emoji, no non-ASCII.** Keep everything ASCII.
- **No forbidden words.** Re-check the word list from Step 2 against the
  message itself.
- DO NOT put double spaces after periods.

Show the full message to the user and wait for confirmation before committing.

## Step 6: Double check the commit message

Is it ELI5?

## Step 7: Commit

Run `git add` for the specific files that belong in this commit (never `git add
-A` or `git add .` -- those can pick up secrets or binaries). Then:

```
git commit -m "$MESSAGE"
```

Verify with `git status` afterward.

## What not to do

- Do not `git push`. The user handles that.
- Do not `git add -A` / `git add .`.
- NEVER `git add -f|--force`. If it's in `.gitignore`, it's for a reason.
- Do not skip hooks (`--no-verify`, `--no-gpg-sign`). If a hook fails, fix the
  issue.
- Do not commit generated files unless the user says to.
- Do not rewrite git config.
- Do not commit secrets, large binaries, or files matching `.gitignore`
  patterns.
