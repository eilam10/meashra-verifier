# Three-minute demo

1. **The claim (20s).** "An outsider can check an agent's identity and status from public chain data, without going through our servers. I'll do it with every Meashra service irrelevant: this page only talks to a public RPC."
2. **Active agent (40s).** Open `web/index.html`, paste `did:nhim:11155111:0x240e3a14b00f2ab714c6f00b404fdd1f1914f680`, Verify. Point at: FOUND / EXPECTED ISSUER, ACTIVE with block and time, the Etherscan link, MATCH, the history.
3. **Prove it is on-chain (30s).** Click the transaction link: the same registration on Etherscan, sent by the platform wallet to the verified registry.
4. **Revoked agent (40s).** Paste `did:nhim:11155111:0x78598717cd4e24cd41f18e449c46f4edace7b1ff`, Verify: REVOKED with the revocation time (block 11891480) and the transaction link. Click the link to show the `DIDRevoked` event on Etherscan. Revocations made in the dashboard typically confirm on-chain within seconds, but an already-revoked agent keeps your active count at 4 and avoids an irreversible live revoke.
5. **A lookalike (30s).** Spoken point, no live command: "The old registry let anyone register, so the tool checks who registered each DID. A lookalike registration is flagged UNVERIFIED ISSUER, and the automated tests cover that case. This is why the check on the owner exists."
6. **The limits (20s).** "It shows what the registry says, not that a registration was legitimate. The platform wallet is still the only signer; MPC is the plan. Sepolia testnet only."
