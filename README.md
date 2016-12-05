# Check git

Nagios plugin to verify a git directory.


## Usage

```shell
USAGE: check_git -d <git dir> [-n [<name>]] [-s|-S] [-r|-R <remote>] [-b [<branch>]|-t [<tag>]] [-g]
                 -h
                 -v

check_git can validate a git repository by different requirements
You can have normal output or nagios (-n) compatible output to
integrate this into your monitoring system.

 Required arguments:

   -d <git dir>       Specify path to git repository.

 Optional arguments (output):

   -n [<name>]        Create nagios check outout. You can optionally
                      add a name for the output display.

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
   -b [<branch>]      Check if repository is checkout out on a branch.
                      No detached HEAD or tag.
                      You can also optionally specify the branch name the repository
                      is supposed to be on.
                      '-b' and '-t' are mutually exclusive.

   -t [<tag>]         Check if repository is checkout out on a tag.
                      No detached HEAD or branch.
                      You can also optionally specify the tag name the repository
                      is supposed to be on.
                      '-b' and '-t' are mutually exclusive.

   -g                 Check if current commit (independent of branch, tag or detached HEAD)
                      is signed and has a valid GPG signature.
                      For this to work, you will have to add your trusted
                      GPG public keys locally.

 Version and Help:

   -v                 Show version information
   -h                 Show this help screen
```
