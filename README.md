# Script Documentation
Only the first script (00) takes an argument, the model. It gathers all the
local information needed by the remaining scripts.

## recover-unit-00-set-stage
- Takes one argument, the name of the model
- Creates a directory for staging all work, \_stage, by default
  - When run multiple times, will rename old \_stage directory
- Gathers information about the local environment
- Stores that information in files named \_stage/00*

### Usage
```
Usage: recover-unit-00-set-stage model
       where model is the model name to check

       You can control the staging directory location:
         export RU_STAGEDIR=_stage

       You can enable debugging:
         export DEBUG=1
         recover-unit-00-set-stage modelname
       or:
         DEBUG=1 recover-unit-00-set-stage modelname
```
### Example Environment Variables Generated
- Active unit is a unit of the subordinate charm that is in an active state
```bash
export RU_ACTIVE_UNIT=canonical-livepatch/1
export RU_ACTIVE_UNIT_FILENAME=unit-canonical-livepatch-1
export RU_ACTIVE_UNIT_PWHASH=aCIe/WXewR4yMuqyHL7F2gPR
```
- Application of the subordinate charm that is stuck
  - Key-handling code has hard-coding to canonical-livepatch
```bash
export RU_APPLICATION=canonical-livepatch
export RU_APPLICATION_KEY=2840b051faf67443a8182526dfdb216e
```
- Juju controller:
  - The code tries to get the primary controller for R/W MongoDB tasks
```bash
export RU_CTRLR_IP=10.5.3.120
export RU_CTRLR_NUM=0
```
- Installing unit is the one stuck:
```bash
export RU_INSTALLING_UNIT=canonical-livepatch/0
export RU_INSTALLING_UNIT_FILENAME=unit-canonical-livepatch-0
export RU_INSTALLING_UNIT_PWHASH=a2Tcsel9QQbwDVFJyVa5lWUW
```
- Model:
```bash
export RU_MODEL_NAME=default
export RU_MODEL_UUID=309ea7a7-00c1-415a-8d02-b62a548d4755
```
- MongoDB:
```bash
export RU_MONGO_EXEC=/snap/bin/juju-db.mongo
export RU_MONGO_PW=pXhyKcRk8nm5UCSasm1eW555
```
- Staging directory:
```bash
export RU_STAGEDIR=_stage
```

## recover-unit-01-get-src-directories
-  
## recover-unit-02-get-tgt-directories

## recover-unit-03-stage-and-edit-new-target-directories

## recover-unit-04-archive-and-upload-new-target-directories

## recover-unit-05-recover-unit

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
  - this is part of the output and is also stored in \_stage/00-LOCALENV
```bash
./recover-unit-00-set-stage default 2>&1 | tee -a 00.log
cat _stage/00-LOCALENV
```
- run scripts 01-03 indicated below, capturing and reviewing their output
- archive the \_stage directory and the \*.log files
  - upload the archive to the case for review before continuing

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
