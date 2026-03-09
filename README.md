# dataforseo-cli

A command-line interface for the [DataForSEO API](https://dataforseo.com/), providing fast access to SERP data, keyword research, and domain analytics directly from your terminal.

[![CI](https://github.com/nightlybuildsco/dataforseo-cli/actions/workflows/ci.yml/badge.svg)](https://github.com/nightlybuildsco/dataforseo-cli/actions/workflows/ci.yml)

## Installation

### Homebrew (macOS/Linux)

```bash
brew install nightlybuildsco/tap/dataforseo-cli
```

### Go Install

```bash
go install github.com/nightlybuildsco/dataforseo-cli@latest
```

### From Source

```bash
git clone https://github.com/nightlybuildsco/dataforseo-cli.git
cd dataforseo-cli
go build -o dataforseo-cli .
```

### Pre-built Binaries

Download the latest release for your platform from the [Releases](https://github.com/nightlybuildsco/dataforseo-cli-releases/releases) page. Available for Linux, macOS, and Windows on both amd64 and arm64 architectures.

## Configuration

You need a [DataForSEO account](https://app.dataforseo.com/register) to use this tool.

### Interactive Setup

```bash
dataforseo-cli configure
```

This creates `~/.dataforseo-cli` with your credentials.

### Environment Variables

```bash
export DATAFORSEO_LOGIN=your_login
export DATAFORSEO_PASSWORD=your_password
```

Environment variables override file-based configuration.

### Config File Format

`~/.dataforseo-cli`:

```json
{
  "login": "your_login",
  "password": "your_password",
  "base_uri": "https://api.dataforseo.com/v3"
}
```

## Quick Start

```bash
# Get search volume for keywords
dataforseo-cli keywords search-volume "seo tools" "keyword research"

# Live SERP results
dataforseo-cli serp live "best project management software"

# Domain technology lookup
dataforseo-cli domain technologies "example.com"

# WHOIS information
dataforseo-cli domain whois "example.com"
```

## Commands

### SERP

Query search engine results pages in real-time or asynchronously.

```bash
# Live SERP query
dataforseo-cli serp live "seo tools"

# Specify engine, location, device
dataforseo-cli serp live "restaurants" --engine google --location "New York" --device mobile

# Search types: organic, maps, news, images, jobs
dataforseo-cli serp live "pizza delivery" --type maps --location "Chicago"

# YouTube search
dataforseo-cli serp live "golang tutorial" --engine youtube

# Control result depth (max 700)
dataforseo-cli serp live "seo tools" --depth 100

# Async: submit tasks for later retrieval
dataforseo-cli serp task-post "keyword1" "keyword2" --tag "my-batch"
dataforseo-cli serp tasks-ready
dataforseo-cli serp task-get <task-id>
```

**Supported engines:** google, bing, yahoo, baidu, youtube, naver, seznam

**Flags:**

| Flag | Default | Description |
|------|---------|-------------|
| `--engine` | `google` | Search engine |
| `--type` | `organic` | Search type: organic, maps, news, images, jobs |
| `--location` | | Location name (e.g., "United States") |
| `--language` | `en` | Language code |
| `--device` | `desktop` | Device: desktop or mobile |
| `--depth` | `10` | Number of results (max 700) |
| `--input-file` | | CSV file with keywords |
| `--input-column` | | Column name or 0-based index |
| `--tag` | | Custom tag for async tasks |

### Keywords

Keyword research and search volume data.

```bash
# Search volume for keywords
dataforseo-cli keywords search-volume "seo tools" "keyword research"

# With location and date range
dataforseo-cli keywords search-volume "seo" --location "United States" --date-from 2024-01-01

# Discover related keywords (max 20 seeds)
dataforseo-cli keywords related "seo tools" "keyword research"
dataforseo-cli keywords related "seo" --sort-by relevance

# Keywords for a domain or URL
dataforseo-cli keywords for-site "example.com"
dataforseo-cli keywords for-site "example.com/page" --target-type page

# Use different data sources
dataforseo-cli keywords search-volume "seo" --source bing
dataforseo-cli keywords search-volume "seo" --source clickstream

# Async keyword tasks
dataforseo-cli keywords task-post search-volume "keyword1" "keyword2" --tag "batch-1"
dataforseo-cli keywords tasks-ready search-volume
dataforseo-cli keywords task-get <task-id>
```

**Flags:**

| Flag | Default | Description |
|------|---------|-------------|
| `--source` | `google_ads` | Data source: google_ads, bing, clickstream |
| `--location` | | Location name |
| `--language` | `en` | Language code |
| `--date-from` | | Start date (YYYY-MM-DD) |
| `--date-to` | | End date (YYYY-MM-DD) |
| `--sort-by` | varies | Sort order (search_volume, relevance) |
| `--target-type` | `site` | For `for-site`: site or page |
| `--input-file` | | CSV file with keywords |
| `--input-column` | | Column name or 0-based index |

### Domain

Domain analytics including technology detection and WHOIS data.

```bash
# See what technologies a domain uses
dataforseo-cli domain technologies "example.com"

# Find domains using a specific technology
dataforseo-cli domain by-technology "WordPress"
dataforseo-cli domain by-technology --category "cms.WordPress" --limit 50

# WHOIS lookup with SEO metrics
dataforseo-cli domain whois "example.com"
dataforseo-cli domain whois "example.com" --limit 10
```

**Flags:**

| Flag | Default | Description |
|------|---------|-------------|
| `--category` | | Technology category path (e.g., "cms.WordPress") |
| `--limit` | `100` | Number of results (max 10000) |
| `--offset` | `0` | Pagination offset |
| `--order-by` | `domain_rank` | Sort: domain_rank, domain, last_visited |
| `--input-file` | | CSV file with domains |
| `--input-column` | | Column name or 0-based index |

### Locations & Languages

List available locations and languages for API queries.

```bash
# List all available locations
dataforseo-cli locations

# Search for specific locations
dataforseo-cli locations --search "United"

# List all available languages
dataforseo-cli languages

# Search for specific languages
dataforseo-cli languages --search "spanish"
```

### Version

```bash
dataforseo-cli version
```

## Input Options

### Inline Arguments

Pass keywords or domains directly as arguments:

```bash
dataforseo-cli keywords search-volume "seo tools" "keyword research" "backlinks"
```

### CSV File Input

Read values from a CSV file:

```bash
# Use first column
dataforseo-cli keywords search-volume --input-file keywords.csv

# Specify column by name (case-insensitive, skips header row)
dataforseo-cli keywords search-volume --input-file keywords.csv --input-column "keyword"

# Specify column by 0-based index
dataforseo-cli keywords search-volume --input-file keywords.csv --input-column 2
```

### Combined Input

Arguments and CSV file values are merged:

```bash
dataforseo-cli keywords search-volume "extra keyword" --input-file keywords.csv
```

## Output Formats

Control output format with the global `--format` flag:

### Table (default)

```bash
dataforseo-cli keywords search-volume "seo tools"
```

```
+------------------+---------------+-------------+------+----------+---------+
|     KEYWORD      | SEARCH_VOLUME | COMPETITION |  CPC | HIGH_BID | LOW_BID |
+------------------+---------------+-------------+------+----------+---------+
| seo tools        |         33100 |        1.00 | 7.50 |    12.00 |    3.00 |
+------------------+---------------+-------------+------+----------+---------+
API cost: $0.0500
```

### JSON

```bash
dataforseo-cli keywords search-volume "seo tools" --format json
```

Returns the full API response envelope as formatted JSON.

### CSV

```bash
dataforseo-cli keywords search-volume "seo tools" --format csv
```

```
keyword,search_volume,competition,cpc,high_bid,low_bid
seo tools,33100,1.00,7.50,12.00,3.00
```

API cost is always displayed on stderr regardless of the output format, so you can safely pipe or redirect output.

## Global Flags

| Flag | Default | Description |
|------|---------|-------------|
| `--format` | `table` | Output format: table, json, csv |
| `--force` | `false` | Skip confirmation prompts |
| `--verbose` | `false` | Enable verbose output |

## Async Workflow

For large-scale data collection, use the async task workflow to avoid timeouts:

```bash
# 1. Submit tasks
dataforseo-cli keywords task-post search-volume --input-file keywords.csv --tag "batch-2024"

# 2. Check for completed tasks
dataforseo-cli keywords tasks-ready search-volume

# 3. Retrieve results
dataforseo-cli keywords task-get <task-id>
```

Async task-post commands automatically batch keywords into groups of 100 per API request.

## Examples

### Keyword Research Pipeline

```bash
# Get search volumes and export to CSV
dataforseo-cli keywords search-volume --input-file seeds.csv --format csv > results.csv

# Find related keywords
dataforseo-cli keywords related "project management" --sort-by search_volume --format csv > related.csv

# Keywords for a competitor domain
dataforseo-cli keywords for-site "competitor.com" --format csv > competitor_keywords.csv
```

### SERP Monitoring

```bash
# Track rankings for specific keywords
dataforseo-cli serp live "your brand name" --location "United States" --format json

# Mobile vs desktop results
dataforseo-cli serp live "your keyword" --device mobile --format json
dataforseo-cli serp live "your keyword" --device desktop --format json
```

### Domain Intelligence

```bash
# Technology stack analysis
dataforseo-cli domain technologies "competitor.com" --format json

# Find WordPress sites with high domain rank
dataforseo-cli domain by-technology "WordPress" --order-by domain_rank --limit 50

# WHOIS and SEO metrics
dataforseo-cli domain whois "example.com" --format json
```

## Development

### Prerequisites

- Go 1.25+

### Build

```bash
go build -o dataforseo-cli .
```

### Test

```bash
go test ./... -v -race
```

### Lint

```bash
golangci-lint run
```

## License

MIT
