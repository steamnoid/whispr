# Whispr - Minimalism Principle

## ⚡ ZERO TOLERANCE FOR UNNECESSARY CODE

### Core Philosophy:
**Every line of code must justify its existence. Every dependency must earn its place. Every feature must solve a real problem.**

## 🚫 IRON RULES OF MINIMALISM

### Rule 1: No Code Without Tests
```python
# ❌ FORBIDDEN: Code without corresponding test
def generate_rewrite(message: str) -> str:
    # This function has no test - DELETE IT
    return f"Rewritten: {message}"

# ✅ REQUIRED: Test-driven implementation
def test_generate_rewrite_removes_profanity():
    result = generate_rewrite("This is fucking terrible!")
    assert "fucking" not in result
    assert "terrible" in result

def generate_rewrite(message: str) -> str:
    # Implementation justified by test above
    clean_message = message.replace("fucking", "really")
    return f"Improved: {clean_message}"
```

### Rule 2: No Dependencies Without Justification
```python
# ❌ FORBIDDEN: Importing heavy libraries for simple tasks
import pandas as pd  # 50MB dependency for simple data manipulation
import numpy as np   # 20MB dependency for basic math

def calculate_average(numbers: list) -> float:
    return pd.Series(numbers).mean()  # WASTE

# ✅ REQUIRED: Use standard library when possible
def calculate_average(numbers: list) -> float:
    return sum(numbers) / len(numbers) if numbers else 0.0

# ❌ FORBIDDEN: Using requests when httpx is already imported
import requests
import httpx

async def fetch_data():
    # Two HTTP libraries for same functionality - WASTE
    pass

# ✅ REQUIRED: Single responsibility per dependency
import httpx  # Only one HTTP client needed

async def fetch_data():
    async with httpx.AsyncClient() as client:
        response = await client.get("https://api.example.com")
        return response.json()
```

### Rule 3: No Features Without Users
```python
# ❌ FORBIDDEN: Speculative features
class AIRewriter:
    def generate_rewrites(self, message: str) -> List[str]:
        pass
    
    def generate_summaries(self, message: str) -> str:
        # Nobody asked for summaries - DELETE THIS
        pass
    
    def translate_message(self, message: str, target_lang: str) -> str:
        # Translation is not in the requirements - DELETE THIS
        pass
    
    def analyze_sentiment(self, message: str) -> float:
        # Sentiment analysis is not needed - DELETE THIS
        pass

# ✅ REQUIRED: Only implement what's specified
class AIRewriter:
    def generate_rewrites(self, message: str, style: str, audience: str) -> List[RewriteOption]:
        """
        Only function in specification:
        - Input: raw message + style + audience
        - Output: 3 rewrite alternatives
        """
        pass
```

## 🔍 MANDATORY CODE REVIEWS

### Deletion Checklist:
Before ANY code is merged, ask these questions:

1. **Is this code tested?**
   - If NO → Delete or write test first
   - If YES → Continue

2. **Is this code used?**
   - If NO → Delete immediately
   - If YES → Continue

3. **Does this code solve a real user problem?**
   - If NO → Delete immediately
   - If YES → Continue

4. **Can this be done simpler?**
   - If YES → Simplify or delete
   - If NO → Continue

5. **Is this the smallest possible implementation?**
   - If NO → Reduce complexity
   - If YES → Accept

### Code Deletion Examples:
```python
# ❌ DELETE: Unnecessary abstraction
class MessageProcessor:
    def __init__(self):
        self.preprocessor = MessagePreprocessor()
        self.postprocessor = MessagePostprocessor()
        self.validator = MessageValidator()
    
    def process(self, message: str) -> str:
        validated = self.validator.validate(message)
        preprocessed = self.preprocessor.preprocess(validated)
        processed = self._actual_processing(preprocessed)
        return self.postprocessor.postprocess(processed)

# ✅ KEEP: Direct implementation
def process_message(message: str) -> str:
    if not message or len(message) > 1000:
        raise ValueError("Invalid message")
    
    # Direct processing - no unnecessary layers
    return ai_rewriter.rewrite(message.strip())

# ❌ DELETE: Unnecessary configuration
class Config:
    AI_MODEL = "gpt-3.5-turbo"
    AI_TEMPERATURE = 0.7
    AI_MAX_TOKENS = 150
    AI_TIMEOUT = 30
    CACHE_TTL = 3600
    CACHE_MAX_SIZE = 1000
    LOG_LEVEL = "INFO"
    LOG_FORMAT = "json"
    DATABASE_URL = None  # Not using database
    REDIS_URL = None     # Not using Redis yet
    METRICS_ENABLED = True
    METRICS_PORT = 9090
    HEALTH_CHECK_PATH = "/health"

# ✅ KEEP: Only essential configuration
AI_MODEL = "gpt-3.5-turbo"
AI_TEMPERATURE = 0.7
MAX_MESSAGE_LENGTH = 1000
```

