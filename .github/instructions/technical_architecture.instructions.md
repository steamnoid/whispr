# Whispr - Technical Architecture

## 🏗️ STATELESS AI COMMUNICATION LAYER

### Core Philosophy:
**Zero state. Maximum modularity. Pure function communication pipeline.**

## 📐 SYSTEM ARCHITECTURE

### High-Level Design:
```
Raw Message → AI Rewriter → User Selection → Cryptographic Signing → Verified Output
```

### Component Structure:
```
whispr/
├── main.py                 # FastAPI application entry
├── ai_rewriter.py         # AI model integration
├── signer.py              # Cryptographic message signing
├── validators.py          # Input/output validation
├── types.py               # Core data structures
└── chainlink_adapter.py   # Chainlink Functions interface
```

## 🔧 CORE COMPONENTS

### 1. AI Rewriter (`ai_rewriter.py`)
```python
# Pure function: message → alternatives
async def generate_rewrites(
    raw_message: str,
    style: RewriteStyle,
    target_audience: str
) -> List[RewriteOption]

# Supported styles
class RewriteStyle(Enum):
    FORMAL = "formal"
    DIPLOMATIC = "diplomatic"
    TECHNICAL = "technical"
    MARKETING = "marketing"
    DAO_PROPOSAL = "dao_proposal"
```

### 2. Cryptographic Signer (`signer.py`)
```python
# Pure function: selected message → signed proof
def sign_message_selection(
    original: str,
    selected: str,
    user_address: str,
    private_key: str
) -> SignedSelection

# Proof structure
@dataclass
class SignedSelection:
    original_hash: str
    selected_text: str
    user_signature: str
    timestamp: int
    node_signature: str  # Node operator attestation
```

### 3. Request Validator (`validators.py`)
```python
# Input validation
def validate_rewrite_request(request: RewriteRequest) -> bool
def validate_message_length(message: str) -> bool
def validate_style_compatibility(style: str, message: str) -> bool

# Output validation
def validate_rewrite_quality(original: str, rewrite: str) -> bool
def validate_signature_integrity(signed: SignedSelection) -> bool
```

## 🌐 API DESIGN

### Core Endpoints:
```
POST /rewrite
├── Input: {message, style, audience}
├── Output: {alternatives[], request_id}
└── Time: <100ms

POST /select
├── Input: {request_id, selected_index, user_signature}
├── Output: {signed_selection, proof_hash}
└── Time: <50ms

GET /verify
├── Input: {proof_hash}
├── Output: {verification_status, original_hash, signature_valid}
└── Time: <10ms
```

### Request/Response Flow:
```json
// 1. Rewrite Request
POST /rewrite
{
  "message": "Kurwa, znowu ten grant odrzucili!",
  "style": "diplomatic",
  "target_audience": "grant_committee"
}

// Response
{
  "request_id": "req_abc123",
  "alternatives": [
    {
      "index": 0,
      "text": "We respectfully request feedback on the grant decision to improve future submissions.",
      "confidence": 0.92
    },
    {
      "index": 1,
      "text": "Could you please provide guidance on how we can strengthen our next grant application?",
      "confidence": 0.89
    },
    {
      "index": 2,
      "text": "We would appreciate constructive feedback to enhance our proposal approach.",
      "confidence": 0.85
    }
  ]
}

// 2. Selection Request
POST /select
{
  "request_id": "req_abc123",
  "selected_index": 1,
  "user_address": "0x742d35Cc6677C4532431",
  "user_signature": "0x..."
}

// Response
{
  "signed_selection": {
    "original_hash": "0xabc...",
    "selected_text": "Could you please provide guidance...",
    "user_signature": "0x123...",
    "node_signature": "0x456...",
    "timestamp": 1735632000,
    "proof_hash": "0x789..."
  }
}
```

## 🔗 CHAINLINK INTEGRATION

### Chainlink Functions Adapter:
```python
# chainlink_adapter.py
async def handle_chainlink_request(
    source_args: Dict[str, Any]
) -> ChainlinkResponse:
    """
    Process Chainlink Functions request
    Args from smart contract:
    - message: raw user message
    - style: rewrite style preference
    - callback_address: where to send result
    """
    
# Response format for Chainlink
@dataclass
class ChainlinkResponse:
    success: bool
    selected_message: str
    proof_hash: str
    gas_used: int
```

### DON Node Operation:
```python
# Node operator mode
class WhisprNode:
    def __init__(self, private_key: str, stake_amount: int):
        self.private_key = private_key
        self.stake = stake_amount
        self.reputation_score = 0.0
    
    async def process_request(self, request: RewriteRequest) -> NodeResponse:
        # AI processing + cryptographic attestation
        pass
    
    def sign_attestation(self, message_hash: str) -> str:
        # Node operator signature for quality/compliance
        pass
```

## 🧠 AI MODEL INTEGRATION

