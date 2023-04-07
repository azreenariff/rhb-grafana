#!/bin/bash

# Check the Pacemaker status
pcs_status=$(pcs status | grep crond | awk '{print $NF}')

# Get the hostname of the local node
local_node=$(hostname)

# Define the remote host to copy files to
if [[ "$local_node" == "myrhbombapi01rhvp" ]]; then
    remote_host="myrhbombapi02rhvp"
else
    remote_host="myrhbombapi01rhvp"
fi

# Define the local file to copy files to
local_file="/var/spool/cron/apache"

# Define the location to copy files to on the remote host
remote_dir="/var/spool/cron/"

# Check if crond is running on the local node
if [[ "$pcs_status" == "$local_node" ]]; then

  # Use rsync to copy only updated files from /var/spool/cron/apache to the remote host
  rsync -avz --update $local_file "$remote_host:$remote_dir"

  echo "Files copied successfully to $remote_host"

else
  echo "crond is not running on the local node - files not copied"
fi
