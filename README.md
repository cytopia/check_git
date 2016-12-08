# Check git

Nagios plugin to verify a git directory.

[![Build Status](https://travis-ci.org/cytopia/check_git.svg?branch=master)](https://travis-ci.org/cytopia/check_git)
 [![Latest Stable Version](https://poser.pugx.org/cytopia/check_git/v/stable)](https://packagist.org/packages/cytopia/check_git) [![Total Downloads](https://poser.pugx.org/cytopia/check_git/downloads)](https://packagist.org/packages/cytopia/check_git) [![Latest Unstable Version](https://poser.pugx.org/cytopia/check_git/v/unstable)](https://packagist.org/packages/cytopia/check_git) [![License](https://poser.pugx.org/cytopia/check_git/license)](http://opensource.org/licenses/MIT)
 [![POSIX](https://img.shields.io/badge/posix-100%25-brightgreen.svg)](https://en.wikipedia.org/?title=POSIX)
 [![Type](https://img.shields.io/badge/type-%2Fbin%2Fsh-red.svg)](https://en.wikipedia.org/?title=Bourne_shell)

---

#### Member of [awesome-nagios-plugins](https://github.com/cytopia/awesome-nagios-plugins)

---

## Nagios / Icinga integration

There are two ways how to integrate this with nagios or icinga.

### 1. Direct approach (not recommended)

Use `check_git` directly from nagios and test a repository every time a check is triggered. In order for the output to be usaeble by Nagios / Icinga, you will have to use `-n <NAME>`, which will amongst others remove the shell colors from the output. Correct exit codes for `success`, `warning`, `error` and `unknown` are thrown by default.

**Example**

```
check_git -d <PATH_TO_GIT_DIR> -s -n <PROJECT_NAME>
```

### 2. Approach to just parse a logfile (recommended)
This approach reduces the overhead to check a repository every 5min by nagios, and rather check the logfile created by `check_git`.
Sometimes a full check (status, gpg, remote check, etc) can take up to a few seconds and u don't want to stress the system every 5min.

For that to work, you simply add `check_git` to your crontab and only check your repository every 4 hours for example.
Nagios / Icinga can still check the logfile every 5 minutes.

**Crontab**
```
0 0,4,8,12,16,20 * * * /full/path/to/check_git -d <GIT_DIR> -n <PROJECT_NAME> -l /var/log/git/<project-name>.log
```
This will update the logfile under `/var/log/git/<project-name>.log` every 4 hours and nagios/icinga can however check on it as often as they want via:

**The actual check**
```
check_git_log -f /var/log/git/<project-name>.log
```


## Examples

The following examples show each options separately, you can of course combine most checks.

### Check git status

Without submodules (`-s`)
```
$ check_git -d /shared/httpd/my-project/ -s -n My-Project
[SUCCESS] My-Project git repo is healthy.
[SUCCESS]  Git status: clean
```
With submodules (`-S`)
```
$ check_git -d /shared/httpd/my-project/ -S -n My-Project
[CRITICAL] My-Project git repo has errors.
[SUCCESS]  Git status: clean
[CRITICAL] Git status: submodule(s) unclean
```


### Check status of HEAD (branch or tag)

HEAD must be on a branch (any branch `-b`)
```
$ check_git -d /shared/httpd/my-project/ -b -n My-Project
[SUCCESS] My-Project git repo is healthy.
[SUCCESS]  Git Branch: on branch 'develop'
```

HEAD must be on branch *develop* (`-B`)
```
$ check_git -d /shared/httpd/my-project/ -B develop -n My-Project
[SUCCESS] My-Project git repo is healthy.
[SUCCESS]  Git Branch: on branch 'develop'
```

HEAD must be on a tag (any tag `-t`)
```
$ check_git -d /shared/httpd/my-project/ -t -n My-Project
[CRITICAL] My-Project git repo has errors.
[CRITICAL] Git Tag:    not on any tag
```

HEAD must be on tag 0.3 (`-T`)
```
$ check_git -d /shared/httpd/my-project/ -T 0.3 -n My-Project
[CRITICAL] My-Project git repo has errors.
[CRITICAL] Git Tag:    on tag '0.2', but should be on: '0.3'
```

### Check GPG signature of commit/tag
Check if the current commit (or if HEAD is a tag, the current tag) is signed with gpg and valid (`-g`).
```
$ check_git -d /shared/httpd/my-project/ -g -n My-Project
[SUCCESS] My-Project git repo is healthy.
[SUCCESS]  GPG Signed: Yes with key: 695128A2
[SUCCESS]  GPG Pubkey: available
[SUCCESS]  GPG Signer: cytopia
[SUCCESS]  GPG Trust:  ULTIMATE
```

Check if the current commit (or if HEAD is a tag, the current tag) is signed with gpg, valid and matches one of the specified key ids (`-G`).
```
$ check_git -d /shared/httpd/my-project/ -G 695128A2,00000000,11111111,22222222 -n My-Project
[SUCCESS] My-Project git repo is healthy.
[SUCCESS]  GPG Signed: Yes with expected key: 695128A2
[SUCCESS]  GPG Pubkey: available
[SUCCESS]  GPG Signer: cytopia
[SUCCESS]  GPG Trust:  ULTIMATE
```

### Check Diff against remote

Check if you have new code to pull (only makes sense if you are on a branch). Check excluding submodules (`-R`)
```
$ check_git -d /shared/httpd/my-project/ -R origin -n My-Project
[SUCCESS] My-Project git repo is healthy.
[SUCCESS]  Git remote: equals with 'origin'
```

Check if you have new code to pull (only makes sense if you are on a branch). Check including submodules (`-r`)
```
$ check_git -d /shared/httpd/my-project/ -r origin -n My-Project
[CRITICAL] My-Project git repo has errors.
[SUCCESS]  Git remote: equals with 'origin'
[CRITICAL] Git remote: submodule(s) differs from 'origin'
```

### Show additional verbose output

Show some verbose output (will also be visible within nagios extended output)
```
$ check_git -d /shared/httpd/my-project/ -v -n My-Project
[SUCCESS] My-Project git repo is healthy.
[INFO]     Bin:        git version 2.10.2
[INFO]     Path:       /shared/httpd/my-project/
[INFO]     Submodules: 2
[INFO]     HEAD:       on branch 'develop'
[INFO]     GPG:        signed 695128A2
```

Check if current commit (only makes sense if you have checked out a branch) is equal with

## Usage

```shell
Usage: check_git -d <git dir> [-s|-S] [-r|-R <remote>] [-b] [-B <branch>] [-t] [-T <tag>]] [-g] [-G <hash>[,<hash>]] [-v]
OR               -d <git dir> [-n <name>] [-l logfile] [-s|-S] [-r|-R <remote>] [-b] [-B <branch>] [-t] [-T <tag>]] [-g] [-G <hash>[,<hash>]] [-v]
OR               -h
OR               -V

check_git can validate a git repository by different requirements
You can have normal output or nagios (-n) compatible output to
integrate this into your monitoring system.

 Required arguments:

   -d <git dir>       Specify path to git repository.

 Optional arguments (output):

   -v                 Be more verbose.

   -n <name>          Create nagios style check outout.
                      Removes colors and adds a project name to the first line.

   -l <logfile>       Log to file instead of stdout.
                      This is especially useful if you want to integrate this check via nagios.
                      You can then add a cronjob which periodically logs to file (as your deploy user)
                      and the nagios check simply parses the logfile via 'check_git_log'.
                      Requires '-n'.

 Optional arguments (checks):

   -s                 Check if git directory is clean.
                      This also take any submodules into account.
                      To prevent checking submodules use '-S'.
                      '-s' and '-S' are mutually exclusive.

   -S                 Check if git directory is clean (ignore submodules).
                      This ignores any submodules.
                      To also check against submodules use '-s'.
                      '-s' and '-S' are mutually exclusive.


   -r <remote>        Check if git repository is in sync with remote.
                      This option makes only sense, if your repository is
                      checked out on a branch that can be compared with remote.
                      This also take any submodules into account.
                      To prevent checking submodules use '-R'.
                      '-r' and '-R' are mutually exclusive.

   -R <remote>        Check if git repository is in sync with remote (ignore submodules).
                      This option makes only sense, if your repository is
                      checked out on a branch that can be compared with remote.
                      This ignores any submodules.
                      To also check submodules use '-r'.
                      '-r' and '-R' are mutually exclusive.
   -b                 Check if repository is checkout out on a branch.
                      No detached HEAD or tag.
                      '-b', '-B', '-t' and '-T' are mutually exclusive.

   -B [<branch>]      Check if repository is checkout out on the specified branch.
                      '-b', '-B', '-t' and '-T' are mutually exclusive.

   -t                 Check if repository is checkout out on a tag.
                      No detached HEAD or branch.
                      '-b', '-B', '-t' and '-T' are mutually exclusive.

   -T [<tag>]         Check if repository is checkout out on the specified tag.
                      No detached HEAD or branch.
                      '-b', '-B', '-t' and '-T' are mutually exclusive.

   -g                 Check if current HEAD is signed and has a valid GPG signature.
                      If current HEAD is a tag, the GPG signature of the tag is checked,
                      instead of the signature of the current commit.
                      For this to pass, you will also have to add your trusted
                      GPG public keys locally.
                      Returns:
                          Error, if not signed.
                          Warning, if signed, but pubkey is not available.
                          Warning, if signed, but pubkey is not trusted.
                          OK, if signed, pubkey is available and trusted.
                      '-g', and '-G' are mutually exclusive.

   -G <hash>[,<hash>] Check if current HEAD is signed by the given key id (hash) and has a valid GPG signature.
                      You can separate multiple key-id's via comma without space.
                      key-id (hash) must be the last 8 characters (all uppercase) of the key.
                      If current HEAD is a tag, the GPG signature of the tag is checked,
                      instead of the signature of the current commit.
                      For this to pass, you will also have to add your trusted
                      GPG public keys locally.
                      Returns:
                          Error, if not signed.
                          Error, if signed with a different key-id.
                          Warning, if signed, but pubkey is not available.
                          Warning, if signed, but pubkey is not trusted.
                          OK, if signed, pubkey is available and trusted.
                      '-g', and '-G' are mutually exclusive.

 Version and Help:

   -V                 Show version information
   -h                 Show this help screen
```
