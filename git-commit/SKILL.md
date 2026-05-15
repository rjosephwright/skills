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

Example commits from the `keights` project:

```
git log --reverse 0cf80e3d6c235a7329cee7708dadf75dbbb6c126

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

commit 7090d465df9b4c604659d853d5acedaf29ae3556
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Nov 16 00:49:42 2017 -0500

    Check for error after attempting file read
    
    Avoid race condition where a file disappears after doing Stat()
    and before attempting to read from it.

commit 942d74fa5947ed393ba1296cccf39702e4e3b507
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Nov 17 01:31:01 2017 -0500

    Add keights .deb package to AMI
    
    keights (https://github.com/cloudboss/keights) contains utilities
    for bootstrapping Kubernetes.

commit 9146de8fd3541424a0a4c120d992b62af27e9f0f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Nov 17 01:32:59 2017 -0500

    Preload Kubernetes docker images into AMI

commit 21a419e31579afbd4c97aca5b3841a96faeb2602
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Nov 17 01:36:59 2017 -0500

    Update README with information on passing keights version to build

commit 7ae89c050fa99c8484588a48497216974162a9a3
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Nov 17 01:54:03 2017 -0500

    Initial commit

commit 50af38ec00a1aa6a1b8497a2379ccdffb1de94c7
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Nov 17 02:44:10 2017 -0500

    Add CloudFormation template to deploy masters

commit d21338f9edb74391dba4345da98f21baa95582c0
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 18 01:21:18 2017 -0500

    Use cluster name as unique etcd token

commit f9928d66467606cf7b3193dbcd60b800286b203c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 18 11:22:26 2017 -0500

    Replace atomic write with ordinary write for /etc/hosts
    
    An atomic write involving a file rename does not get updated
    inside a container with a bind mounted /etc/hosts.

commit bb79ca7d84a5ee56ee08fb49502634858261dc09
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 18 11:40:13 2017 -0500

    Add api hostname to each host entry

commit 28a185c18b3b31c6cfe761fb81f492225a86492c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 18 11:40:39 2017 -0500

    Do not sort map keys for /etc/hosts entries
    
    If the /etc/hosts entries are always in order, then every node
    will connect to the same first host on the list. This will let
    them be distributed more evenly.

commit 1e7608fac1dc3709949035976be846a88e7229ab
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 18 14:39:12 2017 -0500

    Bind mount /etc/hosts for etcd
    
    If this is not done, then changes to /etc/hosts do not get
    updated inside the etcd container.

commit af6bdb31af53e0a48826aa9f837d48f0c1c95cc7
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 19 05:12:59 2017 -0500

    Add additional systemd services and template files
    
    These are being migrated from the `#cloud-config` of EC2 instance
    user data. With these unit files and templates included in the
    .deb package, the instance user data can be much smaller and can
    include mainly just drop-ins with required `Environment` entries.

commit 6c3033562c6a21fd953c0ce703704f9addcc030c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 19 13:20:38 2017 -0500

    Add systemd mount unit for etcd volume

commit ee56698e87ad49f16fd7402be383e52eecb48b42
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 19 14:31:22 2017 -0500

    Add file mode argument to `WriteIfChanged()`

commit bb3d73afa96eae3e36e12942401c7dc69ba738f7
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 19 18:28:26 2017 -0500

    Use atomic file write for templates, and only on content changes

commit 4479a569df3b493cfac3beec8bf7080b0a3665d0
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 19 18:29:37 2017 -0500

    Add tests for atomic file writing functions

commit 31148243cbcb35f509e9de7bf9653e58a8e34f21
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 19 18:31:50 2017 -0500

    Add `go test` command to Travis build

commit 6e0d05efe2659e35e4bf31c913901182b1574a8c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 19 19:13:42 2017 -0500

    Add etcd mount unit file to .deb package

commit 76662c74d792d3ef0aa96ed330112cc0e0e27c23
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 19 21:31:03 2017 -0500

    Remove explicit dependencies on `keights-collector.service`
    
    This prevents these services from running
    `keights-collector.service` every time they run, since it has
    already run and modified the file `/run/keights-collector/asg`,
    which causes these services to be activated through their
    corresponding systemd path units.

commit 5d2b6b83d095f95fec964e8a4df3244fb5f6a840
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 19 23:45:19 2017 -0500

    Allow the `collect` command's autoscaling group to be an argument
    
    This allows it to be used on hosts which do not belong to the
    autoscaling group.

commit ad14f267e3f452a109b64468ed7e630dbff1f9bb
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Nov 20 00:12:45 2017 -0500

    Add environment variable to systemd unit for keights-collector
    
    The variable has a default empty value, which allows it to be
    passed as an argument or not, depending on whether it is being run
    on hosts within the autoscaling group or elsewhere.

commit 60dd0a78afcb1e4fe023a5a826f9df000180dd54
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Nov 20 23:44:25 2017 -0500

    Allow index of "-1" instead of error
    
    For machines that are collecting data on an autoscaling group that
    they do not belong to, their own IP will not be found, and they
    will not have a "MyIndex" in that group. For this case, a negative
    index will be returned instead.

commit 6da423bb1957c478dc215d49c96855ff4c238f4c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 21 00:12:27 2017 -0500

    Remove `keights-collector` dependency on `keights-volumize`
    
    The `keights-collector` service does its own wait for volumes to
    be attached. Putting a dependency on `keights-volumize` means
    that it only works on hosts that themselves have an external
    volume attached.

commit 0e53dcaa5d18063ed4dd9a73874d4b4469fc21fb
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 21 22:21:11 2017 -0500

    Clean up user data
    
    Most of these files have been moved into the `keights` .deb
    package, so they are being replaced with systemd dropins that set
    the required environment variables.

commit 12803224789abc1264ba161f1bf6e6e078b8e0e8
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 21 22:24:23 2017 -0500

    Add apiserver security group
    
    The security group for the apiserver will be assigned to both
    master and node autoscaling groups so that they can all
    communicate, so the security group is being placed in a common
    stack to be created before masters and nodes.

commit 128bdb28c27e7ce91c297cc5715251913a5e4734
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 21 22:56:51 2017 -0500

    Add kubeconfig arguments for kubelet and kube-proxy

commit 24bf2c0a6c13ef1a237ffde096a1cf042844ecd5
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 21 22:58:31 2017 -0500

    Update master CloudFormation template description

commit 04fc485b36281f5563174bd66c2db7bd2c463117
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 21 22:58:50 2017 -0500

    Add `Name` tag for autoscaling group

commit dea6c3cd08dfefb022f6a95368f3df40f8bb532d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 21 23:01:53 2017 -0500

    Add CloudFormation template for nodes

commit a171cda37bb767f39524b8c86b214f31b019dfd8
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 21 23:50:31 2017 -0500

    Add "extra" opts for kubelet and kube-proxy
    
    Having an extra option will allow quicker testing from
    CloudFormation templates without having to build new AMIs.

commit cd99ada16624c370853337fd4522a1d326f942be
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Nov 23 12:45:33 2017 -0500

    Add basic `kubeconfig`s for kubelet and kube-proxy

commit ff91dc7a59eed6bf711aeb7c0cedb0720ddfeb32
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Nov 23 23:48:47 2017 -0500

    Add options to kubelet systemd unit for node labels
    
    The option `--register-node=true` has been removed as a default
    and is now replaced by the value from `KEIGHTS_NODE_REGISTER`
    environment variable.
    
    For a master, the option `KEIGHTS_NODE_REGISTER` should be set to
    `--register-with-taints=kubernetes.io/role=master:NoSchedule`,
    and for a node, it should be `--register-node=true`
    
    To label a node: for a master, the option `KEIGHTS_NODE_LABEL`
    should be
    `kubernetes.io/role=master,node-role.kubernetes.io/master=`, and
    for a node, it should be
    `kubernetes.io/role=node,node-role.kubernetes.io/node=`.

commit 0fe9ef510627fe1e91fe61921b2046f678c9b0bc
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Nov 24 01:10:18 2017 -0500

    Fix having service IPs allocated from pod CIDR

commit e15eca67d58efe9448f5d79e6b81edbb0508b350
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Nov 24 22:27:42 2017 -0500

    Update kube-controller-manager settings
    
    Add pod and service CIDR settings to the kube-controller-manager,
    as well as the systemd service to generate the config.

commit d581ced6834769da8c6fae5198a6adbd3c30c069
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 25 00:22:59 2017 -0500

    Replace kube-proxy with kube-router

commit 2d756e96bb900b5de2a5e8c07e3884b6d072f6ef
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 25 02:04:46 2017 -0500

    Replace kube-proxy with kube-router
    
    kube-router handles pod-to-pod networking and replaces kube-proxy
    as well. Adding it also requires adding CNI plugins.

commit 37cfd2ae1cd6abc9d115bdb6d8f7c44083b61068
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 25 02:05:26 2017 -0500

    Preload etcd and pause container images

commit 2ff2e636e848bc8b7bab9778f5732f9d99f0ec77
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 25 02:32:20 2017 -0500

    Fix kubelet argument to specify CNI bin dir

commit 0025b8a429a690d6ecf842c4109fd48a075af417
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 25 02:56:18 2017 -0500

    Replace kube-proxy with kube-router

commit 5e5f82f596158a62ca4422fb71f37e53172c844c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 25 02:57:06 2017 -0500

    Add and assign security group for kubelet port 10250

commit 75e7fa4d07a625f0621ff25b796639a500ae4f93
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 25 02:57:28 2017 -0500

    Consistify naming of kube-apiserver

commit 546fff0ee2cd0ae5e880d315f9dc08915d4e0874
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Nov 27 01:06:11 2017 -0500

    Initial commit

commit 8d2b0c03bf2050dd65a97f64eb0f11152ed478d9
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Nov 27 01:53:29 2017 -0500

    Add dependencies and dep files

commit b9ce910a2472595091f30d52efafd5b04b4d4724
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Nov 27 01:53:48 2017 -0500

    Add license file

commit b194f07f061ab7cf71279f9cfb91e533f6b21b4e
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Nov 27 01:54:36 2017 -0500

    Add package for sending responses to CloudFormation
    
    CloudFormation custom resources require a response to be sent to a
    presigned URL. This provides a function to send the response and a
    structure for the expected response format.

commit 3c2cb89d897efdf616c46f58953232999fc3c977
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Nov 27 01:57:01 2017 -0500

    Add function to retrieve availability zone from subnet ID

commit 83b614953b76a171aff43d468a3ab01f5aeb4bcc
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Nov 27 01:57:37 2017 -0500

    Add documentation for project and `subnet_to_az`

commit c7761f44b7fc51a9d5bc3e577a70a6d2f2ef9bcc
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 28 00:30:43 2017 -0500

    Refactor to make CloudFormation responses more reusable
    
    Add a function for creating a new response body with defaults
    set from a handler's arguments was added to the `response`
    package.
    
    Remove `sendErrorResponse()` from `subnet_to_az` package and
    replace with the more generic `FireResponse()` in the `response`
    package. This function can fire a success or failure response,
    but returns no value since the Lambda will exit after it has run.

commit 6da46ca432289a9ec4403597ebf2603854f2823a
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 3 01:23:36 2017 -0500

    Add cluster name option to kube-controller-manager

commit 0d857f5f9383ca4e4fb0aebeb6543fd3ffac07e7
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 3 01:24:04 2017 -0500

    Add cloud settings to kube-controller-manager

commit 6f45a43008f86d9e1551e0323fafdecea59a8f46
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 3 01:31:35 2017 -0500

    Set verbository for kube-controller-manager and kube-scheduler

commit ba992980411f2a17685f2c5f80a8ee62a5523a0b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 3 02:02:13 2017 -0500

    Add cluster name to kube-controller-manager templatizer

commit e598e31c3fb00d3922049d669a1e0438d863184e
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 3 02:16:14 2017 -0500

    Rename project

commit bedf0acbee223f1b20eb10ded9d9ffb4594db565
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 3 14:15:10 2017 -0500

    Only do work on `Create` request type
    
    This custom resource does not actually create resources and is
    only useful during creation. For `Delete` and `Update` requests,
    it can simply send back a success signal.

commit 7637c712555516189ee3890bfffdb5a5660b28bf
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 10 15:51:26 2017 -0500

    Update to newer SubnetToAz Lambda archive and handler

commit 5ab025e804f544027b0629cc0dc0f4b26591a6c4
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 10 15:52:05 2017 -0500

    Add load balancer for API endpoint
    
    Presently this is using HTTP, but will be updated to use HTTPS
    when the CA is automated and services have been updated to use
    certificates.

commit 3bbb15bf275fae73de8b6cd2c40542b7b1c8e3a4
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 10 23:14:44 2017 -0500

    Remove unused `Region` parameter

commit 0c9b5035b7ea27d424f544f30027fb39082dcf07
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 10 23:16:07 2017 -0500

    Remove unnecessary `Region` parameter to Lambda

commit 0b688db9b1f815f27461266d01db6f40f8ca6a63
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Jan 10 01:34:33 2018 -0500

    Make responder an interface, for testability

commit 8aa234e3263dc7a205d89fdf28fc99e42bf54fff
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Jan 10 01:40:57 2018 -0500

    Update vendored dependencies with dep

commit ee7497991f137880a8bba00e6bb4f3aeac35226f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Jan 10 01:41:52 2018 -0500

    Add mockery generated mocks

commit 48193be891a6bf5d5d1dc72b89309c1fa6baf874
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Jan 10 01:45:06 2018 -0500

    Add kubernetes CA lambda handler
    
    This handler acts as a CA for a kubernetes cluster, generating the
    CA cert, and then using it to sign certificates for all of the
    controller components. All of the artifacts are stored as SSM
    parameters.

commit 2caf85c1a0474295f0432e68cdba93d53ad1f78d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Jan 10 02:01:19 2018 -0500

    Remove extraneous "k8s" string from names

commit d30722433f6d8a34933f1a8a1cacf7a21380a0c0
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Jan 10 02:05:53 2018 -0500

    Fix wrong SSM path to CA certificate

commit 34c55ae1012a6568a2cca788046a53d42c8f27fe
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Jan 10 22:00:06 2018 -0500

    Remove `KEIGHTS_ASG_NAME` environment variable from systemd unit
    
    Because the environment variables for the keights-collector
    service are configured through a launch configuration, putting the
    name of the ASG into it creates a circular dependency. The
    keights collect subcommand has already been updated to do a lookup
    on the autoscaling group to get the name, and it needed to be
    removed from the systemd unit.

commit 9390c4d80e9ab41ea1630515e81296f9d203c765
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Jan 13 16:01:43 2018 -0500

    Add `keights whisper` command
    
    This command retrieves certificates and keys from SSM parameter
    store and writes them to locations under /run. This commit also
    adds systemd services for running the command to retrieve the
    parameters as part of the keights-configure sytemd target.

commit e6e823f1815eaf03323b2e0f4676930385cdd146
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Jan 13 16:09:34 2018 -0500

    Remove vendored test files

commit ebc46453d123690709d52edd75a1a100c0adc7cd
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Jan 13 16:19:26 2018 -0500

    Add systemd units for `keights whisper` to .goreleaser.yml

commit 0360462b62d987793fa6d235833960d44ad374d2
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Jan 13 21:38:43 2018 -0500

    Fix assignment of command line flag
    
    The `-p` flag was being assigned to the `collectCmd` rather than
    `whisperCmd`.

commit 2864c3bf56010384dec78a7e61d54fd3bda814ab
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Jan 13 23:22:13 2018 -0500

    Remove bootstrap token from whisper systemd units

commit 1023b7a7cdb1f514ab33a9ea996ef07c9540a69c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 14 14:55:04 2018 -0500

    Enable ssl for etcd

commit f5b955391e0f6246e1db435ce901eab7ac46dcd6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Jan 15 23:56:52 2018 -0500

    Start dockerd with overlay2 storage driver before importing images
    
    The images become orphaned if this is not done, although they still
    remain on disk, and they end up getting pulled again when
    containers are started.

commit 9ca176d3014772466799cc085b67f05b5812d406
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Jan 16 01:34:34 2018 -0500

    Add certificates for etcd

commit 67302ccb4cd9de9c093a289408698ce0ecaf9e9d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Jan 16 01:39:58 2018 -0500

    Delete vendor test files

commit 795d737ee43ce33b680a146c158a1b610e6ce38e
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Jan 16 22:42:55 2018 -0500

    Update to use official Go Lambda runtime
    
    Context argument is removed, as it is no longer a required
    argument unless there are two arguments. The log group name is
    now retrieved from the `lambdacontext` library instead of the
    context object.

