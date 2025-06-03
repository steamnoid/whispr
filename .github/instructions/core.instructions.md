# Whispr – core.instructions.md

Your task is to build a minimal AI-assisted messaging relay with user intent verification.

Key modules:
- main.py: accepts raw input and user ID
- ai_rewriter.py: returns AI-rewritten variants
- signer.py: generates signed proof of chosen version
- response.py: formats and returns JSON

You must:
- Avoid state
- Keep code as simple and modular as possible
- No database, no fullstack, no extras

User submits text → AI proposes rewrites → user selects → system signs the final output.
