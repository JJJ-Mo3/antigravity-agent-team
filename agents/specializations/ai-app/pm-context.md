# AI Application - Product Manager Context

## Platform Understanding
You are creating a PRD for an AI-powered application that leverages Large Language Models (LLMs), embeddings, or other AI/ML capabilities. AI applications have unique considerations around prompts, context management, costs, latency, and user experience.

---

## Key Questions to Ask

### AI Integration Strategy
- **Primary AI Capability:**
  - Text generation (LLM)?
  - Chat/conversational AI?
  - Text analysis/extraction?
  - Embeddings/semantic search?
  - Image generation?
  - Image analysis/vision?
  - Voice/speech AI?
  - Multiple AI capabilities?

- **AI Provider:**
  - OpenAI (GPT-4, DALL-E)?
  - Anthropic (Claude)?
  - Google (Gemini, Vertex AI)?
  - Open source (Llama, Mistral)?
  - Multiple providers?
  - Own models?

### Core Functionality
- **Primary Use Case:**
  - Content generation?
  - Data analysis?
  - Customer support?
  - Personal assistant?
  - Code generation?
  - Learning/education?
  - Creative tool?

- **User Interaction:**
  - Chat interface?
  - Form-based input?
  - Document upload?
  - API integration?
  - Real-time streaming?
  - Batch processing?

### Context & Memory
- **Conversation Context:**
  - Single-turn interactions?
  - Multi-turn conversations?
  - How much history to maintain?
  - Context window limits?
  - Session management?

- **Knowledge Base:**
  - User's own documents?
  - Predefined knowledge?
  - Real-time data?
  - RAG (Retrieval Augmented Generation)?
  - Vector database needed?

### Cost Management
- **Token Usage:**
  - Expected tokens per request?
  - Daily/monthly volume?
  - Cost per user interaction?
  - Budget constraints?
  - Optimization strategies?

- **Pricing Model:**
  - Free tier with limits?
  - Pay-per-use?
  - Subscription with included usage?
  - Overage handling?

### Quality & Safety
- **Output Quality:**
  - Accuracy requirements?
  - Factual validation needed?
  - Tone/style requirements?
  - Multi-language support?

- **Safety Measures:**
  - Content filtering?
  - PII redaction?
  - Prompt injection protection?
  - Rate limiting?
  - Abuse prevention?

---

## AI-Specific Features to Consider

### 1. Prompt Engineering
- **System Prompts:**
  - Role definition
  - Behavior guidelines
  - Output format specifications
  - Constraints and limitations

- **User Prompts:**
  - Template structure
  - Variable injection
  - Few-shot examples
  - Context inclusion

- **Prompt Optimization:**
  - A/B testing prompts
  - Version control
  - Performance metrics
  - Cost optimization

### 2. Context Management
- **Context Window:**
  - Model limits (4K, 8K, 32K, 128K tokens)
  - Context prioritization
  - Truncation strategy
  - Sliding window approach

- **Memory Systems:**
  - Short-term (current session)
  - Long-term (user history)
  - Semantic memory (vector search)
  - Conversation summaries

### 3. RAG (Retrieval Augmented Generation)
- **Document Processing:**
  - Upload and indexing
  - Chunking strategy
  - Embedding generation
  - Metadata extraction

- **Retrieval:**
  - Vector similarity search
  - Hybrid search (vector + keyword)
  - Reranking results
  - Context assembly

- **Generation:**
  - Inject retrieved context
  - Source attribution
  - Confidence scores

### 4. Streaming Responses
- **User Experience:**
  - Token-by-token display
  - Cursor animation
  - Cancel mid-stream
  - Progress indicators

- **Technical:**
  - Server-Sent Events (SSE)
  - WebSocket fallback
  - Buffering strategy
  - Error handling mid-stream

### 5. Function Calling / Tool Use
- **Available Tools:**
  - Search web
  - Query database
  - Call APIs
  - Execute code
  - Create content

- **Tool Implementation:**
  - Function definitions
  - Parameter validation
  - Error handling
  - Result formatting