commit 5b60492507114189fdb570b0f2d91027c6852cac
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Jan 16 22:46:21 2018 -0500

    Add `SendData` method to interface
    
    This is to send a response back to the CloudFormation stack
    containing actual data, rather than simply Success/Failure.

commit e12ddbd4bae72b5dd5996feb963891e459167950
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Jan 16 23:09:53 2018 -0500

    Replace eawsy lambda package with official AWS Go sdk

commit 1cb16ecc101d18cbe7f37ea22852f7fd8146b586
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Jan 16 23:10:22 2018 -0500

    Update Gopkg.lock with new lambda dependency

commit 21b1d9f09bef9afde0821748bcdca6f8fb706ea3
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Jan 16 23:11:16 2018 -0500

    Update kube_ca and subnet_to_az lambdas to use offical Go runtime

commit a9167ec274aac2cb608e4977f6f05550eec41f14
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Jan 27 23:32:25 2018 -0500

    Add host to liveness probe for etcd

commit 588493d05209723c21e3d159f007142ed1c60934
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 28 00:06:55 2018 -0500

    Begin adding ssl for apiserver and controller-manager
    
    This breaks kubelets on nodes from joining the cluster for the
    time being, except on controllers, where the apiserver security
    has been loosened to allow local connections over HTTP. This will
    be disabled after TLS bootstrapping is in place for kubelets.

commit bdc1c261b9726ac84d02ff3c2872a8486fb5e363
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 28 01:01:01 2018 -0500

    Put SSM handling code into its own package for reusability

commit 59640127237d0c157b545fcfbf9cf06f2ca1dc25
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 28 01:24:19 2018 -0500

    Modify `kube_ca` to use `whisper` package for secrets
    
    The `whisper` package handles SSM parameter storage and retrieval.

commit 9d51241aeb6e66f93e5c481987e0c9f3b1eb8235
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 28 01:27:07 2018 -0500

    Add new vendored dependency
    
    The `kubeadm` token generator will be used to generate bootstrap
    tokens to be stored in SSM Parameter Store.

commit eaf5fdb04d3db9af908ad7d9fc3e66ae0d385eda
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 28 01:28:16 2018 -0500

    Add bootstrap token to `kube_ca` secrets
    
    Although the bootstrap token is not an SSL certificate, its
    generation and storage is being added to the CA, since it is a
    convenient place for it, though it may be moved later.

commit 9e39ddf33b85bf9206e56e70aad6965a5f92448c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Feb 3 01:48:42 2018 -0500

    Add TLS bootstrapping for kubelets
    
    New systemd units have been added to do the initial setup for
    TLS bootstrapping of kubelets.
    
    * keights-bootstrap-token-splitter.path - Watches token file
      for existence or changes, to cause corresponding service to
      run.
    * keights-bootstrap-token-splitter.service - Splits token file
      that has been retrieved from SSM parameter store into token
      ID and token secret.
    * keights-manifests.target - Target to ensure manifests are
      created before starting prekubelet.
    * keights-prekubelet.service - Starts kubelet once to get it to
      start k8s controller components, then stops it.
    * keights-boostrap-kubeconfig.service - Creates the kubeconfig
      needed by kubelet for bootstrapping TLS.
    
    Other updates have been made to existing services, such as the
    "templatize" services, to target keights-manifests.target instead
    of keights-configure.target, so they will run earlier.
    
    Templates for controller component manifests have been updated
    with new options to enable TLS bootstrapping.

commit 727fbcf1fd656082b040ec7566d88c4c96bd1a6c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Feb 3 01:53:01 2018 -0500

    Remove old pod-cidr option from kubelet

commit 346d4186985e33c68c285476f76401887dab95df
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Feb 3 01:53:33 2018 -0500

    Cloud provider should be passed explicitly to kubelet

commit a84f1ae253f209e243384ec36040fa8d145a8d8e
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Feb 3 10:53:50 2018 -0500

    Add new systemd units to goreleaser config

commit c2ada60c4bb63f3a33f7b96bfa509fa6a5c1e44b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 4 21:42:12 2018 -0500

    Update keights-bootstrap-token-splitter.service to remove newlines

commit 62dd515a266939df835143294b9e80dd0efd5c29
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 4 21:43:20 2018 -0500

    Ensure token splitter runs after token is retrieved from SSM

commit 9ff80b013bc9f10b1c2c981e31fdceda6b15e07a
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 4 21:44:24 2018 -0500

    Add required cluster role bindings in addition to token secret

commit cf8fde5c0a87b991b36a17e4918129e9ed98bbd5
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 4 21:54:30 2018 -0500

    Remove variables to be set in systemd drop-ins

commit 87bdd7a5f877d60f59b6206608d37066c6345a6e
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 4 23:50:48 2018 -0500

    Let certs live on /run in-memory filesystem

commit d38f298b02dd9e673821e5d308b9e38d4d1062c2
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 4 23:51:33 2018 -0500

    Use existing variables for kubeconfig and user values

commit 4817e00c492a15cc8d587a331acb766dd90873cc
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 4 23:52:13 2018 -0500

    Token secret needs full token value including ID

commit df888ec77c40a0f664674fcc63de6a0361f7e2c1
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 4 23:52:43 2018 -0500

    Remove unnecessary description

commit a5b286b3fe55ca06f540d15bff1b8f52f8ef8d90
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 4 23:53:25 2018 -0500

    Remove `Requires` and `After` sytemd dependencies
    
    Let dependencies depend on target, not the other way around.

commit 7e2149d9549176f9438e52114181d50d5bcac134
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 4 23:55:19 2018 -0500

    Use more exact dependency which requires the manifests target

commit ba732c295432a9ffd9ce9e342bccd03d769315ae
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 4 23:57:43 2018 -0500

    Remove SSL connection to kubelet for now

commit 45726199a5ca46b971b6e2493c003c767b044c96
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Feb 5 00:53:18 2018 -0500

    Change to connecting to master on localhost
    
    The exposed IP endpoint is now SSL, and the HTTP endpoint only
    listens on localhost. Until all components are using SSL, change
    to connect to the master on localhost using HTTP.

commit 79aa98a94bb262cce560ea5f0163a507e2f5a7a7
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Feb 5 01:04:41 2018 -0500

    Remove dependency on `keights-whisper-controller.service`
    
    The bootstrap token splitter service already has a systemd path
    unit to trigger the service, and does not need a dependency on
    `keights-whisper-controller.service`, as that is a controller-only
    service. The bootstrap token splitter needs to run on both nodes
    and controllers.

commit f10721dfaababd5936d698424643d14e27973a4a
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Feb 5 01:07:28 2018 -0500

    Let kubelet use the default cert locations
    
    TLS bootstrapping takes care of certificate locations for the
    certificates used to talk to apiserver.

commit e166d1adcf1cdc52f5667725edd2109e3e6bbae6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Feb 5 20:49:27 2018 -0500

    Remove while loop checking for path
    
    The corresponding path unit should ensure path exists before the
    service is started.

commit a1dd0914e67f715734f2e936f4b4d83806e78bf9
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Feb 5 20:50:33 2018 -0500

    Remove `RemainAfterExit` so system may be called multiple times

commit 32d4daffbebf4a0116c95383381f2f651c72a749
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Feb 5 21:09:41 2018 -0500

    Remove all `RemainAfterExit`s from units for easier restart

commit b07fd4a64325a17a4f6a881747f34a5d3afed76f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Feb 5 21:11:26 2018 -0500

    Make `keights-bootstrap-kubeconfig.service` be activated by path
    
    This can safely run as soon as the bootstrap token file is
    available, and it is simpler to depend on a path than other units.

commit d484d0c8560d87f03c477818fd572af19b55322c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Feb 8 01:06:44 2018 -0500

    Update kube-router to use client cert
    
    Add new systemd service to generate kubeconfig for kube-router
    client cert, and add certificate paths in SSM for the whisper
    service to retrieve.

commit 8653b23678812cc85fdb365b06b7b069b7d232b6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Feb 9 22:06:39 2018 -0500

    Add RBAC resources for kube-router

commit 8fbb1560f2d3f122073db705bc7785e2bbc3776f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Feb 9 22:08:32 2018 -0500

    Only run keights-prekubelet on controller with index of 1

commit 7d1ceede467f49ed87a5894210f569d3fbdbbf0e
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Feb 9 23:08:43 2018 -0500

    Add kube-router RBAC configs and systemd unit to goreleaser config

commit 16d44e2731f25cc5c9f89616f22b85f2b1274ff6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Feb 9 23:43:35 2018 -0500

    Add kube-router private key to be retrieved from SSM

commit 5a6ffd79dd17150858cd3ca5db8189a14c08de5a
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Feb 9 23:45:28 2018 -0500

    Add kube-router-kubeconfig systemd service unit to goreleaser config

commit c8ac00705b4a6e27bbbccdfb33ece9b8d72dd32b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Feb 10 12:31:07 2018 -0500

    Add `Requires` to keights-prekubelet.service

commit 8ebf89791adbfa10f1f3b98aac5b7d659a829326
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 11 02:05:50 2018 -0500

    Add file lock between services running kubectl to prevent conflicts

commit e3299a215eedeae3d807b4c33e5f069e6c5412e8
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 11 02:12:49 2018 -0500

    Reorder systemd dependencies

commit ac5d75a572e08dcdb8073a7d44c69601ae12ed03
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 11 11:04:16 2018 -0500

    Run keights-prekubelet on all masters to get etcd running early

commit 0c7b307fc5976089585844885d2cb26dcbf24907
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 11 11:05:19 2018 -0500

    Add `-s` option to curl for consistency and better log output

commit e3f16041bf0378a2efad1f04b9d950fb3717409c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 11 16:21:43 2018 -0500

    Add recommended admission control plugins to controller manager

commit 2929e7d5eb739696006f8158374c128d39037364
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 11 16:22:24 2018 -0500

    Add root CA file, needed for ServiceAccount

commit 4f3913ade95fc165597ac41fa43a82d46185c4d4
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 6 23:22:10 2018 -0500

    Enable SSL
    
    The apiserver now has SSL enabled, as well as SSL bootstrapping
    of kubelets throughout the cluster.
    
    The load balancer was changed to be a network load balancer, doing
    a TCP pass-through to the apiserver, since the certificates are
    signed by an internal CA. The CA is deployed as part of this stack
    via a custom CloudFormation resource backed by a Lambda.

commit 5ea9d2539f25ac490f7a43c9e06bcd4f7b2dd6b3
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 6 23:26:32 2018 -0500

    Pass full images as parameters instead of just k8s version

commit 23c24722f5151b1766ee1d1978b70107fb852b82
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 6 23:27:44 2018 -0500

    Add rolling update policies to autoscaling groups

commit 739964236af14fd094acee7a30c93a053e20bc83
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 6 23:28:18 2018 -0500

    CloudFormation rolling updates now temporarily increase MaxSize of ASG

commit e57dcbdded82f5dcf334c20cd5932a00734e59db
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 6 23:33:44 2018 -0500

    Add Image ID as default

commit f9e1bf5de1b7d010dfd8268f5c8bce13a9cae9b5
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 7 00:15:32 2018 -0500

    Make clear which IAM parameter is an ARN and which is a name

commit bd07cb8604fc40b0ac2020005739af99218adefb
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 7 00:40:23 2018 -0500

    Pass images as parameters for etcd and kube-router

commit f6829d0d51651e0cd9e25064313c09573e9c9e50
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 7 01:20:34 2018 -0500

    Make parameter description more clear

commit 68499646aa42f890ff025153ee77a83f9e37f380
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 7 01:21:26 2018 -0500

    Give default value for ImageId parameter, as in master stack

commit 24d6568990727761bd8f07acc748c7d1efd79554
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 7 01:21:51 2018 -0500

    Pass image as a parameter for kube-router

commit 129b4fdbdb7cff096fe131a892fee8f820ee4770
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 7 01:22:23 2018 -0500

    Add IAM roles and policies to common stack

commit 770e5826c44d1cfafd81dab065d2d06434dd3557
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 7 01:32:00 2018 -0500

    Add script to set up kubeconfig after stack creation

commit b582e100e1bfd8e274517ee37a9c217b5d3909ea
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 7 22:32:40 2018 -0500

    Remove PodCidr parameter which is no longer used

commit ade767cb76ebf33342dcef35ce548f70a285dc5c
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 7 22:33:05 2018 -0500

    Make etcd volume size a parameter

commit 66fef85c2d4205adb80c698892e8815d267ec5e5
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Fri Mar 9 00:39:23 2018 -0500

    Download goreleaser tarball instead of using `go get`
    
    This avoids errors from tracking goreleaser master branch.

commit 01f4979f8e7d269c991bc5bef586df2d3bbd04dc
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Fri Mar 9 00:39:23 2018 -0500

    Download goreleaser tarball instead of using `go get`
    
    This avoids errors from tracking goreleaser master branch.

commit 335bb7f9649a3686bcde010551569d77f4fd23c2
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Fri Mar 9 00:51:04 2018 -0500

    Add wait loops for file to exist
    
    These are put in place to "supplement" systemd's head-scratching
    ordering of services. Intermittent failures occur on reading from
    files that should have been created by services which should
    have already run.

commit cf020afe1442e6b0dbc32758fc5f42486bf817be
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Fri Mar 9 00:56:15 2018 -0500

    Use `flock` for consistency with other "kubectl" services

commit 74fe7f9dbe0d1b2c37b2365b00e19a0c912e76f4
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Fri Mar 9 01:20:37 2018 -0500

    Move goreleaser out of repo to prevent dirtifying it

commit 6f8b8209764398573845524ce0901422f4ab7ab5
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Fri Mar 9 01:27:48 2018 -0500

    Get tarball out of repo for more dirtification prevention

commit efb2b7d208b0334521073d92686f46f5ba892611
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Fri Mar 9 09:11:43 2018 -0500

    Update ca-certificates on build instance

commit 9f7abb62f6c862395841058d05e7c2d43f016bf0
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Fri Mar 9 09:13:36 2018 -0500

    Update default AMI

commit 05a69b3398b476997276d8c776c09d6ceed92154
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Fri Mar 9 09:13:47 2018 -0500

    Allow nodes to be labeled

commit d6804023dc9e1b4c201f0d4283c74daa3a0ed9c8
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Fri Mar 9 21:23:54 2018 -0500

    Modify wait time for volumes
    
    Increase wait for EBS volume from 10 minutes to a default of 60,
    and add a command line option to change the value.

commit 1399d4a9e5c3d3ca740023936b3a99d708f8cb19
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 10 10:26:41 2018 -0500

    Pass API server as hostname instead of constructing it
    
    This allows for more flexibility in how nodes receive the hostname.
    Nodes will now be changed to use the load balancer to talk to the
    apiserver, and no longer have to write `/etc/hosts`.

commit 24ec9e89a89f0ba0bbea54f1d7aa8336473e24cb
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 10 11:19:00 2018 -0500

    Switch kubelets to connect to apiserver via load balancer
    
    `ImageId` is updated with systemd unit changes to make this work.
    The `keights-collector` and `keights-share` service is now removed
    from nodes as they no longer have to write a hosts file with the
    apiserver IP addresses.

commit 95b893de3dc348aefcf9df11bcf5e5a7a04cb3b8
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 10 11:21:29 2018 -0500

    Remove all but `HealthCheck` process suspension during stack update

commit c9b10a5621daaa6ef80077ec3ddf5414b0b19a2d
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 10 11:49:52 2018 -0500

    Make image public

commit b28953982316bca5bc37f8d3e75e3691785deb5b
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 10 14:20:37 2018 -0500

    Add new vendored dependencies

commit 03c5d1c9bdfed4934ec891063ddbbd98e6f0caa5
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 10 14:39:51 2018 -0500

    Add kubeconfig generator for bootstrap and kube-router

commit bfea76b103bab4bff82be90011c9e0a578642f48
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 10 16:59:00 2018 -0500

    Add `prekubelet` subcommand
    
    This is used to bootstrap the apiserver so that the kubelet can
    bootstrap TLS certificates via the apiserver. The kubelet is first
    started, allowing it to bring up the apiserver and other
    componenets, then when the apiserver is healthy, kubelet is shut
    back down.

commit 67031f3f266bd86aece3f2c91b92fdcbc944e400
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 11 21:46:57 2018 -0400

    Remove unused parameter

commit 244b2208650c532824ce2941b09c729dea1bad91
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 11 21:51:54 2018 -0400

    Make parameter description more descriptive

commit 6ce514f1737fd44859e82b272f11bffc1da377c9
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 11 23:47:32 2018 -0400

    Update vendored dependencies

