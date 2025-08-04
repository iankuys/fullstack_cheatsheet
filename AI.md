# 🤖 AI & RAG Cheat Sheet

## 🎯 RAG Fundamentals

**Retrieval-Augmented Generation** = Information retrieval + Language generation

```
Query → Vector Search → Context Retrieval → LLM Generation → Response
```

**Why RAG?**
- ✅ Up-to-date information (vs training cutoff)
- ✅ Domain-specific knowledge  
- ✅ Reduced hallucinations
- ✅ Cost-effective vs fine-tuning
- ✅ Transparent sources

## 🔧 Basic RAG Implementation

```python
def basic_rag(query: str, knowledge_base: list) -> str:
    # 1. Embed query
    query_embedding = embed(query)
    
    # 2. Vector similarity search
    relevant_docs = vector_search(query_embedding, knowledge_base)
    
    # 3. Create context
    context = "\n".join([doc.content for doc in relevant_docs[:3]])
    
    # 4. Generate response
    prompt = f"Context: {context}\n\nQuestion: {query}\n\nAnswer:"
    return llm.generate(prompt)
```

## 🗄️ Vector Databases

| Database | Best For | Pros | Cons |
|----------|----------|------|------|
| **Pinecone** | Production | Managed, scalable | Cost, vendor lock-in |
| **FAISS** | Local dev | Free, fast | Manual scaling |
| **Chroma** | Simple projects | Easy setup | Limited scale |
| **Qdrant** | Self-hosted | Fast, Rust-based | Self-managed |

```python
# Basic vector operations
index = create_index(dimension=1536, metric="cosine")
index.upsert([{"id": "doc_1", "values": embedding, "metadata": {...}}])
results = index.query(vector=query_embedding, top_k=10)
```

## 📊 Embeddings & Chunking

```python
# Generate embeddings
def get_embeddings(texts: list[str]) -> list[list[float]]:
    response = openai.embeddings.create(
        model="text-embedding-3-small",
        input=texts
    )
    return [item.embedding for item in response.data]

# Smart chunking
def smart_chunk(text: str, chunk_size=500, overlap=50):
    sentences = text.split('. ')
    chunks = []
    current_chunk = ""
    
    for sentence in sentences:
        if len(current_chunk + sentence) < chunk_size:
            current_chunk += sentence + ". "
        else:
            chunks.append(current_chunk.strip())
            current_chunk = sentence + ". "
    
    return chunks
```

## 🌐 Web Scraping for Training Data

### Beautiful Soup - HTML Parsing
```python
import requests
from bs4 import BeautifulSoup
import time

def scrape_articles(urls: list[str]) -> list[dict]:
    articles = []
    
    for url in urls:
        try:
            response = requests.get(url, headers={
                'User-Agent': 'Mozilla/5.0 (compatible; AI-Training-Bot/1.0)'
            })
            soup = BeautifulSoup(response.content, 'html.parser')
            
            # Extract content
            title = soup.find('h1').get_text().strip()
            content = soup.find('div', class_='content').get_text().strip()
            
            articles.append({
                'url': url,
                'title': title,
                'content': content,
                'scraped_at': time.time()
            })
            
            time.sleep(1)  # Rate limiting
            
        except Exception as e:
            print(f"Error scraping {url}: {e}")
    
    return articles
```

### Scrapy - Production Scraping
```python
import scrapy

class DocumentSpider(scrapy.Spider):
    name = 'documents'
    start_urls = ['https://example.com/docs/']
    
    custom_settings = {
        'DOWNLOAD_DELAY': 1,
        'CONCURRENT_REQUESTS': 8,
        'ROBOTSTXT_OBEY': True,
    }
    
    def parse(self, response):
        # Extract document links
        doc_links = response.css('a.doc-link::attr(href)').getall()
        
        for link in doc_links:
            yield response.follow(link, self.parse_document)
    
    def parse_document(self, response):
        yield {
            'url': response.url,
            'title': response.css('h1::text').get(),
            'content': ' '.join(response.css('p::text').getall()),
            'categories': response.css('.category::text').getall(),
        }
```

