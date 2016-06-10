# Scheduled File Upload to ATSD


## Before You Begin

1.  Make sure that `/opt/nmon/nmon` binary exists and is executable.
2.  Launch nmon console to make sure nmon works correctly:

```sh
/opt/nmon/nmon
```

## Upload Files to ATSD by API with Wget
To start uploading files to ATSD every hour with Wget, perform the
following steps:

### Step 1.

Create a file `/opt/nmon/nmon_script.sh` and add the following row to
the cron schedule:

```sh
0 * * * * /opt/nmon/nmon_script.sh
```

### Step 2.

After that, add the following content to the file
`/opt/nmon/nmon_script.sh` replacing `atsd_user`,` atsd_password`,
`atsd_server` with actual credentials:
```sh
    #!/bin/sh
    fn="/nmon/nmon/`date +%y%m%d_%H%M`.nmon";pd="`/opt/nmon/nmon -F $fn -s 60 -c 60 -T -p`"; \
    while kill -0 $pd; do sleep 15; done; \
    wget -t 1 -T 10 --user=atsd_user --password=atsd_password --no-check-certificate -O - --post-file="$fn" \
    --header="Content-type: text/csv" "https://atsd_server/api/v1/nmon?f=`basename $fn`"
```

## Upload Files to ATSD with UNIX Socket {.av-special-heading-tag itemprop="headline"}


To start uploading files to ATSD every hour with UNIX socket perform the
following steps (note that you will require `bash`):

### Step 1.

Create a file `/opt/nmon/nmon_script.sh` and add the following row to
the cron schedule:

```sh
0 * * * * /opt/nmon/nmon_script.sh
```

### Step 2.

Add the following contents to` /opt/nmon/nmon_script.sh`
replacing `atsd_server` with ATSD hostname or IP address:
```sh
    #!/bin/bash
    fn="/opt/nmon/`date +%y%m%d_%H%M`.nmon";pd="`/opt/nmon/nmon -F $fn -s 60 -c 60 -T -p`"; \
    while kill -0 $pd; do sleep 15; done; \
    { echo "nmon p:default e:`hostname` f:`hostname`_file.nmon"; cat $fn; } > /dev/tcp/atsd_server/8081
```

## Upload Files to ATSD with Netcat {.av-special-heading-tag itemprop="headline"}

To start uploading files to ATSD every hour with Netcat, perform the following steps:

### Step 1.

Create a file `/opt/nmon/nmon_script.sh` and add the following row tothe cron schedule:

```sh
0 * * * * /opt/nmon/nmon_script.sh
```

### Step 2.

Add the following contents to the `/opt/nmon/nmon_script.sh` file
replacing `atsd_server` with ATSD hostname or IP address:

```sh
    #!/bin/sh
    fn="/opt/nmon/`date +%y%m%d_%H%M`.nmon";pd="`/opt/nmon/nmon -F $fn -s 60 -c 60 -T -p`"; \
    while kill -0 $pd; do sleep 15; done; \
    { echo "nmon p:default e:`hostname` f:`hostname`_file.nmon"; cat $fn; } nc atsd_server 8081
```