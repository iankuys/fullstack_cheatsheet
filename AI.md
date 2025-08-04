# 🤖 AI & RAG Implementation Cheat Sheet

---

## 🧠 RAG (Retrieval-Augmented Generation) Fundamentals

### Core RAG Architecture
```
User Query → Embedding → Vector Search → Retrieved Context + Query → LLM → Response
```

### Key Components:
- **Document Chunking**: Split text into manageable pieces
- **Embeddings**: Convert text to numerical vectors  
- **Vector Database**: Store and search embeddings efficiently
- **Retrieval**: Find most relevant chunks for query
- **Generation**: LLM creates response using retrieved context

---

## 📊 Vector Databases & Embeddings

### Popular Vector Databases
```python
# Pinecone (Managed)
import pinecone
pinecone.init(api_key="your-key", environment="us-west1-gcp")
index = pinecone.Index("your-index")

# ChromaDB (Local/Self-hosted)
import chromadb
client = chromadb.Client()
collection = client.create_collection("documents")

# Weaviate (GraphQL-based)
import weaviate
client = weaviate.Client("http://localhost:8080")

# FAISS (Facebook AI Similarity Search)
import faiss
index = faiss.IndexFlatL2(384)  # 384-dimensional vectors
```

### Embedding Models
```python
# OpenAI Embeddings
import openai
def get_openai_embedding(text, model="text-embedding-ada-002"):
    response = openai.Embedding.create(
        input=text,
        model=model
    )
    return response['data'][0]['embedding']

# Sentence Transformers (Local)
from sentence_transformers import SentenceTransformer
model = SentenceTransformer('all-MiniLM-L6-v2')
embeddings = model.encode(["Your text here"])

# HuggingFace Transformers
from transformers import AutoTokenizer, AutoModel
tokenizer = AutoTokenizer.from_pretrained('sentence-transformers/all-MiniLM-L6-v2')
model = AutoModel.from_pretrained('sentence-transformers/all-MiniLM-L6-v2')
```

---

## 🛠️ Complete RAG Implementation

### Document Processing Pipeline
```python
import PyPDF2
import requests
from bs4 import BeautifulSoup
from sentence_transformers import SentenceTransformer
import chromadb
from typing import List, Dict

class DocumentProcessor:
    def __init__(self):
        self.embedding_model = SentenceTransformer('all-MiniLM-L6-v2')
        self.chunk_size = 1000
        self.chunk_overlap = 200
    
    def chunk_text(self, text: str) -> List[str]:
        """Split text into overlapping chunks"""
        words = text.split()
        chunks = []
        
        for i in range(0, len(words), self.chunk_size - self.chunk_overlap):
            chunk = ' '.join(words[i:i + self.chunk_size])
            chunks.append(chunk)
        
        return chunks
    
    def process_pdf(self, pdf_path: str) -> List[Dict]:
        """Extract and chunk PDF content"""
        with open(pdf_path, 'rb') as file:
            pdf_reader = PyPDF2.PdfReader(file)
            text = ""
            for page in pdf_reader.pages:
                text += page.extract_text()
        
        chunks = self.chunk_text(text)
        return [
            {
                "content": chunk,
                "source": pdf_path,
                "chunk_id": i,
                "embedding": self.embedding_model.encode(chunk).tolist()
            }
            for i, chunk in enumerate(chunks)
        ]
    
    def process_web_content(self, url: str) -> List[Dict]:
        """Scrape and process web content"""
        response = requests.get(url)
        soup = BeautifulSoup(response.content, 'html.parser')
        
        # Extract main content (customize selectors as needed)
        content = soup.find('main') or soup.find('article') or soup.body
        text = content.get_text(strip=True) if content else ""
        
        chunks = self.chunk_text(text)
        return [
            {
                "content": chunk,
                "source": url,
                "chunk_id": i,
                "embedding": self.embedding_model.encode(chunk).tolist()
            }
            for i, chunk in enumerate(chunks)
        ]
```