commit 159459f799dd94adb1929d5db273c37141e29bed
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 11 23:48:50 2018 -0400

    Add `bootoken` command for creating bootstrap token with RBAC

commit 439aa8c21b7d8a60fdb9ab7b0c225c18ac2449e0
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 11 23:54:34 2018 -0400

    Update SSM mocks since AWS SDK was updated

commit 3d359ec2c40e1a4f0a4af4755ff5bc91b63307fc
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 13 00:23:47 2018 -0400

    Move `MyIndex()` and `MyIP()` into `helpers`

commit 9814bf569dbf8c5660181e02f98df20a72a766e9
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 13 00:45:41 2018 -0400

    Add `IsIndexOne()` function
    
    This function tells whether or not the local machine is "index 1"
    of an autoscaling group. Indexes are assigned by attaching EBS
    volumes with a tag whose value is a unique index.

commit 6f5ebe6d53ae8464e43b259fe8478b75dbe193ea
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 13 16:32:56 2018 -0400

    Switch to Classic elastic load balancer
    
    Network load balancers do not support access over a VPN or peered
    VPC, so for now the classic load balancer will be used.

commit a229f4e179552a995b05183ad5e2ef84fff2955e
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 13 23:23:30 2018 -0400

    Consistify spacing

commit eaf314abb47c10e37a89d4b1039071a9b9efa1cb
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 14 00:05:12 2018 -0400

    Add filter to DescribeInstances to return only running instances
    
    This should speed up the time to get back to a healthy state when
    autoscaling group membership has changed.

commit 293140eff342609cbca8c1971b82df251410d2d4
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 14 00:30:01 2018 -0400

    Add explicit exit 0 so unit does not report failure

commit 9d7e5f56071f5ba339af49b133f979852b6d4b5c
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 14 00:51:22 2018 -0400

    No longer depend on prekubelet, just collector
    
    The while loop doing a health check should be enough to determine
    when it is safe to run `kubectl apply`.

commit 549ebf778c75c25de4fd05f8449147e077d9b49e
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 14 00:54:08 2018 -0400

    Switch from `kubectl create` to `kubectl apply` for idempotence

commit 01ab7fd3ccb16c109986899ee6661fe994f73324
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 14 01:07:58 2018 -0400

    Change unit to type `simple` so it can be restarted on failure

commit 0ca18f7e482e1e2c84887b66d3e14302b4475039
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 17 14:15:44 2018 -0400

    Remove SSM agent which has not been used

commit 99b70fb9cde045a75d8e4c3f77ac22105a7eeccd
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 17 17:19:37 2018 -0400

    Use annotations and health check from kubeadm generated manifests

commit 1a270c5d25122e4990918b3fec2992413a546d78
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 17 17:29:39 2018 -0400

    Move etcd certificates to /etc/kubernetes/pki

commit f33d9b36e29f086b96910f976f7627460369afdd
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 17 17:59:12 2018 -0400

    Remove files not needed after switching to use kubeadm

commit 906072a3eaeaabef6fc657b6aa78971ce70427f8
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 17 23:46:37 2018 -0400

    Remove secrets paths no longer needed
    
    Kubeadm will generate the certificates that are no longer being
    retrieved here.

commit 5f3d59224908820db8deb0dad4378aaba70745a4
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 17 23:49:13 2018 -0400

    Add `/etc/kubernetes/pki` to systemd tmpfiles

commit 9ad05c7c7103aee06178f84e63c8004168c37357
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Mar 17 23:50:09 2018 -0400

    Remove kube-router secrets from node

commit a0b2b0485c0d033a58c8bf12d269889e6eafee87
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 00:26:51 2018 -0400

    Add template and systemd service to configure `kubeadm init` config

commit 8b95904e52ac6618dafc6953913fa6e773d06dba
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 01:35:23 2018 -0400

    Add systemd services to run kubeadm

commit 7531be8b82f004712b2e6a665dcebb05cfbea5c4
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 01:37:02 2018 -0400

    Update systemd dependencies

commit c35477a6ff69aafaf0923a730eaae7fa4b2ff36f
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 01:43:04 2018 -0400

    Remove unused files

commit a2bb7d164fa282098b0838586ae460750d99b135
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 01:46:37 2018 -0400

    Update goreleaser config with new file paths

commit cc83efe10fa339f1ee4098f89e84b7ddb6e58483
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 01:53:55 2018 -0400

    Rename `manifests` to `templates`
    
    It is more accurate to use the term `templates`, since not all of
    them are manifests, but they are all templates.

commit 01250e096f22564c7119e2f24d5eb05d072cb817
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 02:36:10 2018 -0400

    Use Hyperkube image for all kubernetes components

commit 66190cd3a34b7e30a0605c9b09c2a311780a5bd2
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 02:50:08 2018 -0400

    Remove reference to deleted kube-router files

commit e035ea1a0c2876b224fd73f979802c7cb50124a5
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 13:28:40 2018 -0400

    Continue using TCP health check for etcd for now
    
    Until certificates are generated on the masters themselves with
    the local IP as a SAN, HTTPS health check will fail because
    kubelet connects over the IP. For now, use TCP health check as a
    workaround.

commit 35019b12a57066ae24e3ffe573ebf40abd123231
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 13:40:10 2018 -0400

    Change `.yml` to `yaml` to be consistent with kubeadm

commit aa94f9594675ab9babf53c521e6208439ad3a651
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 13:48:28 2018 -0400

    Add restart for kubeadm node join service

commit 2371656886c13af555fa9583a330e7969e311574
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 21:26:46 2018 -0400

    Add addon-manager with default addons
    
    This stack will deploy with kube-router as a DaemonSet and the
    kubernetes dashboard by default. The addon manager will be deployed
    as a static pod on the masters, and will start the addons from
    there.

commit 7cca7f670114cb633cd7257f47bb23120a5c1c7c
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 21:45:44 2018 -0400

    Pin image versions for kube-router

commit 5661224617d83b4df171bf48dfe823f5925bea37
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 22:08:34 2018 -0400

    Move missed template renames from .yml to .yaml

commit b0c38414d29c0fd874da98f582edc80feccef8a3
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 22:21:17 2018 -0400

    Add kube-router missing from goreleaser config

commit 3c968c8c6b3c58fb49e6b4b1a562bd533b235bd5
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Mar 18 23:52:54 2018 -0400

    Fix more fallout from the Great Yaml Rename

commit 728c98fac9a765a07c5ecd15f870fea456759fd7
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Mon Mar 19 01:05:19 2018 -0400

    Remove HTTP health check for kube-router
    
    This should be supported in the upcoming release, but for now a
    TCP health check will be used.

commit f924e1a003a6b212a80c8ae96a032281f4ef7231
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Mon Mar 19 01:39:40 2018 -0400

    Use the -amd64 image for addon manager, to be consistent with others

commit 8c194f453a7311e8fef2c96737c6cd88d1115272
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Mon Mar 19 01:48:54 2018 -0400

    Re-add `sa.key` and `sa.pub` back to keights-whisper-controller
    
    The service account signing key must be the same for all masters,
    so it will be retrieved from parameter store like the other
    shared secrets.

commit fb7853eee08752a28f375c01bdd7712cc6d6ef51
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Mon Mar 19 08:44:09 2018 -0400

    Remove kube-router liveness probe
    
    Even with a TCP liveness probe, kube-router fails the check. Wait
    for the upcoming release to re-enable it.

commit 2f68e9ee90251c7ea90b5268d58ba9745472f356
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Mon Mar 19 08:58:07 2018 -0400

    Change templates to support new kubeadm-ified AMI

commit c079d772c634186c5a83192f4f933407f550c336
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Mon Mar 19 09:00:56 2018 -0400

    Update docker image and AMI IDs

commit 1fd40c0e29dd8030f2b0c156dc3f992ee223a1f2
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Mon Mar 19 09:03:56 2018 -0400

    Update to use kubeadm based initialization
    
    The kubernetes binaries that were downloaded from a tarball are
    now being installed via the Ubuntu repository, and docker images
    are pulled from the remote repo rather than the image tarballs.
    
    Additional packages required to support the changes were added.

commit 4ab9592a17439f938b1929717367bbcb1eea7007
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 20 00:04:37 2018 -0400

    Pass Kubernetes version as argument to `kubeadm init`
    
    This prevents kubeadm from doing a version lookup over the
    internet, which may require a proxy in some sites.

commit f2779efaa94215fd43cde371d8fb3a3128619b50
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 20 00:22:40 2018 -0400

    Move kube-addon-manager.yaml out of /etc/manifests by default
    
    A new systemd unit `keights-add-addon.service` has been added to
    copy /usr/share/keights/kube-addon-manager.yaml into the manifest
    directory for only the (master) hosts that enable it.

commit d6fa43be44a25657615add6d58223590295c5f7d
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 20 00:29:03 2018 -0400

    Update goreleaser config with new files

commit c13054f3418db33a1fd5f9429b4f29dd830173ee
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 20 01:23:49 2018 -0400

    Pass Kubernetes version to `kubeadm init`

commit 8ab65061f7f3708a0f7b1bb5e031172a1698a520
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 20 01:24:09 2018 -0400

    Start service to copy addon-manager manfiest on masters

commit 5310949860b8358ef941468bb8f321e11d2a748b
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 20 01:24:47 2018 -0400

    Preload kube-dns container images

commit 299fe6bee3b74dac08d549ea765abbc3efebc4c9
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 20 08:54:08 2018 -0400

    Add DNS domain for kube-dns

commit 3c1531b7070e22d1e0f1b7ef6890a1667b82ba2d
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Mar 20 09:08:43 2018 -0400

    Update default AMI ID

commit b5cc1aca0efb4d3a9f1cdd0e876161f8cfec9726
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 21 00:27:16 2018 -0400

    Use gcr.io dashboard container to sync with images in AMI

commit 8e1ac79ad16e4ae77cea65c80db64f3ffabbe249
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Mar 21 01:43:55 2018 -0400

    Add "standard" storage classes

commit 3d90b43215f201c8def89545eaa52dc50caa3bd5
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Mar 22 01:13:21 2018 -0400

    Use variable for device instead of hard coded values

commit b0a7f16dfadb7c56b71caa3631c830dfb5cecc92
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Mar 22 08:39:47 2018 -0400

    Increase EBS volume size and add docker partition

commit 07ce3518f93dd477d755c7b72014d5b70496ee36
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Mar 22 08:40:55 2018 -0400

    Add missing apt sources file

commit 8cd3f098fe4d4aa635cf44e82e382169fe713861
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Mar 22 09:10:25 2018 -0400

    Put device mappings together in Packer build.json

commit 61343c1fcfe20f56bf1bd134b4bfc538b85e1485
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Apr 3 22:03:20 2018 -0400

    Pass device argument to `keights volumize`
    
    This makes configuration from CloudFormation easier, so that
    the systemd unit does not have to override ExecStart.

commit 430f3a9ace7f9686f62bdd930ca3e296f7780729
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Apr 3 22:39:26 2018 -0400

    Update goreleaser and remove fpm dependency

commit 23358ec5736ee29843efb582916bb750131e278c
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Apr 3 22:39:42 2018 -0400

    Upgrade Go to 1.10

commit da983d880775af207243c6e0853a9094354a7388
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Apr 4 00:07:17 2018 -0400

    Update default AMI to keights 0.10.0

commit 88f0fff1190b0b2bc7482e79752fccc967f2cce9
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Apr 4 00:08:04 2018 -0400

    Add value for device variable since AMI disk layout has changed

commit 4640e23443741b1df3aa07e8b926d1320a28faae
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Apr 4 00:08:31 2018 -0400

    Fix old references to kube-router security group

commit 6dcabac7e45dbe1e86f110e4d6fce35317d868c6
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Apr 4 00:08:48 2018 -0400

    Allow traffic in cluster on service ports

commit 249ac0a0cd660d3efe0b3df65283af30b9113bf1
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 5 01:32:55 2018 -0400

    Use separate EBS volume for docker mount

commit fdec971fcedf3b8209b50feead0f3b4937ce7e00
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 5 01:35:22 2018 -0400

    Rename `ebs_vol` variable to `root_vol`
    
    Since there is now another EBS volume for docker, be more specific
    in the naming of the volume.

commit 3bab9375d25ed80f352eaae983cd7f086403c0e7
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 5 01:36:57 2018 -0400

    Sync with link local Amazon NTP server

commit 9cfb4ae3f310e11608e540154c2a848510eb3acc
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 5 01:40:54 2018 -0400

    Update image version for kube-router addon

commit ec4c346317c074b6a63f879872f2f55daf9e6f48
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 5 02:09:37 2018 -0400

    Remove obsolete "when" conditions

commit 20b4efb491e608dbe1d92ad16bd543307bc3de5b
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 5 02:10:13 2018 -0400

    Update copyright dates

commit ddc883fdf467bd08401daa744ff294bc8c93f37a
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 5 02:13:18 2018 -0400

    Override device for etcd mount used by new AMI

commit 9d1fd62aea2720a35c67f9c6082ea43fe45dba0d
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 5 20:08:32 2018 -0400

    Move license file from subtree to root of repo

commit fd73b78accab4eb0aeda2a69b5d9b763f0f9f93f
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 5 20:12:09 2018 -0400

    Update copyright dates

commit a41f2f8f97a7c4c25c97beb607b012663f3e95db
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Apr 7 13:02:14 2018 -0400

    Add Ansible playbook to build CloudFormation stack

commit 4bfb5390419f712e7d54bd070e04eb450c49c15d
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Apr 7 13:03:39 2018 -0400

    Move kubesetup script out of cloudformation directory

commit 4be6b49a2ceda1d95f7092fddb6b321098204b0d
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Apr 7 13:03:56 2018 -0400

    Error if awscli is not on PATH

commit bbadf3a182f02c673d1816c2d3ae607f469d8053
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Apr 7 13:28:29 2018 -0400

    Make node groups into a list
    
    This enables customizing the number of node groups without having
    to modify the Ansible playbook itself, only the vars file.

commit b9141a6c0be7fdc7f21bd2fbcba3ec30777eea08
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Apr 7 15:13:17 2018 -0400

    Enable enhanced networking for AMI

commit 68853109fbaa0d8024f0b792bb369831d5d32319
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Apr 7 15:47:47 2018 -0400

    Disable cloud-init network configuration

commit 0bee0e49659ae541d545ff67f055c082798c3c4f
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Apr 7 17:15:36 2018 -0400

    Add `controlPlaneEndpoint` option to `kubeadm`
    
    This option tells the API server to respond with its DNS name
    instead of IP when a node joins. It makes it possible to replace
    the masters without nodes losing contact.

commit c1a1534b38847a1b1fd4701f1e854f33b381d80b
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Apr 7 17:19:54 2018 -0400

    Add CoreDNS instead of KubeDNS

commit 6eafa1c7b40f8078947aabb8e65f1c54c971e3bd
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Apr 7 17:21:40 2018 -0400

    Update kube-router version

commit 202cf7e1cb3141c0fa55e30563bbf88a3f345083
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Apr 7 19:34:34 2018 -0400

    Update kube-proxy feature gates syntax for 1.10.x

commit 19c6b344e5502e88a99600ac2962243d04f2fe02
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Apr 7 19:36:05 2018 -0400

    Update default software versions for AMI

commit db461cd6de54b6864ed2d3716bc05ec21b28daab
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Apr 7 19:36:59 2018 -0400

    Add port to apiserver hostname

commit be52eae71cd2a7d8562c6baf3eb639686ea97661
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Apr 7 20:08:18 2018 -0400

    Add systemd restart to failed kubeadm init

commit d7f99bd46a85732ad5b3aa98a45a4146a7ed70dd
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 00:45:19 2018 -0400

    Do not print token after kubeadm init

commit 2de129e8d76816e90f8865375661efe98ee0fd66
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 00:45:45 2018 -0400

    Satisfy kubeadm preflight check for bridge-nf-call-iptables sysctl

commit 8597cfeff4413eaccd365bde8ce2e6947dbaf6cc
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 00:50:03 2018 -0400

    Fix controlPlaneEndpoint port
    
    The controlPlaneEndpoint setting does not work with a port added.
    The bind port of the apiserver is added to controlPlaneEndpoint.
    This makes it necessary for the load balancer to listen on the
    same port as the apiserver. This is unfortunate, but until that is
    fixed, the load balancer will be changed to listen on 6443.

commit 9cfa8d2c282c5e4c5c9b49da65b61b0815dbe9ec
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 00:52:05 2018 -0400

    Bump k8s and etcd versions in example stack

