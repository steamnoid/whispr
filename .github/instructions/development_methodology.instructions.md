# Whispr - Development Methodology

## 🎯 E2E-FIRST + TDD FOR AI PROTOCOLS

### Core Philosophy:
**Start with real AI. Test everything. Zero assumptions about models.**

## 🔬 E2E-FIRST DISCOVERY

### Phase 1: Real AI Integration (Week 1)
```bash
# Start with actual AI models, not mocks
python test_real_ai.py --model=gpt-3.5-turbo
python test_real_ai.py --model=local_llama
python test_real_ai.py --model=ollama_mistral

# Discover actual behavior patterns
pytest tests/ai_discovery/ -v --real-models
```

### AI Behavior Discovery Tests:
```python
# tests/ai_discovery/test_rewrite_patterns.py
def test_gpt_rewrite_quality_polish_to_english():
    """Discover: How well does GPT handle Polish → English professional rewrites?"""
    raw = "Kurwa, znowu ten grant odrzucili!"
    expected_elements = ["professional", "respectful", "constructive"]
    
    result = ai_rewriter.generate_rewrites(
        raw, style="diplomatic", target="grant_committee"
    )
    
    # Real AI analysis - no mocking
    assert len(result.alternatives) >= 3
    assert all(is_professional_tone(alt.text) for alt in result.alternatives)
    assert any("feedback" in alt.text.lower() for alt in result.alternatives)

def test_ai_consistency_across_models():
    """Discover: Do different AI models produce similar quality for same input?"""
    test_cases = [
        "Fuck this deadline, I can't finish",  # Anger → Professional
        "You guys are totally wrong about this",  # Disagreement → Diplomatic
        "This idea sucks hard",  # Criticism → Constructive
    ]
    
    for model in ["gpt-3.5", "claude", "local_llama"]:
        results = []
        for case in test_cases:
            result = ai_rewriter_factory.create(model).generate_rewrites(
                case, style="professional", target="colleagues"
            )
            results.append(evaluate_rewrite_quality(case, result))
        
        # Quality consistency across models
        quality_scores = [r.overall_score for r in results]
        assert min(quality_scores) > 0.7  # Minimum quality threshold
        assert max(quality_scores) - min(quality_scores) < 0.3  # Consistency

def test_chainlink_integration_real_contract():
    """E2E test with actual Chainlink testnet contract"""
    # Deploy real test contract on Polygon Mumbai
    contract = deploy_test_contract("mumbai")
    
    # Real Chainlink Functions request
    tx_hash = contract.requestRewrite(
        message="This proposal is shit",
        style="dao_proposal",
        target_audience="governance_voters"
    )
    
    # Wait for actual Chainlink fulfillment
    result = wait_for_chainlink_response(tx_hash, timeout=300)
    
    assert result.success
    assert "proposal" in result.rewritten_message.lower()
    assert not any(profanity in result.rewritten_message for profanity in PROFANITY_LIST)
```

### Real Network Testing:
```python
# tests/integration/test_production_flow.py
def test_full_production_pipeline():
    """Test complete flow with real AI, real crypto, real time pressure"""
    
    # Real user scenario
    user_wallet = generate_test_wallet()
    raw_message = "Boss is being a total asshole about this project"
    
    # Step 1: Real AI rewrite
    start_time = time.time()
    response = requests.post("http://localhost:8000/rewrite", json={
        "message": raw_message,
        "style": "professional",
        "target_audience": "management"
    })
    
    ai_time = time.time() - start_time
    assert ai_time < 2.0  # Real performance requirement
    assert response.status_code == 200
    
    alternatives = response.json()["alternatives"]
    assert len(alternatives) >= 3
    
    # Step 2: Real cryptographic signing
    selected_index = 1  # Choose middle option
    signed_request = sign_with_private_key(
        message=alternatives[selected_index]["text"],
        private_key=user_wallet.private_key
    )
    
    selection_response = requests.post("http://localhost:8000/select", json={
        "request_id": response.json()["request_id"],
        "selected_index": selected_index,
        "user_signature": signed_request
    })
    
    assert selection_response.status_code == 200
    proof = selection_response.json()["signed_selection"]
    
    # Step 3: Real verification
    verification = requests.get(f"http://localhost:8000/verify?proof_hash={proof['proof_hash']}")
    assert verification.json()["signature_valid"]
    
    # Quality check: professional rewrite
    selected_text = alternatives[selected_index]["text"]
    assert "professional" in classify_text_tone(selected_text)
    assert not any(curse in selected_text.lower() for curse in ["asshole", "shit", "fuck"])
```

