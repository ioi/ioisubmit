# IOI submission tool

## Theory of operation

By default, contestants submit their solutions using the CMS web interface.
Alternatively, they can use the `ioisubmit` command provided by this package.
This command tries to submit the solution to CMS first and if it fails,
the submit is kept locally and moved to CMS after the end of the contest.

The `ioisubmit` command is typically called via wrapper scripts provided
in task packages (see `submit-cpp17` for an example). These scripts provide the
task name and language code, so the contestants have to specify only the source
file.

All submissions (regardless whether they made it to the CMS or not)
are recorded in the local storage directory (see below) in files
named `HHMMSS:microsec:task:lang.status`, where status is one of:

  - `.cmsok` if accepted by CMS
  - `.cmsrej` if rejected by CMS
  - `.local` if recorded locally after CMS failure
  - status can be missing if `ioisubmit` is still running

All actions are logged to the system log, including SHA2-256 hashes
of the submitted files. It is recommended to set up remote logging,
so that if CMS fails, but the network is up, local submissions will be
attested to by the remote log, including trustworthy timestamps.

When the contest is over, all local storage directories are scanned
and submissions recorded locally are added to the CMS in order of
increasing timestamps.

FIXME: Write scripts for the after-contest part.


## Setup

Edit `CMS_URL` and `LOCAL_STORAGE` in `ioisubmit`.

Create local storage directories on contestants' machines, preferably
owned by the contestant user with permissions `300`.

Check that the version of CMS you use supports the provisional submission
API in the contest web server.


## Caveats

Works only with IP-based logins to CMS.

Output-only tasks are not supported.

Tasks with multiple source files are not supported.
