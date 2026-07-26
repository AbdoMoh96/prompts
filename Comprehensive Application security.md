# Comprehensive Application Security Audit

Act as a senior application security engineer and perform a thorough security audit of this codebase.

The application may use one or more of these technologies:

* Next.js
* React
* Node.js
* NestJS
* ASP.NET Core / .NET
* Laravel / PHP
* REST APIs
* GraphQL
* WebSockets
* SQL or NoSQL databases
* Docker
* CI/CD pipelines
* Cloud services

Your objective is to identify real, exploitable security weaknesses, explain their impact, and implement safe fixes where possible.

Do not perform cosmetic refactoring or unrelated architecture changes.

## 1. Initial Repository Analysis

First, inspect the repository and determine:

* Primary programming languages
* Frameworks and framework versions
* Package managers
* Application entry points
* Frontend applications
* Backend services
* API endpoints
* Authentication mechanisms
* Authorization model
* Database technology
* ORM or query builder
* File upload functionality
* External integrations
* Background jobs
* WebSocket services
* Docker and deployment configuration
* CI/CD workflows
* Environment-variable handling
* Logging and monitoring configuration

Create a concise security-relevant architecture summary before modifying code.

## 2. Audit Methodology

Review the application using:

* OWASP Top 10
* OWASP API Security Top 10
* OWASP ASVS principles
* CWE vulnerability categories
* Framework-specific security best practices
* Secure-by-default configuration principles

Prioritize vulnerabilities that are realistically exploitable.

Do not report theoretical issues without showing the vulnerable code path, unsafe configuration, or credible attack scenario.

## 3. Authentication Review

Inspect all authentication-related code.

Check for:

* Missing authentication on protected routes
* Authentication bypasses
* Weak password policies
* Plaintext or weakly hashed passwords
* Incorrect bcrypt, Argon2, PBKDF2, or password-hashing configuration
* User enumeration
* Brute-force vulnerabilities
* Missing login rate limiting
* Missing account lockout or abuse controls
* Insecure password reset flows
* Predictable password-reset tokens
* Password-reset token reuse
* Missing token expiration
* Incorrect token invalidation
* Session fixation
* Insecure persistent sessions
* Missing logout invalidation
* JWT signature validation weaknesses
* Accepting unsigned JWTs
* Weak JWT secrets
* Missing JWT issuer, audience, or expiration checks
* Sensitive data stored inside JWTs
* Refresh-token reuse
* Refresh tokens stored insecurely
* OAuth or OpenID Connect misconfiguration
* Missing state or nonce validation
* Unsafe social-login account linking
* MFA bypasses

Verify that protected backend resources enforce authentication server-side. Do not consider frontend route guards sufficient.

## 4. Authorization and Access Control

Review every API endpoint, server action, controller, handler, resolver, hub, WebSocket event, background task, and administrative operation.

Check for:

* Missing authorization
* Broken role-based access control
* Broken permission checks
* Privilege escalation
* Horizontal privilege escalation
* Vertical privilege escalation
* Insecure direct object references
* Broken object-level authorization
* Broken function-level authorization
* Mass assignment
* Over-posting
* Tenant isolation failures
* Users accessing another company’s data
* Users changing ownership-related fields
* Client-controlled role, permission, tenant, owner, account, or user IDs
* Admin-only functions exposed to normal users
* Authorization enforced only in the UI

For each sensitive operation, trace:

1. The user-controlled input
2. The authenticated identity
3. The authorization decision
4. The database query
5. The returned or modified data

Confirm that authorization is checked before accessing or modifying the resource.

## 5. Multi-Tenant Security

If the application supports multiple companies, organizations, workspaces, accounts, or tenants, perform a dedicated tenant-isolation review.

Check for:

* Queries missing tenant filters
* Tenant IDs accepted directly from the client
* Tenant switching without authorization
* Cross-tenant IDOR vulnerabilities
* Shared caches without tenant-aware keys
* Shared file paths or object-storage keys
* Background jobs running without tenant context
* WebSocket messages leaking across tenants
* Search endpoints returning cross-tenant results
* Reports or exports containing another tenant’s data
* Administrative endpoints bypassing tenant checks

