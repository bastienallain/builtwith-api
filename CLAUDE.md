<!-- OPENSPEC:START -->
# OpenSpec Instructions

These instructions are for AI assistants working in this project.

Always open `@/openspec/AGENTS.md` when the request:
- Mentions planning or proposals (words like proposal, spec, change, plan)
- Introduces new capabilities, breaking changes, architecture shifts, or big performance/security work
- Sounds ambiguous and you need the authoritative spec before coding

Use `@/openspec/AGENTS.md` to learn:
- How to create and apply change proposals
- Spec format and conventions
- Project structure and guidelines

Keep this managed block so 'openspec update' can refresh the instructions.

<!-- OPENSPEC:END -->

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an npm package that provides a JavaScript wrapper for the BuiltWith API suite. The module exports a factory function that returns an object with methods corresponding to different BuiltWith API endpoints (free, domain, lists, relationships, keywords, trends, companyToUrl, domainLive, trust).

## Testing

Run manual tests using the test file:
```bash
node test.js
```

The test.js file contains individual test functions for each API endpoint. Uncomment the specific test function at the bottom of the file to run it. Tests require a BuiltWith API key set in the `BW_API_KEY` environment variable (uses dotenv).

## Code Architecture

### Module Structure

The main module follows a factory pattern:
- `src/index.js` - Main entry point. Exports a factory function that takes an API key and optional config, returns an object with API methods
- `src/utils.js` - Helper functions for HTTP requests and query string building
- `src/config.js` - Constants for valid response types (json, xml, txt)

### Request Handling

Two types of request handlers in utils.js:
- `makeStandardRequest` - For most APIs. Handles JSON and XML responses
- `makeBulletProofRequest` - For Lists and Trends APIs. Includes try-catch for invalid JSON responses from BuiltWith, falls back to text parsing

### Response Format Configuration

Response format is set at module initialization (defaults to 'json'):
```javascript
const builtwith = BuiltWith(apiKey, { responseFormat: 'json' })
```
The format affects URL construction and response parsing. TXT format only works with Lists API.

### API Method Pattern

All API methods follow this pattern:
1. Validate input (checkUrlData for URL-based endpoints)
2. Construct URL using `constructBuiltWithURL` with API name, params, and optional subdomain
3. Make HTTP request using appropriate util function
4. Return parsed response

URL construction in constructBuiltWithURL (line 21-33 in src/index.js):
- Builds URLs with format: `https://{subdomain}.builtwith.com/{apiName}/api.{format}?KEY={key}&{params}`
- Parameters are converted to query strings via `paramsObjToQueryString`
- Most APIs use 'api' subdomain except companyToUrl which uses 'ctu'

## Dependencies

- lodash (runtime) - Used for parameter extraction with `_.get()`
- dotenv (dev) - For loading environment variables in tests
