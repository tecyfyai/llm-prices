# LLM API prices

The API price of every text model from OpenAI, Anthropic, Google, xAI, DeepSeek, Moonshot AI and Alibaba, in US dollars per million tokens: input, cached input, cache write, output, batch and long-prompt prices, with each model's context window, longest answer and capability score.

Every price is read from the maker's own price page, and each row says which page and on which day. The files are refreshed every week and follow [tecyfy.com/prices](https://tecyfy.com/prices), where the same data is shown as a searchable table, a cost calculator and a page for each model.

## Files

| File | What it holds |
| --- | --- |
| [`prices.csv`](prices.csv) | One row per model, every price |
| [`prices.json`](prices.json) | The same rows, with the units, licence and score source |
| [`changes.csv`](changes.csv) | Price changes to models already listed, newest first, each with its source |

## Columns

| Column | Meaning |
| --- | --- |
| `maker`, `makerName` | The company, as an id and as a name |
| `id`, `name` | The model id used in the API, and its name |
| `status` | `current` (the maker's newest), `older` (still sold) or `retired` |
| `input`, `output` | Price per million input and output tokens |
| `cachedInput` | Price per million input tokens read from the prompt cache |
| `cacheWrite` | Price per million tokens written to the prompt cache, where the maker charges for it |
| `batchInput`, `batchOutput` | Prices through the maker's batch API |
| `longAbove`, `longInput`, `longOutput` | A prompt longer than `longAbove` tokens pays these prices instead |
| `context` | The context window, in tokens |
| `maxOutput` | The longest answer, in tokens |
| `score`, `scoreLow`, `scoreHigh` | The Epoch Capabilities Index and its range (GPT-5 = 150) |
| `checked` | The day the maker's price page was read |
| `source` | The maker's price page |
| `page` | The model's page on tecyfy.com |

An empty cell means the maker does not list that price.

## Use it

```python
import pandas as pd

prices = pd.read_csv("https://raw.githubusercontent.com/tecyfyai/llm-prices/main/prices.csv")
current = prices[prices.status == "current"]
current.assign(blend=(3 * current.input + current.output) / 4).sort_values("blend")[["name", "input", "output", "blend"]]
```

```sh
curl -s https://raw.githubusercontent.com/tecyfyai/llm-prices/main/prices.json \
  | jq '.models[] | select(.maker == "anthropic" and .status == "current") | {name, input, output}'
```

## Licence

[CC BY 4.0](LICENSE). Use the data anywhere, in commercial work too, and credit **Tecyfy** with a link to [tecyfy.com/prices](https://tecyfy.com/prices).

The capability scores are the [Epoch Capabilities Index](https://epoch.ai/benchmarks/eci) by Epoch AI, also under CC BY 4.0. Credit Epoch AI where you show them.

## Corrections

If a price is wrong or a model is missing, [open an issue](https://github.com/tecyfyai/llm-prices/issues) with a link to the maker's price page.