### Vector Database Integration
```python
class RAGSystem:
    def __init__(self):
        self.client = chromadb.Client()
        self.collection = self.client.create_collection(
            name="knowledge_base",
            metadata={"hnsw:space": "cosine"}
        )
        self.embedding_model = SentenceTransformer('all-MiniLM-L6-v2')
    
    def add_documents(self, documents: List[Dict]):
        """Add processed documents to vector database"""
        ids = [f"{doc['source']}_{doc['chunk_id']}" for doc in documents]
        embeddings = [doc['embedding'] for doc in documents]
        metadatas = [
            {
                "source": doc['source'],
                "chunk_id": doc['chunk_id']
            }
            for doc in documents
        ]
        documents_text = [doc['content'] for doc in documents]
        
        self.collection.add(
            ids=ids,
            embeddings=embeddings,
            metadatas=metadatas,
            documents=documents_text
        )
    
    def retrieve(self, query: str, n_results: int = 5) -> List[Dict]:
        """Retrieve most relevant documents for query"""
        query_embedding = self.embedding_model.encode(query).tolist()
        
        results = self.collection.query(
            query_embeddings=[query_embedding],
            n_results=n_results
        )
        
        return [
            {
                "content": doc,
                "metadata": meta,
                "distance": dist
            }
            for doc, meta, dist in zip(
                results['documents'][0],
                results['metadatas'][0], 
                results['distances'][0]
            )
        ]
    
    def generate_response(self, query: str, context_docs: List[Dict]) -> str:
        """Generate response using retrieved context"""
        context = "\n\n".join([doc['content'] for doc in context_docs[:3]])
        
        prompt = f"""Based on the following context, answer the question:

Context:
{context}

Question: {query}

Answer:"""
        
        # Use your preferred LLM here (OpenAI, Anthropic, local model, etc.)
        return self.call_llm(prompt)
    
    def call_llm(self, prompt: str) -> str:
        """Call your LLM of choice"""
        # OpenAI Example
        import openai
        response = openai.ChatCompletion.create(
            model="gpt-3.5-turbo",
            messages=[{"role": "user", "content": prompt}],
            max_tokens=500,
            temperature=0.7
        )
        return response.choices[0].message.content
    
    def query(self, question: str) -> Dict:
        """Complete RAG pipeline"""
        # 1. Retrieve relevant documents
        relevant_docs = self.retrieve(question)
        
        # 2. Generate response
        response = self.generate_response(question, relevant_docs)
        
        return {
            "question": question,
            "answer": response,
            "sources": [doc['metadata']['source'] for doc in relevant_docs[:3]],
            "context_used": len(relevant_docs)
        }
```

---

## 🕷️ Web Scraping Strategies for Training Data

### 1. Beautiful Soup - HTML Parsing
```python
import requests
from bs4 import BeautifulSoup
import time
from urllib.parse import urljoin, urlparse
from typing import List, Dict

class WebScraper:
    def __init__(self):
        self.session = requests.Session()
        self.session.headers.update({
            'User-Agent': 'Mozilla/5.0 (compatible; AI-Training-Bot/1.0)'
        })
    
    def scrape_article(self, url: str) -> Dict:
        """Scrape a single article/page"""
        try:
            response = self.session.get(url, timeout=10)
            response.raise_for_status()
            
            soup = BeautifulSoup(response.content, 'html.parser')
            
            # Extract title
            title_tag = soup.find('h1') or soup.find('title')
            title = title_tag.get_text(strip=True) if title_tag else ""
            
            # Extract main content
            content_selectors = ['article', 'main', '.content', '.post-content', '.entry-content']
            content = ""
            
            for selector in content_selectors:
                content_elem = soup.select_one(selector)
                if content_elem:
                    content = content_elem.get_text(strip=True)
                    break
            
            # Extract metadata
            meta_description = soup.find('meta', attrs={'name': 'description'})
            description = meta_description.get('content', '') if meta_description else ""
            
            return {
                'url': url,
                'title': title,
                'content': content,
                'description': description,
                'scraped_at': time.time(),
                'word_count': len(content.split())
            }
            
        except Exception as e:
            print(f"Error scraping {url}: {e}")
            return None
    
    def scrape_multiple_urls(self, urls: List[str], delay: float = 1.0) -> List[Dict]:
        """Scrape multiple URLs with rate limiting"""
        results = []
        
        for url in urls:
            result = self.scrape_article(url)
            if result:
                results.append(result)
            
            time.sleep(delay)  # Be respectful to servers
        
        return results
    
    def discover_links(self, base_url: str, max_links: int = 50) -> List[str]:
        """Discover additional links from a base URL"""
        try:
            response = self.session.get(base_url)
            soup = BeautifulSoup(response.content, 'html.parser')
            
            links = []
            domain = urlparse(base_url).netloc
            
            for link in soup.find_all('a', href=True):
                href = link['href']
                full_url = urljoin(base_url, href)
                
                # Only include links from same domain
                if urlparse(full_url).netloc == domain:
                    links.append(full_url)
                
                if len(links) >= max_links:
                    break
            
            return list(set(links))  # Remove duplicates
            
        except Exception as e:
            print(f"Error discovering links from {base_url}: {e}")
            return []
```

