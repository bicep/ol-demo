# Quick Start Guide

Get up and running with the Promptly API in just a few steps.

---

## Step 1: Install Dependencies

Make sure you have Python 3.7+ installed, then install `requests`:

```bash
pip install requests
```

> Tip: You can also use curl or any HTTP client if you prefer not to use Python.

## Step 2: Create an Account and Get Your API Key

Sign up at https://promptly.ai/signup

> Keep your API key secure! Never commit it to public repositories.

## Step 3: Generate Text

Use the /v1/generate/text endpoint to gen

```
import requests

# Endpoint and headers
url = "https://api.promptly.ai/v1/generate/text"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

# Request payload
data = {
    "model": "promptly-gpt-3",
    "prompt": "Write a haiku about AI",
    "max_tokens": 50
}

# Send request
resp = requests.post(url, headers=headers, json=data)
print(resp.json())

```

#### Example Response
```
{
  "id": "txt-001",
  "text": "Machines learn and grow,\nSilent algorithms hum,\nWisdom blooms in code."
}
```

> Tip: Adjust max_tokens to control the length of the generated text.

Great! You've successfully generated your first piece of text. 🎉  

To explore other capabilities, check out the [Image Generation](reference/image-generation.md) and [Speech Generation](reference/speech-generation.md) sections.
