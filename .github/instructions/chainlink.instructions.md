# Whispr – chainlink.instructions.md

You will expose a `/whispr` FastAPI endpoint callable from Chainlink Functions.

Steps:
- Accept: `user_id`, `input_text`
- Call AI to generate rewrites
- Return: rewrites, hash of input, unsigned proof placeholder

No wallet logic, no smart contracts yet. Keep JSON payload <1KB.
