# Briefing de Pesquisa — Instalação do Terraform

## 1. O que é o Terraform

Terraform é uma ferramenta de **Infrastructure as Code (IaC)** criada pela HashiCorp. Permite definir, provisionar e gerenciar infraestrutura de forma declarativa usando a linguagem **HCL (HashiCorp Configuration Language)**.

**Quando usar:**
- Provisionar infraestrutura em cloud (AWS, Azure, GCP)
- Gerenciar recursos de rede, DNS, bancos de dados
- Automatizar criação de ambientes (dev, staging, produção)
- Documentar infraestrutura como código versionável

---

## 2. Pré-requisitos

- Sistema operacional: Linux, macOS ou Windows
- Terminal/shell com acesso à internet
- Nenhuma conta de cloud necessária para este lab inicial (usaremos o provider `local`)

---

## 3. Métodos de Instalação

### Via APT (Ubuntu/Debian)
```bash
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```

### Via YUM/DNF (RHEL/CentOS/Fedora)
```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo yum -y install terraform
```

### Via Homebrew (macOS)
```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

### Via tfenv (gerenciador de versões)
```bash
git clone https://github.com/tfutils/tfenv.git ~/.tfenv
echo 'export PATH="$HOME/.tfenv/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
tfenv install latest
tfenv use latest
```

---

## 4. Comandos Essenciais

| Comando | Descrição |
|---------|-----------|
| `terraform version` | Exibe a versão instalada |
| `terraform init` | Inicializa o projeto, baixa providers |
| `terraform fmt` | Formata arquivos .tf |
| `terraform validate` | Valida a sintaxe dos arquivos |
| `terraform plan` | Mostra o que será criado/alterado/destruído |
| `terraform apply` | Aplica as mudanças (cria/altera recursos) |
| `terraform destroy` | Remove todos os recursos gerenciados |

---

## 5. Estrutura de Arquivo .tf Básico (Provider Local)

### versions.tf
```hcl
terraform {
  required_version = ">= 1.9.0"
  required_providers {
    local = {
      source  = "hashicorp/local"
      version = "~> 2.5"
    }
  }
}
```

### variables.tf
```hcl
variable "nome_arquivo" {
  description = "Nome do arquivo a ser criado"
  type        = string
  default     = "hello-terraform.txt"
}

variable "conteudo" {
  description = "Conteúdo do arquivo"
  type        = string
  default     = "Olá! Este arquivo foi criado pelo Terraform."
}
```

### main.tf
```hcl
resource "local_file" "exemplo" {
  content  = var.conteudo
  filename = "${path.module}/${var.nome_arquivo}"
}
```

### outputs.tf
```hcl
output "caminho_arquivo" {
  description = "Caminho do arquivo criado"
  value       = local_file.exemplo.filename
}

output "id_recurso" {
  description = "ID do recurso criado"
  value       = local_file.exemplo.id
}
```

---

## 6. Conceitos para Iniciantes

| Conceito | Descrição |
|----------|-----------|
| **IaC** | Gerenciar infraestrutura através de código, não cliques manuais |
| **HCL** | Linguagem declarativa da HashiCorp usada nos arquivos `.tf` |
| **Provider** | Plugin que conecta o Terraform a uma API (AWS, Azure, local, etc.) |
| **Resource** | Componente de infraestrutura gerenciado (arquivo, VM, rede, etc.) |
| **State** | Arquivo `terraform.tfstate` que mapeia código para recursos reais |
| **Plan** | Visualização prévia das mudanças antes de aplicar |
| **Apply** | Execução real das mudanças na infraestrutura |
| **Destroy** | Remoção de todos os recursos gerenciados pelo Terraform |

---

## 7. Erros Comuns

| Erro | Causa | Solução |
|------|-------|---------|
| `terraform: command not found` | Binário não está no PATH | Verificar instalação e PATH |
| `Failed to query available provider packages` | Sem internet | Verificar conectividade |
| `Error: Invalid character` | Erro de sintaxe no HCL | Rodar `terraform fmt` e `terraform validate` |
| `Error: Unsupported Terraform Core version` | Versão incompatível | Atualizar Terraform |

---

## 8. Boas Práticas

1. Sempre rode `terraform plan` antes de `terraform apply`
2. Versione os arquivos .tf no Git (mas NUNCA o `.tfstate`)
3. Use `.gitignore` para excluir `.terraform/`, `*.tfstate*`
4. Fixe versões dos providers no `versions.tf`
5. Use variáveis em vez de valores hardcoded
6. Formate o código com `terraform fmt`
7. Organize em arquivos separados: `main.tf`, `variables.tf`, `outputs.tf`, `versions.tf`
