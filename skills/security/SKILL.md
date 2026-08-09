# Security

## Purpose

Provide practical security guidance for software development, code review, architecture, testing, and dependency management.

Security should be considered throughout the software development lifecycle rather than treated as a final review step.

Use established security principles and standards, with OWASP Top 10:2025 as a primary reference for common web application risks.

Do not invent security mechanisms when well-established standards, libraries, or platform capabilities exist.

---

# Core Security Principles

## Security Is a System Property

Security cannot be guaranteed by a single frontend component, validation function, middleware, or library.

Evaluate security across:

* Client.
* Server.
* APIs.
* Authentication.
* Authorization.
* Data storage.
* Dependencies.
* Infrastructure.
* CI/CD.
* Configuration.
* Logging.
* External integrations.

A security control is only meaningful when applied at a trust boundary that an attacker cannot bypass.

---

## Never Trust the Client

Treat all client-controlled data as untrusted.

This includes:

* Request parameters.
* Query strings.
* Request bodies.
* Headers.
* Cookies.
* Local storage.
* Session storage.
* Hidden form fields.
* URL parameters.
* Client-side state.
* JWT claims received from the client.
* Browser-controlled feature flags.

Client-side validation improves user experience but does not provide security.

Authorization decisions for protected resources must be enforced server-side.

---

# Threat Modeling

When implementing security-sensitive functionality, identify:

1. Assets.
2. Actors.
3. Trust boundaries.
4. Entry points.
5. Sensitive operations.
6. Authentication requirements.
7. Authorization requirements.
8. Potential abuse cases.
9. Security controls.
10. Failure behavior.

Ask:

```text
What can the attacker control?
What can the attacker observe?
What can the attacker modify?
What can the attacker invoke?
What happens if a security check is bypassed?
```

Do not perform elaborate threat modeling for trivial changes.

Use deeper analysis when the change affects authentication, authorization, sensitive data, privileged operations, external integrations, or infrastructure.

---

# OWASP Top 10:2025

Use the OWASP Top 10:2025 categories as a security review framework:

```text
A01 Broken Access Control
A02 Security Misconfiguration
A03 Software Supply Chain Failures
A04 Cryptographic Failures
A05 Injection
A06 Insecure Design
A07 Authentication Failures
A08 Software or Data Integrity Failures
A09 Security Logging and Alerting Failures
A10 Mishandling of Exceptional Conditions
```

Do not treat this list as an exhaustive security standard.

A vulnerability does not need to fit neatly into one category before it should be addressed.

---

# A01 — Broken Access Control

Access control determines what an authenticated or unauthenticated actor is allowed to do.

Use deny-by-default principles for protected resources.

Verify authorization for:

* Reading resources.
* Creating resources.
* Updating resources.
* Deleting resources.
* Executing privileged operations.
* Accessing administrative functions.
* Accessing another user's resources.

Consider:

* Horizontal privilege escalation.
* Vertical privilege escalation.
* IDOR / BOLA.
* Force browsing.
* Parameter tampering.
* Missing API authorization.
* Unauthorized resource ownership changes.
* Privilege escalation.
* CORS-related access control mistakes.

Never rely exclusively on frontend routing or conditional rendering to protect privileged functionality.

Example:

```text
Frontend:
if (user.isAdmin) {
    showAdminButton();
}
```

does not provide authorization.

The backend must independently enforce the permission.

---

# Resource Ownership

When users access resources by identifier, verify ownership or authorization.

Do not assume that possession of an ID grants permission to access the corresponding resource.

For example:

```text
GET /api/users/123/orders/456
```

must verify that the requesting actor is allowed to access order `456`.

Changing:

```text
456 → 457
```

must not expose another user's data.

---

# A02 — Security Misconfiguration

Review security-sensitive configuration for:

* Debug mode in production.
* Default credentials.
* Excessive permissions.
* Unnecessary services.
* Open administrative endpoints.
* Verbose error responses.
* Directory listings.
* Exposed source maps where inappropriate.
* Exposed configuration files.
* Insecure CORS configuration.
* Missing security headers.
* Insecure cookie settings.
* Weak TLS configuration.
* Unnecessary HTTP methods.
* Development endpoints exposed in production.

