# Lead Qualifier

A notebook tool that classifies whether a company is a good sales lead, given only its website URL.
It is aimed at an enterprise selling AI booking agents to the dental industry, but the prompts can be
adapted to any product or sector.

## What it does

Given a business website, the notebook:

1. Fetches the landing page content.
2. Lists all links on the landing page.
3. Uses an LLM to pick the links most relevant to qualifying the lead (services, pricing, booking, reviews, FAQs, etc.).
4. Fetches the content of those relevant pages.
5. Uses an LLM to produce a short prospect profile in markdown, covering:
   - Company Name
   - Company Description
   - Company Industry
   - Potential opportunities for the AI booking agent
   - Potential company pain points

The final profile is streamed to the notebook output as rendered markdown.

## Models

- **Link selection and lead profiling:** OpenAI (`gpt-4o-mini` and `gpt-4.1-mini`).
- **Website summarisation:** local Llama via Ollama (`llama3.2:1b`) using the OpenAI-compatible endpoint at
  `http://localhost:11434/v1`.

## Requirements

- Python with the project dependencies (`openai`, `ollama`, `requests`, `beautifulsoup4`, `python-dotenv`, `ipython`).
- `scraper.py` (in this folder) which provides `fetch_website_contents` and `fetch_website_links`.
- An OpenAI API key.
- [Ollama](https://ollama.com) running locally with the `llama3.2:1b` model pulled.

## Setup

1. Install dependencies (from the project root):

   ```bash
   uv sync
   ```

2. Create a `.env` file with your OpenAI key:

   ```
   OPENAI_API_KEY=sk-...
   ```

3. Start Ollama and pull the model:

   ```bash
   ollama pull llama3.2:1b
   ollama serve
   ```

## Usage

Open the notebook and run the cells in order:

```bash
uv run jupyter lab lead_qualifier.ipynb
```

The key entry point is the final cell:

```python
create_lead_qualifier('Harley Street Dental Studio', 'https://www.harleystreetdentalstudio.com/')
```

Pass the company name and its website URL to generate a lead profile for any company.

## Notes

- `scraper.py` truncates each fetched page to 2,000 characters, and the combined prompt is truncated to
  5,000 characters, to keep requests small. Adjust these limits in the notebook/scraper if you need more context.
- The link-selection and profiling prompts are written for the dental booking-agent use case. Edit the
  system prompts to target a different industry or product.