### Dependency Audit:
```python
# ❌ DELETE: Unnecessary dependencies
"""
requirements.txt with bloat:
fastapi==0.104.1          # ✅ Required for API
uvicorn==0.24.0           # ✅ Required for server
pydantic==2.5.0           # ✅ Required for validation
openai==1.3.0             # ✅ Required for AI
httpx==0.25.0             # ✅ Required for HTTP
cryptography==41.0.0      # ✅ Required for signing

pandas==2.1.0             # ❌ DELETE: Only used for simple average
numpy==1.24.0             # ❌ DELETE: Only used with pandas
matplotlib==3.7.0         # ❌ DELETE: No plotting needed
jupyter==1.0.0            # ❌ DELETE: Not production dependency
pytest-cov==4.1.0         # ❌ DELETE: Coverage tool, not runtime
black==23.0.0             # ❌ DELETE: Formatter, not runtime
mypy==1.5.0               # ❌ DELETE: Type checker, not runtime
"""

# ✅ KEEP: Minimal production requirements
"""
requirements.txt - minimal:
fastapi==0.104.1
uvicorn==0.24.0  
pydantic==2.5.0
openai==1.3.0
httpx==0.25.0
cryptography==41.0.0
"""
```

## 📏 FILE SIZE LIMITS

### Strict File Size Rules:
```python
# Maximum file sizes (enforced by CI)
MAX_LINES_PER_FILE = 200
MAX_FUNCTIONS_PER_FILE = 10
MAX_CLASSES_PER_FILE = 3

# If any file exceeds limits:
# 1. Split into smaller files
# 2. Remove unnecessary complexity
# 3. Delete unused functionality
```

### Example: Before/After Minimalism
```python
# ❌ BEFORE: Bloated file (300+ lines)
class AIRewriterManager:
    def __init__(self):
        self.backends = {}
        self.fallbacks = []
        self.cache = {}
        self.metrics = {}
        self.circuit_breakers = {}
        
    def register_backend(self, name: str, backend: AIBackend):
        # 50 lines of registration logic
        pass
    
    def setup_fallbacks(self, chain: List[str]):
        # 30 lines of fallback logic
        pass
    
    def configure_cache(self, ttl: int, max_size: int):
        # 40 lines of caching logic
        pass
    
    def enable_metrics(self, exporter: MetricsExporter):
        # 35 lines of metrics setup
        pass
    
    def setup_circuit_breaker(self, failure_threshold: int):
        # 45 lines of circuit breaker logic
        pass
    
    def rewrite_message(self, message: str, style: str) -> List[str]:
        # 100+ lines of complex routing and error handling
        pass

# ✅ AFTER: Minimal implementation (50 lines)
async def rewrite_message(message: str, style: str, audience: str) -> List[RewriteOption]:
    """Single function that does exactly what's needed."""
    if not message or len(message) > 1000:
        raise ValueError("Invalid message")
    
    client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))
    
    try:
        response = await client.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=[
                {"role": "system", "content": f"Rewrite this message in {style} style for {audience}"},
                {"role": "user", "content": message}
            ],
            temperature=0.7,
            max_tokens=150
        )
        
        # Parse response into alternatives
        alternatives = parse_ai_response(response.choices[0].message.content)
        return [RewriteOption(text=alt, confidence=0.8) for alt in alternatives]
        
    except Exception as e:
        # Simple fallback
        return [RewriteOption(text=f"Politely: {message}", confidence=0.5)]

def parse_ai_response(text: str) -> List[str]:
    """Extract alternatives from AI response."""
    lines = [line.strip() for line in text.split('\n') if line.strip()]
    return lines[:3]  # Return max 3 alternatives
```

