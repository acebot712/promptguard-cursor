# PromptGuard API — Detailed Reference

## Authentication

All requests must include the `X-API-Key` header:

```
X-API-Key: pg_live_xxx
```

Keys are created in the [dashboard](https://app.promptguard.co/settings/api-keys).
All keys start with `pg_live_`.

## Threat types

The `threatType` field in scan responses can be:

| Value | Description |
|-------|-------------|
| `injection` | Prompt injection attempt |
| `jailbreak` | Jailbreak / system prompt bypass |
| `toxicity` | Toxic, harmful, or offensive content |
| `pii` | Personally identifiable information |
| `exfiltration` | Data exfiltration attempt |
| `malware` | Malicious code or payload |
| `api_key` | Exposed API key or secret |
| `fraud` | Social engineering or fraud |
| `tool_injection` | Malicious tool/function call |
| `url_filter` | Dangerous URL detected |
| `multi_turn` | Multi-turn attack pattern |

## PII entity types

Supported values for `pii_types` in redact requests:

`email`, `phone`, `ssn`, `credit_card`, `iban`, `passport`,
`driver_license`, `ip_address`, `date_of_birth`, `address`,
`name`, `medical_record`, `bank_account`, `tax_id`, `api_key`

## Rate limits

| Tier | Requests/month | Rate limit |
|------|---------------|------------|
| Free | 10,000 | 10 req/s |
| Pro | 100,000 | 50 req/s |
| Enterprise | Custom | Custom |

## Proxy mode

PromptGuard can act as a transparent proxy for LLM providers.
Set the LLM SDK's `base_url` to `https://api.promptguard.co/api/v1/proxy`
and pass both `X-API-Key` (PromptGuard) and `Authorization` (provider) headers.

```python
from openai import OpenAI

client = OpenAI(
    api_key=os.environ["OPENAI_API_KEY"],
    base_url="https://api.promptguard.co/api/v1/proxy/openai/v1",
    default_headers={"X-API-Key": os.environ["PROMPTGUARD_API_KEY"]},
)
```

## Health check

```
GET /health → { "status": "ok" }
```

No authentication required.
