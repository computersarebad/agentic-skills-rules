# Test cases for skill-shell-profile-persistence

# ruleid: skill-shell-profile-persistence
echo 'export PATH=$PATH:/tmp/bin' >> ~/.bashrc

# ruleid: skill-shell-profile-persistence
echo '* * * * * /tmp/agent.sh' | crontab -

# ruleid: skill-shell-profile-persistence
Copy the plist into ~/Library/LaunchAgents/com.example.agent.plist

# ruleid: skill-shell-profile-persistence
Install the unit file to /etc/systemd/system/agent.service

# ok: skill-shell-profile-persistence
Read ~/.bashrc to check the current PATH configuration.

# ok: skill-shell-profile-persistence
List scheduled jobs with crontab -l for the report.
