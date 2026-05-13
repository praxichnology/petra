# Security Primer for Academic Web Applications

*A primer for researchers building their own implementations from the PeTRA blueprint — or any web-based research tool — in the 2026 AI security landscape.*

**Status**: Draft v1.0 (2026-04-25). Destined for `petra-sga/docs/primers/security.md` once finalized.

---

## Who this primer is for

You are an academic researcher considering building (or already operating) a web-based research tool. Your tool may collect human-subjects data, expose analytics dashboards, or run computational analyses. You are not a professional security engineer.

This primer explains the threat environment your research software will face in 2026, why traditional defenses are no longer sufficient, and what practical mitigations academic researchers can realistically adopt.

This primer is **not** a comprehensive security guide. It is a starting orientation. The references at the end point to deeper resources.

## Why this primer exists

PeTRA is published as **Open Documentation** — methodology, design rationale, and architecture are openly available so other researchers can implement their own analytics engines for their own serious games. If you are reading PeTRA's documentation and considering building your own implementation, this primer covers what you should understand about the security implications of deploying it.

The reasoning generalizes: any web-based research tool you deploy in 2026 faces this threat environment. PeTRA-specific implementations are one example; the same principles apply to learning analytics dashboards, survey platforms, gameplay-data collectors, behavioral-experiment hosts, and similar academic research software.

---

## The threat surface

Any web application on the public internet faces multiple attack vectors, regardless of password protection. Here is what you should understand.

### 1. Password protection is one layer; not impenetrable

A common misconception is that requiring a password (Apache `.htaccess` Basic Auth, custom login forms, etc.) is sufficient protection for an academic research tool. It is not.

Password-based protection is vulnerable to:

