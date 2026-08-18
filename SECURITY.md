# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability in this project, please report it responsibly.

**Do NOT open a public GitHub issue for security vulnerabilities.**

Instead, please send an email to the project maintainers with:

1. A description of the vulnerability
2. Steps to reproduce the issue
3. Potential impact
4. Suggested fix (if available)

## Response Timeline

- **Acknowledgment**: Within 48 hours
- **Initial assessment**: Within 1 week
- **Fix or mitigation**: Depends on severity

## Scope

This project contains documentation and code templates for video-to-text processing. Security concerns may include:

- Malicious code injection in templates
- Unsafe file handling in batch processing scripts
- API key exposure in example scripts
- Dependency vulnerabilities in Python packages

## Proven Patterns for Users

- Never hardcode API keys in scripts
- Use virtual environments to isolate dependencies
- Validate file paths before processing
- Keep dependencies updated (`pip install --upgrade`)
- Review scripts before running them on sensitive content

## 安全策略

如果您发现此项目中的安全漏洞，请负责任地报告。

**请勿在公开的 GitHub Issue 中报告安全漏洞。**

请发送电子邮件给项目维护者，包含漏洞描述、复现步骤、潜在影响和建议修复方案。
