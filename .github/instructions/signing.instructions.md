# Whispr – signing.instructions.md

This module handles local cryptographic signing (Ed25519 or ECDSA).

You must:
- sign final text chosen by user
- include user public key (mocked if needed)
- return: message + signature + hash of input + timestamp

Keep all cryptographic steps minimal and standalone.
