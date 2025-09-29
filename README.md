# Automação de Atualização IBM MQ para AAP

Este projeto fornece uma automação completa para atualização do IBM MQ, incluindo backup, snapshot, validações e rollback automático.

## Estrutura do Projeto

```
ibmmq/
├── playbooks/
│   ├── main_update_mq.yml          # Playbook principal que orquestra todo o processo
│   ├── upfix1.yml                  # Validação e acessibilidade
│   ├── upfix2.yml                  # Preparação de logs e diretórios
│   ├── upfix3.yml                  # Coleta de visibilidade (já existente)
│   ├── backup_mq.yml               # Backup completo do MQ
│   ├── snapshot_system.yml         # Snapshot do sistema
│   ├── pre_update_validation.yml   # Validações pré-atualização
│   ├── update_mq.yml               # Atualização do MQ
│   ├── post_update_validation.yml  # Validações pós-atualização
│   ├── functional_tests.yml         # Testes funcionais
│   ├── send_notifications.yml       # Notificações
│   ├── rollback_mq.yml             # Rollback automático
│   └── vars/
│       └── vars.yml                # Variáveis de configuração
├── notas/
│   ├── notas.txt                   # Notas técnicas
│   └── processo.txt                # Processo de atualização
└── README.md                       # Esta documentação
```

## Funcionalidades

### 🔍 **Etapa 1 - Validação e Acessibilidade**
- Verifica se os servidores existem no inventário do AAP
- Testa conectividade com todos os hosts
- Valida acesso aos servidores MQ

### 📁 **Etapa 2 - Preparação**
- Cria diretórios de log estruturados
- Prepara ambiente para a atualização
- Configura permissões adequadas

### 📊 **Etapa 3 - Coleta de Visibilidade**
- Coleta informações detalhadas do MQ
- Documenta estado atual dos Queue Managers
- Registra canais, filas e listeners ativos

### 💾 **Etapa 4 - Backup Completo**
- Backup das configurações do MQ
- Backup dos dados e logs
- Backup das definições dos Queue Managers
- Backup das configurações do sistema
- Criação de inventário do backup

### 📸 **Etapa 5 - Snapshot do Sistema**
- Snapshot LVM (se disponível)
- Snapshot via rsync (fallback)
- Snapshot do sistema de arquivos
- Backup das configurações do sistema

### ✅ **Etapa 6 - Validações Pré-Atualização**
- Verifica espaço em disco (mínimo 10GB)
- Verifica memória disponível (mínimo 4GB)
- Verifica processos MQ em execução
- Verifica conexões ativas
- Verifica filas com mensagens
- Verifica integridade dos arquivos

### 🔄 **Etapa 7 - Atualização do MQ**
- Para todos os Queue Managers
- Faz backup da instalação atual
- Instala a nova versão do MQ
- Aplica configurações pós-atualização
- Reinicia os Queue Managers
- Executa comandos de pós-atualização

### ✅ **Etapa 8 - Validações Pós-Atualização**
- Verifica versão após atualização
- Verifica status dos Queue Managers
- Testa conectividade
- Verifica canais, filas e listeners
- Verifica logs de erro
- Testa integridade dos dados

### 🧪 **Etapa 9 - Testes Funcionais**
- Teste de conectividade básica
- Teste de operações de fila
- Teste de envio/recebimento de mensagens
- Teste de canais e listeners
- Teste de performance
- Teste de autorizações
- Teste de carga básica

### 📧 **Etapa 10 - Notificações**
- Relatório consolidado
- Notificação por email
- Notificação para Slack (opcional)
- Criação de ticket (opcional)
- Registro no AAP Controller

## Como Usar

### 1. Configuração Inicial

Edite o arquivo `playbooks/vars/vars.yml` com suas configurações:

```yaml
# Configurações básicas
updatemq_version: "0001"
ac_inv_all_hosts: "INV-MQ"

# Notificações
notification_email: "admin@company.com"
smtp_server: "smtp.company.com"
smtp_port: 587

# Slack (opcional)
slack_webhook_url: "https://hooks.slack.com/services/..."

# Sistema de tickets (opcional)
ticketing_system_url: "https://tickets.company.com/api"
ticketing_system_token: "your-token"
```

### 2. Execução da Atualização

```bash
# Executar atualização completa
ansible-playbook -i inventory playbooks/main_update_mq.yml

# Executar apenas validações
ansible-playbook -i inventory playbooks/pre_update_validation.yml

# Executar apenas backup
ansible-playbook -i inventory playbooks/backup_mq.yml

# Executar rollback (em caso de falha)
ansible-playbook -i inventory playbooks/rollback_mq.yml
```

### 3. Execução no AAP

1. Crie um Job Template no AAP
2. Configure o inventário `INV-MQ`
3. Selecione o playbook `main_update_mq.yml`
4. Configure as variáveis necessárias
5. Execute o job

## Configurações Avançadas

### Backup
- **Retenção**: 30 dias (configurável)
- **Compressão**: Habilitada por padrão
- **Localização**: `/var/backup/mq/`

### Validações
- **Espaço mínimo**: 10GB
- **Memória mínima**: 4GB
- **Conexões máximas**: 100

### Testes
- **Timeout**: 300 segundos
- **Mensagens de teste**: 10
- **Testes executados**: 11 tipos diferentes

## Monitoramento

### Logs
Todos os logs são salvos em:
```
/var/log/ansible/updatemq/updatemq_{{ updatemq_version }}/
```

### Relatórios
- Relatório de backup
- Relatório de snapshot
- Relatório de validação pré-atualização
- Relatório de atualização
- Relatório de validação pós-atualização
- Relatório de testes funcionais
- Relatório consolidado

## Rollback

Em caso de falha, execute o rollback:

```bash
ansible-playbook -i inventory playbooks/rollback_mq.yml
```

O rollback irá:
1. Parar todos os Queue Managers
2. Restaurar backup da instalação
3. Restaurar dados do MQ
4. Restaurar configurações do sistema
5. Reiniciar os Queue Managers
6. Executar testes básicos

## Troubleshooting

### Problemas Comuns

1. **Espaço insuficiente**
   - Verifique se há pelo menos 10GB livres
   - Limpe logs antigos se necessário

2. **Falha na conectividade**
   - Verifique se os hosts estão acessíveis
   - Confirme as credenciais SSH

3. **Falha no backup**
   - Verifique permissões do usuário mqm
   - Confirme se há espaço suficiente

4. **Falha na atualização**
   - Verifique se os pacotes estão disponíveis
   - Confirme as dependências do sistema

### Logs Importantes

- `/var/log/ansible/updatemq/` - Logs da automação
- `/var/mqm/qmgrs/*/errors/` - Logs de erro do MQ
- `/var/log/messages` - Logs do sistema

## Suporte

Para suporte técnico:
- Email: admin@company.com
- Slack: #mq-automation
- Tickets: Sistema de tickets da empresa

## Versão

- **Versão**: 1.0
- **Data**: 2025-01-29
- **Compatibilidade**: IBM MQ 9.x, Ansible 2.9+, AAP 2.x
