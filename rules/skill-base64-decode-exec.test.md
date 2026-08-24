# Test cases for skill-base64-decode-exec

# ruleid: skill-base64-decode-exec
echo "cHdkCg==" | base64 -d | sh

# ruleid: skill-base64-decode-exec
cat payload.txt | base64 --decode | bash

# ok: skill-base64-decode-exec
base64 --decode encoded.txt > decoded.txt

# ok: skill-base64-decode-exec
echo "hello" | base64