### Supported AI Backends:
```python
class AIBackend(Enum):
    LOCAL_LLAMA = "local_llama"      # Local Llama/Mistral
    OPENAI_GPT = "openai_gpt"        # OpenAI API
    ANTHROPIC = "anthropic_claude"   # Anthropic API
    OLLAMA = "ollama"                # Local Ollama
    CUSTOM = "custom_endpoint"       # Custom API
```

### Model Configuration:
```python
@dataclass
class AIConfig:
    backend: AIBackend
    model_name: str
    api_key: Optional[str]
    endpoint_url: Optional[str]
    max_tokens: int = 150
    temperature: float = 0.7
    system_prompt: str = "Rewrite the following message..."
```

### Quality Scoring:
```python
def calculate_rewrite_quality(
    original: str,
    rewrite: str,
    target_style: str
) -> QualityScore:
    """
    Assess rewrite quality:
    - Intent preservation (0.0-1.0)
    - Style adherence (0.0-1.0)
    - Clarity improvement (0.0-1.0)
    - Professional tone (0.0-1.0)
    """
```

## 💾 STATELESS DATA FLOW

### No Persistent Storage:
- **No database** - all data in request/response
- **No user sessions** - each request independent
- **No message history** - privacy by design
- **No API keys storage** - environment variables only

### Memory Pattern:
```python
# Request lifecycle
request = RewriteRequest(...)  # Input parsing
alternatives = await ai_rewriter.generate(request)  # AI processing
selection = user_selects(alternatives)  # User choice
proof = signer.sign_selection(selection)  # Cryptographic proof
response = format_response(proof)  # Output formatting
# End - no data retained
```

## 🔐 CRYPTOGRAPHIC DESIGN

### Message Integrity:
```python
# Hash original message
original_hash = keccak256(original_message.encode())

# Sign user selection
user_signature = sign_with_private_key(
    message=selected_text + timestamp,
    private_key=user_private_key
)

# Node operator attestation
node_signature = sign_with_private_key(
    message=original_hash + selected_text + user_signature,
    private_key=node_private_key
)

# Final proof
proof = {
    "original_hash": original_hash,
    "selected_text": selected_text,
    "user_signature": user_signature,
    "node_signature": node_signature,
    "timestamp": timestamp
}
```

### Verification Process:
```python
def verify_message_proof(proof: SignedSelection) -> VerificationResult:
    # 1. Verify user signature
    user_valid = verify_signature(
        proof.user_signature,
        proof.selected_text + proof.timestamp,
        user_public_key
    )
    
    # 2. Verify node attestation
    node_valid = verify_signature(
        proof.node_signature,
        proof.original_hash + proof.selected_text + proof.user_signature,
        node_public_key
    )
    
    return VerificationResult(user_valid, node_valid)
```

## ⚡ PERFORMANCE TARGETS

### Response Time Limits:
- **AI Rewrite**: <2000ms (3 alternatives)
- **Selection + Signing**: <100ms
- **Verification**: <50ms
- **Total Pipeline**: <2200ms

### Throughput Goals:
- **Concurrent requests**: 1000+ per node
- **Daily capacity**: 100k+ messages per node
- **Horizontal scaling**: Unlimited (stateless)

### Resource Efficiency:
- **Memory usage**: <512MB per worker
- **CPU utilization**: <80% during peak
- **Network bandwidth**: <10MB/s per node

## 🚀 DEPLOYMENT ARCHITECTURE

### Development Setup:
```bash
# Local development
uvicorn main:app --reload --port 8000

# With local AI (Ollama)
export AI_BACKEND=ollama
export OLLAMA_ENDPOINT=http://localhost:11434
```

### Production Deployment:
```bash
# Docker container
docker build -t whispr-node .
docker run -p 8000:8000 \
  -e AI_BACKEND=openai_gpt \
  -e OPENAI_API_KEY=$OPENAI_KEY \
  -e NODE_PRIVATE_KEY=$NODE_KEY \
  whispr-node

# Kubernetes scaling
kubectl apply -f k8s/whispr-deployment.yaml
```

### Chainlink DON Integration:
```bash
# Node operator setup
export CHAINLINK_NODE_ADDRESS=0x...
export WHISPR_NODE_PRIVATE_KEY=0x...
export WHSPR_STAKE_AMOUNT=10000

python main.py --don-mode --chainlink-endpoint
```

## 📊 MONITORING & METRICS

### Quality Metrics:
- **Rewrite acceptance rate** (user selections)
- **Style adherence score** (automated evaluation)
- **Intent preservation** (semantic similarity)
- **User satisfaction** (optional feedback)

### Performance Metrics:
- **Response latency** (p50, p95, p99)
- **AI backend uptime** (availability)
- **Signature verification speed**
- **Error rate** (failed requests)

### Economic Metrics:
- **WHSPR tokens earned** (node operator revenue)
- **Request volume** (network activity)
- **Stake efficiency** (earnings per staked token)
- **Reputation score** (quality-based ranking)

---

**STATUS**: Technical architecture defined. Stateless + modular + Web3-native.
**NEXT**: Development methodology and TDD practices for AI protocols.