---

## Requirements Sections

Your PRD should include these AI-specific sections:

### AI Model Configuration
```
Primary Model:
- Provider: Anthropic
- Model: Claude Sonnet 3.5
- Context Window: 200K tokens
- Max Output: 4K tokens
- Temperature: 0.7 (balanced creativity)
- Top P: 0.9
- Use Case: General conversation, analysis

Fallback Model:
- Provider: OpenAI
- Model: GPT-4 Turbo
- Context Window: 128K tokens
- Use Case: If primary fails
- Cost: Higher, use sparingly

Image Model (if needed):
- Provider: OpenAI
- Model: DALL-E 3
- Resolution: 1024x1024
- Quality: Standard
- Use Case: Image generation

Embedding Model:
- Provider: OpenAI
- Model: text-embedding-3-large
- Dimensions: 1536
- Use Case: Document search, RAG
```

### System Prompt Strategy
```
Base System Prompt:
"You are a helpful AI assistant specialized in [domain].
Your role is to [primary function].

Guidelines:
- Be concise and clear
- Use markdown formatting
- Cite sources when using RAG
- Ask clarifying questions if needed
- Admit when you don't know
- Never make up information

Constraints:
- Keep responses under 500 words unless asked for more
- Use professional tone
- Focus on [specific domain]
- Decline inappropriate requests

Output Format:
- Use bullet points for lists
- Use code blocks for code
- Use tables for comparisons"

Dynamic Context Addition:
- User preferences: {user_preferences}
- Previous context: {conversation_summary}
- Retrieved knowledge: {rag_results}
- Current date: {current_date}
```

### Token Budget Management
```
Token Allocation:

Per Request (100K tokens total budget):
- System Prompt: 500 tokens (fixed)
- Conversation History: 10K tokens (variable)
- RAG Context: 5K tokens (variable)
- User Message: 2K tokens (variable)
- Available for Response: 82.5K tokens

Optimization Strategies:
1. Summarize old conversation turns
2. Prioritize recent messages
3. Smart context pruning
4. Compress RAG results
5. Cache system prompt

Cost Estimation:
- Input: $3 per 1M tokens
- Output: $15 per 1M tokens
- Average request: 20K input + 500 output
- Cost per request: $0.0675
- Expected daily requests: 1,000
- Daily cost: $67.50
- Monthly cost: $2,025
```

### RAG Implementation Architecture
```
Document Processing Pipeline:

1. Upload & Validation
   - Accept: PDF, DOCX, TXT, MD
   - Max size: 10MB per file
   - Scan for PII/sensitive data
   ↓
2. Text Extraction
   - Parse document structure
   - Extract text content
   - Preserve formatting metadata
   ↓
3. Chunking
   - Strategy: Recursive character splitting
   - Chunk size: 1000 characters
   - Overlap: 200 characters
   - Preserve sentence boundaries
   ↓
4. Embedding Generation
   - Model: text-embedding-3-large
   - Batch process: 100 chunks at a time
   - Store: Supabase pgvector
   ↓
5. Metadata Storage
   - Document ID, title, source
   - Chunk position, parent doc
   - Created date, author
   - Custom tags

Query Processing:

1. User Query → Embedding
   - Generate query embedding
   ↓
2. Vector Search
   - Similarity search (cosine)
   - Top K: 5 most relevant chunks
   - Threshold: > 0.7 similarity
   ↓
3. Reranking (optional)
   - Use cross-encoder model
   - Reorder by relevance
   ↓
4. Context Assembly
   - Format chunks for LLM
   - Add source citations
   - Inject into prompt
   ↓
5. LLM Generation
   - Include retrieved context
   - Generate response
   - Maintain source links
```