### PDF & Document Processing
```python
import PyPDF2
import docx
from pathlib import Path

def extract_document_text(file_path: str) -> str:
    path = Path(file_path)
    
    if path.suffix.lower() == '.pdf':
        with open(file_path, 'rb') as file:
            reader = PyPDF2.PdfReader(file)
            text = ""
            for page in reader.pages:
                text += page.extract_text()
            return text
    
    elif path.suffix.lower() == '.docx':
        doc = docx.Document(file_path)
        return '\n'.join([paragraph.text for paragraph in doc.paragraphs])
    
    else:
        return open(file_path, 'r', encoding='utf-8').read()

# Batch process documents
def process_document_folder(folder_path: str) -> list[dict]:
    documents = []
    
    for file_path in Path(folder_path).rglob('*'):
        if file_path.suffix.lower() in ['.pdf', '.docx', '.txt']:
            try:
                content = extract_document_text(str(file_path))
                documents.append({
                    'filename': file_path.name,
                    'content': content,
                    'file_type': file_path.suffix,
                    'size': len(content)
                })
            except Exception as e:
                print(f"Error processing {file_path}: {e}")
    
    return documents
```

### API Data Collection
```python
import asyncio
import aiohttp

async def fetch_api_data(session, url, params=None):
    async with session.get(url, params=params) as response:
        return await response.json()

async def collect_training_data(api_endpoints: list[str]):
    data = []
    
    async with aiohttp.ClientSession() as session:
        tasks = []
        
        for endpoint in api_endpoints:
            task = fetch_api_data(session, endpoint)
            tasks.append(task)
        
        results = await asyncio.gather(*tasks, return_exceptions=True)
        
        for result in results:
            if not isinstance(result, Exception):
                data.extend(result.get('items', []))
    
    return data

# RSS/News Feed Scraping
import feedparser

def scrape_rss_feeds(feed_urls: list[str]) -> list[dict]:
    articles = []
    
    for feed_url in feed_urls:
        try:
            feed = feedparser.parse(feed_url)
            
            for entry in feed.entries:
                articles.append({
                    'title': entry.title,
                    'content': entry.summary,
                    'url': entry.link,
                    'published': entry.published,
                    'source': feed.feed.title
                })
        except Exception as e:
            print(f"Error parsing feed {feed_url}: {e}")
    
    return articles
```

### Data Cleaning for Training
```python
import re
from typing import List

def clean_text_for_training(text: str) -> str:
    # Remove HTML tags
    text = re.sub(r'<[^>]+>', '', text)
    
    # Remove extra whitespace
    text = re.sub(r'\s+', ' ', text)
    
    # Remove special characters but keep punctuation
    text = re.sub(r'[^\w\s\.\,\!\?\;\:]', '', text)
    
    # Remove very short or long lines
    lines = text.split('\n')
    filtered_lines = [line.strip() for line in lines 
                     if 10 <= len(line.strip()) <= 1000]
    
    return '\n'.join(filtered_lines)

def filter_quality_content(documents: List[dict]) -> List[dict]:
    quality_docs = []
    
    for doc in documents:
        content = doc['content']
        
        # Quality filters
        if len(content) < 100:  # Too short
            continue
        
        if len(content.split()) < 20:  # Too few words
            continue
        
        # Check for meaningful content (not just navigation/ads)
        word_count = len(content.split())
        sentence_count = len(re.findall(r'[.!?]+', content))
        
        if sentence_count == 0 or word_count / sentence_count > 50:
            continue  # Likely low quality
        
        quality_docs.append({
            **doc,
            'content': clean_text_for_training(content),
            'word_count': word_count
        })
    
    return quality_docs
```

## ⚡ Advanced RAG Techniques

### Hybrid Search
```python
def hybrid_search(query: str, alpha=0.7):
    # Vector search
    vector_results = vector_search(query, top_k=20)
    
    # Keyword search (BM25)
    keyword_results = bm25_search(query, top_k=20)
    
    # Combine scores
    combined = {}
    for result in vector_results:
        combined[result.id] = alpha * result.score
    
    for result in keyword_results:
        if result.id in combined:
            combined[result.id] += (1 - alpha) * result.score
        else:
            combined[result.id] = (1 - alpha) * result.score
    
    return sorted(combined.items(), key=lambda x: x[1], reverse=True)
```

### Query Enhancement
```python
def expand_query(query: str) -> list[str]:
    expansion_prompt = f"""
    Generate 3-5 alternative ways to ask: "{query}"
    Focus on synonyms and different phrasings.
    Return as JSON array.
    """
    response = llm.generate(expansion_prompt)
    return json.loads(response)
```

