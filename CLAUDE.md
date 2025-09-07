# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an AnyRouter.top automated check-in script that supports multiple accounts and various notification methods. The project is written in Python and uses Playwright for WAF cookie handling and httpx for HTTP requests.

## Common Development Commands

### Setup and Dependencies
```bash
# Install all dependencies (including dev dependencies)
uv sync --dev

# Install Playwright browsers
playwright install chromium
```

### Running the Application
```bash
# Run check-in script with .env configuration
uv run checkin.py

# Run with specific Python version
uv run python checkin.py
```

### Testing
```bash
# Run all tests
uv run pytest tests/

# Run tests with coverage
uv run pytest tests/ --cov

# Run specific test file
uv run pytest tests/test_notify.py

# Enable real notification testing (requires .env configuration)
ENABLE_REAL_TEST=true uv run pytest tests/test_notify.py::test_real_notification
```

### Code Quality
```bash
# Format and lint code
uv run ruff format .
uv run ruff check .

# Auto-fix linting issues
uv run ruff check --fix .
```

## Architecture Overview

### Core Components

- **checkin.py**: Main script handling multi-account check-in process
  - Uses async/await for concurrent operations
  - Integrates Playwright for WAF bypass
  - Manages account configuration and validation
  
- **notify.py**: Notification system supporting multiple platforms
  - Email, DingTalk, Feishu, WeChat Work, PushPlus, Server酱
  - Unified interface via NotificationKit class
  
- **tests/**: Test suite with mocked external dependencies

### Key Functions

- `load_accounts()`: Loads and validates multi-account configuration from environment
- `get_waf_cookies_with_playwright()`: Uses Playwright to bypass WAF protection
- `check_in_account()`: Handles individual account check-in logic
- `NotificationKit.push_message()`: Sends notifications across all configured platforms

### Configuration Management

The application uses environment variables for configuration:
- `ANYROUTER_ACCOUNTS`: JSON array of account configurations
- Notification platform tokens/webhooks (optional)
- Account data format: `{"cookies": {...}, "api_user": "..."}`

### WAF Bypass Strategy

The script uses a two-step process:
1. Playwright visits login page to collect WAF cookies (`acw_tc`, `cdn_sec_tc`, `acw_sc__v2`)
2. httpx client uses combined WAF + user cookies for API requests

### GitHub Actions Integration

- Runs on Windows with Python 3.11
- Caches Playwright browsers for performance
- Scheduled every 6 hours via cron
- Uses GitHub Environment secrets for sensitive data

## Development Notes

- Code style uses tabs for indentation (configured in ruff)
- Single quotes preferred for strings
- 120 character line length limit
- Async/await patterns throughout for better performance
- Error handling with detailed logging and user feedback
- Comprehensive test coverage with mocked external dependencies