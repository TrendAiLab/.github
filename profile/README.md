# TrendAI

News application with AI-powered search, personalized recommendations, and chatbot built during my internship at SNRT.

## Demo

![Image](https://i.ibb.co/DDLkkgCb/169shots-so.png)

## Overview

TrendAI is a news platform that uses semantic search and user behavior analysis to deliver relevant content. The app provides three main features: recommendations based on reading history, semantic search across articles and videos, and an AI chatbot that answers questions about the news.

## Architecture

The backend runs on FastAPI and uses two databases:
- **Milvus**: Vector database for semantic search and content embeddings
- **Supabase**: PostgreSQL database for user interactions and metadata

Articles and videos are embedded using the `BAAI/bge-m3` multilingual model, creating 1024-dimensional vectors that capture semantic meaning in French and Arabic.

## Core Systems

### Recommendation System

The system builds a user profile by analyzing their reading behavior stored in Supabase (views, bookmarks, interaction scores). For each article they've engaged with, the app fetches the corresponding embedding vector from Milvus. These vectors are averaged with higher weights for bookmarked content, creating a "user preference vector".

When requesting recommendations, the system searches Milvus using this vector to find semantically similar articles. It filters out content the user has already seen and returns the top matches. If no user history exists, it falls back to keyword extraction from their most recent interactions.

### AI-Powered Search

Search queries are converted to embeddings and compared against the article database using cosine similarity. The system classifies search intent (video content, match results, schedules, general news) and applies filters accordingly. For example, queries mentioning "video" automatically filter to video content, and year-specific queries narrow results to that timeframe.

Results are ranked by relevance score and limited to the most similar content.

### Chatbot

The chatbot combines semantic search with Llama 3.3 70B. When a user asks a question:

1. The question is embedded and used to search Milvus for relevant articles
2. Results with similarity above 0.5 are selected
3. Article titles and descriptions are combined into a context prompt
4. The LLM generates a response based on this context
5. The response is returned along with source articles

The chat keeps the last 4 turns of conversation history to maintain context across multiple messages.

## Setup

### Prerequisites

- Python 3.10+
- Flutter SDK
- Dart SDK

### Backend Setup (trendai-fastapi)

1. Clone the backend repository:
```bash
git clone https://github.com/TrendAiLab/TrendAi-FastApi.git
cd trendai-fastapi
```

2. Create a virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Configure environment variables:
```bash
cp .env.example .env
# Edit .env with your credentials
```

Required environment variables:
- `MILVUS_URI`: Your Milvus cloud instance URI
- `MILVUS_TOKEN`: Your Milvus API token
- `SUPABASE_URL`: Your Supabase project URL
- `SUPABASE_ANON_KEY`: Your Supabase anonymous key
- `GROQ_API_KEY`: Your Groq API key

5. Run the application:
```bash
python main.py
```

The API will be available at `http://localhost:8088`

### Frontend Setup (trendai-flutter)

1. Clone the frontend repository:
```bash
git clone https://github.com/TrendAiLab/TrendAi-flutter.git
cd trendai-flutter
```

2. Install dependencies:
```bash
flutter pub get
```

3. Configure the API endpoint in your Flutter app to point to the backend API.

4. Run the application:
```bash
flutter run
```

## API Endpoints

- `GET /health` - Health check
- `GET /api/v1/articles` - Get articles
- `POST /api/v1/search` - Semantic search
- `GET /api/v1/recommendations/{user_id}` - Personalized recommendations
- `POST /api/v1/chat` - Chat with AI assistant

## Technology Stack

**Backend:**
- FastAPI for the API layer
- SentenceTransformers for embeddings
- Milvus for vector search
- Supabase for relational data

**Frontend:**
- Flutter for cross-platform mobile development
- Dart programming language

