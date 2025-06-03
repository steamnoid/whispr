# Whispr – ai.instructions.md

AI rewriter module should:
- receive raw message
- return 2–3 alternative rewrites (as strings)
- assume access to a local LLM or mock function (no OpenAI key)

Design with possible Chainlink Functions integration in mind (later).
Use clean interface: `get_rewrites(input_text: str) -> List[str]`