## 🧪 TDD IRON RULES FOR AI

### Rule 1: Test AI Models First
```python
# Write tests for AI behavior BEFORE implementing wrapper
def test_openai_api_response_format():
    """Test: OpenAI returns expected structure for our prompts"""
    client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))
    
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "Rewrite this message professionally"},
            {"role": "user", "content": "This sucks badly"}
        ],
        temperature=0.7,
        max_tokens=150
    )
    
    # Test actual API behavior
    assert response.choices[0].message.content
    assert len(response.choices[0].message.content) > 10
    assert response.usage.total_tokens > 0

# THEN implement the wrapper
class OpenAIRewriter:
    def generate_rewrites(self, message: str, style: str) -> List[RewriteOption]:
        # Implementation follows after test
        pass
```

### Rule 2: Test Edge Cases First
```python
def test_ai_handles_empty_message():
    """AI should gracefully handle edge case: empty input"""
    with pytest.raises(ValidationError, match="Message cannot be empty"):
        ai_rewriter.generate_rewrites("", "professional", "colleagues")

def test_ai_handles_extremely_long_message():
    """AI should handle edge case: message too long"""
    long_message = "A" * 10000  # 10k characters
    
    result = ai_rewriter.generate_rewrites(long_message, "formal", "board")
    
    # Should truncate or summarize, not crash
    assert result.success
    assert all(len(alt.text) < 500 for alt in result.alternatives)

def test_ai_handles_non_english_input():
    """AI should handle multilingual input gracefully"""
    test_cases = [
        ("Kurwa mać, co za gówno!", "polish"),  # Polish profanity
        ("这个想法很糟糕", "chinese"),  # Chinese criticism
        ("Cette idée est nulle", "french"),  # French criticism
    ]
    
    for message, language in test_cases:
        result = ai_rewriter.generate_rewrites(message, "diplomatic", "international")
        
        assert result.success
        assert len(result.alternatives) >= 2
        # Should produce English professional alternatives
        assert all(is_english(alt.text) for alt in result.alternatives)

def test_crypto_signature_edge_cases():
    """Test cryptographic signing with malformed inputs"""
    test_cases = [
        ("", "Empty message"),
        ("A" * 1000000, "Extremely long message"),  
        ("Message with \x00 null bytes", "Binary data"),
        ("🔥💯🚀 Emoji-only message", "Unicode characters"),
    ]
    
    for message, description in test_cases:
        # Should handle gracefully, not crash
        result = signer.sign_message_selection(
            original="test",
            selected=message,
            user_address="0x123",
            private_key="0xabc"
        )
        
        assert result.user_signature  # Must produce valid signature
        assert verify_signature(result)  # Must be verifiable
```

