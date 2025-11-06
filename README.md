# Automações IBM MQ - Ansible

Coleção de playbooks Ansible para gerenciamento completo do IBM MQ em ambientes Linux.

## 📋 Playbooks Disponíveis

### Instalação e Configuração

- **`mq_install_linux.yml`** - Instalação completa do IBM MQ 9.3
  - Download do instalador do repositório HTTP
  - Instalação de pacotes RPM
  - Criação de Queue Manager com configurações padrão
  - Configuração de canais, listeners e segurança

- **`mq_prepare_env_linux.yml`** - Preparação do ambiente (usuário, grupos, diretórios)

- **`mq_update_linux.yml`** - Atualização do IBM MQ 9.3 → 9.4
  - Backup automático antes da atualização
  - Dump de configuração MQSC
  - Remoção de pacotes antigos
  - Instalação da nova versão
  - Validação pós-atualização

- **`mq_reset_linux.yml`** - Reset completo do ambiente (remoção total do MQ)

### Operações e Manutenção

- **`mq_backup_linux.yml`** - Backup completo do IBM MQ
  - Backup de configurações, dados e logs
  - Dump MQSC de todos os Queue Managers

- **`mq_collect_linux.yml`** - Coleta de informações do ambiente
  - Versão do MQ
  - Status dos Queue Managers
  - Filas, listeners e canais ativos
  - Informações do sistema

- **`mq_command_linux.yml`** - Execução de comandos MQ personalizados

### Utilitários

- **`mq_simulate_linux.yml`** - Simulação de operações MQ
- **`mq_facts_linux.yml`** / **`mq_facts_v2_linux.yml`** - Coleta de facts do sistema


## ⚙️ Configurações

### Variáveis Principais

Os playbooks usam as seguintes variáveis padrão (podem ser sobrescritas):

```yaml
mq_user: mqm
mq_group: mqm
mq_base_dir: /opt/mqm
mq_home: /home/mqm
mq_bin_path: /opt/mqm/bin
qmgr_name: QM1
mq_listener_port: 1414
mq_svrconn_channel: SVRCONN.APP
```

### Repositório

Os pacotes são baixados de:
- **Linux**: `http://54.196.155.119/repowmq/Linux`
- **Windows**: `http://54.196.155.119/repowmq/Windows`

## 📦 Versões Suportadas

- **IBM MQ 9.3** (instalação inicial)
- **IBM MQ 9.4** (atualização)
- **RHEL 8.8+** / **RHEL 9.x** / **RHEL 10.x**

## 🔧 Requisitos

- Ansible 2.9+
- Acesso root/sudo nos servidores alvo
- Conectividade com o repositório de pacotes
- Espaço em disco: mínimo 10GB para instalação/atualização

## 📝 Estrutura do Projeto

```
ibmmq/
├── playbooks/
│   ├── mq_install_linux.yml      # Instalação IBM MQ 9.3
│   ├── mq_update_linux.yml       # Atualização 9.3 → 9.4
│   ├── mq_backup_linux.yml       # Backup completo
│   ├── mq_collect_linux.yml      # Coleta de informações
│   ├── mq_reset_linux.yml        # Reset completo
│   ├── mq_prepare_env_linux.yml  # Preparação ambiente
│   ├── mq_command_linux.yml      # Comandos personalizados
│   ├── mq_simulate_linux.yml     # Simulação
│   ├── mq_facts_linux.yml        # Facts v1
│   └── mq_facts_v2_linux.yml     # Facts v2
├── notas/
│   ├── notas.txt                 # Notas técnicas
│   └── processo.txt              # Processo de atualização
└── README.md                     # Esta documentação
```

## 🔍 Funcionalidades Principais

### Instalação (`mq_install_linux.yml`)
- ✅ Download automático do instalador
- ✅ Extração e validação de RPMs
- ✅ Instalação de pacotes essenciais
- ✅ Criação de Queue Manager com parâmetros otimizados
- ✅ Configuração de canais (CHANNEL1, MONITORACAO, SVRCONN.APP)
- ✅ Configuração de listener TCP
- ✅ Ajustes de segurança (CHLAUTH, AUTHINFO)
- ✅ Configuração de limites de canais no qm.ini

### Atualização (`mq_update_linux.yml`)
- ✅ Validação de versão do RHEL
- ✅ Backup automático antes da atualização
- ✅ Dump MQSC de todos os QMs
- ✅ Parada controlada dos Queue Managers
- ✅ Download do pacote 9.4 do repositório
- ✅ Remoção de pacotes 9.3
- ✅ Instalação de pacotes 9.4
- ✅ Verificação pós-atualização
- ✅ Reinício dos Queue Managers

### Backup (`mq_backup_linux.yml`)
- ✅ Backup de configurações (.mqsc, .conf, .ini)
- ✅ Backup de dados e logs
- ✅ Dump MQSC de cada Queue Manager
- ✅ Backup de definições de QMs

### Coleta (`mq_collect_linux.yml`)
- ✅ Informações do sistema operacional
- ✅ Versão do IBM MQ
- ✅ Lista de Queue Managers
- ✅ Status de filas, listeners e canais
- ✅ Relatório consolidado formatado

## 🛠️ Troubleshooting

### Problema: Instalação falha com "dspmqver não encontrado"
- Verifique se os RPMs foram extraídos corretamente em `/home/mqm/IBM_MQ/MQServer/`
- Confirme que a instalação foi executada com privilégios root

### Problema: Warning sobre diretório tmp do Ansible
- O playbook tenta usar `/home/mqm/.ansible/tmp` mas pode não ter permissão
- O Ansible automaticamente usa `/tmp` como fallback (não é crítico)

### Problema: Atualização não encontra pacote
- Verifique conectividade com `http://54.196.155.119/repowmq/Linux`
- Confirme que o arquivo `9.4.0.10-IBM-MQTRIAL-LinuxX64.tar.gz` está disponível

## 📚 Notas Adicionais

Consulte os arquivos em `notas/` para:
- Detalhes técnicos específicos
- Processos de atualização documentados
- Comandos manuais de referência

## 🔐 Segurança

- Todos os playbooks requerem `become: true` (privilégios elevados)
- Operações sensíveis são executadas como usuário `mqm`
- Backups são criados com permissões adequadas
- Configurações de segurança são aplicadas automaticamente

## 📞 Suporte

Para questões ou melhorias, consulte:
- Arquivos de notas em `notas/`
- Logs de execução do Ansible
- Documentação oficial do IBM MQ

---

**Versão**: 2.0  
**Última atualização**: 2025-01-29  
**Compatibilidade**: IBM MQ 9.3/9.4, RHEL 8.8+, Ansible 2.9+