Prefer secure defaults.

Do not expose internal implementation details unnecessarily.

---

# A03 — Software Supply Chain Failures

Treat dependencies and development tooling as part of the application's security boundary.

Consider:

* Direct dependencies.
* Transitive dependencies.
* Build tools.
* Plugins.
* IDE extensions.
* CI/CD actions.
* Container images.
* Package registries.
* Artifact repositories.
* Infrastructure-as-code dependencies.

Before adding a dependency:

1. Determine whether it is necessary.
2. Check whether equivalent functionality already exists.
3. Verify the source.
4. Consider maintenance status.
5. Consider known vulnerabilities.
6. Consider permissions and execution behavior.
7. Consider package integrity.

Keep dependencies reasonably current according to risk and compatibility.

Remove unused dependencies.

Do not blindly run dependency upgrades across a large project without reviewing the resulting changes.

---

# Dependency Integrity

Prefer trusted package sources.

When supported by the ecosystem, consider:

* Lockfiles.
* Dependency pinning.
* Integrity hashes.
* Signed packages.
* SBOM generation.
* Vulnerability scanning.
* Automated security advisories.

Treat package lifecycle scripts and build-time code as potentially privileged code.

Do not assume development dependencies are harmless.

A compromised build tool can compromise the resulting application or developer environment.

---

# CI/CD Security

Consider CI/CD systems part of the security boundary.

Protect:

* Repository credentials.
* Deployment credentials.
* Cloud credentials.
* Package publishing credentials.
* CI secrets.
* Signing keys.
* Build artifacts.

Use:

* Least privilege.
* Environment-scoped secrets.
* Protected branches.
* Approval controls where appropriate.
* Short-lived credentials where supported.
* Audit logs.
* Immutable or traceable artifacts.

Do not expose secrets through logs, build output, generated artifacts, or client bundles.

---

# A04 — Cryptographic Failures

Do not implement custom cryptography unless there is an exceptional and well-justified reason.

Prefer established cryptographic libraries and platform APIs.

Protect sensitive data:

* In transit.
* At rest when required.
* In logs.
* In caches.
* In backups.
* In error messages.
* In analytics systems.

Do not store passwords using encryption when password hashing is required.

Use modern password hashing algorithms designed for password storage.

Do not invent custom encryption schemes.

---

# Secrets

Never commit secrets to source control.

Examples include:

* API keys.
* Private keys.
* Passwords.
* Access tokens.
* Database credentials.
* Cloud credentials.
* Signing keys.

Do not assume a secret is safe because the repository is private.

If a secret is accidentally committed:

1. Treat it as compromised.
2. Rotate or revoke it.
3. Remove it from active use.
4. Investigate exposure.
5. Clean repository history when appropriate.

Do not merely delete the secret from the latest commit and assume the problem is solved.

---

# Frontend Secrets

Anything shipped to the browser should be considered accessible to the user.

Do not put privileged secrets in:

* JavaScript bundles.
* Environment variables exposed to frontend builds.
* Source maps.
* Local storage.
* Session storage.
* Public configuration files.

An environment variable is not automatically secret merely because it is named `SECRET`.

If the browser can read it, an attacker can generally read it too.

---

# A05 — Injection

Treat external input as untrusted until appropriately validated and safely handled.

Relevant injection targets include:

* SQL.
* NoSQL.
* Shell commands.
* HTML.
* JavaScript.
* LDAP.
* XPath.
* Template engines.
* GraphQL.
* Search engines.
* Log systems.

Prefer parameterized APIs, prepared statements, safe query builders, and established encoding mechanisms.

Do not construct executable queries or commands by concatenating untrusted strings.

---

# XSS

Prevent cross-site scripting by:

* Avoiding unsafe HTML injection.
* Using framework escaping correctly.
* Sanitizing HTML when HTML input is genuinely required.
* Avoiding unsafe DOM APIs.
* Avoiding dynamic script construction.

