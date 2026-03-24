
# S3 Cache ![CI](https://github.com/100rabhg/s3-cache/actions/workflows/ci.yml/badge.svg) [![GitHub Marketplace](https://img.shields.io/badge/marketplace-s3--cache-blue)](https://github.com/marketplace/actions/s3-cache) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A GitHub Action to cache dependencies and artifacts using AWS S3. Useful for monorepos, large dependencies, or workflows where GitHub's built-in cache is insufficient.

---

## Features

- Restore cache from S3
- Auto upload cache in post step
- Simple configuration
- Works with any build system

---

## Inputs

| Name   | Required | Description |
|--------|----------|-------------|
| key    | ✅ | Unique cache key |
| path   | ✅ | Path to cache (e.g. node_modules) |
| bucket | ✅ | S3 bucket name |
| region | ❌ | AWS region (if empty, cache is skipped) |

---

## Outputs

| Name      | Description |
|-----------|-------------|
| cache-hit | true if cache restored, else false |

---

## Usage

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}
      - name: Cache node modules
        id: cache
        uses: 100rabhg/s3-cache@v1
        with:
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
          path: node_modules
          bucket: ${{ env.AWS_BUCKET }}
          region: ${{ env.AWS_REGION }}
      - name: Install dependencies
        if: steps.cache.outputs.cache-hit != 'true'
        run: npm ci
```

---

## Example Workflow

See [example workflow](.github/workflows/example.yml) for a complete setup.

---

## Marketplace

Find this action on the [GitHub Marketplace](https://github.com/marketplace/actions/s3-cache).

---

## License

This project is licensed under the [MIT License](LICENSE).
