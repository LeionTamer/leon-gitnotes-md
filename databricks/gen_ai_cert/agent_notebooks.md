# Genie via Managed MCP

```
from databricks.sdk import WorkspaceClient
from databricks_openai.agents import McpServer
from agents import Agent, Runner

workspace_client = WorkspaceClient()
host = workspace_client.config.host

async with McpServer(
    url=f"{host}/api/2.0/mcp/genie/{genie_space_id}",
    name="sales-data-genie",
    workspace_client=workspace_client,
) as genie:
    agent = Agent(
        name="Analyst",
        instructions="Use the Genie tool to query structured data and answer questions.",
        model="databricks-foundation-model",
        mcp_servers=[genie],
    )
    result = await Runner.run(
        agent,
        "What were the top 10 customers by revenue last quarter?",
    )
```