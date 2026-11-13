# Contributing to Gradeline

Thanks for your interest! Gradeline is in early development, so please open an issue to discuss a change before you
put significant work into it.

## Ground rules

- **Never commit secrets.** That includes API keys, webhook tokens, push credentials, and `.env` files. Use environment
  variables, and document new ones in `.env.example` with placeholder values only.
- **Small, focused pull requests.** One logical change per PR, and one logical change per commit.
- **Tests come with the change.** New behavior needs tests, and bug fixes need a regression test.

## Workflow

`main` is protected. All changes go through pull requests.

1. Fork the repo and create a branch from `main`, for example `feat/setup-grading` or `fix/webhook-timeout`.
2. Enable the repo's git hooks once per clone:

   ```sh
   git config core.hooksPath .githooks
   ```

   The pre-commit hook runs [gitleaks](https://github.com/gitleaks/gitleaks#installing), which must be installed.
   It blocks any commit that contains a secret.
3. Make your change, with tests.
4. Open a pull request against `main`. CI must pass before the PR can merge.

## Commit messages

We use [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: grade alerts against forming setups
fix: reject webhooks with stale timestamps
docs: explain push notification setup
```

Mark breaking changes with `feat!:` or a `BREAKING CHANGE:` footer.

## Signed commits and sign-off (DCO)

- **Signed commits are required** on `main`. See GitHub's guide to
  [signing commits](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits).
- **Sign off every commit** under the [Developer Certificate of Origin](https://developercertificate.org/), which
  certifies that you have the right to submit the work under this project's license:

  ```sh
  git commit -s -m "feat: ..."
  ```

## License

By contributing, you agree that your contributions are licensed under the [Apache License 2.0](LICENSE).
