# mj-git-repo1
mj git repo for testing
#!/bin/bash

# Check if the file exists
if [ ! -f auk54c ]; then
  echo "File auk54c not found!"
  exit 1
fi

# Read the file line by line
while IFS= read -r fqdn; do
  # Run ssh-keygen -R for each fqdn
  ssh-keygen -R "$fqdn"
done < auk54c

echo "Completed running ssh-keygen -R on all FQDNs in the file."