### 2. Scrapy - Production Web Scraping
```python
import scrapy
from scrapy.crawler import CrawlerProcess
import json

class ArticleSpider(scrapy.Spider):
    name = 'articles'
    
    def __init__(self, start_urls=None, *args, **kwargs):
        super(ArticleSpider, self).__init__(*args, **kwargs)
        self.start_urls = start_urls or []
    
    def parse(self, response):
        # Extract article data
        yield {
            'url': response.url,
            'title': response.css('h1::text').get() or response.css('title::text').get(),
            'content': ' '.join(response.css('p::text').getall()),
            'links': response.css('a::attr(href)').getall(),
            'scraped_at': time.time()
        }
        
        # Follow links to other articles
        for link in response.css('a::attr(href)').getall()[:10]:  # Limit to 10 links
            if link.startswith('http') or link.startswith('/'):
                yield response.follow(link, self.parse)

def run_scrapy_spider(urls: List[str], output_file: str = 'scraped_data.json'):
    """Run Scrapy spider programmatically"""
    process = CrawlerProcess({
        'FEEDS': {
            output_file: {'format': 'json'},
        },
        'ROBOTSTXT_OBEY': True,
        'DOWNLOAD_DELAY': 1,  # Be respectful
        'CONCURRENT_REQUESTS_PER_DOMAIN': 2,
    })
    
    process.crawl(ArticleSpider, start_urls=urls)
    process.start()
```

### 3. API-Based Data Collection
```python
import requests
import feedparser
from typing import Generator, Dict

class APIDataCollector:
    def __init__(self):
        self.session = requests.Session()
    
    def collect_from_rss_feeds(self, feed_urls: List[str]) -> Generator[Dict, None, None]:
        """Collect articles from RSS feeds"""
        for feed_url in feed_urls:
            try:
                feed = feedparser.parse(feed_url)
                
                for entry in feed.entries:
                    yield {
                        'title': entry.get('title', ''),
                        'content': entry.get('summary', '') or entry.get('description', ''),
                        'url': entry.get('link', ''),
                        'published': entry.get('published', ''),
                        'source': feed.feed.get('title', 'Unknown'),
                        'tags': [tag.term for tag in entry.get('tags', [])]
                    }
            except Exception as e:
                print(f"Error processing RSS feed {feed_url}: {e}")
    
    def collect_from_reddit_api(self, subreddit: str, limit: int = 100) -> List[Dict]:
        """Collect posts from Reddit API"""
        url = f"https://www.reddit.com/r/{subreddit}/hot.json"
        params = {'limit': limit}
        
        try:
            response = self.session.get(url, params=params)
            data = response.json()
            
            posts = []
            for post in data['data']['children']:
                post_data = post['data']
                posts.append({
                    'title': post_data.get('title', ''),
                    'content': post_data.get('selftext', ''),
                    'url': post_data.get('url', ''),
                    'score': post_data.get('score', 0),
                    'num_comments': post_data.get('num_comments', 0),
                    'created_utc': post_data.get('created_utc', 0),
                    'subreddit': subreddit
                })
            
            return posts
            
        except Exception as e:
            print(f"Error collecting from Reddit r/{subreddit}: {e}")
            return []
    
    def collect_from_hackernews_api(self, max_stories: int = 100) -> List[Dict]:
        """Collect stories from Hacker News API"""
        try:
            # Get top stories
            top_stories_url = "https://hacker-news.firebaseio.com/v0/topstories.json"
            story_ids = requests.get(top_stories_url).json()[:max_stories]
            
            stories = []
            for story_id in story_ids:
                story_url = f"https://hacker-news.firebaseio.com/v0/item/{story_id}.json"
                story_data = requests.get(story_url).json()
                
                if story_data and story_data.get('type') == 'story':
                    stories.append({
                        'title': story_data.get('title', ''),
                        'url': story_data.get('url', ''),
                        'text': story_data.get('text', ''),
                        'score': story_data.get('score', 0),
                        'descendants': story_data.get('descendants', 0),
                        'time': story_data.get('time', 0),
                        'by': story_data.get('by', '')
                    })
            
            return stories
            
        except Exception as e:
            print(f"Error collecting from Hacker News: {e}")
            return []
```