commit f2db9c7df39ad796bb5fa2e0054fc19f7fbcc160
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 01:16:25 2018 -0400

    Systemd service needs to be type=simple to restart

commit 2e0a02ee8b8292c58739597d2c25deaa8ed1bc36
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 01:43:34 2018 -0400

    bridge-nf sysctls need br_netfilter kernel module

commit 49ed197d878a4fbc2617c00c0fb182e0d229f3ce
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 01:52:43 2018 -0400

    ExecStartPost doesn't play so well with service type=simple
    
    With type=simple, systemd treats the ExecStart as successful as
    soon as the process starts, and then runs ExecStartPost. This
    caused the ExecStart to fail as the input file was immediately
    removed.

commit d8bcb81be64e6fb26dadcbf6e5fd68882f26fbe5
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 09:20:41 2018 -0400

    Update pause container to 3.1

commit d5e97326ff5e6ac1274f69db1020236716dd7dd9
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 09:21:27 2018 -0400

    Use k8s.gcr.io repository, since k8s is now pulling from there

commit d57d28b1a494ffb25035eefc4a465597fb78dad1
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 10:01:48 2018 -0400

    Switch back to using individual images instead of hyperkube
    
    The hyperkube image is missing the ipset utility, so using
    kube-proxy in ipvs mode doesn't work.

commit f30246255c64c6fac6db929e306a198cc1a40e77
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 10:07:52 2018 -0400

    Update default k8s version in master CloudFormation template

commit ae6e2d1a9648ccfb5a89f21d0f9b88fbb91989c8
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 10:08:23 2018 -0400

    Sync node CloudFormation template cluster dns parameter with master

commit 990563c7ad7eacfa02f08fd432d841e5aa5b026b
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 12:12:48 2018 -0400

    Fix Ansible playbook passing old parameter to CloudFormation

commit 9861124e13d79434174185f36566f886caa080cf
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 12:13:26 2018 -0400

    Remove ipvs mode for kube-proxy while troubleshooting

commit e6e31b1d317fc3d7d0eadfc0c284edadf9e8c193
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 12:36:47 2018 -0400

    Fix images for addon manager and etcd

commit 9626d2ea88cdafea07c2d588d89e8631de49f095
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 14:15:11 2018 -0400

    Downgrade kube-router for troubleshooting

commit 46421d262f8f3d99fb9a493e48470277980e48fd
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 14:15:25 2018 -0400

    Re-enable ipvs mode for kube-proxy

commit b348ccccbce9de8519746c503bc4d3f2814139fa
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 8 21:41:50 2018 -0400

    Update references to kube-router version

commit 9e04f0b822c106946b459fda561a02286fdece48
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 12 01:31:34 2018 -0400

    Fix dashboard image to match what is in AMI

commit b8558eb2c1cf550377b0e34f1983233baa559daa
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 19 08:42:21 2018 -0400

    Rework security groups
    
    Security group definitions are all put into the `common.yml`
    CloudFormation template, so all the rules between them can be
    done ahead and then applied to the other stacks.
    
    Traffic has been opened between all nodes in the cluster, except
    for nodes having access to etcd on the masters. Later this may be
    updated to be more specific, but there is little documentation on
    which ports are required to be opened.

commit 7062cc6467e6b2c437636f533c3df87066859e77
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 19 21:51:13 2018 -0400

    Pass master and node security groups as lists
    
    Default will be to pass a list of one security group created by
    the common stack. Additional security groups may be defined in
    variables and passed into the stacks.

commit 8e7cb410bcded225fb4bcf356cc45ab7cf29aa9e
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 19 23:32:21 2018 -0400

    Make S3 bucket for Lambda archive files a parameter
    
    Deploying Lambdas from archives stored in S3 buckets requires
    that the bucket live in the region where the Lambda is deployed.
    Now the bucket is a parameter so that a user can copy the zip
    files to their own bucket in their own region. By default, the
    value is `cloudboss-public` in us-east-1.

commit 79a35a6e23dc4c5147277703fad8416392722bc2
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 19 23:36:15 2018 -0400

    Add Ansible tags to playbook for each stack
    
    Each stack may be deployed separately by using Ansible's `--tags`
    option, or `--skip-tags` to skip one stack.

commit be84b6057fdb6074084c5aaaa492bef27e34f72f
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 22 02:05:31 2018 -0400

    Open IPIP protocol for kube-router to work across AZs
    
    The ICMP security group rules have been removed, as they should
    not be necessary.

commit ae54536ecb5cf42c46aa701c49276045ab133cb7
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Apr 24 23:50:13 2018 -0400

    Rename variable from loaded term `ENV` to more accurate `CLUSTER`

commit 10b2b2351861e13fc4aabfac7a5c2f06bf9caa6e
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Apr 25 00:33:22 2018 -0400

    Update AMI IDs

commit 31c1f1f0e6b7c2b236d46acb188d11e11ff8137f
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Apr 25 00:35:01 2018 -0400

    Rearrange and modify for clarity, and remove unused Ansible variables

commit 6be57823c7b8bfe5579d016d67b139e80588b01c
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Apr 25 00:44:26 2018 -0400

    Map CloudFormation outputs more clearly to corresponding inputs

commit e4199dbf557059749c5e3f7fd0d0d367c4dc2dba
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Apr 25 00:50:48 2018 -0400

    Add documentation in README and Ansible vars file

commit f43d9a72ec2070fb744d0c5f1d9d698966842690
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Apr 24 23:50:13 2018 -0400

    Rename variable from loaded term `ENV` to more accurate `CLUSTER`

commit 7e03816b3b65e9a2a6719caf4374b6c7cf5110b8
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Apr 25 00:33:22 2018 -0400

    Update AMI IDs

commit a9f2225a1154bcb8469ac0a933998259acf2aafa
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Apr 25 00:35:01 2018 -0400

    Rearrange and modify for clarity, and remove unused Ansible variables

commit 5b70fd351b4503823f43b2312f834e8c8aebbea2
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Apr 25 00:44:26 2018 -0400

    Map CloudFormation outputs more clearly to corresponding inputs

commit dc236332f677d2e51850df36595a139bfeb3015b
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Wed Apr 25 00:50:48 2018 -0400

    Add documentation in README and Ansible vars file

commit 4fbcc2d502967b9631ea6aaec17cc40e2ea8ff67
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 26 21:05:31 2018 -0400

    Fix reference to old variable `env`

commit 626f5e0166089bcca9db64ea7134a6b2a2a9a324
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 26 21:12:23 2018 -0400

    Update Ansible CloudFormation parameters with modified variable names

commit e1526d556d861ba6f5d1881dbd87479805698822
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 26 22:45:22 2018 -0400

    Allow Ansible vars file to be overridden

commit b0fc069ff4b027d3c4eacb9950d458f80d9aa999
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Apr 26 01:25:07 2018 -0400

    Add resource signals to master CloudFormation stack
    
    For doing stack creation and rolling updates of the masters, it
    should be possible to do a health check and then signal the
    stack when the apiserver is running before the next instance is
    replaced.
    
    A new command, `keights signal`, has been added which acts like
    Amazon's `cfn-signal` command, so that the stack may be
    signaled during creation or updates. The master stack
    autoscaling group resource has a creation policy and update
    policy added which will wait for the signals coming from
    `keights signal`.
    
    A new command was added to `keights` rather than using
    `cfn-signal` because that would have required adding Python 2
    to the AMI, and the implementation to do it was simple enough.

commit 6caa99361b91f330aaea5c7b8e303e340c89eda4
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Apr 29 00:31:58 2018 -0400

    Update default AMI IDs

commit d61ad6c7fd4f8e238eeff63cf242ac98f6f7a8c7
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue May 8 01:50:05 2018 -0400

    Use Makefile instead of goreleaser for building and releasing
    
    This is in preparation for building and releasing CloudFormation
    custom lambdas in the same repo as keights. It seems that
    goreleaser can only build one package, regardless of the number
    of entries in the builds list.

commit 0d40d1d96c42f1861b88df011ccc7399721b545f
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue May 8 08:08:18 2018 -0400

    Fix make target for Travis release

commit f3b344c29a703d9e7e14ab78087ff3a256a5eb88
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue May 8 08:13:35 2018 -0400

    Add VERSION for github-release make target using TRAVIS_TAG

commit 31257f24211220f3bc102cfbddbfc228cf99706c
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue May 8 08:13:58 2018 -0400

    Use after_script so a release failure causes a failed build

commit 58125fae28a936106de4f934ae9c130138e7a5bf
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu May 10 00:12:47 2018 -0400

    Rearrange and fix import paths after importing lambdas as subtree

commit 3b32a959b0f540fa74e6c1df2fa3ec6232b7b05d
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu May 10 00:18:58 2018 -0400

    Update vendored dependencies

commit c6d81b3f9b4a854b25f74a78324eb4e6569b81be
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat May 12 09:04:08 2018 -0400

    Combine .gitignores

commit bd1b6bdda9e0d3ed833160b26610c0d771ecb8d3
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat May 12 11:04:01 2018 -0400

    Update Makefile and github-release for stackbot
    
    The code under `stackbot/` is for creating CloudFormation custom
    lambdas, which need to be zipped after compiling.

commit 267e36701a25f4810ff4fa106a7c2c99bb09aa28
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Aug 25 20:02:23 2018 -0400

    Update to build with go v1.11
    
    This removes the vendor directory and the dep Gopkg files, and
    adds `go.mod` for using go modules.

commit 2c70b6b777d6b51080e72f187a7f462ce07b8c5f
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Aug 25 20:14:06 2018 -0400

    Update .travis.yml to use Go 1.11

commit d3c617429a356d149d5f30049e66febc6c09273a
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Aug 25 20:15:59 2018 -0400

    Remove `GOPATH` from .travis.yml build

commit 6bac8c4e0283c6622c0276c57e63f27b99be5d5b
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Sep 1 00:19:31 2018 -0400

    Update to use aws-lambda-go CloudFormation wrapper
    
    The aws-lambda-go package introduced support for wrapping
    CloudFormation requests and responses in commit
    66be66bd3c451ca540a97686d856d1e88e19ce35. This removes response.go
    since it is now implemented by AWS, and updates the Lambda handlers
    to use their wrapper.

commit 6e676f68faa2e88c27857e8355b32a577fab8abb
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Sep 6 00:28:41 2018 -0400

    Modify subnet_to_az Lambda to output list of AZs from subnets
    
    By outputting a list of availability zones given a list of subnet
    IDs, the subnet_to_az Lambda only needs to be called once, and the
    output for an individual AZ can be retrieved by using `Fn::Split`
    in CloudFormation. The subnet_to_az function must output the AZs
    in the same order as the subnet inputs.

commit 99030a53e4407c85286e0682ac683fc7eef10ace
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Mon Sep 10 01:00:45 2018 -0400

    Convert Ansible playbook directory into an Ansible role

commit 6a145150f27d0a7254947180e79ad3bec1f2cd03
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Mon Sep 10 22:11:15 2018 -0400

    Add retries around `keights volumize`
    
    Since it is not possible to put a restart on a oneshot systemd
    service, a while loop within the service will be used instead.

commit accfbe7dc02582e4aa773ef969ee6b71360e1699
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Mon Sep 10 22:33:48 2018 -0400

    Add CloudFormation signaling to nodes as well as masters
    
    This should improve rolling upgrades for nodes, though there is
    more work needed to add draining before termination.

commit 871474ad291133891f7073ab2f3a76acbbe75394
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Mon Sep 10 22:36:48 2018 -0400

    Use Amazon's recommended process suspension during ASG updates

commit c082df1b19ab28b663e55484f41c4402d4cc59fd
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Mon Sep 10 22:46:23 2018 -0400

    Update versions
    
    k8s 1.10.0 -> 1.10.6
    dashboard 1.8.3 -> 1.10.0
    kube-router 0.0.20 -> 0.2.0
    
    Fix kube-router version in addon manifest

commit 69df01dd6449c4a20f765d3c26a53c2221e6cc13
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Mon Sep 10 22:58:01 2018 -0400

    Update Python requirements for AMI

commit 022e0118aee4728e8af229a39aceed500785a40e
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Sep 11 00:17:47 2018 -0400

    Remove default Kubernetes version from CloudFormation parameters

commit 041438941ddf461b5636d45d1c464cc9d740fdde
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Sep 11 00:18:05 2018 -0400

    Remove `v` from KeightsVersion variable

commit 7ab39b10d5d41f0fee740d32963d3ea49e38f25a
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Sep 11 00:51:25 2018 -0400

    Use RollingUpdate strategy for kube-router daemon set

commit 5d1e3782824ff3fa74ca6fed5f3e20cae39cfed4
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Tue Sep 11 00:57:10 2018 -0400

    Make `keights` version be the AMI version
    
    Remove default keights version from AMI build, and remove extra
    `v` characters added to version, since git tag with `v` is used.

commit 496bf1e85cbdb217c3f9f720f5d12717e9c89c6a
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Sep 13 23:06:45 2018 -0400

    Make update max batch size configurable for nodes

commit a5f73b42c4db8bd60f23cc223e20fe85679d6f37
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Thu Sep 13 23:12:57 2018 -0400

    Make load balancer idle timeout configurable

commit efe7aedd01315e93085292f36ceec896b0734a32
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Fri Sep 14 00:47:43 2018 -0400

    Fix deprecated syntax for Ansible condition check

commit 11d43e571e2b8c9a6d8837952f943fdf07eb31f6
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Sep 15 00:42:37 2018 -0400

    Use for loop in Makefile to avoid repetition

commit 5ef2946d72af1f1e854df05a417e166037461ac4
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sat Sep 15 00:44:59 2018 -0400

    Remove unused makefiles from stackbot directories

commit f29a68cd5de63a13e69bbaba5a73a2da5514636f
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Sep 16 00:26:02 2018 -0400

    Fix Go formatting with gofmt

commit 8adda25c4e452547adaf396538ce4342343eddac
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Sep 16 12:27:43 2018 -0400

    Allow templatize vars to be a comma separated list
    
    Inside of a template, the comma separated list becomes an array.

commit a56201498e7fda82c6c5cc06d7217be989c5ee07
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Sep 16 13:01:26 2018 -0400

    Add stackbot Lambda to auto name masters in Route53
    
    The common CloudFormation template now creates a private hosted
    zone for the cluster for the Lambda to use for adding records.
    This will be used for etcd instead of keeping /etc/hosts up to
    date with all of the master IPs.

commit 755b207336859e154c7a978c0e1f83cd2fc0d443
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Sep 16 13:10:17 2018 -0400

    Clean up CloudFormation template `Ref`s and `GetAtt`s

commit e37b0d5a25322ebb3e4e775a927590be1e61d0f3
Author: Joseph Wright <joseph@cloudboss.co>
Date:   Sun Sep 16 13:15:40 2018 -0400

    Update masters to remove collect and share services
    
    Collecting IPs and sharing via /etc/hosts is no longer necessary,
    now that the `auto_namer` Lambda is in place to add DNS records.
    
    This also includes having kubeadm generate certificates for etcd,
    so that if the naming changes, the certs will be signed for the
    new names as well. It also makes certificate rotation much easier,
    as it will happen automatically on stack updates.

commit 4e569596c9551462a11db0187aeadf0bce95acc6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Sep 26 01:50:19 2018 -0400

    Fix boolean logic error
    
    This should be checking against a value found in map, hence `!ok`
    is wrong.

commit 6c655309f4e3575542837330bebc7d87f9776b34
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Sep 27 02:00:59 2018 -0400

    Change Makefile to only modify _output directory
    
    When building the `keights-stack` Ansible role, some files are
    copied into place. This makes it so the changes only happen in
    the _output directory instead of in place within the source tree.

commit 3adce009f5cebfb01aa7f2362c9e98aa0b04ef30
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Sep 29 01:23:22 2018 -0400

    Add Lambda for modifying EC2 disable-source-dest-check attribute
    
    This will be used after kube-router releases a new version with
    https://github.com/cloudnativelabs/kube-router/pull/541, or when
    using another CNI plugin.

commit d21d9ed540fc198962a15ed2e14bee01e02cbd1c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Sep 29 01:49:20 2018 -0400

    Refactor auto_namer to use asgevent wrapper
    
    The asgevent.Handle() function does validation of a CloudWatch
    Event before calling the real handler. Since the autoscaling
    events are similar between `instattr` and `auto_namer`, it makes
    sense to put the boilerplate in a shared package.

commit d6e5fb50994d36f5c55ac99d996c14886523eced
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Oct 2 23:28:29 2018 -0400

    Make the etcd volume device name configurable
    
    The EBS volume `xvdg` may not always show up as /dev/xvdg,
    depending on the EC2 instance type, so make this a paramter.

