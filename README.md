# Batoto Parser

[![PyPI version](https://badge.fury.io/py/batoto-parser.svg)](https://badge.fury.io/py/batoto-parser)
[![Python versions](https://img.shields.io/pypi/pyversions/batoto-parser.svg)](https://pypi.org/project/batoto-parser/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Lightweight parser for Batoto-style manga sites. Provides both a command-line interface and a Python library for:

- 📚 Listing/browsing manga
- 🔍 Searching manga
- 📖 Fetching manga details (metadata + chapters)
- 🖼️ Retrieving chapter pages (with encrypted image URL decryption)

## Features

- **CLI Tool**: Easy-to-use command-line interface with rich output
- **Python Library**: Import and use in your own projects
- **Flexible**: Works with any Batoto-style manga site
- **Fast**: Efficient parsing with minimal dependencies
- **Typed**: Full type hints for better IDE support

## Requirements

- Python 3.8+
- Node.js (required for chapter page decryption)

### Installing Node.js (Linux)

**Debian/Ubuntu:**
```bash
sudo apt update && sudo apt install -y nodejs
```

**Fedora/RHEL:**
```bash
sudo dnf install nodejs
```

**Arch Linux:**
```bash
sudo pacman -S nodejs
```

## Installation

### From PyPI (recommended)

```bash
pip install batoto-parser
```

### From source

```bash
git clone https://github.com/yourusername/batoto-parser.git
cd batoto-parser
pip install -e .
```

### Development installation

```bash
pip install -e ".[dev]"
```

## CLI Usage

After installation, the `batoto-parser` command will be available:

### List/Browse Manga

```bash
# Browse first page
batoto-parser list

# Browse specific page
batoto-parser list --page 2

# Search for manga
batoto-parser list --query "one piece"

# Save results to file
batoto-parser list --page 1 --output results.json

# Use different domain
batoto-parser list --domain custom-site.com
```

### Get Manga Details

```bash
# By path
batoto-parser details /series/Some-Manga

# By full URL
batoto-parser details https://bato.to/series/Some-Manga

# Save to file
batoto-parser details /series/Some-Manga --output manga.json
```

### Get Chapter Pages

```bash
# Get image URLs for a chapter
batoto-parser pages /reader/12345

# Save to file
batoto-parser pages /reader/12345 --output chapter.json
```

### Options

All commands support:
- `--domain, -d`: Specify domain (default: bato.to)
- `--output, -O`: Save output to file instead of stdout
- `--pretty/--compact`: Pretty-print or compact JSON output
- `--help`: Show detailed help

## Library Usage

Use batoto-parser as a library in your Python projects:

```python
from batoto_parser import BatoToParser, MangaLoaderContext

# Initialize
ctx = MangaLoaderContext()
parser = BatoToParser(ctx, domain="bato.to")

# List manga
mangas = parser.get_list(page=1)
for manga in mangas:
    print(f"{manga.title}: {manga.public_url}")

# Search
results = parser.get_list(page=1, query="one piece")

# Get details
detailed_manga = parser.get_details(mangas[0])
print(f"Chapters: {detailed_manga.chapterCount}")

# Get chapter pages (requires Node.js)
pages = parser.get_pages("/reader/12345")
for page in pages:
    print(f"Page {page.pageNumber}: {page.imageUrl}")
```

See [examples/example_usage.py](examples/example_usage.py) for more examples.

## Project Structure

```
batoto-parser/
├── src/
│   └── batoto_parser/
│       ├── __init__.py        # Package exports
│       ├── __version__.py     # Version info
│       ├── cli.py             # CLI interface (Typer)
│       ├── parser.py          # Main parser logic
│       ├── context.py         # HTTP session & JS eval
│       ├── utils.py           # Crypto & utility functions
│       └── models.py          # Data models
├── tests/                     # Test suite
├── examples/                  # Usage examples
└── pyproject.toml            # Package configuration
```

## API Reference

### Core Classes

- **`BatoToParser`**: Main parser class
  - `get_list(page, order, query)`: List/search manga
  - `get_details(manga)`: Get detailed manga info
  - `get_pages(chapter_url)`: Get chapter image URLs

- **`MangaLoaderContext`**: HTTP session manager with JS evaluation

- **`Manga`**: Manga data model
- **`MangaChapter`**: Chapter data model
- **`MangaPage`**: Page data model

### Utility Functions

- **`generate_uid(url)`**: Generate unique ID from URL
- **`decrypt_batoto(encrypted, password)`**: Decrypt Batoto image URLs
- **`evp_bytes_to_key(...)`**: OpenSSL key derivation

## Development

### Setup

```bash
# Clone repository
git clone https://github.com/mehrinshamim/batoto-parser.git
cd batoto-parser

# Create virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install with dev dependencies
pip install -e ".[dev]"
```

### Testing

```bash
# Run tests
pytest

# With coverage
pytest --cov=batoto_parser --cov-report=html

# Run only unit tests (skip integration tests)
pytest -m "not integration"
```

### Code Quality

```bash
# Format code
black src/ tests/

# Lint
ruff check src/ tests/

# Type check
mypy src/
```

## Troubleshooting

### "Cannot evaluate batoPass" error

Ensure Node.js is installed and available on your PATH:

```bash
node --version  # Should show version number
```

### Empty results

If parsing returns empty results, the site layout may have changed. Check:
1. Site is accessible
2. Selectors in `parser.py` match current HTML structure

### Network errors

Add timeouts or retries in `context.py` for unstable connections.

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Run tests (`pytest`)
5. Commit your changes (`git commit -m 'Add amazing feature'`)
6. Push to branch (`git push origin feature/amazing-feature`)
7. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Disclaimer

This tool is for educational purposes only. Please respect the websites' terms of service and robots.txt. The authors are not responsible for misuse of this tool.

## Changelog

### 0.1.0 (2024-XX-XX)

- Initial release
- CLI tool with list, details, and pages commands
- Library API for programmatic access
- Support for custom domains
- Rich terminal output
- Full test suite

## Acknowledgments

- Built with [Typer](https://typer.tiangolo.com/) for CLI
- [Rich](https://rich.readthedocs.io/) for beautiful terminal output
- [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/) for HTML parsing