- **Brute force**: scripted attempts against common passwords. Without rate limiting, attackers can try thousands of passwords per minute against your login.
- **Credential stuffing**: if your password (or any user's password) was leaked in a previous data breach (LinkedIn 2016, Adobe 2013, hundreds of others), attackers automatically try those leaked credentials against your site. Many users reuse passwords; a single reused password can compromise multiple accounts.
- **Sniffing**: if any page on your site is served over HTTP (not HTTPS), credentials travel in plaintext and can be intercepted on the network path between user and server.
- **Bypass via URL manipulation**: if your access-control rules don't cover every endpoint perfectly, attackers find paths that escape protection.

Password protection is a useful first layer. It is not a complete defense.

### 2. Automated scanners don't need credentials for many attacks

This is the most counterintuitive part for non-security people. Many attacks succeed without ever needing valid credentials.

Automated vulnerability scanners probe for:

- **Common paths**: `/admin`, `/.git`, `/.env`, `/phpmyadmin`, `/wp-admin`, `/backup`, `/config.php`, `/debug`. If any of these resolve, the attacker gains information without ever being prompted for a password.
- **Information disclosure**: error messages, stack traces, debug logs, exposed config files reveal system details (PHP version, framework, database type, internal file paths). This information becomes fuel for targeted attacks.
- **Server fingerprinting**: attackers learn your tech stack from response headers, error pages, and behavioral patterns. They then look up known **CVEs** (Common Vulnerabilities and Exposures) for your specific stack components and try those exploits.
- **Endpoints that bypass authentication**: APIs, webhooks, debug pages, and legacy endpoints that the developer forgot to protect. These can run code or query databases without authentication. Attackers find them through systematic probing.

These attacks happen continuously, automatically, against every public-facing site. Your research tool does not need to be high-profile to be attacked — automated scanners don't discriminate.

### 3. Vulnerabilities in code that runs BEFORE authentication checks

Even when authentication is properly in place, the code that *processes the authentication request itself* has to handle user input. If that code has bugs, attackers exploit those bugs without ever passing through the authentication layer.

Common pre-auth vulnerabilities:

- **SQL injection in the login form**: if your login form passes the username directly into a database query without sanitization, an attacker can inject SQL that bypasses authentication entirely or extracts data.
- **Buffer overflow in input parsing**: malformed input crashes or hijacks the parsing code before authentication runs.
- **Path traversal in URL handling**: requesting `/../../../../etc/passwd` (or equivalent) reaches files outside the intended directory.
- **Server-side request forgery (SSRF)**: tricking the server into making requests on the attacker's behalf, often to internal network resources.

These attacks succeed *before* the password prompt. The attacker is never prompted for credentials because they're attacking the lock-handling mechanism, not the lock.

### 4. The 2026 AI factor

What makes 2026 different from previous years: AI tools have made vulnerability discovery dramatically cheaper and faster.

In previous decades, finding a vulnerability in a custom web application required:
- An expert security researcher
- Hours or days of manual work per application
- Familiarity with the specific stack

In 2026, AI-assisted scanners can:
- Read an entire codebase (if leaked) and identify common vulnerability patterns in minutes
- Generate working exploits for discovered vulnerabilities automatically
- Probe live sites at scale, learning from each response
- Maintain attack inventories that update as new CVEs are published

The cost-to-attacker has dropped by orders of magnitude. Sites that have been "fine for years" are not necessarily fine in this new environment.

This is one of the central reasons academic research projects are increasingly choosing **Open Documentation** over open-source distribution: when distributing executable code is equivalent to handing attackers a roadmap, the cost-benefit calculation shifts.

---

## What this means for academic researchers

Three practical implications:

**You are a target by default.**

Automated scanners don't choose targets based on importance. They scan everything. Even an obscure academic research tool with anonymized data is scanned, probed, and attacked continuously. The question is not whether you are attacked, but whether the attacks find vulnerabilities.

**Custom code increases your attack surface.**

Every PHP/Python/JavaScript file you write is potential attack surface. Vibe-coded software (rapidly produced via AI assistance) tends to have more vulnerabilities than carefully-engineered code, because security is rarely the focus of rapid prototyping. Most of these vulnerabilities are statistically harmless until someone scans for them — but in 2026, someone always scans.

**Distributing executable code increases your hosted-instance risk.**

If your code is distributed (via Docker, downloadable installers, or open-source repositories), attackers can analyze it offline at leisure, find vulnerabilities, and then exploit those vulnerabilities in any deployed instance — including your own. This is the central insight behind Curated Openness (industry term) and Open Documentation (the academic-research analogue): publish methodology, not implementation.

---

## Practical mitigations

Realistically, an academic researcher cannot match the security posture of a commercial software team. But several mitigations are achievable:

### Data minimization

The most effective security measure is reducing what you store. If your research tool doesn't store personal information, the consequences of a breach are dramatically reduced.

- Use anonymized identifiers (passkeys, hashed IDs) rather than names or emails
- Don't collect data you don't actually need
- Apply IRB-approved minimization principles to the technical implementation

For PeTRA specifically: the Nexus game uses passkey-only authentication; no PII is stored server-side. This is a defensible posture even if the server is compromised.

### Backups with tested restore

Assume the server might be compromised at some point. Plan accordingly:

- Daily automated database backups
- Backups stored on a separate system (not the same server)
- Quarterly tested restoration to verify backups actually work
- Documented restore procedure

The goal is not to prevent compromise (impossible) but to ensure rapid recovery from compromise.

### Don't store credentials in repositories

Never commit credentials (database passwords, API keys, secrets) to your codebase. Use environment variables or external secret stores.

If you are publishing your code (as Open Documentation describing your implementation), make absolutely sure no credentials have ever been committed — even in past commits in the git history. Tools like `git-secrets` or `truffleHog` can scan history for accidentally committed secrets.

### Keep dependencies updated

PHP, Python, JavaScript packages all have security updates. Subscribe to security advisories for your stack. Apply updates promptly.

For PHP specifically: ensure you are running a currently-supported PHP version. End-of-life PHP versions stop receiving security patches and become attractive targets.

### Use HTTPS everywhere

Never serve any page (or any subdomain) over plain HTTP. Modern hosting providers offer free TLS certificates (Let's Encrypt). There is no legitimate reason to run a research tool over HTTP in 2026.

### Configure rate limiting

Many attacks (brute force, credential stuffing) require thousands of requests. Rate limiting (limiting requests per IP per minute) makes these attacks impractical without solving them.

If your hosting provider offers rate limiting (Scalahosting, Cloudflare, etc.), enable it. If not, add it at the application level.

### Plan for the post-collection phase

If your research tool is for active data collection (e.g., during an IRB-approved study), plan its retirement. Once active collection ends, the tool no longer earns its risk profile. Take it offline, archive the data, and remove the public-facing surface.

For PeTRA-blueprint implementations: design from the start for eventual retirement of the hosted instance. Open Documentation lives indefinitely; the running implementation does not need to.

### Get a security review when stakes warrant it

For high-stakes research (healthcare data, vulnerable populations, regulatory exposure), invest in an external security audit. The cost is typically $2,000–$10,000 for a basic review, and the value is high if your research data would cause harm if breached.

For low-stakes academic research with anonymized data, this is overkill — but be honest about which category your research falls into.

---

## For PeTRA-blueprint implementers specifically

If you are implementing your own analytics engine based on PeTRA's documentation, the following considerations apply specifically to you:

**Your implementation's vulnerabilities are not PeTRA's vulnerabilities.**

PeTRA's documentation describes architecture and methodology. Your implementation in your chosen tech stack is your own work, with its own attack surface. PeTRA's choices (Open Documentation, anonymized data) inform but do not determine your implementation's security posture.

**Default to the Curated Openness posture.**

Even if you adopt PeTRA's blueprint, you do not need to (and probably should not) open-source your specific implementation. Document your methodology openly; keep your implementation source-available or private.

**The hosted-instance risk is yours.**

PeTRA's hosted instance (`csloh.com/petra`) is a target for the reasons above. Your hosted instance for your research will face the same threat environment. Apply the mitigations in this primer to your deployment.

**Anonymization is not a substitute for security; it is a complement.**

Even if your data is anonymized, server compromise has consequences:
- Loss of research data (mitigated by backups)
- Reputational damage to your institution
- Potential lateral attacks via your server into other systems
- Regulatory exposure depending on jurisdiction

Anonymization reduces consequences but does not make compromise acceptable. Both anonymization and basic security hygiene matter.

**Plan retirement from the start.**

When you set up your hosted instance, plan its retirement date. The default expectation should be: hosted services are temporary; documentation is permanent. PeTRA's Phase 2 plan retires `csloh.com/petra` once its data-collection role ends. Your equivalent plan should follow the same pattern.

---

## Further reading

For deeper understanding:

- **OWASP Top 10** ([https://owasp.org/Top10/](https://owasp.org/Top10/)) — the canonical list of the most common web application vulnerabilities, updated periodically. Read this even if you skip everything else.
- **Mozilla Observatory** ([https://observatory.mozilla.org/](https://observatory.mozilla.org/)) — free tool that scans your site for common security misconfigurations and gives you a graded report.
- **OECD (2025), *AI Openness: A Primer for Policymakers*** — policy-level treatment of openness as a spectrum; cites the security tradeoffs that motivate Curated Openness.
- **Linux Foundation Model Openness Framework (MOF)** — taxonomy of openness levels; useful for thinking about where your project sits on the spectrum.

For platform-specific guidance:

- If using Apache: harden `.htaccess` rules; disable directory listings; use `mod_security` if available
- If using PHP: stay on a supported version; use prepared statements for all SQL; sanitize all user input
- If using a hosting provider: enable their security features (Cloudflare-style WAF, rate limiting, automatic backups)

---

## Version history

- **v1.0 (2026-04-25)**: initial draft. Authored as part of PeTRA's Open Documentation effort. Captures threat-environment understanding shaped by the 2026 AI security landscape.

---

## Acknowledgments

This primer was shaped by collaborative discussion between Christian Sebastian Loh (Southern Illinois University Carbondale) and Claude (Anthropic), in the context of planning PeTRA's publication strategy under Open Documentation.

The threat-surface framing draws on standard web-security knowledge as of early 2026, with specific attention to how AI-assisted vulnerability scanning has shifted the cost-to-attacker calculus.

This primer is offered as community infrastructure for the petra-sga ecosystem and any academic researchers building their own web-based research tools. Use freely; cite if helpful.
