<!-- filepath: /playground/organize-bitwarden-folders-ai/README.md -->
# Bitwarden Vault Categorizer

[![Python Version](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A command-line tool to automatically categorize your Bitwarden vault items using the OpenRouter LLM API. It processes a Bitwarden JSON export and assigns categories to each item, saving results to CSV.

---

## Features
- Categorizes Bitwarden items using LLMs (OpenRouter API)
- Supports batch processing for efficiency
- Auto-categorizes company items by folder or email domain (no API cost)
- Outputs a detailed CSV with categories, confidence, and reasons
- CLI with flexible arguments and model selection

---

## Table of Contents
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Export Bitwarden Vault](#export-bitwarden-vault)
- [Usage](#usage)
- [Input Format](#input-format)
- [Output Format](#output-format)
- [Categories](#categories)
- [Tips](#tips-for-best-results)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

---

## Prerequisites
- Python 3.8 or higher
- pip

## Installation

```bash
# Clone the repository
$ git clone https://github.com/yourusername/organize-bitwarden-folders-ai.git
$ cd organize-bitwarden-folders-ai

# Install dependencies
$ pip install python-dotenv requests pyyaml
```

## Configuration

1. Create a `.env` file in the project directory
2. Add your OpenRouter API key:
   ```env
   OPENROUTER_API_KEY=your_openrouter_api_key_here
   ```
   - **Never commit your API key to version control!**

## Export Bitwarden Vault

1. Open your Bitwarden vault
2. Go to "Tools" > "Export Vault"
3. Choose **JSON** format
4. Save the file (e.g., `input.json`)

## Usage

```bash
python bitwarden_categorizer.py -i input.json -o output.csv -m claude-3-haiku-20240307 -b 10
```

### Command-line Arguments
- `-i, --input`      Input Bitwarden export JSON file (**required**)
- `-o, --output`     Output CSV file for categorized data (**required**)
- `-m, --model`      OpenRouter model (default: `claude-3-haiku-20240307`)
- `-b, --batch-size` Number of items per LLM request (default: `10`)
- `--domain-folder-map` YAML file for domain-to-folder mapping (optional)

Run `python bitwarden_categorizer.py --help` for full options.

## Input Format

The input file must be a Bitwarden JSON export with at least these top-level keys:
- `folders`: Array of folder objects (`id`, `name`)
- `items`: Array of item objects (`id`, `name`, `type`, `folderId`, `login` with `uris` and `username`)

Example:
```json
{
  "folders": [
    { "id": "94de53f7-7698-4f11-904a-b27f00a3ed49", "name": "AI" }
  ],
  "items": [
    {
      "id": "51e7100b-0c56-44ac-afd5-ace000215975",
      "folderId": "94de53f7-7698-4f11-904a-b27f00a3ed49",
      "type": 1,
      "name": "x.ai",
      "login": {
        "uris": [ { "uri": "https://www.x.ai" } ],
        "username": "munim"
      }
    }
  ]
}
```

## Output Format

The output CSV will include:
- `id`: Bitwarden item ID
- `name`: Item title
- `category`: Assigned category
- `confidence`: Confidence score (0-100)
- `reason`: Brief explanation for the category
- All original Bitwarden fields (folder, favorite, type, notes, fields, reprompt, login_uri, login_username, login_password, login_totp)

## Categories

### Regular Categories
- Financial (Banking, Credit Cards, Payment Services)
- Social Media
- Email Services
- Work Tools
- Shopping
- Entertainment
- Government/Legal
- Utilities
- Education
- Healthcare
- Gaming
- Travel
- Cloud Services
- Developer Tools
- Personal Projects
- Communication
- Security
- AI

### Domain-to-Folder Mapping (Optional)

You can provide a YAML file to map email domains to folder names for auto-categorization. Use the `--domain-folder-map` argument:

```bash
python bitwarden_categorizer.py -i input.json -o output.csv --domain-folder-map domain_folder_map.yaml
```

Example `domain_folder_map.yaml`:
```yaml
- domain: google.com
  folder: "Google"
- domain: facebook.com
  folder: "Facebook"
```

If provided, items are auto-categorized (not sent to LLM) if:
- Folder name matches any folder in the YAML file
- Email domain in username matches any domain in the YAML file

**Note:** Items matched by the domain-to-folder map are not sent to the LLM to save API costs and ensure consistent naming.

## Tips for Best Results

1. Use a cost-effective model like `claude-3-haiku-20240307` for balance
2. For higher accuracy, try `claude-3-5-sonnet-20240620` (higher cost)
3. Keep batch sizes reasonable (10-20) to avoid token limits
4. Ensure your JSON export is clean and properly structured
5. For large vaults (2000+ items), allow extra time due to API rate limits

## Troubleshooting

- **API Key Issues:** Check your `.env` file and key format
- **JSON Format Issues:** Ensure your export matches the expected structure
- **Rate Limiting:** Lower batch size or add delays between batches
- **Model Selection:** Try a more advanced model if results are poor

## Contributing

Contributions, bug reports, and feature requests are welcome! Please open an issue or pull request.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.