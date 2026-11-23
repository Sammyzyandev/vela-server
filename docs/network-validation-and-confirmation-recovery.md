# Network validation and confirmation recovery

## Network passphrase

The Stellar module validates `STELLAR_NETWORK_PASSPHRASE` against the selected
testnet or mainnet profile at startup. Transaction construction repeats this
guard before loading the source account, so a misconfigured process cannot
build an envelope for the wrong network. The error is intentionally generic and
does not expose configuration values.

The regression test uses the SDK's public network constants and never contacts
a live Horizon or Stellar RPC endpoint.

## Resuming confirmation after restart

Submission persists the transaction hash and `SUBMITTED` state before
confirmation polling completes. A later request can therefore load that
durable state and resume polling using the stored hash, without accepting or
relaying a new signed envelope. Confirmed payments return idempotently;
bounded timeouts and provider failures leave the payment `SUBMITTED` so a later
retry remains possible.

This recovery path is covered with mocked provider responses. It does not claim
that a test has independently verified a live testnet transaction.
