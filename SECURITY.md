# Security Policy

## Supported versions

Gradeline is pre-release. Only the latest commit on `main` receives security fixes.

## Reporting a vulnerability

**Please do not report security vulnerabilities through public GitHub issues, discussions, or pull requests.**

Report them privately through GitHub's
[private vulnerability reporting](https://github.com/AverageDemo/gradeline/security/advisories/new).

Please include:

- The type of issue (for example webhook authentication bypass, injection, or secret exposure)
- The affected file paths, commit, or version
- Steps to reproduce, and proof-of-concept code if you have it
- The impact you expect an attacker could achieve

### What to expect

- An acknowledgement within **3 business days**
- A first assessment within **7 days**
- Coordinated disclosure: we agree on a timeline together, and you get credit in the advisory unless you'd rather not

## Leaked secrets

If you find a credential committed anywhere in this repository's history, report it the same way. We'll treat it as
compromised: revoke and rotate it first, then clean up the history.