### Rule 3: Performance Tests Drive Architecture
```python
def test_ai_response_time_under_pressure():
    """Performance test: AI must respond <2s under concurrent load"""
    import asyncio
    import aiohttp
    
    async def single_request():
        async with aiohttp.ClientSession() as session:
            start = time.time()
            async with session.post("http://localhost:8000/rewrite", json={
                "message": "This deadline is impossible to meet!",
                "style": "diplomatic",
                "target_audience": "project_manager"
            }) as response:
                result = await response.json()
                elapsed = time.time() - start
                return elapsed, result
    
    # Test 100 concurrent requests
    tasks = [single_request() for _ in range(100)]
    results = await asyncio.gather(*tasks)
    
    response_times = [r[0] for r in results]
    
    # Performance requirements
    assert max(response_times) < 3.0  # No request takes >3s
    assert sum(response_times) / len(response_times) < 2.0  # Average <2s
    assert all(r[1]["alternatives"] for r in results)  # All successful

def test_memory_usage_stays_bounded():
    """Memory test: Stateless design must not leak memory"""
    import psutil
    import gc
    
    process = psutil.Process()
    initial_memory = process.memory_info().rss
    
    # Process 1000 requests
    for i in range(1000):
        response = requests.post("http://localhost:8000/rewrite", json={
            "message": f"Test message {i}",
            "style": "professional",
            "target_audience": "colleagues"
        })
        assert response.status_code == 200
        
        if i % 100 == 0:  # Check every 100 requests
            gc.collect()
            current_memory = process.memory_info().rss
            memory_growth = current_memory - initial_memory
            
            # Memory must stay bounded (stateless!)
            assert memory_growth < 100 * 1024 * 1024  # <100MB growth
```

## 🔄 TEST-DRIVEN DEVELOPMENT CYCLE

### 1. Red Phase: Write Failing Tests
```python
# tests/test_ai_rewriter.py
def test_formal_rewrite_removes_profanity():
    """FAILING TEST: AI should remove profanity in formal rewrites"""
    raw = "This fucking deadline is bullshit!"
    
    result = ai_rewriter.generate_rewrites(raw, "formal", "executives")
    
    # This will fail initially - no implementation yet
    assert len(result.alternatives) >= 3
    assert all(is_profanity_free(alt.text) for alt in result.alternatives)
    assert all(is_formal_tone(alt.text) for alt in result.alternatives)

def test_dao_proposal_style_includes_structure():
    """FAILING TEST: DAO proposal style should include governance structure"""
    raw = "I think we should change the tokenomics"
    
    result = ai_rewriter.generate_rewrites(raw, "dao_proposal", "governance")
    
    # This will fail - no DAO-specific logic yet
    governance_keywords = ["proposal", "vote", "implementation", "rationale"]
    selected = result.alternatives[0].text.lower()
    assert any(keyword in selected for keyword in governance_keywords)
```

### 2. Green Phase: Minimal Implementation
```python
# ai_rewriter.py - Minimal implementation to pass tests
class AIRewriter:
    def generate_rewrites(self, message: str, style: str, audience: str) -> RewriteResult:
        # Minimal implementation just to pass tests
        if style == "formal":
            # Simple profanity removal
            clean_message = remove_profanity(message)
            alternatives = [
                RewriteOption(text=f"I would like to address {clean_message}", confidence=0.8),
                RewriteOption(text=f"Please consider {clean_message}", confidence=0.7),
                RewriteOption(text=f"I respectfully submit {clean_message}", confidence=0.9),
            ]
        elif style == "dao_proposal":
            alternatives = [
                RewriteOption(text=f"Proposal: {message}. Rationale: [To be added]. Implementation: [To be defined].", confidence=0.8)
            ]
        else:
            # Fallback
            alternatives = [RewriteOption(text=message, confidence=0.5)]
        
        return RewriteResult(success=True, alternatives=alternatives)

def remove_profanity(text: str) -> str:
    profanity_map = {
        "fucking": "challenging",
        "bullshit": "unreasonable",
        "shit": "situation",
    }
    for bad, good in profanity_map.items():
        text = text.replace(bad, good)
    return text
```