### Conversation Memory Strategy
```
Short-term Memory (Current Session):
- Store: In-memory or Redis
- Contents: Full conversation history
- Limit: Last 20 messages or 32K tokens
- Expiry: End of session

Long-term Memory (Persistent):
- Store: Database
- Contents: Conversation summaries
- Summarization: After every 10 messages
- Retrieval: When needed for context

Semantic Memory (Knowledge):
- Store: Vector database
- Contents: User documents, preferences
- Retrieval: Similarity search
- Update: Real-time as documents added

Memory Retrieval Priority:
1. Current session (always included)
2. Relevant long-term memories (search)
3. User knowledge base (RAG)
4. System knowledge (if needed)
```

### Streaming Response Implementation
```
Client-Side:
- EventSource for SSE
- Display tokens as received
- Show thinking indicator
- Allow cancellation
- Handle connection errors

Server-Side:
- Stream tokens as generated
- Send deltas, not full text
- Include metadata (model, usage)
- Handle early termination
- Log partial responses

Example Flow:
1. User sends message
   ↓
2. Show "thinking" indicator
   ↓
3. Start SSE connection
   ↓
4. Receive token stream
   - event: token
   - data: "Hello"
   ↓
5. Append to display
   ↓
6. Continue until done
   - event: done
   - data: {usage: {...}}
   ↓
7. Hide indicator
   ↓
8. Enable input
```

### Safety & Content Filtering
```
Input Filtering:
1. Prompt Injection Detection
   - Check for instruction override attempts
   - Sanitize user input
   - Rate limit suspicious patterns

2. PII Detection
   - Scan for SSN, credit cards
   - Detect names, emails, phones
   - Redact or warn user

3. Content Policy
   - Block harmful requests
   - Detect policy violations
   - Log and alert

Output Filtering:
1. Content Moderation
   - Check against policy
   - Filter harmful content
   - Fallback to safe response

2. Fact-Checking (if critical)
   - Validate key facts
   - Add uncertainty labels
   - Provide sources

3. PII Redaction
   - Scan generated text
   - Redact sensitive info
   - Alert user

Rate Limiting:
- Per user: 100 requests/hour
- Per IP: 200 requests/hour
- Spike detection: 10 requests/minute
- Temporary ban: 1 hour for abuse
```

---

## User Flows

### Chat Interface Flow
```
1. User lands on app
   ↓
2. Shows chat interface
   - Welcome message
   - Example prompts
   - Empty chat history
   ↓
3. User types message
   ↓
4. User hits Enter or clicks Send
   ↓
5. Message added to chat
   - User bubble (right side)
   - Timestamp
   ↓
6. "AI is thinking..." indicator
   ↓
7. Response streams in
   - Token by token
   - AI bubble (left side)
   - Typing animation
   ↓
8. Response complete
   - Show full message
   - Add actions (copy, regenerate, feedback)
   ↓
9. User can continue conversation
```

### RAG Document Upload Flow
```
1. User clicks "Upload Document"
   ↓
2. File picker opens
   ↓
3. User selects file(s)
   ↓
4. Validation
   - Check file type
   - Check size
   - Show progress
   ↓
5. Processing
   - Extract text
   - Generate chunks
   - Create embeddings
   - "Processing..." indicator
   ↓
6. Success
   - Show document in library
   - Document count updated
   - "Ready to use" indicator
   ↓
7. User can now ask questions
   - AI will reference uploaded docs
   - Citations shown in responses
```

### Function Calling Flow
```
1. User: "What's the weather in Tokyo?"
   ↓
2. AI decides tool needed
   - Function: get_weather
   - Parameters: {location: "Tokyo"}
   ↓
3. System executes function
   - Call weather API
   - Get result
   ↓
4. AI receives result
   - temperature: 22°C
   - conditions: Partly cloudy
   ↓
5. AI generates natural response
   - "It's 22°C and partly cloudy in Tokyo"
   - Formats nicely for user
   ↓
6. User sees final response
```

---

## Technical Architecture

