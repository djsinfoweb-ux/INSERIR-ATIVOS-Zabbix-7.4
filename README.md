# Zabbix Host Import - v7.4

Script Python para importação em massa de hosts no Zabbix 7.4+ a partir de planilhas Excel.

## 📋 Índice

- [Sobre o Projeto](#sobre-o-projeto)
- [Funcionalidades](#funcionalidades)
- [Requisitos](#requisitos)
- [Instalação](#instalação)
- [Configuração](#configuração)
- [Como Usar](#como-usar)
- [Estrutura do Excel](#estrutura-do-excel)
- [Exemplos de Uso](#exemplos-de-uso)
- [Relatório de Execução](#relatório-de-execução)
- [Troubleshooting](#troubleshooting)
- [Compatibilidade](#compatibilidade)

---

## 🎯 Sobre o Projeto

Este script automatiza o processo de criação e atualização de hosts no Zabbix 7.4+ através da API, utilizando planilhas Excel como fonte de dados. É especialmente útil para:

- Importação em massa de novos ativos
- Atualização de hosts existentes
- Padronização de configurações
- Migração de ambientes
- Documentação automatizada

## ✨ Funcionalidades

### Principais Recursos

- ✅ **Modo DRY-RUN**: Simula a execução sem fazer alterações no Zabbix
- ✅ **Criação Automática**: Cria hosts, grupos e templates que não existem
- ✅ **Atualização Inteligente**: Atualiza hosts existentes sem duplicação
- ✅ **Múltiplos Grupos**: Suporta múltiplos grupos e templates por host (separados por `;`)
- ✅ **Relatório CSV**: Gera relatório detalhado de cada operação
- ✅ **Compatível com Zabbix 7.4+**: Usa as novas APIs de Template Groups e Host Groups
- ✅ **Tratamento de Erros**: Continua a execução mesmo com erros individuais

### Comportamento do Script

1. **Busca por Host Existente**:
   - Primeiro por nome visível (name)
   - Depois por nome técnico (host)
   - Por último por endereço IP

2. **Criação Automática de Recursos**:
   - Host Groups (se não existirem)
   - Template Groups (se não existirem)
   - Templates vazios (se não existirem)

3. **Atualização de Hosts**:
   - Atualiza IP da interface principal
   - Ajusta grupos associados
   - Vincula/desvincula templates
   - Opcionalmente atualiza nomes

## 📦 Requisitos

### Sistema Operacional
- Windows, Linux ou macOS
- Python 3.7 ou superior

### Dependências Python

```bash
pip install requests openpyxl
```

### Zabbix
- Zabbix Server 7.4 ou superior
- Token de API com as seguintes permissões:
  - `host.get`, `host.create`, `host.update`
  - `hostgroup.get`, `hostgroup.create`
  - `template.get`, `template.create`
  - `templategroup.get`, `templategroup.create`
  - `hostinterface.get`, `hostinterface.create`, `hostinterface.update`

## 🔧 Instalação

### 1. Clone o Repositório

```bash
git clone https://github.com/seu-usuario/zabbix-host-import.git
cd zabbix-host-import
```

### 2. Instale as Dependências

```bash
pip install -r requirements.txt
```

Ou manualmente:

```bash
pip install requests openpyxl
```

### 3. Estrutura de Arquivos

```
zabbix-host-import/
├── zabbix_import_excel_v7_4.py    # Script principal
├── zabbix_hosts_template_v2.xlsx  # Template da planilha
├── zabbix_hosts.xlsx              # Exemplo preenchido
├── requirements.txt               # Dependências Python
└── README.md                      # Esta documentação
```

## ⚙️ Configuração

### 1. Obter Token de API do Zabbix

Acesse o Zabbix via interface web:

1. Vá em **Administration** → **API tokens**
2. Clique em **Create API token**
3. Preencha:
   - **Name**: `Host Import Script`
   - **User**: Seu usuário com permissões adequadas
   - **Description**: Token para importação de hosts
4. Copie o token gerado

### 2. Configurar o Script

Edite o arquivo `zabbix_import_excel_v7_4.py` e configure:

```python
# =========================
# CONFIGURE AQUI
# =========================
ZABBIX_URL = "http://seu-servidor.com/zabbix/api_jsonrpc.php"
ZABBIX_TOKEN = "seu_token_aqui"

DEFAULT_EXCEL_NAME = "zabbix_hosts_template_v2.xlsx"
SHEET_NAME = "Hosts"

DEFAULT_TEMPLATE_GROUP = "Templates/Auto"
AGENT_PORT = "10050"
CONTINUE_ON_ERROR = True

# Atualizar nome visível ao fazer update
UPDATE_VISIBLE_NAME = True

# Atualizar nome técnico ao fazer update (cuidado!)
UPDATE_TECHNICAL_HOSTNAME = False
```

#### ⚠️ Configurações Importantes

- **UPDATE_VISIBLE_NAME**: Define se o nome visível do host será atualizado
- **UPDATE_TECHNICAL_HOSTNAME**: ⚠️ **CUIDADO** - Alterar o nome técnico pode causar problemas. Mantenha `False` em ambientes de produção.
- **CONTINUE_ON_ERROR**: Se `True`, continua processando mesmo com erros em linhas individuais

## 📊 Estrutura do Excel

### Planilha "Hosts"

A planilha deve ter **4 colunas** na seguinte ordem:

| Nome | Grupo | IP | Template |
|------|-------|-----|----------|
| Nome visível do host | Grupo(s) do host | Endereço IP | Template(s) |

### Formato das Colunas

#### 1. **Nome** (obrigatório)
- Nome visível que aparece no Zabbix
- Exemplo: `Servidor Web 01`, `Switch Core`, `Router Filial`

#### 2. **Grupo** (obrigatório)
- Nome do grupo de hosts
- Para múltiplos grupos, separar com `;`
- Exemplos:
  - `Servidores/Linux`
  - `Servidores/Linux;Produção;Web Servers`

#### 3. **IP** (obrigatório)
- Endereço IP do host
- Formato: `xxx.xxx.xxx.xxx`
- Exemplo: `192.168.1.10`

#### 4. **Template** (obrigatório)
- Nome do template a ser vinculado
- Para múltiplos templates, separar com `;`
- Exemplos:
  - `Template OS Linux`
  - `Template OS Linux;Template App Apache;Template Module ICMP Ping`

### 📝 Exemplo de Planilha

```
| Nome              | Grupo               | IP             | Template                  |
|-------------------|---------------------|----------------|---------------------------|
| Servidor Web 01   | Servidores/Linux    | 192.168.1.10   | Template OS Linux         |
| Servidor DB 01    | Servidores/Database | 192.168.1.20   | Template OS Linux         |
| Switch Core       | Network/Switches    | 192.168.1.254  | Template Net Cisco IOS    |
| Firewall Principal| Security            | 192.168.1.1    | Template Net Firewall     |
```

### 🔍 Regras Importantes

1. **A primeira linha é cabeçalho** - será ignorada pelo script
2. **Linhas vazias são ignoradas** automaticamente
3. **Todos os campos são obrigatórios** (exceto em linhas vazias)
4. **Nomes técnicos são gerados automaticamente** a partir do nome visível
   - Espaços são substituídos por `_`
   - Caracteres especiais são substituídos por `_`
   - Exemplo: `Servidor Web 01` → `Servidor_Web_01`

## 🚀 Como Usar

### Modo Básico (DRY-RUN - Simulação)

Por padrão, o script executa em **modo de simulação**, não fazendo alterações reais:

```bash
python zabbix_import_excel_v7_4.py --excel caminho/para/planilha.xlsx
```

#### Exemplo:

```bash
python zabbix_import_excel_v7_4.py --excel "C:\Temp\zabbix_hosts.xlsx"
```

**Saída esperada:**
```
[INFO] Zabbix Import Tool - Versão 7.4
[INFO] DRY_RUN=True
[INFO] Excel: C:\Temp\zabbix_hosts.xlsx
[INFO] Report CSV: C:\Temp\zabbix_import_report_20260111_143022.csv
[INFO] Template Group Padrão: Templates/Auto
==================================================================================
Excel linha 2: Nome=Servidor Web 01 | Grupo=Servidores/Linux | IP=192.168.1.10 | Template=Template OS Linux
[DRY] Criaria host group: Servidores/Linux
[DRY] Criaria template group: Templates/Auto
[DRY] Criaria template VAZIO: name='Template OS Linux' host='Template_OS_Linux' no template group 'Templates/Auto'
[CRIAR] Servidor Web 01 (192.168.1.10) | Grupos=['Servidores/Linux'] | Templates=['Template OS Linux']
[DRY] host.create params: {"host": "Servidor_Web_01", "name": "Servidor Web 01", ...}
```

### Modo de Execução Real (--apply)

Para **aplicar as alterações realmente** no Zabbix:

```bash
python zabbix_import_excel_v7_4.py --excel caminho/para/planilha.xlsx --apply
```

#### Exemplo:

```bash
python zabbix_import_excel_v7_4.py --excel "C:\Temp\zabbix_hosts.xlsx" --apply
```

**⚠️ ATENÇÃO**: Com `--apply`, as alterações serão feitas no servidor Zabbix!

### Especificar Arquivo de Relatório Customizado

```bash
python zabbix_import_excel_v7_4.py --excel planilha.xlsx --report /tmp/meu_relatorio.csv --apply
```

### Arquivo Excel no Mesmo Diretório

Se o arquivo Excel estiver no mesmo diretório do script:

```bash
# Usa o arquivo padrão: zabbix_hosts_template_v2.xlsx
python zabbix_import_excel_v7_4.py

# Ou especifica outro arquivo no mesmo diretório
python zabbix_import_excel_v7_4.py --excel minha_planilha.xlsx
```

## 📊 Relatório de Execução

### Arquivo CSV Gerado

Após cada execução, um relatório CSV é gerado automaticamente com as seguintes informações:

**Nome padrão**: `zabbix_import_report_YYYYMMDD_HHMMSS.csv`

**Local**: Mesmo diretório da planilha Excel (ou conforme especificado com `--report`)

### Estrutura do Relatório

| Campo        | Descrição                                |
|--------------|------------------------------------------|
| linha_excel  | Número da linha na planilha             |
| nome         | Nome do host                            |
| ip           | Endereço IP                             |
| grupos       | Grupos configurados                     |
| templates    | Templates configurados                  |
| acao         | Ação executada                          |
| mensagem     | Detalhes da operação                    |

### Possíveis Ações

| Ação          | Significado                                    |
|---------------|------------------------------------------------|
| WOULD_CREATE  | Host seria criado (modo DRY-RUN)              |
| CREATED       | Host criado com sucesso                        |
| WOULD_UPDATE  | Host seria atualizado (modo DRY-RUN)          |
| UPDATED       | Host atualizado com sucesso                    |
| ERROR         | Erro ao processar a linha                      |

### Exemplo de Relatório CSV

```csv
linha_excel;nome;ip;grupos;templates;acao;mensagem
2;Servidor Web 01;192.168.1.10;Servidores/Linux;Template OS Linux;CREATED;Host não existia; criado (name='Servidor Web 01', host='Servidor_Web_01')
3;Servidor DB 01;192.168.1.20;Servidores/Database;Template OS Linux;UPDATED;Host existente (match por nome); atualizado (hostid=10084)
4;Switch Core;192.168.1.254;Network/Switches;Template Net Cisco IOS;ERROR;Linha inválida: 'Nome' e 'IP' são obrigatórios.
```

## 🔍 Exemplos de Uso Completos

### Exemplo 1: Primeira Importação (Teste)

```bash
# 1. Preparar a planilha com os dados
# 2. Testar em modo DRY-RUN
python zabbix_import_excel_v7_4.py --excel meus_hosts.xlsx

# 3. Verificar o relatório gerado
# 4. Se tudo estiver OK, executar de verdade
python zabbix_import_excel_v7_4.py --excel meus_hosts.xlsx --apply
```

### Exemplo 2: Atualização de Hosts Existentes

```bash
# Atualizar IPs e templates de hosts já cadastrados
python zabbix_import_excel_v7_4.py --excel atualizacao_ips.xlsx --apply
```

### Exemplo 3: Importação com Múltiplos Grupos e Templates

**Planilha:**
```
| Nome              | Grupo                              | IP            | Template                                    |
|-------------------|------------------------------------|---------------|---------------------------------------------|
| Servidor App 01   | Servidores/Linux;Produção;WebApps | 192.168.1.30  | Template OS Linux;Template App Apache       |
```

**Comando:**
```bash
python zabbix_import_excel_v7_4.py --excel hosts_multiplos.xlsx --apply
```

### Exemplo 4: Ambiente Windows

```cmd
# Modo teste
python zabbix_import_excel_v7_4.py --excel "C:\Zabbix\hosts.xlsx"

# Aplicar alterações
python zabbix_import_excel_v7_4.py --excel "C:\Zabbix\hosts.xlsx" --apply

# Com relatório customizado
python zabbix_import_excel_v7_4.py --excel "C:\Zabbix\hosts.xlsx" --report "C:\Logs\relatorio.csv" --apply
```

### Exemplo 5: Ambiente Linux/Mac

```bash
# Modo teste
python3 zabbix_import_excel_v7_4.py --excel /opt/zabbix/hosts.xlsx

# Aplicar alterações
python3 zabbix_import_excel_v7_4.py --excel /opt/zabbix/hosts.xlsx --apply

# Com relatório customizado
python3 zabbix_import_excel_v7_4.py --excel ~/hosts.xlsx --report ~/logs/report.csv --apply
```

## 🔧 Troubleshooting

### Problema: "Falha HTTP ao chamar ..."

**Causa**: Erro de conexão com o servidor Zabbix

**Solução**:
1. Verifique se a URL do Zabbix está correta no script
2. Confirme que o servidor está acessível
3. Teste a URL no navegador: `http://seu-servidor/zabbix/api_jsonrpc.php`

### Problema: "Erro na API ... Not authorized"

**Causa**: Token inválido ou sem permissões

**Solução**:
1. Gere um novo token no Zabbix
2. Verifique se o usuário tem as permissões necessárias
3. Confirme se o token foi copiado corretamente para o script

### Problema: "Aba 'Hosts' não encontrada"

**Causa**: Nome da aba na planilha está diferente

**Solução**:
1. Renomeie a aba para "Hosts" (padrão)
2. OU altere a variável `SHEET_NAME` no script

### Problema: "Linha inválida: ... é obrigatório"

**Causa**: Campos obrigatórios vazios na planilha

**Solução**:
1. Verifique se todos os campos estão preenchidos
2. Confirme que não há linhas parcialmente preenchidas
3. Remova linhas vazias entre os dados

### Problema: Hosts duplicados sendo criados

**Causa**: Script não encontra o host existente

**Solução**:
1. Verifique se o nome na planilha corresponde exatamente ao nome no Zabbix
2. Use o mesmo IP para que o script encontre por IP
3. Verifique o relatório CSV para entender o que aconteceu

### Problema: "ModuleNotFoundError: No module named 'openpyxl'"

**Causa**: Dependência não instalada

**Solução**:
```bash
pip install openpyxl requests
```

### Problema: Template não está sendo vinculado

**Causa**: Template pode não existir no Zabbix

**Verificação**:
1. Execute em modo DRY-RUN primeiro
2. Veja se aparece a mensagem "[DRY] Criaria template VAZIO"
3. Se sim, o template será criado vazio - você precisa configurá-lo manualmente no Zabbix

**Solução**:
- Crie o template manualmente no Zabbix antes
- OU deixe o script criar um template vazio e configure depois

## 📋 Compatibilidade

### Versões do Zabbix

| Versão | Compatível | Observações |
|--------|------------|-------------|
| 7.4+   | ✅ Sim     | Totalmente compatível |
| 7.0-7.3| ⚠️ Parcial | Pode requerer ajustes na API |
| 6.x    | ❌ Não     | APIs diferentes |
| 5.x    | ❌ Não     | APIs diferentes |

### Versões do Python

| Versão | Compatível |
|--------|------------|
| 3.11+  | ✅ Sim     |
| 3.7-3.10| ✅ Sim    |
| 3.6    | ⚠️ Pode funcionar |
| 2.x    | ❌ Não     |

## 🤝 Contribuindo

Contribuições são bem-vindas! Para contribuir:

1. Fork o projeto
2. Crie uma branch para sua feature (`git checkout -b feature/NovaFuncionalidade`)
3. Commit suas mudanças (`git commit -m 'Adiciona nova funcionalidade'`)
4. Push para a branch (`git push origin feature/NovaFuncionalidade`)
5. Abra um Pull Request

## 📝 Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.

## 📧 Suporte

Para reportar problemas ou sugerir melhorias:
- Abra uma [Issue](https://github.com/seu-usuario/zabbix-host-import/issues)
- Entre em contato: seu-email@example.com

## 🔄 Changelog

### v7.4 (2025-01-11)
- ✅ Compatibilidade total com Zabbix 7.4+
- ✅ Suporte a Template Groups e Host Groups separados
- ✅ Modo DRY-RUN por padrão
- ✅ Relatório CSV detalhado
- ✅ Criação automática de grupos e templates
- ✅ Atualização inteligente de hosts existentes

## 🙏 Agradecimentos

- Equipe Zabbix pelo excelente sistema de monitoramento
- Comunidade open source pelas contribuições e feedback

---

**Desenvolvido com ❤️ para automação de infraestrutura**