commit 558b934dd3ee2fa35b329b1eec2880d2676dbf6d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Oct 5 22:10:41 2018 -0400

    Always attempt deleting all secrets from SSM Parameter Store
    
    The `kube_ca` Lambda should not return error if it fails to
    delete a secret, as that may leave some behind unnecessarily.
    Instead, attempt to delete all of them, and then return a
    concatenated error of all combined failures.

commit 0acce7634fb50b2be1e3aa91143dd85b60d02aab
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Oct 5 22:13:06 2018 -0400

    Remove unnecessary certificates generation from `kube_ca` Lambda
    
    The `kube_ca` Lambda mostly just needs to generate the CA
    certificates, letting kubeadm generate most of the others. This
    removes certificates from `kube_ca` that are no longer being used.

commit 4fd5a92509feff3cd2d5c485acb77aed34c3903b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Oct 5 22:17:18 2018 -0400

    Update copyright dates

commit d10f0675b7cb62fe58cc2b6144348f7d618b4686
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Oct 5 22:18:06 2018 -0400

    Remove old files from when stackbot belonged to another repo

commit 2f6b5c24fc8a704fc2269386e811ea9884496293
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Oct 5 23:44:11 2018 -0400

    Really make the etcd volume device name configurable
    
    This fixes issues with the previous patch, which only changed the
    one parameter being passed to `keights volumize`. There actually
    needs to be two parameters, one to represent the EBS volume name,
    and other to represent the device name as seen internally within
    the operating system.

commit cc74a9000a230261303caf6d9bad9f71126845cf
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 6 12:32:03 2018 -0400

    Publish snapshot versions to Github marked prerelease

commit 1cb0c37103c4f92face99cf4ffe9fe10f667cf5f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 6 22:42:29 2018 -0400

    Remove launch configuration names from CloudFormation templates
    
    Using an exact name for the LaunchConfiguration prevents stack
    updates from succeeding when the LaunchConfiguration is updated.
    It is better to allow the name to be auto generated so that the
    stack update can proceed smoothly.

commit 5b5bb13085c95f758823c1cd8bd7f2bbf2821981
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 6 23:28:47 2018 -0400

    Remove default AMI IDs from CloudFormation parameters
    
    It's not very useful to have a default value, as it most often
    needs to change with each release.

commit 823514d08a908fab7576026cd83104df7f468f9a
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 6 23:36:29 2018 -0400

    Make `resource_bucket` a required stack parameter
    
    Ansible does not always succeed in reading the contents of the
    default bucket from other accounts, even though the bucket is
    public. It is easier to require a bucket than have the stack
    fail in mysterious ways.

commit 236986514097e511beeb3dd3882dab20b94b5d6d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 6 01:37:10 2018 -0400

    Update to run Kubernetes 1.11
    
    Most of the changes for Kubernetes 1.11 focus on removing the
    static pod manifest for etcd, and allowing kubeadm to handle that.
    The kubeadm init config file has been expanded to specify the
    options that should be passed to etcd, as well as updates to the
    kubelet configuration. The kubelet configuration is mostly
    handled by kubeadm now, so most of the options that were formerly
    passed in the systemd dropin have been removed.

commit adce28e14e46bc25afc4c1e277d597bfc3e865ef
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Oct 7 15:50:18 2018 -0400

    Add new Ansible role to replace addon manager
    
    The new Ansible role is called `keights-system` and is used to
    set up the `kube-system` namespace in the way that the addon
    manager did. Using the addon manager required changes to the AMI
    if it needed modifying, and slowed down development time and the
    ability to modify the cluster.

commit cd80f2e434019e0b2dac1ae541b0a480c1a39835
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Oct 7 15:52:33 2018 -0400

    Remove pre-kubeadm-managed etcd leftovers
    
    The etcd manifest template is no longer required, since kubeadm
    generates it, and kubeadm will generate the etcd certificates as
    part of `init`, so it is not necessary to run the kubeadm cert
    phase to generate those.

commit 94503b782fb80a0a7fd6704418725f59b8a63ac1
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Oct 8 23:37:06 2018 -0400

    Fix docker images to match images expected for kubeadm settings.

commit 003aeea8f4df5a94f4404c1c9b7aba5a25a744fa
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Oct 9 08:18:33 2018 -0400

    Add cloud-provider=aws to /etc/default/kubelet
    
    This does not get automatically passed down from kubeadm, see
    https://github.com/kubernetes/kubeadm/issues/1021.

commit 1cdd2c96ca7b420bb48c33c70d432136277289eb
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Oct 21 10:40:02 2018 -0400

    Add Concourse pipeline
    
    This also tweaks Ansible and the Packer build to work in
    the Concourse task:
     * Add `user` to the Packer Ansible provisioner, as it was
       failing without it in the Concourse build container.
     * Remove pkg_resources=0.0.0 from requirements.txt, which fails
       on the official python docker images, used in the build.
     * Remove spot_price_auto_product which was not being used.
     * The `keights-stack` Ansible role can now take a service role
       ARN to be passed to CloudFormation. This is used by the
       pipeline to limit permissions on the worker.

commit 998edce9ac62a70da8edb8e050d2e94310fa9a10
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Oct 21 16:42:21 2018 -0400

    Add e2e tests to Concourse pipeline
    
    This includes a change to the `keights-system` Ansible role to
    wait for the network, otherwise `sonobuoy`, used for running the
    e2e tests, can fail to start properly. And because `sonobuoy`
    keeps running on exit, the pod does not restart and recover.
    
    This also changes the default cluster domain from `k8s.local` to
    `cluster.local`, as setting to anything other than `cluster.local`
    causes DNS e2e tests to fail. This may be overridden with Ansible
    and CloudFormation parameters.

commit 737c9bb84626936120cea156e26bee2d4920d124
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Oct 28 11:45:58 2018 -0400

    Add Concourse pipeline job to build release on git tag

commit 1f4b6e8ca2d2ff0f70e705e82879831a19fef59b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Oct 28 12:24:25 2018 -0400

    Add Concourse job to upgrade cluster and run e2e tests on it

commit 2c0f8eb4ee17db517c89c1acd40c4b50dbe7efd5
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Oct 28 22:20:36 2018 -0400

    Fix version so it always starts with `v` character
    
    The build-ami Concourse task may be called with a version that
    begins with a `v` or not, so this ensures that the version passed
    to Packer always starts with `v`.

commit f377179aba38ceb1b4dae82a2d0872b316288eae
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Oct 30 08:34:17 2018 -0400

    Add version to upgrade from for v1.11 pipeline

commit d6d20e9d7ff7964c80834f07ba23047d1cc153c5
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Nov 1 21:50:12 2018 -0400

    Add the ability to override Route53 zone with an unmanaged zone
    
    For etcd hostnames, it may be desirable to use an existing Route53
    zone instead of creating one for each cluster. This adds parameters
    for specifying a zone that is unmanaged by this automation.

commit 65cd46eb0dc4c7fa880ee923b3e6f7ee2a2afec2
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 4 17:43:07 2018 -0500

    Bump k8s version in AMI to 1.11.4

commit f6921a38c0dba97df902ea08b93b673fa6820fdd
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 4 11:44:11 2018 -0500

    Tighten version of golang docker image for Concourse task
    
    After 1.11 became 1.11.2, the following error occurred
    when building keights:
    
    go: verifying k8s.io/client-go@v5.0.1+incompatible: checksum mismatch
            downloaded: h1:IPZ0cnux5ui8+X8r1HdeFPXucpQ4HyJQigjo1clq1QM=
            go.sum:     h1:QDi2+OuVrLBqk0wPJ/5fG0jMia81BA5qFGrqoPeCpsA=
    
    This pins the patch version to 1.11.1 instead of floating between
    patch releases.

commit 908023b2f737cf0ecb9e7c61ceb2a252d68d3301
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Nov 4 23:08:56 2018 -0500

    Improve resiliency of master initialization
    
    This improves the volumize command:
    The `WaitForVolume()` function adds `status: available` to its
    filters when querying AWS. This should make the `AttachVolume`
    operation more likely to succeed. Retry is also added around
    `AttachVolume` so that it does not abort on the first error.
    Length of time to attach and wait for volume is also increased.
    
    The master.yml CloudFormation template has the timeout for
    receiving a signal from masters increased to 30 minutes, for cases
    where volumize is taking especially long to run.

commit 0fa7e6947a56aaf5c7d47d9b4d58632abd5c3770
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 6 20:34:38 2018 -0500

    Set permissions on snapshot in addition to the AMI
    
    By default, the AMI and snapshot are public, but this adds a user
    variable to override the value.

commit 6f5176682bc73bc05e9e222585c7e2a954d99bb0
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 6 22:07:19 2018 -0500

    Remove addon-manager and dashboard images from AMI

commit 51da6a320e6b01ddc43cf1fa4791527c9d184b78
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Nov 9 22:16:56 2018 -0500

    Add encryption to etcd volumes

commit e37b475f7c5636b7c59bab3558e4cff79c438236
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Nov 9 23:31:11 2018 -0500

    Pass JSON options for docker instead of command line arguments
    
    The `/etc/docker/daemon.json` config file will be written instead
    of passing command line arguments via `/etc/default/docker`. This
    method is more flexible and allows passing the docker options as
    a dictionary to Ansible.

commit 2fd64d4ad62a9e41c73be95c74a2a648e328bc73
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Nov 22 00:39:20 2018 -0500

    Remove unnecessary calls to systemd-tmpfiles in systemd units
    
    The tmpfiles should be created by systemd-tmpfiles-setup.service,
    and it should not be necessary to call the command directly here.

commit f2d3b67cba1a915059d0545f9f9cb639de12ab92
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Nov 22 00:44:54 2018 -0500

    Remove path names from utilities that are executed
    
    Go's `exec.Command` will look up executables on the `PATH`, and
    having the path hard coded is not portable.

commit 93c939a06729424839bf8f9db54627a07da2cf05
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Nov 22 02:51:09 2018 -0500

    Allow overriding of pause container from kubeadm config file
    
    Previously, the node did not use a config file for `kubeadm join`,
    but it is being added here to make adding kubelet options easier.

commit 7f0a1964e14f03fa2feb9485b48a870d275c685b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Nov 22 02:58:09 2018 -0500

    Show stderr if unexpected error occurs when running blkid

commit cfd6f19b11031788248eb08cf5e044588c766774
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Nov 12 00:15:56 2018 -0500

    Update to Kubernetes 1.12.2

commit 13eea661270fba2b1a432ca6f49aa43db6101cb9
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 20 23:05:13 2018 -0500

    Update docker options for test clusters
    
    With CRI, only the `json-file` docker log driver is fully
    supported. This is being updated so that e2e tests will pass the
    `kubectl logs --limit-bytes` option.

commit 9c74fb315e3b1de2712ffdd9de566d7add053a66
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Dec 15 10:56:12 2018 -0500

    Derive Kubernetes version from keights version in Concourse task
    
    This changes the `build-ami` task to get the version of k8s from
    the tag or semver used for the keights version, so the two are
    always in sync, and a change to the keights repo is not needed to
    build a new version of Kubernetes. Instead, the snapshot semver
    can be bumped in the git repo where the semver is stored.

commit 0f0ab151249fd67ee55ef3cf06aba73dd580bc76
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Dec 15 22:50:05 2018 -0500

    Add release verification step to Concourse pipeline
    
    This is intended to ensure that the release has been tested by the
    pipeline. It checks the PR git ref against the tagged git ref,
    and fails if they do not match. It also checks the Kubernetes
    version against the one most recently tested.

commit 73967ad855ab62750b191b22a9cc2a4dfe512a97
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 16 10:30:20 2018 -0500

    Remove merge commits from branch comparison in verify-release task

commit 165bedd6c35869277f169e39a1dd90270f4d6904
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 16 15:50:20 2018 -0500

    Use Lambda to disable source dest check on EC2 instances
    
    When kube-router handles disabling of source dest check, it can
    cause EC2 throttling if the cluster size gets large enough. This
    ensures that the attribute is only set once on instance launch,
    and disables kube-router from setting the attribute. This also
    modifies calico to use `CrossSubnet` mode for IPIP, since the
    Lambda can now set the attribute, whereas before it was unset when
    using calico.

commit 44566072c191e8d24568ec93048dace39ea08cb6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 16 23:15:42 2018 -0500

    Add toleration needed by kube-router for 1.12

commit e9bd9da41783d9ef949f80af2130de30944dfa53
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 23 15:44:17 2018 -0500

    Update pipeline triggers and branch glob
    
    * Make pipeline jobs trigger on version change
    * Update branch glob to work with multi-digit version numbers

commit 2216abdd1741e4acd71f34f584f4d0b4953f5cf9
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Jan 5 01:13:18 2019 -0500

    Fix github-release to tag the correct git ref
    
    By default, the GitHub releases API tags master if the
    `target_commitish` value is not sent in the request. This causes
    the pipeline to create snapshot tags always for the tip of master.

commit 7a2bda231d34703121541f82614caba8e46709e6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 6 13:58:47 2019 -0500

    Update IAM policicies for EC2 instances
    
    For nodes:
    * sts:AssumeRole - added for e.g. kube2iam. Assumed role still
      must have a trust relationship with node role for the role
      assumption to succeed.
    * ec2:ModifyInstanceAttribute is removed - kube-router is no longer
      configured to disable source dest check EC2 instance attribute.
    For masters:
    * elasticloadbalancing:AddTags - used when creating a service of
      type LoadBalancer.

commit 7ad0416029e56d8cb006a27238533b564097baba
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 6 14:17:11 2019 -0500

    Update to docker CE 18.06

commit e5a71c3f1e4a17a52dbd2403c1f327ed2923b258
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 6 14:17:23 2019 -0500

    Stop importing docker images into AMI
    
    Occasionally the versions, such as for etcd, get out of sync with
    those configured by kubeadm, and they end up getting pulled as
    different versions anyway.

commit fbf61e097ef2c1b01399c3b6d886015e2a7a288f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 6 14:37:35 2019 -0500

    Include tools to support more filesystems for storage classes
    
    This includes support for btrfs, nfs, and xfs.

commit 95cd247726a2f24e4a3cff0fb673fd7fa1fefd5e
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 6 16:09:39 2019 -0500

    Use separate EBS volumes instead of partitioning root volume
    
    This simplifies the partitioning, and allows for dynamic resizing
    while instances are running if necessary.

commit 712382fcfe06a135fb58ca3050b69125cbcaccab
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 6 22:31:28 2019 -0500

    Initial changes for supporting Kubernetes 1.13
    
    * The kubeadm init config has been updated using `kubeadm config
      migrate`.
    * Pipeline files and directories in `ci` have been updated for
      doing upgrades from 1.12 -> 1.13.
    * A kubelet restart has been put in place on masters after the
      CloudFormation signal has been sent. This is because the 1.13
      kubelet is not 100% compatible with the 1.12 apiserver, and
      during rolling upgrades, the 1.13 kubelet may cause the master
      to go into a `NotReady` state while the other masters are still
      running 1.12. The kubelet recovers after restarting when the
      masters as a whole are now running 1.13. This usually only
      happens to the first master to be updated, sometimes the second.
      A sleep of 300 seconds is put in place after the CloudFormation
      signal is sent to give the next master time to come up, then the
      kubelet is restarted.
    * Update sonobuoy to 0.13.0, as the previous version does not
      support Kubernetes 1.13.

commit 7c5b6efc4d37678c1f4dbe320a682e8124b3f673
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Jan 21 23:39:16 2019 -0500

    Add ci upload for sonobuoy results to S3 bucket

commit 1f6b0353e01e46415c05de44e53e3467cf1409c3
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Jan 21 23:40:14 2019 -0500

    Fix ci task to use release branch during release build

commit 8ce6ce10dc42d6c70df18de524d47dc62e0149c1
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 27 13:15:29 2019 -0500

    Add `ebsnvme-id` command with corresponding udev rules to AMI
    
    The `ebsnvme-id` command is used in Amazon Linux AMIs to map nvme
    device names to the name given to EC2 for an EBS volume device.
    For example, if a volume is created with the device name
    `/dev/xvdg`, it may have an internal nvme device name of, say,
    `/dev/nvme1n1`, though this is not always predictable. The udev
    rule runs `ebsnvme-id` to determine the name chosen by the user,
    and then creates a symlink for it to the nvme device.
    
    The command used by Amazon contains Python 2 syntax, so it was
    modified to work with Python 3, as that is the default in Debian
    stretch.

commit d09fca66cef21d068868c3300aef287f33561a77
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 27 13:23:34 2019 -0500

    Upgrade to the latest packages when building a new AMI

