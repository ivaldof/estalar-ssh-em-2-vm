

---

# 🖧 Configuração de SSH entre duas VMs Ubuntu no VirtualBox

Este guia ensina como configurar uma comunicação segura via SSH entre duas máquinas virtuais (VMs) Ubuntu no VirtualBox, utilizando rede interna e autenticação por chave pública.

---

## 📋 Requisitos

* Duas VMs Ubuntu instaladas no VirtualBox
* Acesso root ou sudo em ambas as VMs
* Rede VirtualBox configurada como **"rede interna"**

---

## 1️⃣ Atualizar os repositórios

Em ambas as VMs (R1 e R2), execute:

```bash
sudo apt-get update
```

---

## 2️⃣ Instalar o servidor SSH

Em ambas as VMs:

```bash
sudo apt-get install openssh-server
```

---

## 3️⃣ Configurar endereçamento IP

Configure os IPs nas VMs manualmente:

* **VM R1:** `192.186.0.10`
* **VM R2:** `192.186.0.11`
* **Gateway (opcional):** `192.186.0.1`
* **DNS:** `192.186.0.10` (ou use `8.8.8.8` para DNS externo)

> 💡 Você pode configurar isso através das configurações de rede cabeada no Ubuntu.

---

## 4️⃣ Alterar modo de rede para "Rede Interna"

1. Desligue as VMs.
2. Vá em **Configurações da VM > Rede**.
3. Mude o adaptador de **NAT** para **Rede Interna**.
4. Faça isso em **ambas as VMs**, usando o mesmo nome de rede.

---

## 5️⃣ Testar conectividade

Ligue as VMs e teste a conexão com `ping`:

```bash
ping 192.186.0.11  # de R1 para R2
ping 192.186.0.10  # de R2 para R1
```

---

## 6️⃣ Gerar chave SSH (em ambas as VMs)

```bash
ssh-keygen
```

> Pressione ENTER para todas as opções (sem senha). Isso gera:

* Chave pública: `~/.ssh/id_rsa.pub`
* Chave privada: `~/.ssh/id_rsa`

---

## 7️⃣ Compartilhar chave pública

Para que o R1 possa acessar o R2 via SSH:

```bash
ssh-copy-id usuario@192.186.0.11
```

E vice-versa (de R2 para R1):

```bash
ssh-copy-id usuario@192.186.0.10
```

---

## 8️⃣ Alterações no SSHD para segurança

Edite o arquivo de configuração SSH:

```bash
sudo nano /etc/ssh/sshd_config
```

Adicione ou modifique as seguintes linhas:

```ini
PermitRootLogin no
MaxAuthTries 4
MaxSessions 8
PubKeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys .ssh/authorized_keys2
PasswordAuthentication no
PermitEmptyPasswords no
ClientAliveInterval 600
ClientAliveCountMax 3
```

Salve e feche o arquivo, então reinicie o serviço SSH:

```bash
sudo systemctl restart ssh
```

---

## ✅ Pronto!

Agora você pode se conectar entre as VMs com segurança e sem usar senha:

```bash
ssh usuario@192.186.0.11  # de R1 para R2
```

```bash
ssh usuario@192.186.0.10  # de R2 para R1
```

---

## 🛡️ Notas de Segurança

* Nunca compartilhe sua **chave privada**.
* Use apenas a **chave pública** para autenticação.
* Desative o login por senha para maior segurança (já incluído acima).

---

