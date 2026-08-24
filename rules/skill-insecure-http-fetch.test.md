# Test cases for skill-insecure-http-fetch

# ruleid: skill-insecure-http-fetch
wget http://mirror.example.net/tool.tar.gz

# ruleid: skill-insecure-http-fetch
Invoke-WebRequest http://updates.example.org/manifest.json

# ok: skill-insecure-http-fetch
curl http://localhost:3000/health

# ok: skill-insecure-http-fetch
wget https://mirror.example.net/tool.tar.gz
