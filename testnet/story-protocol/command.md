---
cover: ../../.gitbook/assets/1080x360 (1).jpg
coverY: 0
---

# 🛰️ Command

## Delegate to Yourself

```bash
story validator stake \
  --validator-pubkey Akn2LfO1JjJkoMB6wJpr02WqwVcaa0yxxxxxxxxx \
  --stake 5000000000000000000 \
  --private-key ""
```

## Validator Unstaking

```bash
story validator unstake \
  --validator-pubkey ${VALIDATOR_PUB_KEY_IN_BASE64} \
  --unstake ${AMOUNT_TO_UNSTAKE_IN_WEI} \
  --private-key ""
```

## Validator Stake-on-behalf

```bash
story validator stake \
   --delegator-pubkey An/k/1HntF2r8ZaqrwxEOTKSIXdVsZk5wxkt4i5uhk3V \
   --validator-pubkey A0PSykpcrXXomPnNJcITuCawqg+G0JokQBGd9hU2f5CM \
   --stake 1000000000000000000 \
   --private-key ""
```

## Validator Unstake-on-behalf

```bash
story validator unstake-on-behalf \
  --delegator-pubkey ${DELEGATOR_PUB_KEY_IN_BASE64} \
  --validator-pubkey ${VALIDATOR_PUB_KEY_IN_BASE64} \
  --unstake ${AMOUNT_TO_STAKE_IN_WEI} \
  --private-key ""
```