commit 22d3b7108939696e57f5e6e4d84f4b0603a908ed
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jan 27 13:24:04 2019 -0500

    Update kernel command line arguments
    
    The original arguments were taken from official Debian images.
    This turns off `quiet`, so that kernel messages can be seeen from
    the AWS console. It adds `biosdevname=0` to prevent device renaming,
    and adds `nvme_core.io_timeout` as recommended by Amazon.
    
    https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/nvme-ebs-volumes.html

commit c3aee5b1712210f0db16bf429b7ace994dd7807b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Jan 29 21:48:27 2019 -0500

    Update ci for v1.13 upgrade from v1.12

commit e062f83dcf80b5af633d2779d1b500f27307c6ed
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Jan 29 21:53:03 2019 -0500

    Add missing variable from example

commit 56ef983ae393e2e20504389eebb6d02b55ab99be
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Feb 2 13:26:35 2019 -0500

    Build an RPM in addition to a Debian package

commit d95382aa9cfb9c403aa5638b2821003e24c07db8
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Feb 2 21:23:01 2019 -0500

    Update the default listen port for the kubesetup script
    
    Versions 1.11 and above uses port 443, so this should be the
    default value.

commit fdb76557f33d53fe80349102bfc3840bf650d4c6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Feb 2 21:27:46 2019 -0500

    Fix Ansible example directory
    
    The Ansible example had gotten somewhat out of date, and should
    be configured to be easy to create a cluster with minimal changes.
    This updates the example to require only a few AWS IDs to be
    changed, while having the flexibility to customize it.

commit 58b0ba4c99b741cd4b240f42b62924003505eb0b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Feb 2 21:30:19 2019 -0500

    Update documentation
    
    This improves the documentation to be more accurate, hopefully
    easier to understand, and it fixes some improper YAML formatting.

commit 09d4fa328a277627332a4c061f99a27a9b8f0012
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 3 23:26:39 2019 -0500

    Remove `.travis.yml`, as Concourse is now used for all CI

commit 9e610c5ab74fe4a615e36ef05a4941e973e06147
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Mar 20 23:39:38 2019 -0400

    Check that the etcd EBS volume is attached before waiting
    
    Waiting for the volume in cases of a reboot of the master may end
    up with a long wait ending in error. The volume should be already
    attached in such cases, so this adds a check before waiting.

commit cb20845e9d160191128203baf0db722f93567f69
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Mar 20 23:46:58 2019 -0400

    Remove use of etcd internal device
    
    Since adding the ebsnvme-id script into the AMI with a udev rule
    to create a symlink to the name given to Amazon for the etcd
    block device mapping, there is no more internal name vs external
    device name. This should no longer be surfaced to the user as
    CloudFormation parameters, or as arguments to the keights binary.

commit c0c973f834b638cf74644b63359c043bda6fd9f1
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Mar 25 23:57:30 2019 -0400

    Sync kube-router with upstream for keights-system Ansible role
    
    This brings in a few new enhancements:
    
    * Updates kube-router version to 0.2.5
    * Adds liveness probe on kube-router pod
    * Adds bgp-graceful-restart to enable rolling updates without
      losing routes during the update

commit 8310117ef43a37552ec4625c9c11856dd977dc85
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Mar 25 23:58:40 2019 -0400

    Update CI clusters to use kube-router

commit e38e2ee5dfe6a76d3467df92c4153e511a263288
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Mar 12 23:32:37 2019 -0400

    Update to support Kubernetes 1.14
    
    * kubeadm init & join configs have been updated after running
      `kubeadm config migrate`
    * update CNI to 0.7.5
    * update kubernetes-dashboard to 1.10.1
    * update README with 1.14 CI statuses
    * update CI asset directories for v1.14 and upgrades from v1.13
    * update Sonobuoy version in CI task with 1.14 support
    * remove timed restart of kubelet after master is initialized;
      this was used for 1.12 -> 1.13 upgrades

commit 98cdbfdb1b7c6be40ed899f2b118aa4e50228a7a
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Mar 20 22:30:11 2019 -0400

    Improve documentation and comments in example stack

commit a7828e0057ee97310e275b0c1861fa66c34ac11d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Apr 4 00:21:30 2019 -0400

    Generate front proxy CA from kube_ca Lambda
    
    API aggregator test failed with the following error due to kubeadm
    signing separate front proxy CA certificates on each master:
    
          <*errors.StatusError | 0xc0021d7050>: {
              ErrStatus: {
                  TypeMeta: {Kind: "", APIVersion: ""},
                  ListMeta: {SelfLink: "", ResourceVersion: "", Continue: ""},
                  Status: "Failure",
                  Message: "the server has asked for the client to provide credentials",
                  Reason: "Unauthorized",
                  Details: {
                      Name: "",
                      Group: "",
                      Kind: "",
                      UID: "",
                      Causes: [
                          {
                              Type: "UnexpectedServerResponse",
                              Message: "unknown",
                              Field: "",
                          },
                      ],
                      RetryAfterSeconds: 0,
                  },
                  Code: 401,
              },
          }
          the server has asked for the client to provide credentials
    
    This commit enhances the kube_ca Lambda to generate the front proxy
    CA certificate and key to be shared among all masters, as is done
    with the other CA certificates.
    
    In order to support introducing a new SSM parameter to an existing
    stack, the Lambda has been modified to handle CloudFormation stack
    updates, which it previously ignored. Because of this, it also
    runs the delete on a stack rollback. It is undesirable to delete
    certificates and keys on a rollback, so the Lambda no longer does
    deletion of SSM parameters.

commit 204733cbe0508f0e3a1c0ede6742ad853dec22e8
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Apr 4 00:51:12 2019 -0400

    Update CI `make-keights` task to Go 1.12
    
    This also updates go.mod dependencies and go.sum, as some of the
    checksums have changed going to Go 1.12.

commit 88c2659c8ee246bb14770b245ac8f27d8229a59b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Apr 7 12:34:33 2019 -0400

    Clean up KubeCA Lambda
    
    * Add some missing newlines in log messages
    * Reuse Amazon's constants for event types
    * Use guard clause in handler instead of multiple conditionals

commit 3d8874126e4a6e4ab94c0c4c203fa1505e855630
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Apr 14 17:30:57 2019 -0400

    Bump Sonobuoy version in CI task and increase timeout

commit b38b13c5239dea99f3f902d82cf03c4ef088c6a8
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu May 2 21:30:43 2019 -0400

    Move Concourse pipeline "ensure" block
    
    The ensure block to upload e2e results to S3 should only run on
    the `run-e2e` task, not the whole job.

commit 101a7fe32b1f8e5282f8320523e538f86a47ae7d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Jun 7 20:04:52 2019 -0400

    Update requirements.txt files with new dependencies
    
    This also removes requirements.txt from the Ansible roles
    directories, as they are not being used.

commit 433cbc470c4ba3975972d811ca5f7c700ac01405
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Jun 7 22:11:31 2019 -0400

    Update Ansible playbook for Packer build
    
    This updates the playbook to use newer syntax, such as `loop`
    instead of `with_items`, and gives a list of packages to the `apt`
    module instead of looping.

commit 45311c80623e339822733f52f7b8ba9263572f78
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Jun 7 22:10:20 2019 -0400

    Remove no-op code from Concourse build-ami task

commit 45102553fddfe278c3453e1dbcf49bd335b961fc
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jun 16 13:35:05 2019 -0400

    Update to support Kubernetes 1.15
    
    * Update kubeadm init and join configs with new versions and
      options.
    * This includes switching to cgroupfs cgroup driver in kubelet
      due to https://github.com/kubernetes/kubernetes/issues/76531.
    * Add AWS RBAC to keights-system Ansible role since
      https://github.com/kubernetes/kubernetes/pull/66635 removes
      those permissions by default.
    * Update CI assets and README build status badges.

commit 7efef41c492501ab6f6b3068e8678aae7d3a004f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jul 14 14:31:10 2019 -0400

    Pin Debian release to stretch instead of stable
    
    Debian stable is now buster, which causes breakage due to iptables
    update, see https://github.com/kubernetes/kubernetes/issues/71305.

commit 7d6663f5aecc6ccc0dadd1512821622deac5b449
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Aug 18 01:00:00 2019 -0400

    Update Concourse pipeline to remove deprecated syntax

commit 5d30878ceb18df10eaa62ac0e879447f1ea11bae
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Aug 18 23:17:44 2019 -0400

    Remove v1.11 branch from CI status in README

commit 5c82774e3162245f2e21d1d16ae172c6350128b7
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Aug 19 21:29:13 2019 -0400

    Update Ansible version in requirements.txt

commit 346844278029d4db591cfaafefec85344bc2dda7
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Sep 21 10:30:08 2019 -0400

    Update to support Kubernetes 1.16
    
    * Remove deprecated node labels
    * Add/update `cniVersion` in CNI configs.
    * Update API versions for resources in CNI plugin manifests.
    * Replace `scheduler.alpha.kubernetes.io/critical-pod` annotations with a
      `priorityClassName` of `system-node-critical` in CNI plugin manifests.
    * Use `matchLabels` in Deployments and DaemonSets in manifests.
    * Update CI assets for creating and updating clusters.
    * Update Sonobuoy to new version for 1.16 with new CLI option.
    * Update README for CI status badges.

commit 92190a382015ce90c1151fd9843c7cd6646dee8f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Sep 23 19:12:57 2019 -0400

    Increase size of log volume
    
    The log volume fills quickly with only 2G, and more so on Kubernetes 1.16.

commit 82c0703946db781cfa4b8d777a85c56dbb7ac0ec
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Oct 13 23:29:48 2019 -0400

    Make access to NodePorts configurable
    
    This adds an a parameter `NodePortAccessCidr` to the common CloudFormation
    template. This is being done mainly for the purpose of testing, as the
    conformance suite now tests NodePort on the public IPs of the nodes.

commit 9f9d1447b96453c62c02755ffb60279ac2f9cc5a
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Oct 13 23:33:20 2019 -0400

    Open up NodePort range for CI clusters
    
    The conformance suite tests NodePorts on the public IP of the nodes, so this
    allows access to those ports on CI clusters.

commit e4f494385dc0b81d1d705070f14e7664982fe472
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 12 00:41:06 2019 -0400

    Update kube-router to 0.3.2

commit 72f375513fd14444cd0574164f3f2be5cadbc447
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Oct 11 09:02:24 2019 -0400

    Update Calico to 3.9.1
    
    Changes include:
    * Configuring flex volume plugin path for kubelet and kube-controller-manager
      for use by Calico pod2daemon.
    * Adding the cluster-proportional-autoscaler controller to automatically set
      the number of Typha replicas based on cluster size.

commit b93bac3cc6456878eae9463af7cf9c0561f572e2
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Oct 15 00:40:41 2019 -0400

    Update calico-typha cluster-proportional-autoscaler
    
    This updates cluster-proportional-autoscaler to a newer version to work with
    Kubernetes 1.16, and modifies RBAC for 1.16.

commit 7baba90eb6b2c4cc88d4185c9431698b4ded23e2
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 19 12:43:40 2019 -0400

    Add route reflector configuration to kube-router
    
    If `keights_system.network.rr_node_label` is defined for the keights-system
    Ansible role, nodes matching the given label will be annotated as route reflector
    servers. Nodes not matching will be annotated as route reflector clients. If
    `keights_system.network.rr_node_label` is not defined at all, no nodes will be
    annotated and kube-router will be run as a full mesh, like the current
    configuration.

commit 3ee0e246a386b0332c6b6fdbf6b3c9ec268fcbbd
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 19 22:36:55 2019 -0400

    Add route reflector configuration to calico
    
    Similar to changes for kube-router, if `keights_system.network.rr_node_label`
    is defined for the keights-system Ansible role, nodes with matching labels
    will be modified to act as route reflector servers.

commit 9bc370eb6390bf037690302dc453944176043903
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 19 22:40:12 2019 -0400

    Update keights-system README for route reflector configuration

commit a872e22e7db70b91863818501f60d93e815d4328
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 19 23:14:52 2019 -0400

    Enable route reflectors for CI clusters

commit d881661d134f8da77c55332196b579bd775c86ba
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Oct 20 08:59:33 2019 -0400

    Fix link in README

commit f3280bd3b83242b6434d4cca4a081fc78eef1355
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Oct 21 22:15:35 2019 -0400

    Improve verify-network job used before running conformance tests
    
    This makes the job run on every node to ensure the network is fully up before
    running the conformance suite.

commit 82978c0eeefd12497ce58a51e57e9a7be0e23f0d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Oct 23 17:18:34 2019 -0400

    Make more packer parameters overridable
    
    This makes it possible to build AMIs in restrictive accounts, e.g. GovCloud.

commit 98e10ad3e49821ed66c486dd98c31b80ec8feada
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Oct 23 17:24:38 2019 -0400

    Update go.mod and go.sum
    
    The AWS SDK for Go needs to be updated to overcome issue
    https://github.com/aws/aws-cli/issues/4241 with Route53 in GovCloud. This
    updates that library, with a regenenerated go.sum and regenerated mocks for
    ssmiface.SSMAPI.
    
    The Go version is updated to 1.13.3 in go.mod and in `make-keights` Concourse task.

commit d1459f25c882ca51872a333bdd984f2a19af4e99
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Oct 23 22:22:25 2019 -0400

    Use `AWS::Partition` pseudo-parameter for ARNs in CloudFormation
    
    In GovCloud, ARNs are prefixed with `arn:aws-us-gov` instead of the usual
    `arn:aws`. Using `AWS::Partition` in the ARN chooses the correct prefix.

commit c43d82d2c71586be8e2afe7fe787e1d9a7efa29e
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Oct 24 00:14:56 2019 -0400

    Increase number of verify-network pods
    
    When the conformance test starts running in upgrade-cluster Concourse job, there
    are sometimes timeouts when connecting to the apiserver. This increases the
    number of pods that run the verify-network test before starting the conformance
    test, to ensure the network has really settled. There should be approximately
    four passes of the job per node.

commit f1e8406ab8a7f3cfca8a87798fa75a633effe214
Author: Joseph Wright <joseph.wright@clearme.com>
Date:   Mon Oct 28 20:49:37 2019 -0400

    Update IAM policy for masters to build NLBs
    
    This allows creating a Service of `type: LoadBalancer` with the annotation
    `service.beta.kubernetes.io/aws-load-balancer-type: nlb` to create a network
    load balancer.

commit 0fc8296358f07e98d7fab4b43406ab4bb997d6fc
Author: Joseph Wright <joseph.wright@clearme.com>
Date:   Tue Oct 29 21:40:37 2019 -0400

    Add subnet tagging to keights-stack Ansible role
    
    This adds the option to tag node subnets for autodiscovery by the controller
    manager for the creation of ELBs.

commit e498643c3f787ba8fbbba4cf915a18dd1c4d570d
Author: Joseph Wright <joseph.wright@clearme.com>
Date:   Tue Oct 29 21:43:19 2019 -0400

    Add missing documentation of `docker_options` for node groups

commit 1d75c158e896040f39a6435e5b3531abbad79675
Author: Joseph Wright <joseph.wright@clearme.com>
Date:   Tue Oct 29 22:42:56 2019 -0400

    Give each Lambda its own IAM role
    
    This scopes the permissions of each Lambda only to use the API calls needed,
    instead of sharing permissions among all Lambdas.

commit a6d491cd9277bff2b324b02945bb1de971a59a94
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Nov 4 00:24:54 2019 -0500

    Update AMI to Debian Buster
    
    This includes the following changes:
    * Run debootstrap with variant=minbase for a more minimal base
    * Trim packages which are installed
    * Use a more idiomatic directory layout for Ansible playbook

commit 2b5581025daaaa325f7fa42cd1054d69d72247b6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Nov 4 00:31:53 2019 -0500

    Update requirements.txt for Ansible in CI

commit 9d92d755626a0434f1675b7801b31f3ee20686e7
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 12 07:32:02 2019 -0500

    Add the ability to choose etcd mode
    
    The `keights-stack` Ansible role can now be given an option `etcd_mode`, which may
    be either `external` or `stacked`, defaulting to `stacked` where etcd runs on the
    masters. If `external`, then etcd runs in its own autoscaling group.

commit a032efd4698c39b00fcaa9c593fb36219b071444
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Nov 13 08:29:52 2019 -0500

    Tighten security groups
    
    * Masters can communicate with each other only on required ports
    * Nodes can communicate with Masters only on required ports

