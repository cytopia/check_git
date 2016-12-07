# Check git

Nagios plugin to verify a git directory.

[![Build Status](https://travis-ci.org/cytopia/check_git.svg?branch=master)](https://travis-ci.org/cytopia/check_git)
 [![Latest Stable Version](https://poser.pugx.org/cytopia/check_git/v/stable)](https://packagist.org/packages/cytopia/check_git) [![Total Downloads](https://poser.pugx.org/cytopia/check_git/downloads)](https://packagist.org/packages/cytopia/check_git) [![Latest Unstable Version](https://poser.pugx.org/cytopia/check_git/v/unstable)](https://packagist.org/packages/cytopia/check_git) [![License](https://poser.pugx.org/cytopia/check_git/license)](http://opensource.org/licenses/MIT)
 [![POSIX](https://img.shields.io/badge/posix-100%25-brightgreen.svg)](https://en.wikipedia.org/?title=POSIX)
 [![Type](https://img.shields.io/badge/type-%2Fbin%2Fsh-red.svg)](https://en.wikipedia.org/?title=Bourne_shell)

## Example


### Check git status (without submodules)

```
$ check_git -d /shared/httpd/my-project/ -S -n My-Project
[SUCCESS] My-Project git repo is healthy.
[SUCCESS]  Git status: clean
```

### Check git status (with submodules) and make sure it is on branch 'develop'

```
$ check_git -d /shared/httpd/my-project/ -s -B develop -n My-Project
[CRITICAL] My-Project git repo has errors.
[SUCCESS]  Git status: clean
[CRITICAL] Git status: submodule(s) unclean
[SUCCESS]  Git Branch: on branch 'develop'
```

### Check if any tag is checked out
```
$ check_git -d /shared/httpd/my-project/ -t -n My-Project
[CRITICAL] My-Project git repo has errors.
[CRITICAL] Git Tag:    not on any tag
```

### Check if current HEAD (commit or tag) is gpg signed
```
$ check_git -d /shared/httpd/my-project/ -g -n My-Project
[SUCCESS] My-Project git repo is healthy.
[SUCCESS]  GPG Signed: Yes with key: 695128A2
[SUCCESS]  GPG Pubkey: available
[SUCCESS]  GPG Signer: cytopia <cytopia@everythingcli.org>
[SUCCESS]  GPG Trust:  ULTIMATE
```

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
