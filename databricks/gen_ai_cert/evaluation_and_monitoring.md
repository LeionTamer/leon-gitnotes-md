## Scoring Setup

```python
from mlflow.genai.scorers import Safety, ScorerSamplingConfig

# 1. Register the scorer (binds to active experiment)
safety_judge = Safety().register(name="my_safety_judge")

# 2. Start with a sampling rate
safety_judge = safety_judge.start(
    sampling_config=ScorerSamplingConfig(sample_rate=0.7)
)

# Custom model override
custom = Safety(
model="databricks:/databricks-gpt-oss-20b"
).register(name="custom_safety_judge")
```

## Guidelines Judge

```python
from mlflow.genai.scorers import Guidelines, ScorerSamplingConfig

english_judge = Guidelines(
    name="english",
    guidelines=["The response must be in English"]
).register(name="is_english")

english_judge = english_judge.start(
    sampling_config=ScorerSamplingConfig(sample_rate=0.7)
)
```

## Custom Prompt Judge

Only five template variables are allowed: inputs, outputs, expectations, trace, conversation
feedback_value_type uses Literal types
model parameter is required when using trace

```python
from typing import Literal
from mlflow.genai.judges import make_judge

formality_judge = make_judge(
    name="formality",
    instructions="""Evaluate whether the response is
        formal, somewhat formal, or not formal.
        Request: {{ inputs }}
        Response: {{ outputs }}
    """,
    feedback_value_type=Literal[
        "formal", "semi_formal", "not_formal"
    ],
).register(name="formality_judge")
```

## scorer decorator with aggregations

```python
from mlflow.genai.scorers import scorer

@scorer
def mentions_databricks(outputs):
    return "databricks" in str(outputs.get("response", "")).lower()

@scorer(aggregations=["mean", "min", "max"])
def response_length(outputs):
    return len(str(outputs.get("response", "")))
```

## multi-turn judge deployment

```python
from mlflow.genai.scorers import (
    ConversationCompleteness, UserFrustration,
    ConversationalSafety, ScorerSamplingConfig,
)

completeness = ConversationCompleteness().register(
    name="conversation_completeness")
completeness = completeness.start(
    sampling_config=ScorerSamplingConfig(sample_rate=1.0)
)
```

## load historical scores with Backfill Scorer

This uses Historical Traces

```python
from databricks.agents.scorers import backfill_scorers, BackfillScorerConfig
from datetime import datetime, timedelta

# Simple backfill using registered sample rates
job_id = backfill_scorers(
    scorers=["safety_check", "response_length"]
)

# Custom sample rates with time range
job_id = backfill_scorers(
    experiment_id=YOUR_EXPERIMENT_ID,
    scorers=[
        BackfillScorerConfig(scorer=safety_judge, sample_rate=0.8),
        BackfillScorerConfig(scorer=response_length, sample_rate=0.9),
    ],
    start_time=datetime.now() - timedelta(days=7),
    end_time=datetime.now(),
)
```

## archival traces

```python
from mlflow.tracing.archival import (
    enable_databricks_trace_archival,
    disable_databricks_trace_archival,
)

# Enable archival to a Delta table
enable_databricks_trace_archival(
    delta_table_fullname="my_catalog.my_schema.archived_traces",
    experiment_id="YOUR_EXPERIMENT_ID",
)

# Disable (existing data preserved, new traces stop flowing)
disable_databricks_trace_archival(
    experiment_id="YOUR_EXPERIMENT_ID"
)
```