# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Ice Breaker is an AI-powered application that generates personalized ice breakers by analyzing LinkedIn and Twitter profiles using LangChain. The application uses a multi-step pipeline: profile discovery → data extraction → AI analysis → ice breaker generation.

## Development Commands

### Setup
```bash
pipenv install              # Install dependencies
```

### Running the Application
```bash
pipenv run app.py           # Start Flask server on http://localhost:5000
```

### Testing
```bash
pipenv run pytest .         # Run all tests
```

### Code Quality
```bash
pipenv run black .          # Format code
pipenv run isort .          # Sort imports
pipenv run pylint .         # Lint code
```

## Architecture

### Core Pipeline (ice_breaker.py)

The main `ice_break_with(name: str)` function orchestrates the entire pipeline:

1. **Profile Lookup** - LangChain agents discover profile URLs from names
   - `linkedin_lookup_agent` - Uses Tavily search to find LinkedIn profile URL
   - `twitter_lookup_agent` - Uses Tavily search to find Twitter username

2. **Data Scraping** - External API integrations fetch profile data
   - `scrape_linkedin_profile` - Calls Scrapin.io API for LinkedIn data
   - `scrape_user_tweets_mock` - Fetches Twitter data (uses mock by default)

3. **AI Analysis** - LangChain chains process the data through LLMs
   - `get_summary_chain` - Generates summary and interesting facts
   - `get_interests_chain` - Identifies 3 topics of interest
   - `get_ice_breaker_chain` - Creates 2 creative ice breakers

4. **Output** - Returns structured data via Pydantic models (Summary, TopicOfInterest, IceBreaker)

### Key Components

**agents/** - ReAct agents that use Tavily search to find profile URLs
- Both agents use the same pattern: ChatOpenAI + Tool + ReActAgent + AgentExecutor
- Verbose mode is enabled for debugging

**chains/** - LangChain custom chains for LLM processing
- All chains follow the pattern: PromptTemplate | LLM | PydanticOutputParser
- Uses `gpt-3.5-turbo` with temperature=0 for deterministic output (except ice_breaker uses temperature=1)
- Chains are defined as RunnableSequence objects

**third_parties/** - External API integrations
- LinkedIn uses Scrapin.io API (supports mock mode with gist data)
- Twitter uses Tweepy client (defaults to mock mode in main pipeline)
- Both return filtered/cleaned data dictionaries

**tools/** - Search utilities
- `get_profile_url_tavily` - Wrapper around TavilySearch for web lookups

**output_parsers.py** - Pydantic models for structured output
- Each model has a `to_dict()` method for JSON serialization
- Uses LangChain's PydanticOutputParser for automatic validation

**app.py** - Flask web application
- Single POST endpoint `/process` that takes a name and returns all analysis results
- Returns JSON with summary, interests, ice breakers, and profile picture URL

## Environment Configuration

Required API keys (see .env.example):
- `OPENAI_API_KEY` - For LLM access
- `SCRAPIN_API_KEY` - For LinkedIn scraping (or use mock=True)
- `TAVILY_API_KEY` - For web search in agents
- `TWITTER_*` - Twitter API credentials (optional if using mock)
- `LANGCHAIN_TRACING_V2` and `LANGCHAIN_API_KEY` - Optional LangSmith tracing (must have valid key if enabled)

## Important Notes

- The main pipeline uses `scrape_user_tweets_mock` by default, not the real Twitter API
- LinkedIn scraping has a `mock` parameter but defaults to real API calls
- All agents run with `verbose=True` for debugging
- The application filters out empty/null values from scraped data
- Python 3.10 is specified in Pipfile

## Personnel info

- i like pizza
