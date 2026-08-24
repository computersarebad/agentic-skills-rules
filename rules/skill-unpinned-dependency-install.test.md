# Test cases for skill-unpinned-dependency-install

# ruleid: skill-unpinned-dependency-install
pip install requests

# ruleid: skill-unpinned-dependency-install
npm install -g some-cli-tool

# ruleid: skill-unpinned-dependency-install
gem install rails

# ok: skill-unpinned-dependency-install
pip install requests==2.31.0

# ok: skill-unpinned-dependency-install
npm install express@4.18.2

# ok: skill-unpinned-dependency-install
pip install -r requirements.txt

# ok: skill-unpinned-dependency-install
gem install rails -v 7.1.3
