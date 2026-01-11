# 📚 Exemplos Práticos de Uso

Este documento contém exemplos reais de uso do Zabbix Host Import em diferentes cenários.

## 📋 Índice

1. [Importação Básica](#1-importação-básica)
2. [Múltiplos Grupos e Templates](#2-múltiplos-grupos-e-templates)
3. [Atualização em Massa](#3-atualização-em-massa)
4. [Migração de Ambiente](#4-migração-de-ambiente)
5. [Padronização de Infraestrutura](#5-padronização-de-infraestrutura)

---

## 1. Importação Básica

### Cenário: Adicionar 10 novos servidores Linux

**Planilha: `novos_servidores.xlsx`**

```
| Nome              | Grupo            | IP            | Template          |
|-------------------|------------------|---------------|-------------------|
| SRV-WEB-01       | Servidores/Linux | 10.0.1.10     | Template OS Linux |
| SRV-WEB-02       | Servidores/Linux | 10.0.1.11     | Template OS Linux |
| SRV-DB-01        | Servidores/Linux | 10.0.1.20     | Template OS Linux |
| SRV-DB-02        | Servidores/Linux | 10.0.1.21     | Template OS Linux |
| SRV-APP-01       | Servidores/Linux | 10.0.1.30     | Template OS Linux |
```

**Comandos:**

```bash
# 1. Testar primeiro (DRY-RUN)
python zabbix_import_excel_v7_4.py --excel novos_servidores.xlsx

# 2. Revisar o relatório CSV gerado

# 3. Aplicar se tudo estiver OK
python zabbix_import_excel_v7_4.py --excel novos_servidores.xlsx --apply
```

**Resultado:**
- 10 hosts criados
- Grupo "Servidores/Linux" criado (se não existir)
- Template vinculado automaticamente

---

## 2. Múltiplos Grupos e Templates

### Cenário: Servidores web com múltiplos templates e grupos

**Planilha: `webservers_completo.xlsx`**

```
| Nome         | Grupo                                    | IP         | Template                                              |
|--------------|------------------------------------------|------------|-------------------------------------------------------|
| WEB-PROD-01  | Servidores/Linux;Produção;Web Servers   | 10.0.2.10  | Template OS Linux;Template App Apache;Template ICMP  |
| WEB-PROD-02  | Servidores/Linux;Produção;Web Servers   | 10.0.2.11  | Template OS Linux;Template App Apache;Template ICMP  |
| WEB-DEV-01   | Servidores/Linux;Desenvolvimento         | 10.0.3.10  | Template OS Linux;Template App Apache                |
```

**Nota:** Use `;` (ponto e vírgula) para separar múltiplos valores

**Comandos:**

```bash
# Teste
python zabbix_import_excel_v7_4.py --excel webservers_completo.xlsx

# Aplicar
python zabbix_import_excel_v7_4.py --excel webservers_completo.xlsx --apply
```

**Resultado:**
- Cada host em 3 grupos diferentes
- 3 templates vinculados por host
- Grupos criados automaticamente se não existirem

---

## 3. Atualização em Massa

### Cenário: Atualizar IPs de 50 servidores após mudança de subnet

**Planilha: `atualizacao_ips.xlsx`**

```
| Nome         | Grupo            | IP           | Template          |
|--------------|------------------|--------------|-------------------|
| SRV-WEB-01   | Servidores/Linux | 192.168.1.10 | Template OS Linux |
| SRV-WEB-02   | Servidores/Linux | 192.168.1.11 | Template OS Linux |
| SRV-DB-01    | Servidores/Linux | 192.168.1.20 | Template OS Linux |
```

**Comandos:**

```bash
# Teste para ver o que será alterado
python zabbix_import_excel_v7_4.py --excel atualizacao_ips.xlsx

# Aplicar mudanças
python zabbix_import_excel_v7_4.py --excel atualizacao_ips.xlsx --apply
```

**O que acontece:**
- Script identifica hosts existentes pelo nome
- Atualiza APENAS o IP da interface
- Mantém grupos e templates existentes
- Não cria hosts duplicados

---

## 4. Migração de Ambiente

### Cenário: Migrar configuração de Zabbix de teste para produção

**Planilha: `migracao_prod.xlsx`**

```
| Nome              | Grupo                    | IP            | Template                        |
|-------------------|--------------------------|---------------|---------------------------------|
| PROD-DB-MASTER    | Produção/Database        | 10.10.1.10    | Template DB MySQL               |
| PROD-DB-SLAVE-01  | Produção/Database        | 10.10.1.11    | Template DB MySQL               |
| PROD-WEB-LB       | Produção/Load Balancer   | 10.10.2.10    | Template Net Linux              |
| PROD-APP-01       | Produção/Application     | 10.10.3.10    | Template OS Linux;Template Java |
| PROD-APP-02       | Produção/Application     | 10.10.3.11    | Template OS Linux;Template Java |
```

**Comandos:**

```bash
# 1. Exportar configuração do ambiente de teste
# (você precisa criar a planilha manualmente ou com outro script)

# 2. Ajustar IPs e nomes na planilha para produção

# 3. Testar no ambiente de produção
python zabbix_import_excel_v7_4.py --excel migracao_prod.xlsx

# 4. Aplicar após validação
python zabbix_import_excel_v7_4.py --excel migracao_prod.xlsx --apply
```

---

## 5. Padronização de Infraestrutura

### Cenário: Padronizar 100 hosts com templates e grupos corretos

**Planilha: `padronizacao.xlsx`**

```
| Nome          | Grupo                                | IP          | Template                                    |
|---------------|--------------------------------------|-------------|---------------------------------------------|
| Switch-Core-1 | Network/Switches;Core;Datacenter-1  | 10.0.0.1    | Template Net Cisco IOS;Template SNMP       |
| Switch-Core-2 | Network/Switches;Core;Datacenter-1  | 10.0.0.2    | Template Net Cisco IOS;Template SNMP       |
| Router-WAN    | Network/Routers;WAN;Datacenter-1    | 10.0.0.254  | Template Net Cisco IOS;Template Module BGP |
| FW-Primary    | Security/Firewalls;Datacenter-1     | 10.0.0.10   | Template Net Firewall                      |
```

**Comandos:**

```bash
# Verificar o que será alterado
python zabbix_import_excel_v7_4.py --excel padronizacao.xlsx

# Aplicar padronização
python zabbix_import_excel_v7_4.py --excel padronizacao.xlsx --apply
```

**Resultado:**
- Hosts existentes são atualizados (não duplicados)
- Grupos padronizados
- Templates vinculados/atualizados
- Estrutura organizacional consistente

---

## 🎯 Casos de Uso Avançados

### Criar Template Vazio e Configurar Depois

```
| Nome         | Grupo            | IP         | Template            |
|--------------|------------------|------------|---------------------|
| Custom-App-1 | Servidores/Apps  | 10.0.5.10  | Template Custom App |
```

**O que acontece:**
1. Script cria template vazio "Template Custom App" se não existir
2. Host é criado e vinculado ao template vazio
3. Você configura items/triggers no template depois
4. Configurações se propagam automaticamente para os hosts

### Atualizar Apenas Grupos (Manter Templates)

```
| Nome         | Grupo                      | IP         | Template          |
|--------------|----------------------------|------------|-------------------|
| SRV-WEB-01   | Produção;Critical;Web     | 10.0.1.10  | Template OS Linux |
```

- Se o host já existir com templates, os templates são mantidos
- Apenas os grupos são atualizados conforme a planilha

---

## 📊 Análise do Relatório CSV

Após cada execução, analise o arquivo CSV:

```csv
linha_excel;nome;ip;grupos;templates;acao;mensagem
2;SRV-WEB-01;10.0.1.10;Servidores/Linux;Template OS Linux;CREATED;Host não existia; criado
3;SRV-WEB-02;10.0.1.11;Servidores/Linux;Template OS Linux;CREATED;Host não existia; criado
4;SRV-DB-01;10.0.1.20;Servidores/Linux;Template OS Linux;UPDATED;Host existente; atualizado (hostid=10084)
```

**Análise:**
- ✅ Linhas 2-3: Novos hosts criados com sucesso
- ✅ Linha 4: Host existente atualizado (não duplicado)
- ❌ Se houver ERROR: Verificar mensagem de erro

---

## 💡 Dicas Práticas

### 1. Sempre Teste Primeiro
```bash
# SEMPRE execute sem --apply primeiro
python zabbix_import_excel_v7_4.py --excel planilha.xlsx
```

### 2. Mantenha Backup da Planilha
```bash
# Antes de executar
cp planilha.xlsx planilha_backup_20260111.xlsx
```

### 3. Execute em Lotes Pequenos
Para grandes quantidades:
- Divida em lotes de 50-100 hosts
- Facilita troubleshooting
- Reduz impacto em caso de erro

### 4. Documente no Relatório
```bash
# Use nomes descritivos para o relatório
python zabbix_import_excel_v7_4.py \
  --excel planilha.xlsx \
  --report relatorio_migracao_datacenter_2.csv \
  --apply
```

---

## 🔍 Verificação Pós-Importação

### Checklist no Zabbix

1. **Conferir hosts criados:**
   - Configuration → Hosts
   - Filtrar por grupo

2. **Verificar templates vinculados:**
   - Abrir host
   - Aba "Templates"

3. **Testar conectividade:**
   - Monitoring → Latest data
   - Verificar se dados estão sendo coletados

4. **Revisar alertas:**
   - Monitoring → Problems
   - Verificar se não há erros de configuração

---

## 📞 Suporte

Se encontrar problemas com algum cenário, consulte:
- [README.md](README.md) - Documentação completa
- [QUICK_START.md](QUICK_START.md) - Guia rápido
- Issues do GitHub para reportar bugs