Tenant identity should normally be derived from the authenticated user or trusted server-side context, not accepted blindly from request input.

## 6. Input Validation and Injection

Trace all untrusted input from:

* Query parameters
* Route parameters
* Request bodies
* HTTP headers
* Cookies
* Forms
* File uploads
* GraphQL arguments
* WebSocket messages
* CLI arguments
* Message queues
* Webhooks
* External APIs

Check for:

* SQL injection
* NoSQL injection
* Command injection
* OS command execution
* LDAP injection
* XPath injection
* Template injection
* Expression-language injection
* Server-side JavaScript injection
* Header injection
* HTTP response splitting
* Log injection
* CSV injection
* CRLF injection
* Regular-expression denial of service
* Unsafe dynamic query construction
* Unsafe raw SQL
* Unsafe ORM escape hatches
* Unsafe shell execution
* Unsafe deserialization

Prefer:

* Parameterized queries
* ORM-safe query methods
* Allowlist validation
* Typed request models
* Framework validation pipelines
* Strict schemas
* Safe command APIs that avoid shell interpretation

Do not rely only on frontend validation.

## 7. Cross-Site Scripting

Check frontend and server-rendered code for:

* Reflected XSS
* Stored XSS
* DOM-based XSS
* Unsafe HTML rendering
* Unsafe markdown rendering
* Unsafe rich-text rendering
* React `dangerouslySetInnerHTML`
* Vue-style raw HTML equivalents, if present
* Next.js server-rendered injection
* Unescaped template output
* JavaScript URLs
* User-controlled inline styles
* Unsafe URL handling
* Third-party HTML sanitizers configured incorrectly

When HTML rendering is required, verify that trusted sanitization is applied with an appropriate allowlist.

Do not recommend sanitization where safe text rendering is sufficient.

## 8. CSRF and Browser Security

Check state-changing browser-accessible endpoints for CSRF protection.

Review:

* Cookie-based authentication
* SameSite cookie settings
* CSRF tokens
* Origin validation
* Referer validation
* CORS policy
* Credentialed cross-origin requests
* Server actions
* Form submissions
* Admin actions
* Logout endpoints
* Password or email changes

Check security headers, including:

* Content-Security-Policy
* Strict-Transport-Security
* X-Content-Type-Options
* Referrer-Policy
* Permissions-Policy
* Frame-ancestors or X-Frame-Options
* Cross-Origin-Opener-Policy
* Cross-Origin-Resource-Policy
* Cross-Origin-Embedder-Policy, where appropriate

Avoid recommending permissive wildcard CORS rules for authenticated APIs.

## 9. Cookies and Session Security

Verify that sensitive cookies use appropriate settings:

* `HttpOnly`
* `Secure`
* `SameSite`
* Restricted `Path`
* Restricted `Domain`
* Reasonable expiration
* Server-side invalidation when required

Check whether:

* Authentication tokens are unnecessarily stored in `localStorage`
* Sensitive tokens are exposed to JavaScript
* Session IDs rotate after authentication
* Session IDs rotate after privilege changes
* Sessions can be revoked
* Concurrent-session handling is appropriate
* Production cookies are accidentally configured for HTTP

## 10. Sensitive Data and Secrets

Search for:

* API keys
* Database passwords
* JWT secrets
* OAuth client secrets
* Private keys
* Cloud credentials
* SMTP credentials
* Payment credentials
* Encryption keys
* Hardcoded tokens
* Secrets committed in Git history
* Secrets in Docker files
* Secrets in CI/CD workflows
* Secrets exposed to frontend bundles
* Secrets logged during startup
* `.env` files committed to source control

Distinguish between:

* Public configuration
* Server-only secrets
* Build-time variables
* Runtime variables
* Frontend-exposed variables

For Next.js, inspect variables prefixed with `NEXT_PUBLIC_`.

For React build systems, inspect public environment-variable prefixes such as `VITE_` or `REACT_APP_`.

Report any secret that can be included in client-side JavaScript.

Do not print full secret values in the report. Redact them.

## 11. Cryptography

Review cryptographic usage for:

