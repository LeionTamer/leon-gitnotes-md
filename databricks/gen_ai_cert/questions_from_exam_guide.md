Question 3 
Objective: Choose the appropriate Python package to extract document content from provided 
source data and format.   
A Generative AI Engineer is building a RAG application that will rely on context retrieved from 
source documents that have been scanned and saved as image files in formats like .jpeg or .png. 
They want to develop a solution using the least amount of lines of code. 
Which Python package should be used to extract the text from the source documents?  
A. beautifulsoup 
B. scrapy 
C. pytesseract 
D. pyquery

Question 4
Objective: Select an embedding model context length based on source documents, expected 
queries, and optimization strategy  
A Generative AI Engineer is creating a LLM-based application. The documents for its retriever have 
been chunked to a maximum of 512 tokens each. The Generative AI Engineer knows that cost and 
latency are more important than quality for this application. They have several context length 
levels to choose from. 
Which will fulfill their need? 
A. context length 512: smallest model is 0.13GB and embedding dimension 384 
B. context length 514: smallest model is 0.44GB and embedding dimension 768 
C. context length 2048: smallest model is 11GB and embedding dimension 2560 
D. context length 32768: smallest model is 14GB and embedding dimension 4096

Question 6 
Objective: Configure vector search for a particular solution based on number of embeddings, 
update frequency, latency, and cost requirements. 
A Generative AI Engineer working for an online retailer is attempting to improve their search 
functionality with vector search plus metadata filtering. Searches can total as many as 80 per 
second and latency is their most critical metric. They don't mind up front development costs if it 
improves accuracy without harming latency. The inventory consists of 100 million items 
nationwide. 
How should the engineer set this up? 
A. Leverage GTE Large embedding model, use standard vector search with hybrid search and 
reranking turned on. 
B. Leverage GTE Large embedding model, use storage optimized vector search with hybrid 
search and reranking turned on. 
C. Fine tune a custom embedding model, use standard vector search, keep hybrid search and 
reranking off. 
D. Fine tune a custom embedding model, use storage optimized vector search, keep hybrid 
search and reranking off.

Question 7 
Objective: Apply CI/CD best practices such as updating a Vector Search index, promoting 
prompts across environments, and testing individual components of an agent. 
A Generative AI Engineer needs to manage prompt templates for an agent across development, 
staging, and production. The team requires a gated release process: prompts are updated in 
development, validated in staging with automated tests, and promoted to production only after 
approval. The solution must preserve version history and allow rollback to a previous prompt 
version if needed. 
Which approach supports this promotion workflow? 
A. Store prompt templates in the application repository and promote them by merging the 
staging branch into the production branch after tests pass. 
B. Track prompts as MLflow versions and promote the prompts using aliases after they pass. 
C. Save prompts in a JSON file on the CI runner and overwrite the prod prompt on each run. 
D. Put prompts in Delta tables and overwrite the table in prod on every deployment to ensure 
consistency.

Question 8 
Objective: Develop an appropriate interactive user facing interface for an agent usage scenario 
(Apps, Slack, Teams, etc.). 
Generative AI Engineer is building a Databricks App that lets customer support agents ask 
questions and receive answers grounded in internal PDFs. Requirements: users must authenticate 
with their corporate identity, the app must call a Mosaic AI Agent endpoint without exposing 
long-lived tokens in the browser, and access to answers must respect each user’s permissions. 
Which approach meets these requirements? 
A. Use a Databricks App backend to call the Agent endpoint with the app’s credentials and 
enforce user identity/permissions via the app’s authenticated context. 
B. Store a Databricks personal access token (PAT) in the app’s JavaScript and call the Agent 
endpoint directly from the browser. 
C. Publish the Agent endpoint publicly and protect it with an API key embedded in the app 
frontend. 
D. Export the PDFs to a public bucket so the Agent can read them without identity checks.

Answers 
Question 1: A, B 
Question 2: B 
Question 3: C 
Question 4: A 
Question 5: D 
Question 6: C 
Question 7: B 
Question 8: A 
Question 9: D, E 
Question 10: B

# Databricks Generative AI Certification - Study Guide Summary

This guide summarizes key concepts, exam questions, and architectural rationales for building and deploying Generative AI applications on Databricks.

