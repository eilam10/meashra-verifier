# Three-minute demo

1. **The claim (20s).** "An outsider can check an agent's identity and status from public chain data, without going through our servers. I'll do it with every Meashra service irrelevant: this page only talks to a public RPC."
2. **Active agent (40s).** Open `web/index.html`, paste `did:nhim:11155111:0x240e3a14b00f2ab714c6f00b404fdd1f1914f680`, Verify. Point at: FOUND / EXPECTED ISSUER, ACTIVE with block and time, the Etherscan link, MATCH, the history.
3. **Prove it is on-chain (30s).** Click the transaction link: the same registration on Etherscan, sent by the platform wallet to the verified registry.
4. **Revoked agent (40s).** Revoke one demo agent in the dashboard, wait a minute, verify it again: REVOKED with the revocation time and transaction.
5. **A lookalike (30s).** Verify a DID registered on the old open registry with `--registry 0x722D91007D2576F5Cc8479525131ce18D4A9DB5C --from-block <deploy block>`: UNVERIFIED ISSUER. "This is why the check on the owner exists."
6. **The limits (20s).** "It shows what the registry says, not that a registration was legitimate. The platform wallet is still the only signer; MPC is the plan. Sepolia testnet only."
