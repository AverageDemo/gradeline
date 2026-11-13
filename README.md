# Gradeline

**Grade TradingView webhook alerts against your trading setups, and get them pushed to your phone.**

> 🚧 **Early development.** Planning is underway; nothing here is usable yet.

## What it will do

1. **Receive** alert webhooks from TradingView.
2. **Analyze** each alert against known trading setups.
3. **Grade** the alert against setups you define as they form.
4. **Push** graded alerts to your mobile devices.

## Security

- Secrets are **never** committed. Configuration comes from environment variables only; see `.env.example` once it exists.
- Every commit is scanned for secrets by [gitleaks](https://github.com/gitleaks/gitleaks), both locally and in CI.
- Found a vulnerability? Follow [SECURITY.md](SECURITY.md). Please don't open a public issue.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## Disclaimer

Gradeline is a software tool, **not financial advice**. Alert grades are mechanical evaluations of user-defined rules and
are not recommendations to buy or sell anything. Trading involves substantial risk of loss. You alone are responsible for
your trading decisions. The software is provided "as is", without warranty of any kind; see the [LICENSE](LICENSE).

Gradeline is an independent project. It is not affiliated with, endorsed by, or sponsored by TradingView, Inc.
"TradingView" is a trademark of its respective owner and is used here only to describe compatibility.

## License

[Apache License 2.0](LICENSE)