### 3. Refactor Phase: Improve While Keeping Tests Green
```python
# Refactor to use real AI while maintaining test coverage
class OpenAIRewriter(AIRewriter):
    def __init__(self, api_key: str):
        self.client = OpenAI(api_key=api_key)
    
    def generate_rewrites(self, message: str, style: str, audience: str) -> RewriteResult:
        # Real AI implementation
        system_prompt = self._build_system_prompt(style, audience)
        
        try:
            response = self.client.chat.completions.create(
                model="gpt-3.5-turbo",
                messages=[
                    {"role": "system", "content": system_prompt},
                    {"role": "user", "content": message}
                ],
                temperature=0.7,
                max_tokens=300
            )
            
            # Parse AI response into alternatives
            alternatives = self._parse_ai_response(response.choices[0].message.content)
            return RewriteResult(success=True, alternatives=alternatives)
            
        except Exception as e:
            # Fallback to simple implementation
            return super().generate_rewrites(message, style, audience)
    
    def _build_system_prompt(self, style: str, audience: str) -> str:
        style_prompts = {
            "formal": "Rewrite the following message in a formal, professional tone suitable for business communication.",
            "diplomatic": "Rewrite the following message diplomatically, removing any harsh language while preserving the core intent.",
            "dao_proposal": "Rewrite the following as a structured DAO governance proposal with clear rationale and implementation steps.",
        }
        
        base_prompt = style_prompts.get(style, "Rewrite the following message professionally.")
        return f"{base_prompt} Target audience: {audience}. Provide 3 alternative versions."
```

## 🔁 CONTINUOUS TESTING STRATEGY

### Test Categories:
1. **Unit Tests** (Fast, isolated)
   - AI prompt generation
   - Cryptographic functions
   - Input validation
   - Response formatting

2. **Integration Tests** (Medium speed, real services)
   - AI API calls with real models
   - Chainlink Functions integration
   - End-to-end request flows
   - Cross-model consistency

3. **E2E Tests** (Slow, full system)
   - User journey simulation
   - Production environment testing
   - Performance under load
   - Real blockchain interactions

### Test Execution:
```bash
# Fast feedback loop (runs on every commit)
pytest tests/unit/ -v --duration=10

# Medium feedback loop (runs on PR)
pytest tests/integration/ -v --real-ai --duration=60

# Full feedback loop (runs nightly)
pytest tests/e2e/ -v --real-chainlink --real-ai --duration=300
```

### CI/CD Pipeline:
```yaml
# .github/workflows/test.yml
name: Whispr TDD Pipeline

on: [push, pull_request]

jobs:
  unit-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run unit tests
        run: pytest tests/unit/ -v
  
  integration-tests:
    runs-on: ubuntu-latest
    needs: unit-tests
    steps:
      - uses: actions/checkout@v3
      - name: Run integration tests with real AI
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: pytest tests/integration/ -v --real-ai
  
  e2e-tests:
    runs-on: ubuntu-latest
    needs: integration-tests
    steps:
      - uses: actions/checkout@v3
      - name: Run E2E tests
        env:
          CHAINLINK_TESTNET_URL: ${{ secrets.CHAINLINK_TESTNET_URL }}
          POLYGON_MUMBAI_RPC: ${{ secrets.POLYGON_MUMBAI_RPC }}
        run: pytest tests/e2e/ -v --real-chainlink
```

## 📊 QUALITY METRICS

### Code Coverage Requirements:
- **Unit tests**: 95%+ coverage
- **Integration tests**: 85%+ coverage
- **E2E tests**: 70%+ coverage

### AI Quality Metrics:
- **Intent preservation**: >90% (semantic similarity)
- **Style adherence**: >85% (automated evaluation)
- **Profanity removal**: 100% (zero tolerance)
- **Response consistency**: <20% variance across models

### Performance Benchmarks:
- **AI response time**: <2000ms (p95)
- **Signature verification**: <50ms (p99)
- **Memory usage**: <512MB per worker
- **Concurrent throughput**: >1000 req/s

---

**STATUS**: TDD methodology defined. E2E-first + AI discovery + crypto testing.
**NEXT**: Implementation roadmap with week-by-week deliverables.
