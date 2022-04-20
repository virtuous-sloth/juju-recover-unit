# Running the Scripts

The scripts are intended to be run from a machine with the juju client
installed using an account that is configured with access to the juju
controller as well as password-less sudo-to-root access on the local
machine. This latter requirement is to allow tar files to be extracted
by root to preserve file ownership and modes.

The scripts are to be run in sequence with the output reviewed at each
step. Scripts 00-04 are non-destructive. Script 05 is the one that makes
changes on the target machine with the juju unit agent that is stuck
with an 'installing agent' message.

Run the following in two separate shell session to observe:
```bash
watch --color juju status --color
```
and
```bash
juju debug-log
```
Please:
- run script 00 and review the environment variables produced
-- this is part of the output and is also stored in \_stage/00-LOCALENV
```bash
./recover-unit-00-set-stage default 2>&1 | tee -a 00.log
cat _stage/00-LOCALENV
```
- run scripts 01-03 indicated below, capturing and reviewing their output
- archive the \_stage directory and the \*.log files
-- upload the archive to the case for review before continuing

```bash
./recover-unit-01-get-src-directories 2>&1 | tee -a 01.log
./recover-unit-02-get-tgt-directories 2>&1 | tee -a 02.log
./recover-unit-03-stage-and-edit-new-target-directories 2>&1 | tee -a 03.log
vim _stage/03-diffs.log
```
Only run the following when confident about the results of the previous four
scripts.

```bash
./recover-unit-04-archive-and-upload-new-target-directories 2>&1 | tee -a 04.log
./recover-unit-05-recover-unit 2>&1 | tee -a 05.log
```