* Weak hashing algorithms
* MD5 or SHA-1 used for passwords or security decisions
* Hardcoded encryption keys
* Static initialization vectors
* ECB mode
* Predictable random values
* Non-cryptographic RNG used for tokens
* Incorrect password hashing
* Encryption without authentication
* Incorrect key storage
* Reused nonces
* Custom cryptographic implementations
* Insecure token generation

Use platform-provided cryptographically secure random-number generators.

Do not create custom cryptographic algorithms.

## 12. File Upload and File Handling

If the application accepts files, inspect:

* File size limits
* File-count limits
* MIME type validation
* Extension validation
* Magic-byte validation
* Filename sanitization
* Path traversal
* Archive extraction
* Zip-slip vulnerabilities
* Executable file uploads
* SVG uploads
* HTML uploads
* Public storage exposure
* Predictable storage names
* Overwriting existing files
* Malware-scanning hooks
* Image-processing vulnerabilities
* Server-side request forgery through remote file URLs

Uploaded files should normally be renamed server-side and stored outside executable application directories.

Check download endpoints for IDOR and path traversal.

## 13. Server-Side Request Forgery

Find all features that make server-side requests using user-controlled input.

Examples:

* URL previews
* Image imports
* Webhooks
* PDF generation
* Remote file downloads
* Avatar imports
* Feed readers
* Proxy endpoints
* Callback testing
* Integration setup
* URL validation

Check for access to:

* `localhost`
* Loopback addresses
* Private network ranges
* Link-local addresses
* Cloud metadata services
* IPv6 local addresses
* Redirect chains
* Alternate IP formats
* DNS rebinding scenarios

Recommend allowlists where the business case permits.

## 14. API Security

Review all APIs for:

* Missing authentication
* Missing authorization
* Excessive data exposure
* Mass assignment
* Missing pagination
* Unbounded queries
* Missing rate limiting
* Resource exhaustion
* Weak filtering
* Unsafe sorting fields
* Information leakage
* Verbose errors
* Missing request-size limits
* Missing content-type validation
* Deprecated API versions
* Undocumented privileged endpoints
* Debug endpoints
* Health endpoints exposing sensitive data

Review whether DTOs, resources, serializers, or response models expose fields such as:

* Password hashes
* Reset tokens
* Internal IDs
* Access tokens
* Refresh tokens
* Private notes
* Permission internals
* Audit metadata
* Payment information
* Personal data

## 15. Rate Limiting and Abuse Prevention

Identify security-sensitive or expensive endpoints that require throttling.

Examples:

* Login
* Registration
* Password reset
* OTP verification
* Email verification
* MFA
* Search
* Exports
* File uploads
* Contact forms
* Payment endpoints
* Invitation endpoints
* Webhook testing
* Report generation
* AI endpoints
* SMS or email delivery
* Resource-intensive queries

Check whether rate limits can be bypassed through:

* Spoofed proxy headers
* Rotating identifiers
* Missing authenticated-user limits
* Distributed deployments with in-memory limits
* IPv6 address variation

## 16. Error Handling and Information Disclosure

Inspect error responses, exception handling, and logging.

Check for exposure of:

* Stack traces
* SQL statements
* Connection strings
* File-system paths
* Framework versions
* Internal service names
* Environment variables
* Tokens
* Personal data
* Passwords
* Request bodies
* Authentication headers
* Payment data

Ensure production errors are generic for clients while retaining appropriate server-side observability.

## 17. Logging and Auditability

Review whether security-sensitive actions are logged appropriately.

Examples:

* Login successes and failures
* Password changes
* Email changes
* MFA changes
* Role and permission changes
* Administrative actions
* Tenant changes
* Data exports
* Account deletion
* Payment operations
* API-key creation or revocation
* Suspicious authorization failures

Check that logs do not contain secrets or sensitive personal data.

Review log-injection risk where user-controlled values are logged.

## 18. Dependency and Supply-Chain Security

Inspect dependency files and lock files.

Examples:

* `package.json`
* `package-lock.json`
* `pnpm-lock.yaml`
* `yarn.lock`
* `bun.lock`
* `.csproj`
* `packages.lock.json`
* `composer.json`
* `composer.lock`

Check for:

* Known vulnerable dependencies
* Unmaintained packages
* Deprecated packages
* Typosquatting risks
* Git-based dependencies
* Unpinned dependencies
* Missing lock files
* Dependency confusion risks
* Unsafe install scripts
* Packages executing post-install scripts
* Duplicate security-sensitive libraries
* Framework versions outside supported ranges

Use available package-manager audit tools where possible, but verify whether findings are reachable and relevant.

Do not blindly upgrade major versions. Explain compatibility risks first.

## 19. Next.js-Specific Review

If this is a Next.js application, inspect:

* Server Components versus Client Components
* Secrets imported into Client Components
* Server Actions
* API route handlers
* Middleware
* Route protection
* Cache behavior
* Static generation
* Incremental static regeneration
* User-specific data accidentally cached globally
* Authorization inside server actions
* Request headers trusted from the client
* Unsafe redirects
* Open redirects
* Image optimization configuration
* Remote image patterns
* React hydration injection
* Public environment variables
* Edge runtime limitations
* Revalidation endpoints
* Draft or preview mode
* Internal API endpoints called from the browser
* Sensitive source maps
* Custom headers
* `next.config` security settings

Pay particular attention to user-specific or tenant-specific data cached through shared server caches.

## 20. React-Specific Review

If this is a React frontend, inspect:

* Client-side route guards treated as security controls
* Tokens stored in `localStorage`
* Sensitive data embedded in bundles
* Insecure API base URLs
* XSS sinks
* Unsafe HTML rendering
* Unsafe URL handling
* Exposed source maps
* Debug functionality enabled in production
* Client-controlled roles or permissions
* Hidden buttons used instead of backend authorization
* Sensitive Redux, Zustand, Context, or query cache state
* Persisted state containing tokens or personal data

Clearly distinguish frontend hardening from actual backend security enforcement.

## 21. Node.js and NestJS-Specific Review

If this is a Node.js or NestJS backend, inspect:

* Global validation pipes
* DTO validation
* `whitelist`
* `forbidNonWhitelisted`
* Type transformation
* Guards
* Interceptors
* Middleware order
* Exception filters
* Passport strategies
* JWT configuration
* CORS configuration
* Helmet configuration
* Body-size limits
* Prototype pollution
* Unsafe object merging
* Dynamic `require`
* `eval`
* `Function`
* `child_process`
* Unsafe serialization
* ORM raw queries
* Swagger exposure in production
* GraphQL introspection in production
* WebSocket gateway authorization
* Background job authorization context

Check whether validation decorators are actually executed and whether plain JavaScript objects can bypass expected validation.

## 22. ASP.NET Core / .NET-Specific Review

If this is an ASP.NET Core application, inspect:

* `[Authorize]` and `[AllowAnonymous]`
* Authorization policies
* Claims validation
* Resource-based authorization
* Controller and minimal API protection
* Model binding
* Over-posting
* DTO usage
* Entity Framework raw SQL
* LINQ query filtering
* Data Protection configuration
* Cookie authentication settings
* JWT bearer configuration
* CORS
* Antiforgery protection
* Forwarded headers
* Proxy trust
* Kestrel limits
* Exception handling
* Developer exception pages in production
* Swagger exposure
* Health-check information exposure
* SignalR hub authorization
* `IFormFile` validation
* Background services
* Mass-assignment of entity models
* Unsafe JSON polymorphic deserialization

Confirm that authorization exists on minimal API route groups as well as individual routes.

## 23. Laravel-Specific Review

If this is a Laravel application, inspect:

* Middleware assignments
* Authentication guards
* Authorization gates and policies
* Route-model binding
* Form Request validation
* Eloquent mass assignment
* `$fillable`
* `$guarded`
* Raw database queries
* Blade escaping
* `{!! !!}` output
* CSRF exclusions
* Signed URLs
* Storage configuration
* Public disk exposure
* File upload validation
* Queue jobs
* Events and listeners
* Broadcasting channel authorization
* Sanctum or Passport configuration
* Debug mode
* Telescope exposure
* Horizon exposure
* Ignition exposure
* Trusted proxies
* CORS
* APP_KEY handling
* Serialization of models
* Hidden model attributes
* Rate limiting
* Password reset configuration