### 4. PDF Processing for Training Data
```python
import pdfplumber
import PyPDF2
from pathlib import Path
import requests
from io import BytesIO

class PDFProcessor:
    def __init__(self):
        pass
    
    def extract_from_pdf_file(self, pdf_path: str) -> Dict:
        """Extract text from local PDF file"""
        try:
            with pdfplumber.open(pdf_path) as pdf:
                text = ""
                tables = []
                
                for page_num, page in enumerate(pdf.pages):
                    # Extract text
                    page_text = page.extract_text() or ""
                    text += page_text + "\n\n"
                    
                    # Extract tables
                    page_tables = page.extract_tables()
                    for table in page_tables:
                        tables.append({
                            'page': page_num + 1,
                            'data': table
                        })
                
                return {
                    'filename': Path(pdf_path).name,
                    'text': text.strip(),
                    'page_count': len(pdf.pages),
                    'tables': tables,
                    'word_count': len(text.split())
                }
                
        except Exception as e:
            print(f"Error processing PDF {pdf_path}: {e}")
            return None
    
    def extract_from_pdf_url(self, pdf_url: str) -> Dict:
        """Extract text from PDF at URL"""
        try:
            response = requests.get(pdf_url)
            pdf_content = BytesIO(response.content)
            
            with pdfplumber.open(pdf_content) as pdf:
                text = ""
                for page in pdf.pages:
                    page_text = page.extract_text() or ""
                    text += page_text + "\n\n"
                
                return {
                    'url': pdf_url,
                    'text': text.strip(),
                    'page_count': len(pdf.pages),
                    'word_count': len(text.split())
                }
                
        except Exception as e:
            print(f"Error processing PDF from URL {pdf_url}: {e}")
            return None
    
    def batch_process_pdfs(self, pdf_directory: str) -> List[Dict]:
        """Process all PDFs in a directory"""
        pdf_dir = Path(pdf_directory)
        results = []
        
        for pdf_file in pdf_dir.glob("*.pdf"):
            result = self.extract_from_pdf_file(str(pdf_file))
            if result:
                results.append(result)
        
        return results
```

---

## 🚀 FastAPI RAG Endpoints

### Complete RAG API Implementation
```python
from fastapi import FastAPI, HTTPException, UploadFile, File
from pydantic import BaseModel
from typing import List, Optional
import tempfile
import os

app = FastAPI(title="RAG API", description="Retrieval-Augmented Generation API")

# Initialize RAG system
rag_system = RAGSystem()
document_processor = DocumentProcessor()

class QueryRequest(BaseModel):
    question: str
    max_results: Optional[int] = 5

class QueryResponse(BaseModel):
    question: str
    answer: str
    sources: List[str]
    context_used: int

@app.post("/upload-document", response_model=dict)
async def upload_document(file: UploadFile = File(...)):
    """Upload and process a document for RAG"""
    try:
        # Save uploaded file temporarily
        with tempfile.NamedTemporaryFile(delete=False, suffix=f".{file.filename.split('.')[-1]}") as tmp_file:
            content = await file.read()
            tmp_file.write(content)
            tmp_file_path = tmp_file.name
        
        # Process based on file type
        if file.filename.lower().endswith('.pdf'):
            documents = document_processor.process_pdf(tmp_file_path)
        else:
            raise HTTPException(status_code=400, detail="Unsupported file type")
        
        # Add to vector database
        rag_system.add_documents(documents)
        
        # Clean up temporary file
        os.unlink(tmp_file_path)
        
        return {
            "message": f"Successfully processed {file.filename}",
            "chunks_created": len(documents),
            "filename": file.filename
        }
        
    except Exception as e:
        raise HTTPException(status_code=500, detail=f"Error processing document: {str(e)}")

@app.post("/add-url", response_model=dict)
async def add_url(url: str):
    """Add web content to RAG system"""
    try:
        documents = document_processor.process_web_content(url)
        rag_system.add_documents(documents)
        
        return {
            "message": f"Successfully processed URL: {url}",
            "chunks_created": len(documents)
        }
        
    except Exception as e:
        raise HTTPException(status_code=500, detail=f"Error processing URL: {str(e)}")

@app.post("/query", response_model=QueryResponse)
async def query_rag(request: QueryRequest):
    """Query the RAG system"""
    try:
        result = rag_system.query(request.question)
        
        return QueryResponse(
            question=result["question"],
            answer=result["answer"],
            sources=result["sources"],
            context_used=result["context_used"]
        )
        
    except Exception as e:
        raise HTTPException(status_code=500, detail=f"Error processing query: {str(e)}")

@app.get("/health")
async def health_check():
    """Health check endpoint"""
    return {"status": "healthy", "system": "RAG API"}
```