commit 98430d144db119e5a819c7498cfa019172c4936d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Nov 15 18:29:51 2019 -0500

    Update CI cluster versions
    
    The v1.15 branch now can build with an external etcd, so an `upgrade_from` has been
    added for the external etcd CI cluster. Versions have been updated to the latest
    v1.15 release.

commit 6ac095820a447cd472a990a61561de2e74246246
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 16 00:35:41 2019 -0500

    Update README with v1.15 CI jobs

commit 759642077e57845f688cb94254885c636c04f6c6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Nov 21 22:40:45 2019 -0500

    Add the ability to delete a cluster
    
    The `keights-stack` Ansible role now takes a `state` parameter which may be
    either `present` or `absent`, defaulting to `present` to keep the existing
    behavior. CI pipelines now ensure stacks are deleted after testing clusters.

commit 61e3061dd64f15dc2ebedd92e281707ea502127b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Nov 29 17:13:36 2019 -0500

    Remove defaults from kubeadm configs to reduce size

commit 90c4a447c08f6cef8c2b498a9ee09c0b6eb68c26
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Nov 29 17:15:02 2019 -0500

    Add the ability to override kubeadm config templates
    
    Master and node CloudFormation stacks are given parameters which may contain
    a Go template for kubeadm init and join configurations. If a value is not
    given, the default built into the AMI will be used instead.

commit d299fbff8e5260250ac7043df51c34da82802f30
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 30 14:34:29 2019 -0500

    Make creation of IAM resources optional
    
    IAM roles and instance profiles can now be passed as parameters instead of
    being built by the stack, for environments where IAM is managed separately.

commit 1f04db0d1d85a09cd533b909a4e6d8659ae8d39b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Nov 30 14:35:58 2019 -0500

    Fix subnet tagging in example cluster

commit 26b1ed8aa7d026fd5837d3edd7aaf1623f6a7c39
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Dec 2 19:10:20 2019 -0500

    Add options to use spot instances with node groups
    
    This replaces all LaunchConfigurations with LaunchTemplates, which have more
    options for spot instances. Only the node groups see a functional change from
    switching to LaunchTemplates, however. The master and etcd stacks have been
    changed to use them for consistency with node groups.

commit b0b92863f77d4420546c5342e7f7a0504521e6b0
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Dec 2 23:39:06 2019 -0500

    Clean up handling of kubeadm config templates as parameters
    
    When passing kubeadm configs, using an empty string as a CloudFormation
    parameter for a zero value is better than a nested quoted empty string.

commit 886318b0a494436af7a74087c415d3830875a40f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Dec 3 15:29:30 2019 -0500

    Update Ansible roles to support Ansible 2.9
    
    Since modifying the autoscaling groups in the CloudFormation templates to use
    LaunchTemplates instead of LaunchConfigurations, Ansible needs to be updated
    in order to be able to run with state=absent. This is because in older versions
    of Ansible, the ec2_asg_facts module assumed that autoscaling groups had a
    LaunchConfiguration, and the absent.yml tasks uses that module.
    
    Changes include:
    * Updating all relevant requirements.txt files to use Ansible 2.9.x and
      other updated dependencies.
    * Updating roles to replace *_facts modules with newer *_info modules.
    * Replacing k8s_raw module with k8s module.
    * Applying calico manifests in two parts to work with newer k8s module.

commit c63f99faa3e5a7ef6cb586761fd92cc8b0af8580
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Dec 3 17:33:33 2019 -0500

    Update README to remove no-op CI job status

commit 0e7426a46f0f9c2109b992e04f078a5b3a558ca2
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Dec 3 22:10:18 2019 -0500

    Improve waiting for network to come up
    
    This changes to wait for pods to be ready instead of running, and adds
    additional network related pods to the label selectors.

commit 9677ae03c55aec469bd59aa8b7d00e52b742c05d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Dec 9 17:43:27 2019 -0500

    Update kubeadm configs for v1.17 after running config migrate

commit 85e91cbd9ff2d556156b9317e850883c4542aa21
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Dec 9 17:45:47 2019 -0500

    Remove running of iptables in legacy mode in AMI
    
    See https://github.com/kubernetes/kubernetes/pull/82966.

commit c039936c07f1b49aaf920bca8c8276fc4f107fe8
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Dec 9 18:19:38 2019 -0500

    Update version for external etcd to match kubeadm

commit 750ee187f90270f021fe661255458027f5ac1720
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Dec 9 19:45:55 2019 -0500

    Remove extra volume for kube-controller-manager
    
    In v1.17, kubeadm adds a volume for flex volume plugins, so it is no longer
    necessary to add this, and it conflicts with the one created by kubeadm.

commit 3cec1c2c33e9d477de1479401b7ad290b3f08fcd
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Dec 9 17:56:52 2019 -0500

    Update CI jobs and README for v1.17

commit 0952922f401b1679c07024763df34fb7d5b48ad6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Dec 12 20:44:46 2019 -0500

    Update sonobuoy and kubectl for CI e2e task

commit 6dcf69a3b164c0658b8b2d2f1870b7b396275cb0
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Dec 12 21:00:11 2019 -0500

    Update calico to 3.10.2
    
    This includes cleanups to the manifest, and init container for modifying the
    node to be a route reflector, now that calicoctl includes a `patch` command.

commit 8075907bc5ad2e283cce6d6e0f103532ac59b6c7
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Dec 12 21:03:05 2019 -0500

    Add a boolean option to allocate node CIDRs
    
    This should be set to false when using calico, which includes its own IPAM.

commit b193c5be50cd2351eaa2fcf9abb553e82a7c1731
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Dec 13 15:09:35 2019 -0500

    Use systemd cgroup driver instead of cgroupfs
    
    See https://github.com/kubernetes/kubeadm/issues/1394 for details.

commit 083b77d66b3f454c98617d468d1a950ca691f94d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Dec 15 01:17:18 2019 -0500

    Mount etcd volume in keights binary instead of systemd mount
    
    Systemd mount units cannot do retries in case of failure or dependency
    failures, so mounting is being put into the keights binary for better
    error handling. Now the of the EBS volume, creating of the filesystem,
    and mounting of the filesystem is together in one place instead of having
    the mounting be a separate process with more chance of failure. The mount
    is now placed directly into /etc/fstab instead of using a systemd unit.

commit d8050879da563e7828022143a939b5c291e43be2
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Dec 16 12:33:11 2019 -0500

    Add the ability to taint nodes

commit 2bd3e9939f3ea08924609ceed39f44b6128ffc97
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Mar 18 11:09:31 2020 -0400

    Update Ansible versions in Python requirements.txt files

commit b0c7cd6c1964331175e164953d02774b8021a11b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Mar 25 18:24:52 2020 -0400

    Update CI files and assets for v1.18
    
    * Clusters in upgrade jobs will upgrade from v1.17.
    * Sonobuoy is updated to v0.17.2.
    * The kubectl used in CI jobs is updated to v1.18.0.
    * Update README with CI job statuses.

commit f1dc604030dc49aa5af62f5db0fedef66d7a3f45
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Apr 11 18:34:39 2020 -0400

    Update sonobuoy version to 0.18.0 for running e2e tests

commit 5a95d6cdd65bab2e2e0cfaa60737265151bc4066
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat May 23 16:07:49 2020 -0400

    Pin `docker-ce-cli` to the same version as `docker-ce`
    
    With a newer version of Docker having been released, if `docker-ce-cli`
    is not pinned, a newer version than `docker-ce` will get installed and
    cause the error "client version 1.40 is too new. Maximum supported API
    version is 1.39".

commit 6a47136faa69fbc1da00a0b039202afcc5dd01be
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Jun 14 17:51:11 2020 -0400

    Upgrade Calico CNI plugin to 3.14.1

commit c14f656d14e8f30ed0014957162a46657f6c9895
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Jun 20 00:50:06 2020 -0400

    Update Concourse pipeline to use newer GitHub PR resource
    
    This replaces the deprecated `jtarchie/pr` with `teliaoss/github-pr-resource`.

commit 7656d68f730bf4b30c11b82df4a306dc00e4f6be
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Jun 20 01:11:07 2020 -0400

    Remove `kubernetes-cni` package from AMI
    
    This is now being bundled in the `kubelet` package, see:
    https://github.com/kubernetes/release/pull/1309.

commit a5c7c30e4565b40c3ee49a200c347fe99462f23b
Author: Joseph Wright <joseph.wright@googlecloud.corp-partner.google.com>
Date:   Tue Jun 30 18:53:15 2020 -0400

    Fix pipeline task comparing git commits
    
    With the newer `teliaoss/github-pr-resource` Concourse resource being used for
    GitHub PRs, comparison of git commits works differently. This changes the task
    to look in a file written by the resource to get the head commit of the PR.

commit f88897344a36d7a9d63ec10ce1e39669d7ccae22
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Jun 23 23:34:24 2020 -0400

    Allow to skip kube-proxy addon for `kubeadm init`
    
    This makes it so that when `keights_stack.enable_kube_proxy` is `false`,
    `kubeadm` will receive the argument `--skip-phases=addon/kube-proxy`. Along with
    this, `keights_system.network.replace_kube_proxy` may be set to `true`, if using
    kube-router, to enable it to act in place of kube-proxy.

commit 1c92c4bdc6817d87f35e6a3c8a91dbcb65f87f0e
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Jul 31 13:48:40 2020 -0400

    Upgrade kube-router to v1.0.1
    
    Changes to the kube-router manifest include:
    * Add portmap CNI plugin
    * Use *.conflist format for CNI config, with cleanup for old config
    * Mount /run/xtables.lock in kube-router container

commit ded1adb3218c18323af4343e04dfaa25ee1ccdbb
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Aug 22 17:31:37 2020 -0400

    Define Sonobuoy conformance image version
    
    Normally sonobuoy uses the conformance image version to match the version of
    Kubernetes, however the image for v1.18.8 is not found in gcr.io.

commit 1408a47ef3690b773879eb26e51d0c5b6ad044c4
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Aug 28 01:20:30 2020 -0400

    Update default etcd version for Kubernetes v1.19

commit a544880dc9588fc67110a22fcac6d008452210cc
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Aug 28 01:39:22 2020 -0400

    Update CI directory for Kubernetes v1.19.x
    
    * Add new directories for building v1.19 clusters and for upgrading from v1.18
    * Update run-e2e Concourse task to use new versions of sonobuoy and kubectl

commit d613d107b11b03a859966e37461aff7b38b8def9
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Aug 28 01:42:07 2020 -0400

    Update Kubernetes dashboard to v2.0.3

commit 24f0e647eb9f31c16f00661771218a6c4f2e4860
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Aug 28 02:07:13 2020 -0400

    Replace hyperkube image in kube-router manifest
    
    The hyperkube image is gone as of Kubernetes v1.19. This will now use
    bitnami/kubectl:1.18.8, as there is not a v1.19.x version yet available.

commit 4c96c8bf8e6582c3915a251cada50e99ff3c4b51
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Aug 28 02:15:26 2020 -0400

    Update README for v1.19 CI status

commit fc58de5dc20ff90e8b7d9e2099d63be995e39bac
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Aug 29 00:50:46 2020 -0400

    Move deprecated kubelet argument to config file

commit 41200616258b809daedd0bb6776aa809a546aed7
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Aug 29 23:17:16 2020 -0400

    Update dependencies in requirements.txt files used for Ansible

commit 8c2ba87a5637585578e9426b5725e3cf482f0273
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Aug 30 14:27:24 2020 -0400

    Stop passing --pod-infra-container-image to the kubelet
    
    This configuration is now handled by kubeadm.

commit 17516c0d0fd14219365908a9538df917f9d48153
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Sep 7 15:42:14 2020 -0400

    Add /bin/sh and dependencies to controller-manager

commit 4873c3cf13c1096de5c42eb58d05576fc5a74ec0
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Sep 8 21:16:05 2020 -0400

    Add icons to Concourse pipeline resources

commit 45b6f92e0d06cec2304467b0263f06cfdff1fdb9
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 17 22:00:14 2020 -0500

    Update Python requirements.txt files for Ansible

commit f888e74e4f1dcfe3f2de01f7589bd787a35380da
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Nov 17 23:19:24 2020 -0500

    Update to Go 1.15

commit ba5c729324773cda18049e605ddea0cd56da3265
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Dec 10 00:32:42 2020 -0500

    Remove Dashboard tolerations for master nodes

commit fa7d1378fbd2a33194df0ca2bf0a43599c173fd5
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Dec 10 00:58:11 2020 -0500

    Update CI files for Kubernetes 1.20

commit af65de50767f102ed5f7ddf8ea8e4f0083f451d1
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Dec 12 00:16:17 2020 -0500

    Update external etcd version to be in sync with kubeadm

commit ba7a0cf0a978a4ecef702b864c2b551beb850553
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Dec 15 18:57:40 2020 -0500

    Update CI images to use a registry mirror
    
    To avoid docker hub rate limiting, images will be pulled from
    cloudboss.jfrog.io/containers.

commit 9fcb10d30b335a518f37754e0458ec1e3f1ce485
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Feb 19 22:18:54 2021 -0500

    Update Python dependencies
    
    Update all requirements.txt files for Ansible and dependencies. This includes
    the requirements.txt used for Packer image builds. Updating Ansible for Packer
    requires setting the ansible-remote provisioner setting `use_proxy: false`, to
    avoid Ansible hanging. This also requires an updated version of Packer.
    
    The images used for Concourse have been updated to a newer Python version so
    that they get a newer version of pip for installing cryptography, which fails
    on an older version.

commit e93555c3edb3c0fa3ab120c753b2695959c613c3
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Feb 20 15:10:15 2021 -0500

    Install and configure grub in chroot when creating AMI
    
    It seems that debconf no longer does this when installing grub-pc.

commit 003280799c393e0dbb5994b4973b5fb1a2040326
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Feb 21 22:34:44 2021 -0500

    Use `try` on pipeline `ensure` step
    
    This is added so that the whole pipeline doesn't fail if there is an error
    when deleting the stack.

commit f433a30cd89ff417a67366263d8d50197abe6755
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Apr 9 00:56:51 2021 -0400

    Update kubeadm init config for v1.21
    
    * The kubelet cgroup driver now defaults to systemd.
    
    * Add extra argument to the apiserver for --service-account-jwks-uri. If it is
      not given, it is derived from the external hostname but the internal port.
      This causes a timeout when running e2e tests.

commit 6f223e21747946fd9fc89b5656b2d68a286f017a
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Apr 9 23:20:33 2021 -0400

    Update README and example variables regarding subnet tags
    
    In Kubernetes v1.21, tagging subnets is not required to create load
    balancers. Keights will still support tagging them. See
    https://github.com/kubernetes/kubernetes/pull/97431.

commit bda787ff4000a29ee678209604724e2d5ea0f565
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Apr 11 00:19:45 2021 -0400

    Update Python requirements.txt files

commit 7e99097706c46fc2436c6f746f0db7068c04693b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Apr 11 00:22:24 2021 -0400

    Update ci files for Kubernetes v1.21

commit 0db746e28d1cd32bb7ba557ccfe54976759a02be
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Apr 11 16:31:36 2021 -0400

    Update kube-router to v1.2.1

commit 1407ed3fcbe19aeb2b78f561184a7e3906b9be5e
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Apr 17 18:19:49 2021 -0400

    Improve behavior of stack deletion
    
    Deleting the etcd or master auto scaling group could be prone to failure because
    the etcd volumes need to be detached from the instances before the stack is
    deleted, and sometimes the volumes reattached before the instances were deleted.
    
    This disables health checks and replacement of unhealthy instances in the auto
    scaling group so that new machines will not be created that will reattach the
    etcd volumes to themselves while the stack is being deleted.

commit 9a93d57f1c0b202e27906f457fe154983b0f520a
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Apr 17 18:24:59 2021 -0400

    Clean up Ansible `when` conditions
    
    This is a style cleanup that removes surrounding quotes when not required.

commit d9c9f564b5d385b75ead4bb8dc1c7c190fc134b1
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Apr 17 18:53:24 2021 -0400

    Use Ansible collections in module names

commit 72fa36cb314fc7637e8da410e7e744e3f298ffa4
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Apr 17 19:03:37 2021 -0400

    Consistify YAML indentation in Ansible roles

commit 49dd2350a70bba0179a9f96951b9fceb702b2ef5
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Jun 22 23:02:27 2021 -0400

    Upgrade Sonobuoy to 0.52.0 for e2e tests

commit 54d96c26cabf14c95136b3894f64c7377195543d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Jun 24 00:12:44 2021 -0400

    Update Python requirements for example and CI stacks

commit 9b89133221751ba04b062a8e29d1bbaf5edc37c1
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Jun 24 00:59:47 2021 -0400

    Update Go version and dependencies

