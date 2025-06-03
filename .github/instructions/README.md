# Whispr - Master Development Instructions

## 🎯 AUTONOMOUS DEVELOPMENT FRAMEWORK

### Project Overview:
**Whispr is a decentralized AI-assisted communication layer for Web3 that allows users to write raw messages, get AI-generated refined alternatives, choose one, and send it with cryptographic proof of intent.**

## 📚 INSTRUCTION FRAMEWORK

### Core Instructions (READ ALL BEFORE STARTING):

1. **[Project Vision](./project_vision.instructions.md)**
   - Mission and competitive advantages
   - Token economics (WHSPR)
   - Web3 integration scenarios
   - Success metrics

2. **[Technical Architecture](./technical_architecture.instructions.md)**
   - Stateless AI communication layer design
   - API endpoints and data flow
   - Chainlink Functions integration
   - Cryptographic message signing

3. **[Development Methodology](./development_methodology.instructions.md)**
   - E2E-first with real AI models
   - TDD iron rules for AI protocols
   - Testing strategy and CI/CD

4. **[Implementation Roadmap](./implementation_roadmap.instructions.md)**
   - 6-week delivery timeline
   - Week-by-week milestones
   - Technical deliverables

5. **[Minimalism Principle](./minimalism_principle.instructions.md)**
   - Zero tolerance for unnecessary code
   - Mandatory deletion ceremonies
   - File size and dependency limits

## 🚀 GETTING STARTED

### Prerequisites:
```bash
# Required tools
python 3.11+
docker
node.js 18+
git

# AI API access
export OPENAI_API_KEY="sk-..."
export ANTHROPIC_API_KEY="sk-ant-..."

# Blockchain access
export POLYGON_MUMBAI_RPC="https://..."
export CHAINLINK_TESTNET_URL="https://..."
```

### Initial Setup:
```bash
# 1. Clone and setup
git clone https://github.com/steamnoid/whispr.git
cd whispr
python -m venv venv
source venv/bin/activate

# 2. Install minimal dependencies
pip install fastapi uvicorn pydantic openai httpx cryptography

# 3. Start with TDD - write first test
mkdir tests
touch tests/test_ai_rewriter.py

# 4. Begin Week 1 development
echo "Starting Week 1: AI Core + Cryptographic Foundation"
```

### First Test (TDD Mandatory):
```python
# tests/test_ai_rewriter.py
def test_ai_rewriter_removes_profanity():
    """First test: AI must remove profanity in professional rewrites"""
    from ai_rewriter import rewrite_message
    
    raw = "This fucking deadline is bullshit!"
    result = rewrite_message(raw, "professional", "colleagues")
    
    assert len(result) >= 3  # Must return 3 alternatives
    assert all("fuck" not in alt.text.lower() for alt in result)
    assert all("bullshit" not in alt.text.lower() for alt in result)
    assert all(len(alt.text) > 10 for alt in result)  # Meaningful rewrites

# This test will fail - implement minimal code to pass
```

## 🔄 DEVELOPMENT WORKFLOW

### Daily Routine:
1. **Morning**: Review previous day's minimalism metrics
2. **TDD Cycle**: Red → Green → Refactor (30-min cycles)
3. **Real AI Testing**: No mocks, test with actual models
4. **Evening**: Delete unused code, update metrics

### Weekly Milestones:
- **Week 1**: Core AI + Crypto working locally
- **Week 2**: Chainlink testnet integration
- **Week 3**: Production hardening
- **Week 4**: Web3 ecosystem integration
- **Week 5**: Mainnet preparation
- **Week 6**: Public launch

### Quality Gates:
- All tests pass before any commit
- <200 lines per file (enforced)
- <10 dependencies total (enforced)
- >95% test coverage (enforced)
- Real AI models tested daily

## 🎯 SUCCESS CRITERIA

### Technical KPIs:
- **Response Time**: <2000ms (AI rewrites)
- **Throughput**: >1000 req/s per node
- **Uptime**: >99.9%
- **Code Quality**: >95% test coverage
- **Security**: Zero vulnerabilities

### Business KPIs:
- **Users**: >1000 DAU by Week 6
- **Volume**: >10k messages/day by Week 6
- **Revenue**: >$10k/month by Week 6
- **Satisfaction**: >95% user approval

### Web3 KPIs:
- **TVL**: >$1M by Week 6
- **Node Operators**: >50 by Week 6
- **Cross-chain**: >20% volume
- **DAO Participation**: >10% token holders

## ⚠️ CRITICAL REQUIREMENTS

### Non-Negotiable Rules:
1. **Zero unnecessary code** - Delete ruthlessly
2. **E2E-first** - Start with real AI, not mocks
3. **TDD mandatory** - No code without tests
4. **Stateless design** - No persistent storage
5. **Web3-native** - Crypto proofs for everything

### Immediate Failures:
- Any file >200 lines
- Any untested code
- Any unused dependency
- Any mock in E2E tests
- Any feature without user request

## 🛠️ TOOLS AND AUTOMATION

### Development Tools:
```bash
# Code quality
pip install pytest black mypy vulture radon

# Testing
pip install pytest-asyncio pytest-cov httpx

# Security
pip install bandit safety

# Performance
pip install locust artillery
```

### CI/CD Automation:
- Pre-commit hooks enforce minimalism
- All tests run on every commit
- Security scans on every PR
- Performance tests on release branches
- Automated deployment to staging

## 📊 MONITORING AND METRICS

### Daily Metrics:
- Lines of code (should decrease)
- Test coverage (should increase)
- Performance benchmarks
- AI quality scores
- User feedback

### Weekly Reviews:
- Code deletion ceremony (every Friday)
- Architecture review
- Performance optimization
- Security audit
- User experience analysis

## 🎉 LAUNCH CHECKLIST

### Pre-Launch Requirements:
- [ ] All tests passing (100%)
- [ ] Security audit completed
- [ ] Performance benchmarks met
- [ ] User acceptance testing done
- [ ] Mainnet contracts deployed
- [ ] Node operators recruited
- [ ] Marketing campaign ready

### Launch Day:
- [ ] Monitor all systems
- [ ] Track user acquisition
- [ ] Measure performance metrics
- [ ] Collect user feedback
- [ ] Prepare hot fixes if needed

---

**STATUS**: Complete autonomous development framework ready.
**ACTION**: Begin Week 1 development following all instructions.
**REMEMBER**: Every line of code must justify its existence.
