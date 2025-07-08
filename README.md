# IOI submission tool

## Theory of operation

By default, contestants submit their solutions using the CMS web interface.
Alternatively, they can use the `ioisubmit` command provided by this package.
This command tries to submit the solution to CMS first and if it fails,
the submit is kept locally and moved to CMS after the end of the contest.

The `ioisubmit` command is typically called via wrapper scripts provided
in task packages (see `examples/*`). These scripts provide the task name,
so the contestants have to specify only the source file(s).

All submissions (regardless whether they made it to the CMS or not)
are recorded in the local storage directory (see below) in ZIP files
named `YYYYMMDD-HHMMSS-microsec:task.status`, where status is one of:

  - `cmsok` if accepted by CMS
  - `cmsrej` if rejected by CMS
  - `local` if recorded locally after CMS failure
  - `.status` can be missing if `ioisubmit` is still running

All actions are logged to the system log, including SHA2-256 hashes
of the stored ZIP files. It is recommended to set up remote logging,
so that if CMS fails, but the network is up, local submissions will be
attested to by the remote log, including trustworthy timestamps.

When the contest is over, all local storage directories are scanned
and submissions recorded locally are added to the CMS in order of
their increasing timestamps.


## Setup

Edit `CMS_URL`, `LOCAL_STORAGE`, and `LANGUAGE_EXTENSIONS` in `ioisubmit`.

Create local storage directories on contestants' machines, preferably
owned by the contestant user with permissions `700`.

Set up both local and remote syslog. Remote syslog should record messages
from each machine in a separate file.


## Collecting local submissions

After the contest is over, you need to download local submissions and
log files. Create a directory `submissions` with a sub-directory for
each contestant, named after the contestant's login name. The sub-directory
should contain:

  - `local-syslog` (local syslog from the contestant's machine)
  - `remote-syslog` (remote syslog from the contestant's machine)
  - `local-storage` (the local storage directory from the contestant's machine)

Run `./process-submissions`, which produces a log file (to stderr) and a script
(to stdout) that sends submissions to CMS using `./admin-submit`.


## Caveats

Works only with IP-based logins to CMS.