commit ce030759ae5e0bae8bc5e0afcf3e8c00ea7c79c2
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Jul 19 13:15:40 2021 -0400

    Update default kube router image to v1.3.0

commit 4cd864a1f2e481aab479bd5153af469908706a65
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Aug 6 23:09:29 2021 -0400

    Switch container runtime from Docker to containerd
    
    Changes include:
    * Replacing docker installation and configuration in AMI with containerd.
    * Update systemd services for external etcd to use containerd.
    * Changing mounts to use UUIDs instead of labels in AMI, as the mount
      points were previously used as labels, and `/var/lib/containerd` is longer
      than the maximum label length of 16 on ext4.
    * Write `/etc/containerd/config.toml` in CloudFormation templates instead of
      `/etc/docker/daemon.json`.
    * Export `CONTAINER_RUNTIME_ENDPOINT` in /etc/profile.d so that crictl uses
      /run/containerd/containerd.sock.
    * Update references to docker in documentation, for example replacing "docker
      image" with "container image".

commit c2541bbf178cfcc3ab52ef284436146b328ebe5c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Aug 8 02:34:22 2021 -0400

    Update volumizer to use UUID instead of device for mount
    
    This makes the etcd mount consistent with the mounts for other filesystems
    as configured in the AMI.

commit c9b49c79ff83cdeb280ee5499890dbcbd7edc6dc
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Aug 8 02:37:03 2021 -0400

    Update kubeadm init and join configs for v1beta3

commit 60f111d76f1ba584ec64ab40ad937caaeed60af8
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Aug 8 03:00:18 2021 -0400

    Sync external etcd version with kubeadm for Kubernetes v1.22

commit 99ef756d0a447973848f27176e2a52bbe266f898
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Aug 8 03:15:11 2021 -0400

    Fix conditionals in kube router manifest template
    
    Changes include:
    * The `write-kubeconfig` init container is not needed if
      `keights_system.network.rr_node_label` is left as the default, so
      the `if` is raised up to remove it.
    * Remove `--kubeconfig` argument to kube-router and let it use service account.
    * The `endif` should have been below the volumeMounts of the
      `annotate-node` init container.

commit 4ffa516a44e8efdc7c831c01e8069ef48ed9f3ba
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Aug 8 03:39:30 2021 -0400

    Add conditional to Ansible task when deleting a stack
    
    The task to suspend processes failed if the auto scaling group was already
    deleted, so this fixes it so that it skips the task instead.

commit 66d2f2689548d6dd492b95c806f511f7487ae037
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Aug 8 23:15:50 2021 -0400

    Fix assertions for the number of subnets in `keights-stack` role
    
    When running etcd in "stacked" mode, the assertion for the number of subnets
    should be done for the masters, and when running it in "external" mode, the
    assertion should be done for etcd.

commit 1c31b71335dbec29f43e71eefc6808c05c1ca9ab
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Aug 8 23:54:06 2021 -0400

    Update CI files for Kubernetes v1.22

commit fbe35bf931a59177a63747aee972a5f4d9be0ed2
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Aug 10 00:04:01 2021 -0400

    Upgrade Calico CNI plugin to 3.20.0

commit 9c1c5406ce68baa90e024ef745518e06ff019044
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Aug 14 00:49:09 2021 -0400

    Pass apiserver into instances from CloudFormation
    
    Kube-router needs to communicate with the apiserver on the load balancer when
    `replace_kube_proxy: true`. This writes the DNS name of the load balancer to a
    file on all instances so it can be mounted as a volume for kube-router. An init
    container uses the value from the file to expand a kubeconfig template
    placeholder.
    
    This also modifies the kube-router manifest to store the kubeconfig template in a
    ConfigMap instead of writing it with a shell heredoc.

commit 11cc7642056fa40727a7add1895235d123d18eb9
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Aug 14 21:56:39 2021 -0400

    Update default kube router image to v1.3.1

commit 98d5a3bd7c28be7f6436d6f4fdb7d92aa3c9cb02
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Aug 16 18:07:22 2021 -0400

    Run iptables in legacy mode
    
    This fixes an intermittent crash when starting kube-router. See
    https://github.com/cloudnativelabs/kube-router/issues/1155.

commit ea096f848b216b9662a7d75e59ed884b7c688d88
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Aug 16 18:10:02 2021 -0400

    Make it easier to build development AMIs
    
    This adds a `dev-mode` variable to the Packer build. When set to `true`,
    the Ansible playbook will intentionally exit with an error. This should
    be used together with the `-on-error=abort` argument to Packer so that an
    image is not created. When using `dev-mode`, the Ansible provisioner's
    `keep_inventory_file` option will also be set to `true`, so that Ansible
    can be run again manually using the inventory.

commit aeec27d4c131439c00ea4c4d3c4622e6922c79ac
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Aug 16 18:19:50 2021 -0400

    Update Debian base version to bullseye

commit dae2ad8035e47d8de514e6c4197ea4e0ef097da8
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Aug 16 18:21:08 2021 -0400

    Use crictl config file
    
    Instead of setting the containerd socket for crictl with an environment
    variable, use /etc/crictl.yaml. This is more explicit and doesn't depend
    on the shell sourcing in /etc/profile.d/crictl.sh.

commit bfa59707273b176720b5c7ce62bc80a278a4f3c6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Aug 16 18:32:35 2021 -0400

    Update containerd options
    
    This sets the configuration version to 2 and updates the pause container
    to be in sync with upstream Kubernetes.

commit 52d93270573c80ad8cf439a850e33d5738858f07
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Aug 16 18:34:40 2021 -0400

    Clean up kube-router init container
    
    This is a small change to put the `.` character of the temporary file
    in the file name and not before the full path.

commit 0f921969cb4489ad9db41dbcda4bbb3e94b52290
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Aug 16 18:36:54 2021 -0400

    Update AMI description

commit 0ef156cc1ea20729b0dfd7f461caa834beeb7fad
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Aug 17 15:34:15 2021 -0400

    Optimize s3-upload CI task
    
    This puts the check for a test results tarball before anything else so
    that it can exit quickly if there is nothing to do.

commit 1ee2bf881215757f259e2f0fc42719eae3e7fc0c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Tue Aug 17 15:45:27 2021 -0400

    Update version to upgrade from for CI

commit f7ed27c9e2a67927eee69e2f8081b6117ef08d54
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Sep 16 18:48:35 2021 -0400

    Update version to upgrade from for CI

commit e7fb46c322fe39664f50731ff9e3098e7ca81890
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 30 18:47:48 2021 -0400

    Update version to upgrade from for CI

commit 352c14ab60525b0fe5b2ce54f39c5da2e7fa1cb2
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 30 19:21:19 2021 -0400

    Sync python version for CI containers

commit 4a627776341247bf48107de84ca209871ee94bc5
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 30 19:43:16 2021 -0400

    Remove CI directory for previous version

commit 0c25bc1a46d2df18957ff29098f90f0d5b6ad891
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Oct 30 19:45:55 2021 -0400

    Update requirements.txt for Ansible

commit 1792faea54680160a2aa43ac46e2fb43a8c5f5ea
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Oct 31 02:03:12 2021 -0400

    Update sonobuoy for conformance tests to v0.54.0

commit ca87db4e4ba97bdf44cdee23fc1224e05618551e
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Nov 24 10:22:56 2021 -0500

    Update version to upgrade from for CI

commit a66cb6c8a1be801debc77519cc0b707f559a3333
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Dec 15 22:19:54 2021 -0500

    Update kubeadm init config for v1.23

commit ea4d439425dc2cad3ff33a2729e9e324d5cbf24f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Dec 15 22:21:10 2021 -0500

    Update CI files for v1.23

commit 5685b5069b7554cbcbeefa4fd8802b376a5dd742
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Jan 28 01:41:22 2022 -0500

    Update default kube-router image to 1.4.0

commit 11491f50193a5f00f613e7203b036d74b991e7da
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Jan 28 19:53:59 2022 -0500

    Update AMI to boot from UEFI
    
    This includes updating packer and converting build.json to the new HCL2 syntax.

commit f00c3832dbbef8c5713e570b206f3344f976a4c9
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Feb 25 01:04:30 2022 -0500

    Add AWS EBS CSI driver
    
    This is to support CSI Migration updates as of Kubernetes 1.23.0.
    
    Changes include:
    * aws-ebs-csi-driver deployment
    * ebs-csi-node daemonset
    * snapshot-controller deployment
    * snapshot-validation-webhook deployment
    * cert-manager, to generate the keypair for snapshot-validation-webhook

commit 461b447ffd335c3e6194867b07dcfdca3aa926a4
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Feb 26 21:58:21 2022 -0500

    Update version to upgrade from for CI

commit a88411c717f6864ef0be04544d89a787e0d8ff7e
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Feb 26 22:06:31 2022 -0500

    Increase retries for applying manifests for keights-system role

commit 57c5093006d462e42347d468dc62d03841e40d18
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Mar 9 00:37:02 2022 -0500

    Update to Go 1.17
    
    This updates the Makefile to use `go install` instead of `go get` for goreleaser
    to avoid modifying go.mod. The go.mod is also regenerated with `go mod tidy`.

commit b1a600502657a872fac9ae203aad3644bba14574
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Mar 9 00:43:07 2022 -0500

    Update Go dependencies
    
    This updates and Go Kubernetes libraries to v1.23.4. The kube_ca Lambda has been
    modified to work with changes in the newer Kubernetes version, specifically some
    certificate and key generation functions have been renamed and have different
    arguments or return values.

commit 6dc42c2f7907ae6154fd43c6d39b8bca4baa893d
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Wed Mar 9 23:02:44 2022 -0500

    Update Ansible and Python dependencies
    
    This includes changes to module names in roles to sync with Ansible
    updates.

commit f937de5e4d23a0d52117ce4ba5f0965599ec8c1b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat Mar 19 14:36:05 2022 -0400

    Update version to upgrade from for CI

commit 3008527f9418efeff35b8b377d47210cfa9aeae6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Apr 24 00:22:31 2022 -0400

    Update repository for CI images

commit 17deda561861436f00a3b1334b42cde550d900a0
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Apr 24 20:11:06 2022 -0400

    Update version to upgrade from for CI

commit c2248593c97580e53d3c2ac6d1f6f729ebb28b5c
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Apr 25 01:24:56 2022 -0400

    Refresh network pods after rollout has finished in CI
    
    In some cases, the network pods require a refresh after upgrading,
    so this deletes all network pods after the rollout has finished.

commit c10c816763d44879430412ef05f5ba9dd268fe01
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat May 7 15:08:06 2022 -0400

    Update pause image for v1.24
    
    Update to match kubeadm constants:
    https://github.com/kubernetes/kubernetes/blob/v1.24.0/cmd/kubeadm/app/constants/constants.go#L428

commit c30e166be26c013ca4f027c02526de8e62423676
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat May 7 15:29:02 2022 -0400

    Update etcd version for v1.24
    
    Update to match kubeadm constants:
    https://github.com/kubernetes/kubernetes/blob/v1.24.0/cmd/kubeadm/app/constants/constants.go#L323

commit 54b5e5605dd04c6286d27215c57e9262387741fd
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat May 7 15:41:25 2022 -0400

    Update taints and tolerations for v1.24
    
    Updates per release notes:
    https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.24.md

commit 0a43fb24560bf77279b5b9a219257d8a75d0dc2f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat May 7 15:44:56 2022 -0400

    Update path to containerd socket in kubeadm config

commit fb0e0a86060eaba14a053b91d33315850991734f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat May 7 15:50:40 2022 -0400

    Update CI files and README for v1.24

commit b8eead6d4d9a93ea66338c8880330384289b59a7
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat May 7 16:04:18 2022 -0400

    Update Ansible dependencies

commit 40d8f6b03ff192bb616984308bfff49eff7b864b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat May 7 16:07:41 2022 -0400

    Use fully qualified Ansible module names

commit a76429bee488e6bf8948c3fa4cc71935df17f02b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat May 7 16:08:07 2022 -0400

    Remove paths to unmount in AMI playbook

commit a37f77dfab58c811d0f955ccc4140968415000f8
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sat May 7 16:18:36 2022 -0400

    Update Kubernetes Go dependencies for v1.24

commit 9360b7c40e515b2e2a8714af1b47af515f4d9ff7
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Jun 10 23:16:59 2022 -0400

    Update version to upgrade from for CI

commit 24b898d35c7b20e4d488217871eb2113c1219915
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Jun 27 01:28:46 2022 -0400

    Update version to upgrade from for CI

commit 7ef66bdc9b3cc53f0520bc5443d3e0df19ae62f9
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Jul 21 21:14:52 2022 -0400

    Update version to upgrade from for CI

commit 544a999abd4ce44a23229467d31bb65131047874
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Aug 19 19:29:34 2022 -0400

    Update version to upgrade from for CI

commit 147a76d5e7fc99cada293dee89818e5779663cb5
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Aug 26 00:25:20 2022 -0400

    Update CI files and README for v1.25

commit eefd9935b6d18f8799d606792b136556726646ae
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Aug 26 00:26:18 2022 -0400

    Update kube-router to v1.5.1
    
    This includes updates to images used in the kube-router manifest:
    * busybox:1.35.0
    * bitnami/kubectl:1.24.4

commit 14d3dc0af1fdf82709fdee809525c731d3b7c4b3
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Aug 26 00:57:15 2022 -0400

    Update Go to 1.19

commit 288abf72ce4727505abfcaa658be5070c0a08a53
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Aug 26 00:58:12 2022 -0400

    Update Go dependencies
    
    This updates Kubernetes libraries to v1.25 compatible versions,
    and updates all transitive dependencies.

commit c8fe381d5ba673c75e6928ff97e12b37b95a1174
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Sep 15 00:04:50 2022 -0400

    Update calico to 3.24.1

commit 1e50251edb18a5fae12bf9e22afbf009867f3ab0
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Sun Sep 18 03:21:41 2022 -0400

    Add VPC config to stack lambdas
    
    This ensures lambdas run within the configured VPC when
    `keights_stack.lambda_subnets` is defined. The lambdas used by
    Keights do not accept any external requests, but this is a
    security requirement in some environments.
    
    The security group is configured to allow only outbound traffic
    on port 443 so they can access Amazon APIs.
    
    This changes `AWS::IAM::Role` CloudFormation resources to include
    `ManagedPolicyArns` instead of the `AWS::IAM::ManagedPolicy`
    resources including `Roles`, to ensure that the policy is created
    and attached before the Lambdas are deployed.

commit 00f9f96d8d64cad77f8f3d68a9b413263757d565
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Sep 19 00:05:32 2022 -0400

    Update Python runtime for lambda in CloudFormation

commit 48a90b78474925c82bb8f972165b00fabe865660
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Aug 26 00:34:56 2022 -0400

    Update Ansible and its dependencies

commit c031766c0fd45672abd0cf38bcae8ed58861413b
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Thu Sep 22 22:02:29 2022 -0400

    Make etcd volumes configurable
    
    This adds `volume_type`, `volume_iops`, and `volume_throughput` to
    the configuration of etcd volumes.

commit 4eb7ca34ff12d98a64ac5f345d646e270a555b9f
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Sep 23 00:12:15 2022 -0400

    Update deprecated camel case to snake case
    
    This is to fix a deprecation warning in the
    `community.aws.route53_info` module.

commit fe3fe584cc5726da452412d2aca6f661f10b7e20
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Sep 26 21:56:53 2022 -0400

    Update default etcd version per kubeadm defaults

commit 3614c1447de40e90d940199544abd00d6ed619f6
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Sep 26 21:57:07 2022 -0400

    Update sandbox image per kubeadm defaults

commit 6f5e01e385c8e0e2c950a64e74ae8a74f3f10d10
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Sep 26 21:57:12 2022 -0400

    Update references to `k8s.gcr.io` to `registry.k8s.io`
    
    See https://github.com/kubernetes/k8s.io/wiki/New-Registry-url-for-Kubernetes-(registry.k8s.io).

commit 877df91f71c277080079f89bdea66ab6e7e5a3cb
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Mon Sep 26 21:57:16 2022 -0400

    Update `policy/v1beta1` to `policy/v1` for `PodDisruptionBudget`
    
    See https://kubernetes.io/docs/reference/using-api/deprecation-guide/#poddisruptionbudget-v125.

commit 6429fc7d403f976e20caf1900f6e529f596ff1b1
Author: Joseph Wright <rjosephwright@gmail.com>
Date:   Fri Sep 30 20:25:16 2022 -0400

    Update version to upgrade from for CI
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