## 🗑️ DELETION CEREMONIES

### Weekly Code Deletion Review:
```bash
# Every Friday: MANDATORY deletion ceremony
echo "🗑️ DELETION FRIDAY - Remove unnecessary code"

# 1. Find unused functions
python find_unused_code.py --delete

# 2. Find unused imports
python find_unused_imports.py --delete

# 3. Find dead code paths
python find_dead_code.py --delete

# 4. Measure code reduction
git diff --stat HEAD~7 | grep "deletions"
echo "Lines deleted this week: CELEBRATE IF >0"
```

### Metrics to Track:
- **Lines of code** (should decrease over time)
- **Number of dependencies** (should be minimal)
- **File count** (should be stable or decreasing)
- **Cyclomatic complexity** (should be low)
- **Test coverage** (should be high)

## 🎯 MINIMALISM ENFORCEMENT

### Pre-commit Hooks:
```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "🔍 Minimalism Check..."

# Check file sizes
for file in $(git diff --cached --name-only | grep "\.py$"); do
    lines=$(wc -l < "$file")
    if [ $lines -gt 200 ]; then
        echo "❌ File too large: $file ($lines lines > 200 limit)"
        exit 1
    fi
done

# Check dependency count
deps=$(cat requirements.txt | wc -l)
if [ $deps -gt 10 ]; then
    echo "❌ Too many dependencies: $deps > 10 limit"
    exit 1
fi

# Check for unused imports
python -m unimport --check

echo "✅ Minimalism check passed"
```

### CI Minimalism Validation:
```yaml
# .github/workflows/minimalism.yml
name: Minimalism Enforcement

on: [push, pull_request]

jobs:
  minimalism-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Check file sizes
        run: |
          find . -name "*.py" -exec wc -l {} + | \
          awk '$1 > 200 {print "❌ File too large:", $2, "("$1" lines)"; exit 1}'
      
      - name: Check dependency count
        run: |
          deps=$(cat requirements.txt | wc -l)
          if [ $deps -gt 10 ]; then
            echo "❌ Too many dependencies: $deps"
            exit 1
          fi
      
      - name: Check for unused code
        run: |
          pip install vulture
          vulture . --min-confidence 60
      
      - name: Complexity check
        run: |
          pip install radon
          radon cc . --min B
```

### Zero Tolerance Examples:
```python
# ❌ IMMEDIATE DELETION - No exceptions
def unused_function():
    """This function is never called - DELETE"""
    pass

import unused_module  # DELETE

class UnusedClass:  # DELETE
    pass

# Dead code paths
if False:  # DELETE
    dead_code()

# Commented code
# def old_implementation():  # DELETE
#     pass

# Debug prints
print("Debug info")  # DELETE before commit

# Hardcoded values that should be constants
timeout = 30  # Should be TIMEOUT_SECONDS = 30

# Magic numbers
if len(message) > 1000:  # Should be MAX_MESSAGE_LENGTH
    pass

# Duplicate functionality
def process_message_v1():  # DELETE old version
    pass

def process_message_v2():  # Keep only latest
    pass
```

## 🏆 MINIMALISM SUCCESS METRICS

### Target Metrics:
- **Total lines of code**: <2000 for entire project
- **Files count**: <20 total files
- **Dependencies**: <10 in requirements.txt
- **Functions per file**: <10 average
- **Cyclomatic complexity**: <5 average
- **Test coverage**: >95%

### Quality Indicators:
- **Lines deleted per week** > 0
- **New dependencies added** = 0 (unless absolutely necessary)
- **File size growth** < 5% per sprint
- **Code review rejection rate** > 50% (good - being selective)

### Anti-patterns to Delete:
- Configuration classes for simple values
- Manager/Handler/Service classes with single responsibility
- Abstract base classes with one implementation
- Wrapper functions that just call another function
- Utility classes with static methods (use functions)
- Complex inheritance hierarchies
- Premature optimization code
- Feature flags for features that will never be toggled

---

**STATUS**: Minimalism principle enforced. Zero tolerance for code bloat.
**NEXT**: Begin Week 1 implementation with strict minimalism monitoring.
