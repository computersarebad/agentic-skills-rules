# Test cases for skill-sudo-or-world-writable

# ruleid: skill-sudo-or-world-writable
sudo apt-get install build-essential

# ruleid: skill-sudo-or-world-writable
chmod 777 /opt/agent/workdir

# ruleid: skill-sudo-or-world-writable
chmod -R a+rwx ./output

# ok: skill-sudo-or-world-writable
chmod 755 ./scripts/run.sh

# ok: skill-sudo-or-world-writable
Ask the user to run privileged installers themselves.
