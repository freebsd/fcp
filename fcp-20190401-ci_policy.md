---
authors: Kristof Provost <kp@FreeBSD.org>,
    Li-Wen Hsu <lwhsu@FreeBSD.org>
state: feedback
---

# FCP 20190401-ci_policy: CI policy

This document aims to present a set of policies to ensure that FreeBSD src is
consistently in a buildable and functional state.

## Problem Statement

FreeBSD has a continuous integration system (currently setup by Jenkins, at
https://ci.freebsd.org), to validate that the src tree compiles in the
most supported architectures and configurations.  The same tool also runs
automated tests for src.

Sometimes developers commit changes which break compilation on one or more
platforms or cause test failures.  Finally, a panic or a similar problem during
a test run may prevent us from obtaining any test results at all.

One of the main functions of the CI system is letting committer know if there
is a build failure or test regression as early as possible.  The FreeBSD's CI
system is configured to send failure or unstable notification to the committer
and the dev-ci@freebsd.org mailing list. We hope the original committer watch
the states and commit a fix or revert themselves.  If these failure or unstable
reports are not addressed in a timely manner a number of negative effects
compound:

 - Other issues may be hidden by this failure
 - Development on affected platforms may be blocked
 - Downstream users may struggle to find a usable version

While developers are usually very responsive to reports of build failures they
may not always know about this issue, or may not have the time to immediately
address it.

Other developers do not always feel empowered to revert such commits.

## Proposed Solution

We propose a policy explicitly empowering developers to revert commits which
cause compilation problems, test failures or panics during tests.

We describe the recommended steps to take, and notifications to send before
doing so, as well as the recommended timeout periods.

### Notification

When a failure occurs in the FreeBSD's CI system (ci.freebsd.org)'s
non-experimental jobs [1], anyone (committers and non-committers alike) is
encouraged to investigate this. The commit which triggered this failure should
be identified, and the author contacted.

The notification should be sent to all of the following:
 - The original author
 - Other people, if any, who've touched the relevant code after the triggering
   commit
 - The dev-ci@freebsd.org list, so that duplicate notifications can be avoided
   as much as possible

### Waiting period

Once the notification has been sent the author of the problematic commit should
be given a reasonable amount of time to respond.
This time will vary depending on the severity of the problem and how many
platforms it affects:

 - If a commit broke compilation on all platforms it can be reverted
   immediately
 - If a commit broke compilation on a tier 1 platform it can be reverted after
   4 hours
 - If a commit broke compilation on tier 2 or 3 platforms it can be reverted
   after 12 hours
 - If a commit triggers a panic during the automated test run (and thus blocks
   all tests) it can be reverted after 24 hours
 - If a commit causes a test regression (of one of the automated tests) it can
   be reverted after 48 hours

Note that these are minimal guidelines. If the author of the problem has
responded with a request for more time this should be respected.

### Revert

When it does become necessary to revert a commit the commit message should
explicitly mention the revision it is reverting, and explain why.

For example:

    Revert rNNNNNN:

    This does not build in i386:
      <Compiler error message>

[1] Non-experimental job means a job which sends build status notification to
    the committer or the dev-ci@freebsd.org list.