---

## 💡 Advanced RAG Techniques

### 1. Hybrid Search (Keyword + Semantic)
```python
from rank_bm25 import BM25Okapi
import numpy as np

class HybridSearchRAG:
    def __init__(self):
        self.semantic_search = RAGSystem()
        self.keyword_search = None
        self.documents = []
    
    def add_documents(self, documents: List[Dict]):
        """Add documents to both semantic and keyword search"""
        self.documents.extend(documents)
        self.semantic_search.add_documents(documents)
        
        # Prepare for BM25 keyword search
        corpus = [doc['content'] for doc in self.documents]
        tokenized_corpus = [doc.split() for doc in corpus]
        self.keyword_search = BM25Okapi(tokenized_corpus)
    
    def hybrid_retrieve(self, query: str, n_results: int = 10) -> List[Dict]:
        """Combine semantic and keyword search results"""
        # Semantic search
        semantic_results = self.semantic_search.retrieve(query, n_results)
        
        # Keyword search
        tokenized_query = query.split()
        keyword_scores = self.keyword_search.get_scores(tokenized_query)
        keyword_results = [
            {"content": self.documents[i]['content'], "score": score, "index": i}
            for i, score in enumerate(keyword_scores)
        ]
        keyword_results.sort(key=lambda x: x['score'], reverse=True)
        
        # Combine and re-rank results
        combined_results = self.combine_results(semantic_results, keyword_results[:n_results])
        return combined_results[:n_results]
    
    def combine_results(self, semantic_results: List[Dict], keyword_results: List[Dict]) -> List[Dict]:
        """Combine and re-rank semantic and keyword results"""
        # Simple weighted combination (customize as needed)
        semantic_weight = 0.7
        keyword_weight = 0.3
        
        result_map = {}
        
        # Add semantic results
        for i, result in enumerate(semantic_results):
            score = semantic_weight * (1 - result['distance'])  # Convert distance to similarity
            result_map[result['content']] = {
                **result,
                'combined_score': score,
                'semantic_rank': i
            }
        
        # Add keyword results
        for i, result in enumerate(keyword_results):
            content = result['content']
            keyword_score = keyword_weight * result['score']
            
            if content in result_map:
                result_map[content]['combined_score'] += keyword_score
                result_map[content]['keyword_rank'] = i
            else:
                result_map[content] = {
                    'content': content,
                    'combined_score': keyword_score,
                    'keyword_rank': i,
                    'semantic_rank': None
                }
        
        # Sort by combined score
        combined_results = list(result_map.values())
        combined_results.sort(key=lambda x: x['combined_score'], reverse=True)
        
        return combined_results
```