Check for unsafe use of `Model::unguard()` and broadly writable model fields.

## 24. Database Security

Review database access and schema usage for:

* SQL injection
* Missing tenant constraints
* Missing ownership checks
* Excessive database privileges
* Application accounts with administrative privileges
* Sensitive data stored unencrypted
* Passwords stored incorrectly
* Reset tokens stored in plaintext
* API keys stored in plaintext
* Missing unique constraints for security assumptions
* Race conditions
* Duplicate redemption or transaction processing
* Unsafe soft-delete behavior
* Deleted records remaining accessible
* Insecure backups or dump files committed to the repository

Identify operations that require transactions, row locking, optimistic concurrency, or idempotency.

## 25. Business Logic and Race Conditions

Do not limit the audit to common scanner findings.

Review business logic for:

* Reusing invitations
* Reusing coupons
* Reusing reset tokens
* Duplicate payment processing
* Negative quantities
* Negative balances
* Price manipulation
* Client-controlled totals
* Client-controlled discounts
* Client-controlled subscription levels
* Skipping workflow stages
* Approving one’s own request
* Replaying requests
* Double submission
* Race conditions
* Missing idempotency
* Changing records after approval
* Bypassing limits through concurrent requests
* Inconsistent state transitions

Trace important financial, permission, approval, inventory, subscription, and account-management workflows.

## 26. Webhook Security

If webhooks are used, check:

* Signature verification
* Timestamp validation
* Replay protection
* Constant-time signature comparison
* Secret rotation
* Raw-body handling
* Event-type allowlists
* Idempotency
* Duplicate-event handling
* Untrusted payload validation
* Logging of webhook secrets
* Test endpoints exposed publicly

Never trust webhook data solely because it came from a known URL.

## 27. WebSocket and Real-Time Security

If the application uses WebSockets, Socket.IO, SignalR, or NestJS gateways, inspect:

* Authentication during connection
* Token expiration after connection
* Authorization for every event
* Room or channel authorization
* Tenant isolation
* Subscription authorization
* Reconnection behavior
* Message-size limits
* Event rate limiting
* User-controlled room names
* Broadcast data leakage
* Origin validation
* Sensitive data in connection query strings

Connection authentication alone is not enough. Verify authorization for each sensitive action and subscription.

## 28. Docker and Deployment Security

Inspect:

* Dockerfiles
* Docker Compose files
* Kubernetes manifests
* Reverse-proxy configuration
* Environment files
* Startup scripts

Check for:

* Running as root
* Secrets embedded in images
* Secrets passed through build arguments
* Large or unnecessary base images
* Unpinned base images
* Debug ports exposed
* Database ports publicly exposed
* Development servers used in production
* Source code unintentionally copied
* `.env` files copied into images
* Writable application directories
* Excessive Linux capabilities
* Privileged containers
* Docker socket exposure
* Missing health checks
* Insecure TLS termination
* Unsafe proxy-header trust
* Insecure default credentials

## 29. CI/CD Security

Inspect GitHub Actions, Azure DevOps, GitLab CI, Jenkins, or other pipelines.

Check for:

* Secrets printed to logs
* Pull requests accessing privileged secrets
* Untrusted code executed with write permissions
* Actions referenced by mutable tags
* Excessive workflow permissions
* Artifact poisoning
* Cache poisoning
* Unsafe shell interpolation
* Deployment from untrusted branches
* Missing environment approvals
* Long-lived cloud credentials
* Production credentials available to test jobs
* Dependency installation without lock files
* Disabled security checks
* Build artifacts containing `.env` files or secrets

Use least-privilege permissions for workflow tokens.

## 30. Security Configuration and Production Hardening

Check for development-only functionality enabled in production:

* Debug mode
* Verbose errors
* Swagger
* GraphQL Playground
* GraphQL introspection
* Telescope
* Horizon
* Development exception pages
* Mock authentication
* Test accounts
* Seeded default passwords
* Open database consoles
* Source maps
* Hot-reload servers
* Unprotected metrics
* Unprotected health endpoints

Check whether production and development configurations are clearly separated.

## 31. Testing Requirements