Be particularly careful with:

```text
innerHTML
dangerouslySetInnerHTML
eval
new Function
document.write
dynamic script creation
```

Do not remove escaping merely because the current input "comes from a trusted source."

Trust boundaries can change.

---

# Command Injection

Never construct operating system commands directly from untrusted input.

Prefer APIs that accept structured arguments rather than shell command strings.

If shell execution is unavoidable:

* Validate inputs.
* Restrict allowed values.
* Avoid shell interpretation when possible.
* Apply least privilege.
* Treat the execution environment as security-sensitive.

---

# Path Traversal

When handling file paths supplied by users:

* Validate allowed paths.
* Prevent traversal sequences.
* Resolve paths safely.
* Restrict access to intended directories.
* Avoid exposing arbitrary filesystem operations.

Do not assume URL encoding or normalization makes path traversal safe automatically.

---

# A06 — Insecure Design

Security should be considered during design, not only implementation.

Identify abuse cases for sensitive functionality.

Examples:

* Repeated password reset attempts.
* Unlimited resource creation.
* Privilege escalation.
* Unauthorized bulk operations.
* Abuse of expensive APIs.
* Replay of sensitive operations.
* Automated account creation.

Use controls such as:

* Rate limiting.
* Authorization.
* Resource limits.
* Validation.
* Idempotency.
* Workflow constraints.
* Abuse detection.

Do not rely on users behaving correctly when a feature can be abused automatically.

---

# Business Logic Security

Security vulnerabilities can exist even when all inputs are technically valid.

Consider whether a user can manipulate a legitimate workflow to achieve an unauthorized result.

Examples:

```text
Apply a discount multiple times.
Approve one's own request.
Change ownership after authorization.
Skip a required workflow step.
Repeat a one-time operation.
Perform an action outside business limits.
```

Enforce business rules on trusted server-side boundaries.

---

# A07 — Authentication Failures

Authentication establishes who an actor is.

Review:

* Login.
* Logout.
* Password reset.
* MFA.
* Session creation.
* Session expiration.
* Credential recovery.
* Account enumeration.
* Brute-force protection.
* Token validation.

Do not implement authentication protocols from scratch when established standards and providers are available.

---

# Sessions

Session identifiers should:

* Be unpredictable.
* Be protected in transit.
* Have appropriate expiration.
* Be invalidated when required.
* Not contain unnecessary sensitive information.

For cookie-based sessions, consider appropriate:

* `Secure`.
* `HttpOnly`.
* `SameSite`.

Do not store sensitive session tokens in browser storage without carefully evaluating the threat model.

---

# JWT

When using JWTs:

* Validate signatures correctly.
* Validate expiration.
* Validate issuer and audience when applicable.
* Do not trust claims merely because they are decoded.
* Do not allow clients to choose authorization claims.
* Keep token lifetimes appropriate.
* Protect signing keys.
* Have a deliberate revocation strategy where required.

A decoded JWT is not a trusted JWT.

---

# Passwords

Never store plaintext passwords.

Use a password hashing algorithm designed for password storage.

Do not:

* Log passwords.
* Send passwords unnecessarily.
* Store passwords in reversible form.
* Use fast general-purpose hashes as password storage.

Follow established platform and authentication-provider guidance.

---

# A08 — Software or Data Integrity Failures

Protect the integrity of:

* Application code.
* Build artifacts.
* Updates.
* Configuration.
* Serialized data.
* Tokens.
* Critical messages.

Consider:

* Signature verification.
* Artifact integrity.
* Trusted update channels.
* Dependency integrity.
* Serialization safety.

Do not deserialize untrusted data using mechanisms that can execute arbitrary code.

---

# Untrusted Data

Treat serialized or externally supplied data as untrusted.

Verify:

* Source.
* Integrity.
* Schema.
* Expected structure.
* Allowed values.

Do not assume data is safe because it was previously generated by your own application.

Data can be modified in transit, at rest, or by another compromised component.