### 2. Query Expansion & Rewriting
```python
class QueryProcessor:
    def __init__(self):
        self.llm = None  # Initialize your LLM
    
    def expand_query(self, original_query: str) -> List[str]:
        """Generate related queries to improve retrieval"""
        prompt = f"""Given the query: "{original_query}"
        
Generate 3 related queries that would help find relevant information:

1."""
        
        response = self.llm.generate(prompt)
        expanded_queries = [original_query] + self.parse_expanded_queries(response)
        return expanded_queries
    
    def rewrite_query_for_retrieval(self, query: str) -> str:
        """Rewrite query to be more suitable for retrieval"""
        prompt = f"""Rewrite this question to be more suitable for document search.
Make it more specific and include key terms:

Original: {query}
Rewritten:"""
        
        return self.llm.generate(prompt).strip()
    
    def extract_key_concepts(self, query: str) -> List[str]:
        """Extract key concepts from query for better matching"""
        # Simple implementation - can be enhanced with NER or LLM
        stop_words = {'the', 'a', 'an', 'and', 'or', 'but', 'in', 'on', 'at', 'to', 'for', 'of', 'with', 'by'}
        words = query.lower().split()
        key_concepts = [word for word in words if word not in stop_words and len(word) > 2]
        return key_concepts
```

---

## 📈 Performance Optimization Tips

### 1. Caching Strategies
```python
from functools import lru_cache
import redis
import pickle

class CachedRAG:
    def __init__(self):
        self.rag_system = RAGSystem()
        self.redis_client = redis.Redis(host='localhost', port=6379, db=0)
    
    def get_cached_response(self, query: str) -> Optional[Dict]:
        """Get cached response if available"""
        cache_key = f"rag_query:{hash(query)}"
        cached = self.redis_client.get(cache_key)
        if cached:
            return pickle.loads(cached)
        return None
    
    def cache_response(self, query: str, response: Dict, ttl: int = 3600):
        """Cache response with TTL"""
        cache_key = f"rag_query:{hash(query)}"
        self.redis_client.setex(cache_key, ttl, pickle.dumps(response))
    
    def query_with_cache(self, query: str) -> Dict:
        """Query with caching"""
        # Check cache first
        cached_response = self.get_cached_response(query)
        if cached_response:
            return cached_response
        
        # Generate new response
        response = self.rag_system.query(query)
        
        # Cache the response
        self.cache_response(query, response)
        
        return response
```

### 2. Batch Processing
```python
class BatchRAGProcessor:
    def __init__(self):
        self.rag_system = RAGSystem()
        self.batch_size = 100
    
    def batch_add_documents(self, documents: List[Dict]):
        """Add documents in batches for better performance"""
        for i in range(0, len(documents), self.batch_size):
            batch = documents[i:i + self.batch_size]
            self.rag_system.add_documents(batch)
            print(f"Processed batch {i//self.batch_size + 1}/{(len(documents) + self.batch_size - 1)//self.batch_size}")
    
    def batch_query(self, queries: List[str]) -> List[Dict]:
        """Process multiple queries efficiently"""
        results = []
        for query in queries:
            result = self.rag_system.query(query)
            results.append(result)
        return results
```

---

## 🔍 Evaluation & Monitoring

### RAG Evaluation Metrics
```python
class RAGEvaluator:
    def __init__(self, rag_system: RAGSystem):
        self.rag_system = rag_system
    
    def evaluate_retrieval_quality(self, test_queries: List[Dict]) -> Dict:
        """Evaluate retrieval quality using ground truth"""
        metrics = {
            'precision_at_k': [],
            'recall_at_k': [],
            'mrr': []  # Mean Reciprocal Rank
        }
        
        for test_case in test_queries:
            query = test_case['query']
            relevant_docs = set(test_case['relevant_docs'])
            
            retrieved_docs = self.rag_system.retrieve(query, n_results=10)
            retrieved_ids = set([doc['metadata'].get('doc_id', '') for doc in retrieved_docs])
            
            # Calculate metrics
            intersection = relevant_docs.intersection(retrieved_ids)
            precision = len(intersection) / len(retrieved_ids) if retrieved_ids else 0
            recall = len(intersection) / len(relevant_docs) if relevant_docs else 0
            
            metrics['precision_at_k'].append(precision)
            metrics['recall_at_k'].append(recall)
            
            # MRR calculation
            mrr_score = 0
            for i, doc_id in enumerate([doc['metadata'].get('doc_id', '') for doc in retrieved_docs]):
                if doc_id in relevant_docs:
                    mrr_score = 1 / (i + 1)
                    break
            metrics['mrr'].append(mrr_score)
        
        return {
            'avg_precision_at_k': sum(metrics['precision_at_k']) / len(metrics['precision_at_k']),
            'avg_recall_at_k': sum(metrics['recall_at_k']) / len(metrics['recall_at_k']),
            'avg_mrr': sum(metrics['mrr']) / len(metrics['mrr'])
        }
    
    def evaluate_answer_quality(self, test_cases: List[Dict]) -> Dict:
        """Evaluate generated answer quality"""
        from sentence_transformers import SentenceTransformer
        
        model = SentenceTransformer('all-MiniLM-L6-v2')
        similarities = []
        
        for test_case in test_cases:
            query = test_case['query']
            expected_answer = test_case['expected_answer']
            
            result = self.rag_system.query(query)
            generated_answer = result['answer']
            
            # Calculate semantic similarity
            embeddings = model.encode([expected_answer, generated_answer])
            similarity = np.dot(embeddings[0], embeddings[1]) / (
                np.linalg.norm(embeddings[0]) * np.linalg.norm(embeddings[1])
            )
            similarities.append(similarity)
        
        return {
            'avg_semantic_similarity': np.mean(similarities),
            'min_similarity': np.min(similarities),
            'max_similarity': np.max(similarities)
        }
```

