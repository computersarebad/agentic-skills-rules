# Test cases for skill-hidden-unicode

# ruleid: skill-hidden-unicode
This line hides ​ an instruction.

# ruleid: skill-hidden-unicode
A joiner smuggled between ascii‍letters is suspicious.

# ok: skill-hidden-unicode
Emoji sequences like 🏴‍☠️ use a legitimate zero-width joiner.

# ok: skill-hidden-unicode
This line is plain visible ASCII text only.
