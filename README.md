# Meashra public verifier

Static page that checks a Meashra agent identity (DID) directly against the public registry on Sepolia.
It calls no Meashra server and needs no account or key.

Registry: 0x57F1475b01E867F0E3E2bA1123c5c9F7021E548b (Sepolia, chain 11155111)
https://sepolia.etherscan.io/address/0x57F1475b01E867F0E3E2bA1123c5c9F7021E548b

Command-line version and source: meashra-verifier.zip (read README inside).
It shows what the registry says. It does not prove a registration was legitimate, and it trusts the RPC provider you use. Testnet only.