---

## 🚨 Common Issues & Solutions

### 1. **Context Window Limitations**
```python
def truncate_context(context: str, max_tokens: int = 3000) -> str:
    """Truncate context to fit within token limits"""
    # Simple word-based truncation (improve with tokenizer)
    words = context.split()
    if len(words) > max_tokens:
        return ' '.join(words[:max_tokens]) + "..."
    return context
```

### 2. **Hallucination Detection**
```python
def detect_hallucination(query: str, context: str, answer: str) -> float:
    """Simple hallucination detection based on context overlap"""
    from sentence_transformers import SentenceTransformer
    
    model = SentenceTransformer('all-MiniLM-L6-v2')
    
    # Check semantic similarity between answer and context
    embeddings = model.encode([answer, context])
    similarity = np.dot(embeddings[0], embeddings[1]) / (
        np.linalg.norm(embeddings[0]) * np.linalg.norm(embeddings[1])
    )
    
    return similarity  # Higher similarity = less likely to be hallucination
```

### 3. **Retrieval Quality Issues**
- **Low relevance**: Improve chunk size, overlap, or embedding model
- **Missing context**: Use query expansion or hybrid search
- **Poor ranking**: Implement re-ranking with cross-encoders

---

## 🎯 Production Deployment Tips

### 1. **Scalability**
- Use managed vector databases (Pinecone, Weaviate Cloud)
- Implement horizontal scaling with load balancers
- Cache frequently accessed embeddings
- Use async processing for document ingestion

### 2. **Monitoring**
```python
import logging
from prometheus_client import Counter, Histogram, generate_latest

# Metrics
query_counter = Counter('rag_queries_total', 'Total RAG queries')
query_duration = Histogram('rag_query_duration_seconds', 'RAG query duration')
retrieval_quality = Histogram('rag_retrieval_relevance', 'Retrieval relevance scores')

class MonitoredRAG(RAGSystem):
    def query(self, question: str) -> Dict:
        query_counter.inc()
        
        with query_duration.time():
            result = super().query(question)
        
        # Log query for analysis
        logging.info(f"RAG Query: {question[:100]}... | Sources: {len(result['sources'])}")
        
        return result
```

### 3. **Security**
- Sanitize user inputs
- Implement rate limiting
- Use authentication/authorization
- Validate document sources
- Monitor for sensitive data exposure

---

## 📚 Key Takeaways for Interviews

1. **Understand RAG fundamentals**: Retrieval → Augmentation → Generation
2. **Know vector database options**: Pinecone, ChromaDB, Weaviate, FAISS
3. **Explain embedding strategies**: Different models, chunk sizes, overlap
4. **Discuss evaluation metrics**: Precision@K, Recall@K, MRR
5. **Address common challenges**: Hallucination, context limits, retrieval quality
6. **Show production awareness**: Caching, monitoring, scaling, security

**Remember**: RAG is about connecting LLMs with your specific knowledge base effectively!