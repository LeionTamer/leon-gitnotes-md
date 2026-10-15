# Databricks CLI Guide for Generative AI Engineering

For the Databricks Generative AI Certification, you need to understand how to use the Databricks CLI to provision infrastructure, manage security, and deploy AI assets. 

Here are the essential CLI command groups you should know for the exam, starting with a deep dive into Secrets.

## 1. Secrets Management (`databricks secrets`)
Generative AI applications frequently need to communicate with external APIs (e.g., external LLM providers, corporate databases, or external vector stores). The Secrets CLI is how you manage these credentials securely.

*   **Create a Secret Scope:** 
    Creates a secure container for your keys.
    ```bash
    databricks secrets create-scope my_genai_scope
    ```

*   **Store a Secret (Key-Value Pair):**
    Opens a secure text editor in your terminal to paste the sensitive value (prevents keys from being saved in your bash history).
    ```bash
    databricks secrets put-secret --scope my_genai_scope --key openai_api_key
    ```

*   **List Secrets in a Scope:**
    Shows the keys available in a scope, but *never* reveals the actual values.
    ```bash
    databricks secrets list-secrets my_genai_scope
    ```

*   **Manage Access Control (ACLs):**
    Crucial for apps and automated pipelines. Grants a Service Principal or a specific user the ability to read the secrets.
    ```bash
    databricks secrets put-acl --scope my_genai_scope --principal <service-principal-id> --permission READ
    ```

## 2. Unity Catalog Volumes (`databricks fs`)
In GenAI (specifically RAG architectures), you don't typically store raw PDFs or text files in traditional databases. You store them in **Unity Catalog Volumes**. You need to know how to move local files to volumes so your PySpark chunking jobs can read them.

*   **Upload a Document for RAG:**
    Copies a local file into a Unity Catalog Volume.
    ```bash
    databricks fs cp ./knowledge_base.pdf dbfs:/Volumes/prod_catalog/rag_schema/raw_docs/knowledge_base.pdf
    ```
*   **List Files in a Volume:**
    ```bash
    databricks fs ls dbfs:/Volumes/prod_catalog/rag_schema/raw_docs/
    ```

## 3. Model Serving (`databricks serving-endpoints`)
Once you log your custom LangChain agent using `mlflow.pyfunc`, you need to host it. The CLI allows you to create and query these endpoints.

*   **Create a Model Serving Endpoint:**
    Deploys an MLflow model as a REST API. (Usually requires passing a JSON configuration file defining the compute size).
    ```bash
    databricks serving-endpoints create --name my-rag-agent --config-file endpoint_config.json
    ```

*   **Query an LLM/Agent Endpoint directly:**
    Useful for quick CLI testing without writing a Python script. You pass the JSON payload your model expects.
    ```bash
    databricks serving-endpoints query my-rag-agent \
      --json '{"dataframe_split": {"columns": ["query"], "data": [["What is the return policy?"]]}}'
    ```

## 4. Vector Search (`databricks vector-search`)
Vector databases require underlying compute infrastructure (endpoints) and the actual data structures (indexes).

*   **Create a Vector Search Compute Endpoint:**
    Provisions the underlying serverless infrastructure to host vector indexes.
    ```bash
    databricks vector-search endpoints create --name prod-vector-compute --endpoint-type STANDARD
    ```

*   **Create a Vector Index:**
    While often done in Python via the SDK (`databricks-vectorsearch`), you can initialize indexes via the CLI. This links a Delta table containing your chunked text and embeddings to the compute endpoint.
    ```bash
    databricks vector-search indexes create \
      --name prod_catalog.rag_schema.doc_index \
      --endpoint-name prod-vector-compute \
      --primary-key "chunk_id" \
      --index-type DELTA_SYNC \
      --source-table prod_catalog.rag_schema.chunked_docs
    ```

## 5. Databricks Apps (`databricks apps`)
As covered previously, this manages the deployment of serverless UI frameworks (Streamlit, Flask).

*   **Provision App Infrastructure:**
    ```bash
    databricks apps create my-genai-app
    ```
*   **Deploy Local Code:**
    ```bash
    databricks apps deploy my-genai-app --source .
    ```