---

# A09 — Security Logging and Alerting Failures

Security-relevant events should be observable when appropriate.

Consider logging:

* Authentication failures.
* Authorization failures.
* Privilege changes.
* Suspicious activity.
* Security configuration changes.
* Important administrative operations.
* Repeated abuse attempts.

Logs should support investigation without exposing sensitive information.

Never log unnecessarily:

* Passwords.
* Access tokens.
* Session identifiers.
* Private keys.
* Sensitive personal information.

Security logs should be protected against unauthorized modification and access.

---

# Alerting

Not every security event should trigger an alert.

Prioritize alerts that indicate:

* Repeated authentication attacks.
* Privilege escalation.
* Suspicious administrative behavior.
* Significant access control failures.
* Credential compromise.
* Infrastructure compromise.

Avoid creating noisy alerts that are routinely ignored.

---

# A10 — Mishandling of Exceptional Conditions

Security-sensitive failures should fail safely.

Consider:

* Exceptions.
* Timeouts.
* Partial failures.
* Network failures.
* Dependency failures.
* Authorization failures.
* Invalid state transitions.
* Unexpected input.

Do not allow an exception to accidentally bypass a security control.

For example:

```text
if authorizationCheckFails:
    allowAccess()
```

is a critical security failure.

Prefer secure failure behavior:

```text
if authorizationCannotBeVerified:
    denyAccess()
```

---

# Error Responses

Do not expose unnecessary internal information through errors.

Avoid returning:

* Stack traces.
* Database queries.
* Internal filesystem paths.
* Secrets.
* Internal service topology.
* Authentication implementation details.

Provide useful user-facing errors without exposing information that assists attackers.

Retain diagnostic information in protected server-side logs when appropriate.

---

# CORS

CORS is a browser access-control mechanism, not authentication.

Do not use CORS as a substitute for authorization.

Review:

* Allowed origins.
* Credentials.
* Allowed methods.
* Allowed headers.
* Wildcards.
* Environment-specific configuration.

Avoid allowing arbitrary origins when credentials or sensitive resources are involved.

---

# CSRF

When authentication relies on browser credentials that are automatically attached to requests, consider CSRF protections.

Depending on the architecture, appropriate controls may include:

* SameSite cookies.
* CSRF tokens.
* Origin validation.
* Framework-provided protections.

Do not assume that a request being sent over HTTPS prevents CSRF.

---

# SSRF

When the application fetches a URL supplied or influenced by a user, consider Server-Side Request Forgery.

Potential targets include:

* Internal services.
* Cloud metadata endpoints.
* Localhost.
* Private network addresses.
* Internal administrative interfaces.

Do not allow arbitrary server-side URL fetching without appropriate restrictions.

Consider:

* Allowlisting destinations.
* Restricting protocols.
* Validating resolved addresses.
* Preventing access to private network ranges.
* Limiting redirects.
* Network-level controls.

---

# File Uploads

Treat uploaded files as untrusted.

Consider:

* File type validation.
* File size limits.
* Filename handling.
* Storage location.
* Content validation.
* Malware scanning where appropriate.
* Execution permissions.
* Access control.
* Download behavior.

Do not trust the filename or client-provided MIME type alone.

Do not store executable uploads in locations where the web server can execute them.

---

# Rate Limiting

Apply rate limits where abuse can cause:

* Credential attacks.
* Resource exhaustion.
* Excessive API costs.
* Enumeration.
* Spam.
* Automated abuse.

Consider limits for:

* Login.
* Password reset.
* Account creation.
* Expensive operations.
* Public APIs.
* File uploads.

Rate limits should be designed around the actual abuse case.

Do not add arbitrary rate limits that prevent legitimate usage without addressing a real threat.

---

# Least Privilege

Grant only the permissions required for an operation.

Apply least privilege to:

* Users.
* Service accounts.
* API tokens.
* CI/CD jobs.
* Applications.
* Containers.
* Database users.
* Cloud resources.

Avoid broad permissions for convenience.

