# 🚀 Guia Rápido de Instalação

Este é um guia passo a passo para começar a usar o Zabbix Host Import rapidamente.

## ⚡ Instalação Rápida (5 minutos)

### 1️⃣ Pré-requisitos

```bash
# Verificar versão do Python (deve ser 3.7+)
python --version
# ou
python3 --version
```

### 2️⃣ Baixar o Projeto

```bash
# Clonar o repositório
git clone https://github.com/seu-usuario/zabbix-host-import.git
cd zabbix-host-import

# OU baixar o ZIP e extrair
```

### 3️⃣ Instalar Dependências

```bash
# Windows
pip install -r requirements.txt

# Linux/Mac
pip3 install -r requirements.txt
```

### 4️⃣ Obter Token do Zabbix

1. Acesse seu Zabbix: `http://seu-servidor/zabbix`
2. Vá em **Administration** → **API tokens**
3. Clique em **Create API token**
4. Preencha os dados e copie o token gerado

### 5️⃣ Configurar o Script

Edite `zabbix_import_excel_v7_4.py`:

```python
ZABBIX_URL = "http://192.168.1.100/zabbix/api_jsonrpc.php"  # ← Seu servidor
ZABBIX_TOKEN = "abc123..."  # ← Seu token
```

### 6️⃣ Preparar Planilha Excel

Use o template `zabbix_hosts_template_v2.xlsx` ou crie uma planilha com estas colunas:

| Nome | Grupo | IP | Template |
|------|-------|-----|----------|

**Exemplo:**
```
Nome: Servidor Web 01
Grupo: Servidores/Linux
IP: 192.168.1.10
Template: Template OS Linux
```

### 7️⃣ Testar (Simulação)

```bash
python zabbix_import_excel_v7_4.py --excel sua_planilha.xlsx
```

✅ Revise a saída e o arquivo CSV gerado

### 8️⃣ Executar de Verdade

```bash
python zabbix_import_excel_v7_4.py --excel sua_planilha.xlsx --apply
```

## ✅ Pronto!

Seus hosts devem aparecer no Zabbix agora.

---

## 🆘 Problemas Comuns

### "ModuleNotFoundError: No module named 'openpyxl'"
```bash
pip install openpyxl requests
```

### "Not authorized"
- Verifique se o token está correto
- Confirme permissões do usuário no Zabbix

### "Aba 'Hosts' não encontrada"
- Renomeie a aba da planilha para "Hosts"

---

## 📚 Mais Informações

Leia o [README.md](README.md) completo para documentação detalhada.
