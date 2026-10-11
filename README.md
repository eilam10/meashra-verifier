# Meashra Agent Verifier

A small, read-only tool that checks a `did:nhim` agent identity against the public DIDRegistry on Ethereum Sepolia.
It needs **only a Sepolia RPC URL**. It calls no Meashra API, database or account, and uses no key of ours.
It works with every Meashra service switched off.

- Registry: `0x57F1475b01E867F0E3E2bA1123c5c9F7021E548b` on Sepolia (testnet), deployed at block 11863194
- Source verified on [Etherscan](https://sepolia.etherscan.io/address/0x57F1475b01E867F0E3E2bA1123c5c9F7021E548b#code)
- Sample agent to try: `did:nhim:11155111:0x78598717cd4e24cd41f18e449c46f4edace7b1ff`

## Run it (command line)

Requires Node.js 18 or newer. No install step.

```
node dist/cli.js did:nhim:11155111:0x78598717cd4e24cd41f18e449c46f4edace7b1ff --rpc <your Sepolia RPC URL>
```

Without `--rpc` it uses a free public endpoint (`sepolia.gateway.tenderly.co`) run by a third party.
Repeat `--rpc` with a second provider to cross-check: the tool reports whether they agree.

## Run it (browser)

Open `web/index.html` in a browser (double-click is enough), paste a DID, press Verify.
The page runs the same code and talks only to the RPC URL in the form.

## What it checks

| # | Check | How | Output |
|---|---|---|---|
| 1 | Existence | Reads the registry record; checks the owner (the wallet that registered it) is an expected issuer | FOUND / NOT FOUND, EXPECTED or UNVERIFIED ISSUER |
| 2 | Status | Registry state plus the event log (revocation, expiry changes) | ACTIVE / REVOKED / EXPIRED with block, UTC time and transaction link |
| 3 | Claims integrity | The DID document is stored on-chain. Compares registry state with the latest document event, checks the document names this DID and the subject key, and optionally compares a document you were given (`--doc file.json`) | MATCH / MISMATCH |
| 4 | Control of key | You create a challenge, the agent signs it, the tool recovers the signer and compares it with the subject address | PASS / FAIL (NOT CHECKED if no signature) |
| 5 | History | Every registry event for this DID, oldest first | Registration, updates, transfers, expiry changes, revocation, each with a transaction link |

Expected issuer: by default the platform wallet `0x7968dDA24aF04BEfF495e24Aa3655c5b3F2238F3`. Use `--issuer 0x...` (repeatable) to change it.
A record owned by anyone else is reported **UNVERIFIED ISSUER** and the result is NOT VERIFIED, because the old registry let anyone register a DID.

### Key-control flow

```
node dist/cli.js <did> --challenge > challenge.txt          # verifier makes a fresh challenge
AGENT_PRIVATE_KEY=0x... node dist/sign.js challenge.txt     # agent holder signs it (key stays local)
node dist/cli.js <did> --rpc <url> --message-file challenge.txt --signature <sig>
```

### Other options

`--doc <file>` compare a presented DID document; `--print-doc` print the DID document stored on-chain; `--json` machine-readable output; `--registry <addr> --from-block <n>` check a different registry (for example the superseded pre-release one); `--help`.
Exit codes: 0 verified, 1 not verified or not found, 2 usage or RPC error.

## Monitor mode: make a fake registration impossible to hide

The platform wallet is the only registrar today, so it could register an agent that should not exist. Monitor mode does not prevent that. It makes it visible: it reads every registry event and lists every DID ever registered, with its owner, status and transaction links.

```
node dist/cli.js --monitor --rpc https://sepolia.gateway.tenderly.co
node dist/cli.js --monitor --expected inventory.txt --rpc https://sepolia.gateway.tenderly.co
```

`inventory.txt` is your own list of agents, one DID or address per line (`#` starts a comment). With it, the tool also reports agents that are active in the registry but not in your inventory, agents in your inventory that were never registered, and agents in your inventory that are revoked on-chain. Exit code 0 means no findings, 1 means review them. Repeat `--rpc` to cross-check providers. Without `--expected` it only flags registrations by owners that are not expected issuers.

## What it does not tell you

- It shows what the registry says, **not that a registration was legitimate.** The platform wallet is still the only signer of registrations, so the operator could register an agent that should not exist; the chain would record it faithfully. MPC signing is planned, not built.
- It trusts the RPC provider. Cross-check with two providers.
- Identity and revocation only. Access events are not anchored on-chain.
- Sepolia testnet only.
- Revocations made in the dashboard (standard revoke and Emergency revoke) are recorded on-chain by a revocation workflow, typically within a couple of minutes of the click. Until the transaction confirms, the verifier will still show the agent as ACTIVE; run it again after the `DIDRevoked` event appears.
- Some free Sepolia RPC providers prune old logs or block crypto domains on some networks. If one fails, the verifier says so within 15 seconds; use another endpoint, and `--rpc` can be repeated to cross-check providers.
- An expired DID could be revived on the old registry without any event. The tool flags any expiry in registry state that no logged event explains.

## Status of this build

- Automated tests: unit tests with a fake chain reader plus end-to-end tests that run the real code against a local mock RPC. They cover active, revoked, unknown, altered claims, wrong key, lookalike issuer, silent expiry change, small log-range providers and two-provider cross-check.
- The contract ABI was taken from the verified source page. The tool refuses to run if the record it decodes does not name the subject you asked about, which would indicate an ABI mismatch.
- Live run against Sepolia, 2026-10-08, two providers (publicnode, tenderly) agreeing: an active demo agent returned `VERIFIED` with a registration transaction link; an agent revoked through the standard flow returned `REVOKED` with the `DIDRevoked` transaction (block 11870216). On 2026-10-09 an agent revoked through Emergency revoke (CISO role) returned `REVOKED` with its `DIDRevoked` transaction (block 11877261), and `--monitor` listed all 4 registrations ever made (2 active, 2 revoked) and, against a test inventory, flagged an active agent missing from the inventory and an inventory entry revoked on-chain. A revoked or expired DID headlines `REVOKED`/`EXPIRED` and exits 1.
- Run by someone outside the team on a clean machine: [pending].

## Rebuild

```
npm install
npm run build   # produces dist/cli.js, dist/sign.js and web/verifier.js
npm test
```