---

# Secure Defaults

Prefer secure behavior by default.

Examples:

* Protected resources require authorization.
* Sensitive features are disabled unless explicitly enabled.
* Production debugging is disabled.
* Secure cookie attributes are enabled.
* Dangerous functionality requires explicit configuration.

Do not make security dependent on developers remembering an optional configuration step.

---

# Security Testing

Use multiple forms of security validation.

Consider:

* Unit tests.
* Integration tests.
* Authorization tests.
* Dependency scanning.
* Static analysis.
* Dynamic testing.
* Secret scanning.
* Infrastructure scanning.
* Manual security testing.

For authorization, test both:

```text
Allowed action → succeeds
Forbidden action → fails
```

Do not test only the happy path.

---

# Security Regression Tests

When fixing a security vulnerability:

1. Reproduce or model the vulnerability.
2. Add a regression test when practical.
3. Fix the underlying control.
4. Verify the exploit path is closed.
5. Check related paths for the same weakness.

Do not fix only the exact endpoint or input that exposed the vulnerability if the same architectural weakness exists elsewhere.

---

# Security and Frontend

Frontend code should:

* Avoid exposing secrets.
* Use safe rendering mechanisms.
* Avoid unsafe DOM manipulation.
* Handle authentication state carefully.
* Avoid assuming UI visibility equals authorization.
* Avoid storing sensitive information unnecessarily.

Remember:

```text
Frontend controls UX.
Backend controls trust.
```

---

# Security and Architecture

Security boundaries should influence architecture when dealing with:

* Authentication.
* Authorization.
* Sensitive data.
* External systems.
* Privileged operations.
* Trust boundaries.

Do not bury security-sensitive behavior inside generic UI components or convenience utilities.

Security controls should be easy to identify, test, and maintain.

---

# Security and Dependencies

When a dependency introduces security-sensitive behavior:

* Understand what it does.
* Understand what privileges it requires.
* Review how it handles data.
* Check maintenance status.
* Monitor vulnerabilities.
* Avoid unnecessary dependencies.

Do not assume a popular dependency is automatically secure.

---

# Security Review Workflow

When investigating a security-sensitive change:

```text
Understand the feature
        ↓
Identify assets and trust boundaries
        ↓
Identify attacker-controlled inputs
        ↓
Identify authentication requirements
        ↓
Identify authorization requirements
        ↓
Identify abuse cases
        ↓
Evaluate relevant OWASP categories
        ↓
Implement controls
        ↓
Test allowed and forbidden behavior
        ↓
Review failure paths
        ↓
Validate dependencies and configuration
```

---

# Security Review Checklist

Before approving security-sensitive code, consider:

* Is all external input treated as untrusted?
* Is authorization enforced server-side?
* Is access denied by default where appropriate?
* Can resource ownership be bypassed?
* Can privileges be escalated?
* Are authentication flows secure?
* Are sessions and tokens handled appropriately?
* Are secrets protected?
* Is sensitive data minimized?
* Are queries and commands safely constructed?
* Is HTML safely handled?
* Are dependencies trustworthy and maintained?
* Is configuration secure?
* Are errors handled safely?
* Are security events logged appropriately?
* Are logs free of secrets?
* Are abuse cases addressed?
* Are rate limits needed?
* Are uploaded files handled safely?
* Could SSRF occur?
* Could CSRF occur?
* Could sensitive resources be exposed through CORS?
* Are security controls tested?
* Does the solution fail securely?

---

# Security Definition of Done

A security-sensitive change is complete when:

* Trust boundaries are understood.
* Attacker-controlled input is identified.
* Authentication requirements are understood.
* Authorization is enforced at trusted boundaries.
* Relevant OWASP risks have been considered.
* Secrets and sensitive data are protected.
* Dependencies and configuration have been considered.
* Failure paths have been reviewed.
* Security-sensitive behavior is tested.
* Security regressions have been considered.
* The implementation follows established security mechanisms rather than custom cryptography or authentication.
* No security control depends solely on client-side behavior.
