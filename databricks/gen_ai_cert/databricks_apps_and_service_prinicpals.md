# Databricks Apps Deployment & Service Principal Guide

## 1. Core Application Files
Your project requires three core files in the same directory before deployment.

**`app.py`** (Application Logic)
```python
import os
import streamlit as st
from databricks.sdk import WorkspaceClient

# Native auth: Automatically inherits the app's Service Principal
w = WorkspaceClient() 

# Fetch secure environment variables injected at runtime
api_key = os.environ.get("EXTERNAL_API_KEY")

st.title("Internal RAG Assistant")
user_input = st.text_input("Ask a question:")
if user_input:
    st.write("Processing...")
```

**`requirements.txt`** (Dependencies)
```text
streamlit==1.36.0
mlflow
```

**`app.yaml`** (Configuration, Identity, & Secrets)
```yaml
name: internal-rag-assistant
# Optional: Bind a custom Service Principal (omit to use the automatic system-managed one)
service_principal_client_id: "12345678-abcd-efgh-ijkl-9876543210ab"
command: [
  "streamlit", 
  "run", 
  "app.py", 
  "--server.port", "8000"
]
env:
  # Plaintext variable
  - name: ENVIRONMENT_TIER
    value: "production"
  # Secure secret dynamically pulled from Databricks Secrets
  - name: EXTERNAL_API_KEY
    valueFrom:
      secretKeyRef:
        scope: my_app_secrets
        key: external_api_key
```

## 2. Infrastructure & Secrets Provisioning (CLI)
Before deploying the code, provision the app and any required secret scopes using the Databricks CLI.

```bash
# 1. Create the serverless app infrastructure
databricks apps create internal-rag-assistant

# 2. Create a secret scope and store external API keys securely
databricks secrets create-scope my_app_secrets
databricks secrets put-secret --scope my_app_secrets --key external_api_key
```

## 3. Code Deployment (CLI)
Upload the local directory, install dependencies, and start the app.

```bash
# Deploy all files in the current directory (.) to the app
databricks apps deploy internal-rag-assistant --source .
```

## 4. Identity Management & Service Principals
Production apps must run under a non-human identity (Service Principal) to ensure stability if a developer leaves the company or their token expires.

**Method A: System-Managed Service Principal (Default & Easiest)**
If you do not hardcode a `service_principal_client_id` in `app.yaml`, Databricks creates an identity automatically for your app. You grant permissions directly to the app's name using standard SQL:

```sql
-- Grant the app permission to read your chunked PDF documents
GRANT SELECT ON TABLE genai_catalog.rag_schema.pdf_chunks TO `internal-rag-assistant`;

-- Grant the app permission to query your Mosaic AI Agent/Model Serving endpoint
GRANT EXECUTE ON ENDPOINT `databricks-meta-llama-3-70b` TO `internal-rag-assistant`;
```

**Method B: Custom Service Principal**
If your IT team provides a pre-configured Service Principal, bind its Client ID in your `app.yaml` (as shown in Section 1). Databricks will verify you have "Can Use" permissions on that Service Principal during the deploy step, and the app will assume that identity for all data and endpoint access.

# Databricks Apps & Secrets: Key Concepts

When working with Databricks Apps and Secrets, the most important mental shift is moving away from how secrets are handled in standard Databricks Notebooks. Because Databricks Apps run in dedicated serverless containers rather than standard Spark clusters, the architecture for injecting and consuming sensitive data changes.

### 1. The Core Rule: No `dbutils`
If you are used to writing data engineering pipelines in Databricks notebooks, your instinct will be to use `dbutils.secrets.get(scope="x", key="y")`. 

**This does not work in Databricks Apps.** The `dbutils` library is tightly coupled to the Spark runtime environment. Instead, Databricks Apps rely on standard POSIX environment variables. Databricks handles the secure extraction from the secret vault and injects them into your app's container as standard OS variables right before the app boots.

This is a massive advantage because your code remains framework-agnostic. You just use standard Python:
```python
import os
api_key = os.environ.get("MY_API_KEY")
```

### 2. The `app.yaml` is the Bridge
To get a secret out of the Databricks vault and into your Python `os.environ`, you must map it explicitly in your `app.yaml` file.

The Databricks App infrastructure parses this YAML file during deployment, reaches into the specified secret scope, grabs the key, and mounts it into the container's memory.

```yaml
env:
  - name: EXTERNAL_DB_PASSWORD # How it appears in Python (os.environ)
    valueFrom:
      secretKeyRef:
        scope: production_credentials # The Databricks Secret Scope
        key: db_password              # The specific Secret Key
```

### 3. Permissions and the Service Principal
Secrets are governed by Access Control Lists (ACLs). This is where deployments often fail if permissions are not set correctly.

*   **Who fetches the secret?** The secret is fetched at runtime by the identity running the app. 
*   **The App Identity:** If your app is running under a system-managed Service Principal or a custom Service Principal (configured via `service_principal_client_id`), **that specific Service Principal must have `READ` permissions on the Secret Scope.**
*   **How to grant it:** You typically do this via the Databricks CLI or Terraform.
    ```bash
    databricks secrets put-acl --scope production_credentials --principal <service-principal-application-id> --permission READ
    ```
If the Service Principal lacks this permission, the app container will fail to start because it cannot resolve the environment variable requested in the `app.yaml`.

### 4. Local Development vs. Production
Because your production app uses Databricks infrastructure to securely inject the secret into the environment, you need a way to replicate this when running `streamlit run app.py` on your local Windows/WSL machine.

**The Best Practice:**
1.  Create a `.env` file on your local machine in the same directory as your app.
2.  Store your plaintext secrets in this file for local testing:
    ```text
    # .env
    EXTERNAL_DB_PASSWORD=my_local_dev_password
    ```
3.  Use a library like `python-dotenv` in your `app.py` to load them during local development.
4.  **Crucial:** Add `.env` to your `.gitignore` file so you never accidentally commit your local secrets to your repository. When deployed to Databricks, the app ignores the missing `.env` file and relies on the Databricks Secret Manager injection instead.