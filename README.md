#!/bin/bash

# Path to the zones file
ZONES_FILE=~/zones

# Check if the user provided a zone name
if [ -z "$1" ]; then
    echo "Usage: $0 <zone_name>"
    exit 1
fi

ZONE_NAME=$1

# Find the FQDN for the given zone name
FQDN=$(grep -i "^${ZONE_NAME} " "$ZONES_FILE" | awk '{print $2}')

# Check if the FQDN was found
if [ -z "$FQDN" ]; then
    echo "Error: Zone $ZONE_NAME not found in $ZONES_FILE"
    exit 1
fi

# Connect to the FQDN
ssh "$FQDN"