Where practical, add or update security-focused tests for confirmed vulnerabilities.

Examples:

* Unauthenticated requests are rejected
* Normal users cannot access admin endpoints
* One tenant cannot access another tenant’s records
* Users cannot modify another user’s records
* Invalid DTO fields are rejected
* Mass-assignment fields are ignored or rejected
* Malicious HTML is safely rendered
* CSRF protection works
* Rate limits are enforced
* Expired or malformed tokens are rejected
* Webhook replay is rejected
* Duplicate financial operations are idempotent
* File path traversal attempts fail
* Unsafe upload types are rejected

Tests must prove the security boundary, not merely increase coverage.

## 32. Safe Fixing Rules

You may automatically fix vulnerabilities when:

* The intended behavior is clear
* The fix is localized
* The fix does not introduce a breaking API change
* The fix can be validated with tests or static analysis
* The fix follows the framework’s recommended security mechanism

Before making broad or potentially breaking changes, document:

* The vulnerability
* The proposed fix
* Compatibility impact
* Required migration
* Deployment implications

Avoid:

* Disabling functionality without explanation
* Replacing authentication systems unnecessarily
* Large dependency upgrades without compatibility analysis
* Suppressing scanner warnings without understanding them
* Adding custom security frameworks when the existing framework already provides the required control

## 33. Required Output Format

Create a security report named:

`SECURITY_AUDIT.md`

Use this structure:

# Security Audit Report

## Executive Summary

Include:

* Overall security posture
* Number of findings by severity
* Most important risks
* Whether the application should currently be considered safe for production

## Application Architecture

Summarize:

* Frameworks
* Services
* Authentication
* Authorization
* Data stores
* External integrations
* Deployment model
* Important trust boundaries

## Findings Summary

Use a table with:

| ID | Severity | Category | Location | Status |
| -- | -------- | -------- | -------- | ------ |

Severity levels:

* Critical
* High
* Medium
* Low
* Informational

Status values:

* Confirmed
* Fixed
* Partially Fixed
* Requires Decision
* False Positive

## Detailed Findings

For each finding, use:

### SEC-001: Finding title

**Severity:**
**Confidence:** High, Medium, or Low
**Category:**
**CWE:**
**OWASP mapping:**
**Affected files:**
**Affected endpoints:**
**Status:**

#### Description

Explain the weakness clearly.

#### Evidence

Show the relevant code path, configuration, or data flow.

Do not expose complete secrets.

#### Exploitation Scenario

Describe a realistic attack scenario.

#### Impact

Explain the technical and business impact.

#### Recommended Fix

Provide a concrete remediation.

#### Applied Fix

Describe any change you implemented.

#### Verification

Explain how the fix was tested or how it should be tested.

## Dependency Findings

List vulnerable or outdated dependencies separately.

Include:

* Package name
* Installed version
* Affected component
* Vulnerability
* Reachability
* Recommended version
* Upgrade risk

## Security Hardening Recommendations

Separate confirmed vulnerabilities from optional defense-in-depth improvements.

## Remaining Risks

List risks that could not be fixed automatically.

## Files Changed

List every modified file and why it was changed.

## Verification Commands

Provide exact commands for:

* Tests
* Type checking
* Linting
* Build
* Dependency audit
* Security checks

## 34. Final Console Summary

At the end of your work, provide a concise console summary containing:

* Frameworks detected
* Critical findings
* High findings
* Medium findings
* Low findings
* Findings fixed
* Findings requiring manual decisions
* Tests added
* Tests passed
* Tests failed
* Build status
* Location of `SECURITY_AUDIT.md`

## 35. Execution Process

Follow this sequence:

1. Inspect repository structure.
2. Identify frameworks and trust boundaries.
3. Review authentication and authorization.
4. Review all externally reachable endpoints.
5. Trace untrusted input into sensitive operations.
6. Review database queries and tenant isolation.
7. Review frontend security.
8. Review secrets and environment configuration.
9. Review dependencies.
10. Review Docker and CI/CD configuration.
11. Review business logic and race conditions.
12. Confirm findings with code evidence.
13. Fix safe, high-confidence vulnerabilities.
14. Add security regression tests.
15. Run tests, linting, type checks, and builds.
16. Generate `SECURITY_AUDIT.md`.
17. Present the final security summary.

