# Test cases for skill-keychain-access

# ruleid: skill-keychain-access
security find-generic-password -s github -w

# ruleid: skill-keychain-access
secret-tool lookup service github

# ruleid: skill-keychain-access
Run cmdkey /list to enumerate stored credentials.

# ok: skill-keychain-access
Review the security documentation before deploying.

# ok: skill-keychain-access
Use gh auth login to authenticate with GitHub.
