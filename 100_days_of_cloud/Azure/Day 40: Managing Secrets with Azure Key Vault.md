# Azure Key Vault Data Security Lab

The Nautilus DevOps team is focusing on improving their data security by using Azure Key Vault. Your task is to create a Key Vault with an RSA key and manage the encryption and decryption of a pre-existing sensitive file using this key.

## Specific Requirements

### 1. Create a Key Vault
* **Name:** `nautilus-25985`
* **Region:** East US
* **Pricing Tier:** Standard
* **Soft Delete Retention:** 7 days
* **Permission Model:** Vault access policy
* **Access Policy:** Allow **Get**, **List**, **Encrypt**, and **Decrypt** permissions for the lab identity.

### 2. Create an RSA Key
* **Name:** `nautilus-key`
* **Key Type:** RSA
* **RSA Key Size:** 4096
* **Other Settings:** Default

### 3. Encrypt the Sensitive Data
* Use the key to encrypt the provided `SensitiveData.txt` file (located in `/root/`) on the `azure-client` host.
* Use the `RSA-OAEP` algorithm.
* Base64 encode the plaintext before encryption.
* Save the encrypted version as `EncryptedData.bin` in the `/root/` directory.

> 💡 **Note:** If you encounter a permissions error, retrieve the Service Principal ID using:
> `az account show --query user.name -o tsv`
> and grant the required Key Vault permissions.

### 4. Verify Decryption
* Decrypt `EncryptedData.bin`.
* Base64 decode the decrypted output.
* Save the result as `DecryptedData.txt` in `/root/`.
* Ensure the decrypted file matches the original `SensitiveData.txt`.

Ensure that the Key Vault and key are correctly configured. The validation script will test your configuration by decrypting the `EncryptedData.bin` file using the key you created.

---

## Task 1: Create the Key Vault

### Step 1: Login to Azure
```bash
az login
```




### Step 2: Create the Vault with Access Policies
This command initializes the vault under the Standard tier, configures a 7-day soft-delete period, and grants the executing identity (`az account show`) immediate management permissions.
```bash
# Get your active user principal name or object ID
LAB_IDENTITY=\$(az account show --query user.name -o tsv)

# Create the vault
az keyvault create \
  --name "nautilus-25985" \
  --resource-group "nautilus-rg" \
  --location "eastus" \
  --sku "standard" \
  --retention-days 7 \
  --enable-rbac-authorization false
```

### Step 3: Apply Explicit Key Permissions
Ensure your lab identity explicitly possesses Get, List, Encrypt, and Decrypt privileges:
```bash
az keyvault set-policy \
  --name "nautilus-25985" \
  --upn "\$LAB_IDENTITY" \
  --key-permissions get list encrypt decrypt
```

---

## Task 2: Create the RSA Key

### Step 1: Generate the Key
Create your 4096-bit RSA cryptographic key inside the newly created Key Vault:
```bash
az keyvault key create \
  --vault-name "nautilus-25985" \
  --name "nautilus-key" \
  --kty RSA \
  --size 4096
```

---

## Task 3: Encrypt the Sensitive Data

### Step 1: Convert File to Base64
Azure Key Vault encrypts base64-encoded data. Run:
```bash
base64 /root/SensitiveData.txt > /root/plaintext.b64
```

### Step 2: Encrypt Using Key Vault
```bash
az keyvault key encrypt \
 --vault-name "nautilus-25985" \
 --name "nautilus-key" \
 --algorithm RSA-OAEP \
 --value "\$(cat /root/plaintext.b64)" \
 --query result \
 -o tsv > /root/EncryptedData.bin
```

**Expected Directory Contents:**
* `EncryptedData.bin`
* `plaintext.b64`
* `SensitiveData.txt`

---

## Task 4: Decrypt and Verify

### Step 1: Decrypt the File
```bash
az keyvault key decrypt \
 --vault-name "nautilus-25985" \
 --name "nautilus-key" \
 --algorithm RSA-OAEP \
 --value "\$(cat /root/EncryptedData.bin)" \
 --query result \
 -o tsv > /root/decrypted.b64
```

**Expected Directory Contents:**
* `decrypted.b64`
* `EncryptedData.bin`
* `plaintext.b64`
* `SensitiveData.txt`

### Step 2: Convert Back to Original Text
```bash
base64 --decode /root/decrypted.b64 > /root/DecryptedData.txt
```

**Expected Directory Contents:**
* `decrypted.b64`
* `EncryptedData.bin`
* `SensitiveData.txt`
* `DecryptedData.txt`
* `plaintext.b64`

### Step 3: Verify Data Integrity
```bash
diff /root/SensitiveData.txt /root/DecryptedData.txt
```

> 💡 **Tip:** No output from the `diff` command means the verification was successful!
