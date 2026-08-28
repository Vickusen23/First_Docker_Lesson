
# 🧭 How to Login to an EC2 Instance from WSL Ubuntu

### 1. Move the Key into WSL Home
```bash
cd ~
sudo cp /mnt/c/Users/hp/Downloads/Docker_Key_Pair.pem ~/Docker_Key_Pair.pem
```
**Explanation:**  
Files inside `/mnt/c/...` are stored on the Windows side. Windows permissions override Linux `chmod`, so SSH thinks the key is “too open.” Copying the key into your WSL home (`~/`) puts it under native Linux permissions, which SSH respects.

---

### 2. Fix Permissions
```bash
sudo chmod 400 ~/Docker_Key_Pair.pem
```
**Explanation:**  
SSH requires private keys to be readable **only by the owner**.  
- `400` means: owner can read, no one else can access.  
- If permissions are too open (like `777` or `555`), SSH will refuse to use the key.

---

### 3. Connect to EC2
```bash
ssh -i ~/Docker_Key_Pair.pem ubuntu@<Public-IP>
```
**Explanation:**  
- The `-i` flag tells SSH which private key to use.  
- Replace `<Public-IP>` with your EC2 instance’s public IPv4 (e.g., `54.89.177.223`).  
- Use `ubuntu` if your AMI is Ubuntu, or `ec2-user` if it’s Amazon Linux.

---

### 4. Check Security Group (if connection fails)
- Go to **AWS Console → EC2 → Security Groups → Inbound Rules**.  
- Ensure there’s a rule allowing **SSH (port 22)** from your IP.  
**Explanation:**  
Even with the right key, you can’t connect if the firewall blocks port 22.

---

## 📊 Why This Works
| Location | Problem | Fix |
|----------|---------|-----|
| `/mnt/c/...` | Windows ACLs override Linux chmod → SSH rejects | Copy into WSL home |
| `~/` | Native Linux permissions respected | `chmod 400` works |
| Security group closed | No network access | Open port 22 to your IP |

---

## ✅ Quick Checklist
- Key copied into WSL home (`~/Docker_Key_Pair.pem`)  
- Permissions set with `chmod 400`  
- Security group allows SSH (port 22)  
- Connect with `ssh -i ~/Docker_Key_Pair.pem ubuntu@<Public-IP>`