## 1. AI Data Extraction (SQL & Databricks Functions)
**Concept:** Extracting structured data from unstructured text using built-in Databricks SQL functions.
*   **`ai_extract(content, '["label1", "label2"]')`**: Passes unstructured text to an LLM to find specific data points and returns them as a structured JSON object.
*   **Variant Path Extraction (`:`)**: Used to unpack the resulting JSON object into standard SQL columns (e.g., `ext:response.invoice_id`).
*   **Rationale:** Eliminates the need for complex, brittle regular expressions (RegEx) in data engineering ETL pipelines by leveraging LLMs directly in SQL.

## 2. Document Processing & OCR (Exam Q3)
**Scenario:** Extracting text from scanned image files (.jpeg, .png) for a RAG application with minimal code.
*   **Correct Tool:** `pytesseract` (Python wrapper for Tesseract-OCR).
*   **Rationale:** Scanned images require Optical Character Recognition (OCR) to read text. 
*   **Why others fail:** `beautifulsoup`, `scrapy`, and `pyquery` are HTML/XML web-scraping and parsing libraries. They cannot process image files. 

## 3. Embedding Model Selection & Optimization (Exam Q4)
**Scenario:** Selecting an embedding model for 512-token chunks where cost and latency are prioritized over absolute quality.
*   **Correct Configuration:** Context length 512, smallest model size (0.13GB), and smallest embedding dimension (384).
*   **Rationale:**
    *   *Context Length:* Should match your maximum chunk size. Choosing a 2048+ context window for 512-token chunks wastes capacity.
    *   *Model Size (GB):* Smaller models (e.g., 0.13GB) can run on cheap CPU endpoints, drastically reducing infrastructure costs compared to 11GB+ models requiring GPUs.
    *   *Dimensions:* Smaller vector dimensions (384 vs. 4096) require less storage and make mathematical distance calculations (cosine similarity) significantly faster at query time.

## 4. Vector Search Configuration (Exam Q6)
**Scenario:** High-traffic e-commerce search (80 QPS). Latency is the absolute priority, but upfront development cost is acceptable to improve accuracy.
*   **Correct Configuration:** Fine-tune a custom embedding model, use Standard Vector Search, keep hybrid search and reranking OFF.
*   **Rationale:**
    *   *Vector Search Tier:* **Standard** keeps vectors in memory (RAM) for millisecond latency. Storage Optimized (disk) is too slow for 80 QPS.
    *   *Reranking:* Must be **OFF**. Cross-encoder reranking adds massive latency spikes at inference time.
    *   *Accuracy Solution:* **Fine-tuning** an open-source model (like GTE-Large) on domain-specific data improves accuracy without adding *any* latency penalty during the actual search query.

## 5. LLMOps, CI/CD, and Model Promotion (Exam Q7)
**Scenario:** Gated release process for managing prompt templates/agents across dev, staging, and prod with version history and rollback capabilities.
*   **Correct Configuration:** Track prompts as MLflow versions and promote them using Unity Catalog Aliases (e.g., `@champion`, `@staging`).
*   **Rationale:**
    *   In Databricks, prompts and chains are treated as ML models using the `mlflow.pyfunc` flavor.
    *   Logging to MLflow provides an immutable version history (Version 1, Version 2).
    *   Using **Aliases** allows CI/CD pipelines to instantly promote or rollback a prompt (using `MlflowClient.set_registered_model_alias`) without requiring a Git redeploy or application code change. Overwriting files or Delta tables destroys version history.

## 6. Key Libraries to Know
*   **`mlflow` (specifically `pyfunc`)**: Used to wrap, log, and deploy custom LangChain/GenAI logic so Databricks Model Serving can execute it.
*   **`langchain` / `llama-index`**: For orchestration, chunking, and chaining.
*   **`databricks-vectorsearch`**: For programmatic management of vector indexes.

# Databricks GenAI Certification: MCP Servers & Tool Integration (Exam Q9)

**Scenario:** Integrating a managed MCP (Model Context Protocol) server for an internet data source and an external MCP server for a web search API (which requires an API key). The goal is to minimize maintenance while ensuring reliability.

*   **Correct Answers:** **D** and **E**

### Rationale Breakdown

**1. Configuring the Managed MCP Server (Option D)**
*   **The Logic:** Model Context Protocol (MCP) allows agents to securely connect to external tools and data. When Databricks provides a **managed MCP server** for a specific resource, the underlying infrastructure, scaling, and authentication are handled by Databricks.
*   **Why D is correct:** To minimize maintenance overhead (a strict requirement in the prompt), you simply declare the server in your agent's configuration file/code as `type: "managed"` and provide its identifier. You do not need to build, host, or maintain the server yourself.