### High-Level Architecture
```
┌─────────────────────────────────┐
│         Frontend (React)         │
│  - Chat UI                       │
│  - Streaming display             │
│  - Document upload               │
└────────────┬────────────────────┘
             │
             ▼
┌─────────────────────────────────┐
│    Backend (Supabase Functions) │
│  - Request handling              │
│  - Prompt assembly               │
│  - Stream coordination           │
└────────────┬────────────────────┘
             │
      ┌──────┴──────┐
      │             │
      ▼             ▼
┌──────────┐  ┌──────────────┐
│ AI APIs  │  │   Database   │
│ (Claude, │  │  - Postgres  │
│  OpenAI) │  │  - pgvector  │
└──────────┘  │  - Chat logs │
              │  - Documents │
              └──────────────┘
```

### Database Schema (AI-specific)
```sql
-- Conversations
CREATE TABLE conversations (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users,
  title TEXT,
  model TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Messages
CREATE TABLE messages (
  id UUID PRIMARY KEY,
  conversation_id UUID REFERENCES conversations,
  role TEXT CHECK (role IN ('user', 'assistant', 'system')),
  content TEXT,
  tokens_used INTEGER,
  model TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Documents (RAG)
CREATE TABLE documents (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users,
  filename TEXT,
  content TEXT,
  metadata JSONB,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Document Chunks (with embeddings)
CREATE TABLE document_chunks (
  id UUID PRIMARY KEY,
  document_id UUID REFERENCES documents,
  content TEXT,
  embedding vector(1536),  -- pgvector
  chunk_index INTEGER,
  metadata JSONB,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Create vector index
CREATE INDEX ON document_chunks 
  USING ivfflat (embedding vector_cosine_ops)
  WITH (lists = 100);

-- Usage tracking
CREATE TABLE api_usage (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users,
  model TEXT,
  input_tokens INTEGER,
  output_tokens INTEGER,
  cost NUMERIC(10,6),
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

---

## Cost Optimization Strategies

### 1. Token Optimization
```
Strategies:
- Compress conversation history
- Smart context selection
- Efficient prompts (no fluff)
- Cache common responses
- Use cheaper models for simple tasks
```

### 2. Model Selection
```
Task Routing:
- Simple queries → Claude Haiku (cheap, fast)
- Complex analysis → Claude Sonnet (balanced)
- Maximum capability → Claude Opus (expensive)
- Embeddings → OpenAI (cost-effective)

Cost Comparison:
- Haiku: $0.25 input, $1.25 output per 1M tokens
- Sonnet: $3 input, $15 output per 1M tokens
- Opus: $15 input, $75 output per 1M tokens
```

### 3. Caching
```
What to Cache:
- System prompts (high hit rate)
- Common queries
- Document embeddings
- RAG results

Where to Cache:
- Redis: Hot data, short TTL
- Database: Warm data, longer TTL
- CDN: Static prompts
```

### 4. Batching
```
Batch Process:
- Embeddings: 100 chunks at once
- Document processing: Off-peak hours
- Analytics: Daily aggregation
```

---

## Anti-patterns to Avoid

❌ **Don't:**
- Send entire document to LLM (use RAG)
- Include all conversation history
- Ignore token limits
- Use most expensive model for everything
- Store raw API keys client-side
- Trust AI output blindly
- Forget to handle rate limits
- Ignore streaming for long responses
- Skip content filtering
- Make synchronous API calls from frontend

✅ **Do:**
- Use RAG for large documents
- Implement smart context management
- Monitor token usage
- Route to appropriate model
- Secure API keys server-side
- Validate and fact-check when critical
- Implement proper rate limiting
- Stream responses for better UX
- Filter input and output
- Use serverless functions for AI calls

---

## Success Metrics

### Usage Metrics
- Daily Active Users
- Messages per user
- Average session length
- Feature adoption (RAG, tools, etc.)

### Quality Metrics
- Response relevance (user feedback)
- Task completion rate
- User satisfaction score
- Regeneration rate (indicates poor response)

### Cost Metrics
- Cost per user per month
- Cost per conversation
- Token efficiency (tokens/value delivered)
- Model distribution

### Performance Metrics
- Response latency (p50, p95, p99)
- Time to first token
- Streaming speed
- Error rate

---

This context ensures your AI application PRD covers all the unique considerations for LLM-powered products.
