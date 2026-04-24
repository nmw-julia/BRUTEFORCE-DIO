Relatório de Simulação: Ataque de Força Bruta com Medusa no Kali Linux
1. Introdução
Este projeto documenta a execução de testes de intrusão controlados, focados em ataques de força bruta (brute force) contra serviços comuns (FTP e SMB) e aplicações web (DVWA). O objetivo é demonstrar como credenciais fracas podem ser exploradas e como configurar um ambiente de auditoria utilizando Kali Linux e Medusa.

2. Cenário e Ambiente
Para garantir a segurança e a legalidade dos testes, utilizei um ambiente isolado:
Atacante: Kali Linux (IP: 192.168.56.101)
Alvo: Metasploitable 2 (IP: 192.168.56.102)
Rede: VirtualBox Host-Only Adapter (Rede Interna).
Ferramentas Utilizadas:
Nmap: Para reconhecimento e enumeração de serviços.
Medusa: Ferramenta modular de força bruta paralela.
Smbclient: Para validação de acesso SMB.

4. Desenvolvimento da Atividade
Passo 1: Reconhecimento
Iniciei mapeando os serviços ativos no alvo para identificar vetores de ataque:
bash
nmap -sV 192.168.56.102
Use code with caution.
Resultado: Identificados os serviços FTP (21), SMB (445) e HTTP (80) abertos.

Passo 2: Criação de Wordlists
Criei listas customizadas de usuários e senhas baseadas em padrões comuns de sistemas mal configurados:
users.txt: admin, root, user, msfadmin.
passwords.txt: 123456, password, admin, msfadmin.

Passo 3: Ataque Brute Force via FTP
Utilizei o Medusa para testar as combinações de credenciais no serviço FTP:
bash
medusa -h 192.168.56.102 -U users.txt -P passwords.txt -M ftp
Use code with caution.
-h: Endereço do host alvo.
-U/-P: Caminho para as listas de usuários/senhas.
-M: Módulo do protocolo.

Passo 4: Password Spraying em SMB
Neste cenário, simulei um ataque corporativo onde tentei uma única senha comum contra vários usuários para evitar o bloqueio de contas:
bash
medusa -h 192.168.56.102 -U users.txt -p "msfadmin" -M smbnt
Use code with caution.
Validação: Após o Medusa encontrar a combinação msfadmin:msfadmin, validei o acesso com:
bash
smbclient -L //192.168.56.102/ -U msfadmin
Use code with caution.

5. Resultados Obtidos
Serviço	Usuário Encontrado	Senha Encontrada	Status
FTP	msfadmin	msfadmin	Sucesso
SMB	msfadmin	msfadmin	Sucesso
SSH	root	(não testado)	-

7. Medidas de Mitigação (Recomendações)
Com base nos testes, as seguintes defesas são essenciais:
Política de Senhas Fortes: Implementar requisitos mínimos (caracteres especiais, números, letras maiúsculas).
Bloqueio de Conta (Account Lockout): Configurar o sistema para bloquear o usuário após 3 ou 5 tentativas falhas.
Desativação de Serviços Desnecessários: Desativar FTP anônimo ou Telnet, preferindo SSH.
Monitoramento de Logs: Utilizar ferramentas como Fail2Ban para detectar e banir automaticamente IPs que realizam múltiplas tentativas de login.
8. Conclusão
A simulação demonstrou que, com ferramentas automatizadas como o Medusa, um invasor pode obter acesso a sistemas em segundos se as credenciais forem previsíveis. A prática reforçou a importância da camada de autenticação e do monitoramento contínuo em segurança defensiva.