## 🏭 Production Optimization

### Async Processing
```python
async def async_rag(query: str):
    # Parallel embedding and search
    query_embedding_task = asyncio.create_task(get_embedding_async(query))
    
    # Check cache first
    cached = await get_from_cache(query)
    if cached:
        return cached
    
    query_embedding = await query_embedding_task
    results = await vector_search_async(query_embedding)
    
    await cache_results(query, results)
    return process_results(results)
```

### Cost Management
```python
def cost_aware_context(documents: list, budget_tokens: int):
    tokenizer = tiktoken.encoding_for_model("gpt-4")
    
    # Score by relevance/token ratio
    scored_docs = []
    for doc in documents:
        tokens = len(tokenizer.encode(doc.content))
        score_per_token = doc.relevance_score / tokens
        scored_docs.append((doc, score_per_token, tokens))
    
    # Greedy selection within budget
    selected = []
    used_tokens = 0
    
    for doc, score, tokens in sorted(scored_docs, key=lambda x: x[1], reverse=True):
        if used_tokens + tokens <= budget_tokens:
            selected.append(doc)
            used_tokens += tokens
    
    return selected
```

## 🎯 Interview Q&A

**Q: "Difference between embedding similarity and semantic similarity?"**
- **Embedding similarity**: Mathematical distance in vector space
- **Semantic similarity**: Actual meaning similarity  
- **Gap**: Embeddings are approximations, can miss nuances

**Q: "How to handle 'lost in the middle' problem?"**
- Put important info at beginning/end of context
- Use multiple shorter contexts
- Implement attention visualization

**Q: "How to evaluate RAG performance?"**
```python
# Retrieval metrics
precision = len(retrieved ∩ expected) / len(retrieved)
recall = len(retrieved ∩ expected) / len(expected)

# Generation metrics  
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy

result = evaluate(dataset, metrics=[faithfulness, answer_relevancy])
```

**Q: "RAG vs Fine-tuning vs Prompt Engineering?"**

| Approach | Use Case | Cost | Update Speed |
|----------|----------|------|--------------|
| **RAG** | External knowledge | 💰 Low | 🔄 Real-time |
| **Fine-tuning** | Style/domain | 💰💰💰 High | 📅 Slow |
| **Prompt Engineering** | Task format | 💰 Very Low | ⚡ Instant |

## 🔒 Security & Ethics

```python
def secure_rag(query: str, user_permissions: set):
    # Input sanitization
    sanitized_query = sanitize_input(query)
    
    # Permission filtering
    allowed_docs = filter_by_permissions(all_documents, user_permissions)
    
    # Search with security context
    results = secure_search(sanitized_query, allowed_docs)
    
    # Output filtering
    response = generate_response(sanitized_query, results)
    return filter_sensitive_info(response, user_permissions)

# Prevent prompt injection
def prevent_injection(query: str):
    injection_patterns = [
        r'ignore.{0,20}previous.{0,20}instructions',
        r'you.{0,10}are.{0,10}now',
        r'system.{0,10}prompt'
    ]
    
    for pattern in injection_patterns:
        if re.search(pattern, query, re.IGNORECASE):
            raise SecurityError("Potential injection detected")
    
    return query
```

## 📊 Key Metrics

**Retrieval Metrics:**
- Precision@K, Recall@K, F1@K
- Mean Reciprocal Rank (MRR)
- Normalized Discounted Cumulative Gain (NDCG)

**Generation Metrics:**
- BLEU, ROUGE, BERTScore
- Faithfulness (grounded in context)
- Answer relevancy (addresses question)

**Production Metrics:**
- Latency (< 2s response time)
- Throughput (queries/second)
- Cost per query
- User satisfaction scores

## 🚀 Best Practices

✅ **Data Collection**: Respect robots.txt, rate limits, ToS  
✅ **Chunking**: Preserve semantic boundaries  
✅ **Embeddings**: Use appropriate model for domain  
✅ **Context**: Balance relevance vs token limits  
✅ **Evaluation**: Test with real user queries  
✅ **Monitoring**: Track performance and costs  
✅ **Security**: Sanitize inputs, filter outputs  
✅ **Caching**: Cache embeddings and frequent queries