## 36. Important Constraints

* Do not claim a vulnerability without evidence.
* Do not claim an issue is fixed unless validation succeeds.
* Do not expose secret values.
* Do not delete user data.
* Do not run destructive database commands.
* Do not contact external systems.
* Do not perform active exploitation against production services.
* Do not change production credentials.
* Do not weaken security controls to make tests pass.
* Do not treat frontend checks as authorization.
* Do not ignore tenant isolation.
* Do not modify unrelated code.
* Clearly label anything that could not be verified.
* Clearly distinguish confirmed vulnerabilities from defense-in-depth recommendations.

Begin by analyzing the repository and documenting its security-relevant architecture, trust boundaries, frameworks, authentication model, authorization model, data stores, external integrations, and deployment configuration.

Then create the following directory at the repository root:

```text
cyber_security_report/
```

Store the complete audit inside this directory using well-organized Markdown files. Do not place the entire report in a single file.

Use a structure similar to:

```text
cyber_security_report/
├── README.md
├── executive-summary.md
├── architecture-and-trust-boundaries.md
├── findings-summary.md
├── authentication-and-authorization.md
├── api-security.md
├── input-validation-and-injection.md
├── frontend-security.md
├── data-and-tenant-isolation.md
├── dependencies-and-supply-chain.md
├── infrastructure-and-deployment.md
├── business-logic-and-race-conditions.md
├── verification-results.md
└── fixes/
    ├── README.md
    ├── SEC-001-finding-name.md
    ├── SEC-002-finding-name.md
    └── ...
```

The root `cyber_security_report/README.md` must act as the main index and include:

* Overall security posture
* Findings grouped by severity
* Links to every report file
* Links to every required fix
* Number of confirmed, fixed, partially fixed, and unresolved findings
* Test, build, lint, and dependency-audit status
* Clear production-readiness conclusion

Create one Markdown file inside `cyber_security_report/fixes/` for every confirmed vulnerability or required security change.

Each fix file must include:

````markdown
# SEC-XXX: Finding Title

## Severity

## Status

## Affected Components

## Affected Files and Locations

- `path/to/file.ext:line`
- `path/to/another-file.ext:line`

## Vulnerable Code

```language
// Relevant vulnerable code snippet
````

## Security Risk

## Exploitation Scenario

## Required Fix

## Recommended Code Changes

```language
// Secure replacement or patch example
```

## Implementation Steps

## Compatibility or Migration Impact

## Verification Steps

## Recommended Tests

## Related Finding

Link back to the corresponding audit finding.

```

Code snippets must be concise, directly relevant, and include the exact file path and approximate line location whenever possible.

For every finding:

- Use a stable identifier such as `SEC-001`.
- Use the same identifier in the findings summary, detailed report, and corresponding fix file.
- Clearly distinguish confirmed vulnerabilities from potential risks and defense-in-depth recommendations.
- Do not create a fix file for unsupported or speculative findings unless it is explicitly marked as requiring further investigation.
- Redact secrets and sensitive values from all reports and code snippets.
- Include links between related Markdown files using relative paths.

After generating the report:

1. Apply only safe, high-confidence fixes whose intended behavior is clear.
2. Document every changed source file.
3. Add security regression tests where practical.
4. Run the available tests, linting, type checks, builds, and dependency audits.
5. Record the exact commands and results in `cyber_security_report/verification-results.md`.
6. Mark each finding as `Confirmed`, `Fixed`, `Partially Fixed`, `Requires Decision`, `Needs Investigation`, or `False Positive`.
7. Do not claim that a finding is fixed unless the relevant validation succeeds.

At the end, print a concise console summary containing:

- Frameworks detected
- Report directory location
- Total findings by severity
- Findings fixed
- Findings still requiring action
- Tests added
- Tests passed and failed
- Build status
- Dependency-audit status
- Production-readiness conclusion

The final output must leave the repository with a complete, navigable security audit under `cyber_security_report/`, with actionable remediation documents under `cyber_security_report/fixes/`.
```

