# Polymarket Proxy Ownership Transfer

A step-by-step guide for transferring ownership of the proxy wallet deployed during Polymarket account creation. Since the [Safe](https://safe.global) web interface does not currently support this workflow, this repository documents how to complete the process using the Safe CLI and can help secure the upcoming Polymarket airdrop, especially for compromised wallets. (See [ika’s incident](https://x.com/ika_xbt/status/2024145824687477042))

> Note: No gas is required until Step 4. Do not fund a compromised wallet prematurely.

---

## Prerequisites

1. **Docker Desktop**  
   Download and install Docker Desktop from [Docker’s website](https://www.docker.com/products/docker-desktop/). Open the program once installed.

2. **WSL Update (Windows only)**  
   If you see a warning that WSL is out of date, open a Command Prompt or Bash terminal and run the following command:  
```bash
wsl --update
```

---

## Step-by-Step Guide

### Step 1: Open Terminal in Docker Desktop
- Launch Docker Desktop.  
- Click on **Terminal** in the bottom-right corner.

### Step 2: Run the Safe CLI
```bash
docker run -it safeglobal/safe-cli safe-cli <proxy_wallet> <rpc_url>
```

**Parameters:**
- **<proxy_wallet>** – Your Polymarket proxy wallet address.
  - > **Note:** The address must be **checksummed**.
  - Connect your wallet to Polymarket and go to [Settings](https://polymarket.com/settings) to copy it.
- **<rpc_url>** – RPC endpoint to connect to Polygon.
  - Official: https://polygon.drpc.org  
  - Private RPC: e.g., Alchemy  

**Example:**
```bash
docker run -it safeglobal/safe-cli safe-cli 0x485Aaa9bE3462E9C2c4C40e2867489217F1e233b https://polygon.drpc.org
```

### Step 3: Verify Your Signer
```bash
load_cli_owners <polymarket_signer_private_key>
```

### Step 4: Start Transaction Service
```bash
tx-service
```

> At this point, you will need some POL for gas (even a small amount like 0.1 POL is sufficient).

### Step 5: Add a New Owner
```bash
add_owner <new_polymarket_signer_addr> --threshold 1
```

- Press `y` to confirm.
- > **Note:** The address must be **checksummed**. 
- If you don’t have a checksummed address, search for your wallet on [PolygonScan](https://polygonscan.com) and copy it.  


### Step 6: Execute the Transaction
```bash
execute-tx <tx_hash>
```

- Press `y` to confirm.

### Step 7: Sign the Transaction
```bash
sign-tx <tx_hash>
```

- Press `y` to confirm.
- > **Note:** At this point, your proxy wallet has **2 signers (owners)** with a threshold of 1.

### Step 8: Remove the Initial Signer
```bash
remove_owner <initial_signer_wallet_addr> --threshold 1
```

- Press `y` to confirm.

### Step 9: Execute the Removal Transaction
```bash
execute-tx <tx_hash>
```

- Press `y` to confirm.

### Step 10: Sign the Removal Transaction
```bash
sign-tx <tx_hash>
```

- Press `y` to confirm.

### Step 11: Verify
- Exit the CLI:
```bash
exit
```

- Run the Safe CLI again:
```bash
docker run -it safeglobal/safe-cli safe-cli <checksummed_proxy_wallet> <rpc_url>
```

- > **Note:**The initial signer should no longer appear in the owners list.

---

## Notes

- This process **does not fully secure the proxy wallet**. The signer that created the wallet can still place bets but cannot withdraw funds.  
- If any of your wallets are compromised, this process can help secure your Polymarket airdrop allocation.  
- Always double-check addresses and keep private keys safe.