**2. Configuring the External MCP Server securely (Option E)**
*   **The Logic:** An **external MCP server** is hosted outside of the managed Databricks environment (e.g., a third-party web search API) and requires authentication (an API key).
*   **Why E is correct:** As covered in previous security questions, you must *never* hardcode API keys or leave them in plaintext configurations. You store the API key in **Databricks Secrets**. You then configure the agent to point to the external MCP server's connection URL and reference the Databricks Secret for the authorization header.

### Why the other options are wrong:
*   **A (Build a custom MCP server):** The prompt explicitly states to "minimize maintenance overhead." Building and maintaining a custom wrapper when managed and external servers already exist is an anti-pattern and wastes engineering time.
*   **B (Web browser automation):** Programmatically navigating web pages (like using Selenium/Puppeteer via an agent) to scrape data is highly brittle, slow, and unreliable compared to using dedicated, structured MCP API servers.
*   **C (Unity Catalog caching):** Caching live, dynamic web search results into a static Unity Catalog external table defeats the purpose of a real-time research agent. 

---

### Conceptual Code Example: Agent MCP Configuration
While the exact YAML/JSON schema can vary based on the specific framework (like LangChain or native Mosaic AI Agent Framework), the logical configuration looks like this:

```yaml
# Example Agent Configuration
mcp_servers:
  # 1. The Managed Server (Option D)
  - name: internal_knowledge_base
    type: managed
    identifier: databricks-managed-internet-resource-v1

  # 2. The External Server (Option E)
  - name: web_search_api
    type: external
    connection_url: "[https://api.external-search.com/mcp](https://api.external-search.com/mcp)"
    api_key:
      # Securely referencing Databricks Secrets
      secret_scope: my_genai_scope
      secret_key: external_search_api_key
```

# Databricks MCP Servers Configuration Details

When configuring `mcp_servers` for a Databricks Generative AI agent, the configuration dictates not just *where* the tools are located, but *how* the agent authenticates, passes context, and manages security. 

Here are the core concepts and advanced properties you need to know:

## 1. The Three Types of MCP Servers (`type`)
Databricks categorizes Model Context Protocol (MCP) servers into three distinct deployment patterns:

*   **`type: managed`**: Tools fully hosted and maintained by Databricks (e.g., Unity Catalog functions, built-in Databricks internet search). These require an `identifier` or a catalog/schema path. Databricks handles all infrastructure and auth.
*   **`type: external`**: Third-party MCP servers hosted entirely outside of Databricks (e.g., an enterprise Glean server or a custom corporate API). Requires a `connection_url` and explicit authentication credentials.
*   **`type: custom` (Databricks Apps):** A hybrid approach where you write custom Python tools or OpenAPI wrappers, but deploy them onto **Databricks Apps** (serverless compute). This is the recommended approach for internal proprietary tools because Databricks handles the hosting and SSL, but you control the logic.

## 2. Authorization Context (`auth: "user"` vs `"app"`)
When the AI agent decides to call a tool, whose permissions should that tool execute under? You control this via the `auth` property.

*   **`auth: "user"` (Passthrough / On-Behalf-Of):** The agent passes the exact identity of the human user interacting with the chat UI down to the MCP server. This is critical for data governance—if the MCP server queries Unity Catalog or internal docs, it will only return data that specific user is authorized to see.
*   **`auth: "app"` (Service Principal):** The agent uses the application's underlying Service Principal identity. This is used for global tools where user-level permissions don't apply, such as doing a public internet search or pulling a live stock price.

## 3. Unity Catalog Connections (The Modern Alternative to Secrets)
In the previous example, we used an `api_key` block pointing to Databricks Secrets. While valid for simple APIs, the enterprise-recommended approach in Databricks for external MCP servers is to use **Unity Catalog HTTP Connections**.

Instead of managing raw secret strings in your agent config, you define a Connection in Unity Catalog that handles the OAuth flow or API keys centrally. Your YAML then simply references that connection name:

```yaml
mcp_servers:
  - name: external_salesforce_tools
    type: external
    connection_url: "[https://api.salesforce.com/mcp](https://api.salesforce.com/mcp)"
    # Replaces the raw API Key / Secret Scope block
    uc_connection_name: salesforce_prod_connection
    auth: "user" # Databricks natively handles passing the user's OAuth token