#!/bin/bash

# List of jump servers
JUMP_SERVERS=(
    "ausplxijmp06.infra.labops.att.com"
    "srlxijmp06.infra.labops.att.com"
    "mtnlxijmp06.infra.labops.att.com"
)

# Files to sync
FILES=("$HOME/connect_zone.sh" "$HOME/zones")

# Loop through each jump server and sync files
for SERVER in "${JUMP_SERVERS[@]}"; do
    echo "Syncing to $SERVER..."
    rsync -avz ${FILES[@]} mj239e@$SERVER:$HOME
done

echo "Sync completed."
