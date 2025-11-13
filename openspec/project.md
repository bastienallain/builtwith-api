# **Project Context**

## **Purpose**

This project is an **npm package** that provides a JavaScript/Node.js wrapper for the BuiltWith API suite. It simplifies interaction with BuiltWith's various API endpoints by providing a clean, consistent interface.

Key objectives:

- Provide a simple factory function that returns an authenticated API client
- Support all major BuiltWith API endpoints (free, domain, lists, relationships, keywords, trends, companyToUrl, domainLive, trust)
- Support multiple response formats (JSON, XML, TXT)
- Handle BuiltWith API quirks (invalid JSON responses from certain endpoints)
- Provide a production-ready npm package that other developers can use

**Current Status**: v1.0.8 uses BuiltWith Free API (1 req/sec limit)
**Roadmap**: v2.x will migrate to paid BuiltWith API for higher throughput

## **Tech Stack**

- **Language**: Node.js (JavaScript, CommonJS modules)
- **Runtime**: Node.js ≥ v14 (uses native fetch API)

- **Runtime Dependencies**:
  - `lodash` (^4.17.15) - For safe parameter extraction with `_.get()`

- **Dev Dependencies**:
  - `dotenv` (^8.2.0) - For loading environment variables in test file

- **Package Distribution**:
  - Published to npm registry as `builtwith-api`
  - Entry point: `src/index.js`
  - Distributed files: `src/` directory

- **Development Environment**:
  - Git + GitHub for version control
  - No build step required (vanilla JavaScript)
  - Manual testing via test.js file

## **Project Conventions**

### **Code Style**

- JavaScript (CommonJS): 2-space indentation, variables/functions in camelCase, no semicolons
- All exported functions should have JSDoc comments with `@see` links to official API docs
- Keep error messages clear and actionable
- Use `_.get()` from lodash for safe parameter extraction with defaults
- API keys and sensitive data must use environment variables (never committed)

### **Architecture Patterns**

- **Factory Pattern**: Main module exports a factory function that takes API key and config, returns object with methods
- **Module Structure**:
  - `src/index.js` - Main entry point with factory function and all API method implementations
  - `src/utils.js` - Shared utilities for HTTP requests and query string building
  - `src/config.js` - Constants and configuration values
- **Request Handling**: Two types of request handlers
  - `makeStandardRequest` - For most APIs (handles JSON/XML)
  - `makeBulletProofRequest` - For Lists/Trends APIs (handles BuiltWith's invalid JSON responses with fallback)
- **URL Construction**: Centralized `constructBuiltWithURL` function builds API URLs with format: `https://{subdomain}.builtwith.com/{apiName}/api.{format}?KEY={key}&{params}`
- **Input Validation**: `checkUrlData` validates URL inputs before making requests (checks array size limits, multi-domain support)
- **Response Format**: Set once at initialization, affects both URL construction and response parsing

### **Testing Strategy**

- Manual testing via `test.js` file with individual test functions for each endpoint
- Test functions are commented out - uncomment specific test to run it
- Tests require `BW_API_KEY` environment variable set (loaded via dotenv)
- Run tests: `node test.js`
- Each test function exercises one API endpoint and logs JSON output
- No automated test framework currently in place

### **Git Workflow**

- Main branch: `master` (stable production version)
- Feature branches: `feature/<feature-name>` for new developments
- Bugfix branches: `bugfix/<issue-description>` for corrections
- Version tagging: Semantic versioning (current: v1.0.8)
- Repository: https://github.com/zcaceres/builtwith-api

## **Domain Context**

- **BuiltWith API**: Third-party service that provides technology profiling and web analytics data
- **API Endpoints Currently Implemented** (9 of 15+ available):
  - `free` - Free API for basic technology lookup (free1 endpoint, rate limited to 1 req/sec)
  - `domain` - Domain API for detailed technology stack analysis (v15 endpoint, latest is v22)
  - `lists` - Lists API for finding sites using specific technologies (lists5 endpoint, latest is lists12)
  - `relationships` - Relationships API for related domains (rv1 endpoint, latest is rv4)
  - `keywords` - Keywords API for domain keyword analysis (kw2 endpoint)
  - `trends` - Trends API for technology usage trends over time (trends/v6 endpoint)
  - `companyToUrl` - Company to URL mapping (ctu1 endpoint, latest is ctu3)
  - `domainLive` - Live domain lookup (ddlv2 endpoint)
  - `trust` - Trust/security analysis (trustv1 endpoint)
- **API Endpoints NOT Yet Implemented** (documented in `/docs/BuiltWith API/`):
  - Financial API (financial1)
  - Tags API (tag1)
  - Recommendations API (rec1)
  - Social API (social1)
  - Redirects API (redirect1)
  - Product API (productv1)
  - Bulk Domain API
- **Response Formats**: JSON (default), XML, TXT (Lists API only), CSV/TSV (some endpoints)
- **Multi-domain Support**: Most endpoints support array of URLs (max 16 domains per request)
- **URL Construction**: Different endpoints use different subdomains (most use 'api', companyToUrl uses 'ctu')
- **API Versioning**: Many endpoints in wrapper use older versions than current (see above list)

## **Important Constraints**

- **BuiltWith API Quirks**: Lists and Trends APIs sometimes return invalid JSON that breaks standard parsing - this is why `makeBulletProofRequest` exists with fallback to text parsing
- **API Key Required**: All requests require a valid BuiltWith API key passed to factory function
- **Rate Limits**:
  - **Current (v1.x)**: Using Free API with 1 request per second limit (enforced by BuiltWith)
  - **Future (v2.x)**: Will migrate to paid API with higher rate limits
  - Lists API: Credit-based usage system
  - Relationships API: 1 API credit per 500 relationships
  - Note: Rate limiting is not currently enforced in the wrapper code itself
- **Multi-domain Limits**: Maximum 16 domains per request for multi-domain endpoints
- **TXT Format**: Only supported by Lists API - warning shown if used with other endpoints
- **Node.js Version**: Requires Node.js with native fetch support (v18+)
- **Known Bugs**:
  - **Company Name Parameter Bug**: In `companyToUrl` method (line 180 in src/index.js), `amount` parameter incorrectly reads from `noMetaData` instead of `amount`
- **Version Lag**: Many endpoints use older API versions (e.g., lists5 vs lists12, v15 vs v22, rv1 vs rv4) - may be missing newer features or data

## **External Dependencies**

- **BuiltWith API**: All API endpoints depend on BuiltWith service availability and authentication
- **Official API Documentation** (saved locally in `/docs/BuiltWith API/`):
  - Main overview: https://api.builtwith.com/
  - Free API: https://api.builtwith.com/free-api
  - Domain API: https://api.builtwith.com/domain-api
  - Lists API: https://api.builtwith.com/lists-api
  - Relationships API: https://api.builtwith.com/relationships-api
  - Keywords API: https://api.builtwith.com/keywords-api
  - Trends API: https://api.builtwith.com/trends-api
  - Trust API: https://api.builtwith.com/trust-api
  - Company to URL: https://api.builtwith.com/company-to-url
  - Error Codes: https://api.builtwith.com/errorCodes
- **Package References**: This package is referenced in official BuiltWith documentation as a recommended Node.js client library
