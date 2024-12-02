#!/bin/bash

# List of jump servers
JUMP_SERVERS=(
    "jump-us.example.com"
    "jump-eu.example.com"
    "jump-asia.example.com"
)

# Files to sync
FILES=("~/connect_zone.sh" "~/zones")

# Loop through each jump server and sync files
for SERVER in "${JUMP_SERVERS[@]}"; do
    echo "Syncing to $SERVER..."
    rsync -avz ${FILES[@]} user@$SERVER:~
done

echo "Sync completed."
