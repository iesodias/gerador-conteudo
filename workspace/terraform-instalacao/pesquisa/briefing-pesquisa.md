# Briefing de Pesquisa — Instalação do Terraform

## 1. O que é Terraform

**Terraform** é uma ferramenta de **Infrastructure as Code (IaC)** desenvolvida pela HashiCorp. Permite definir e provisionar infraestrutura usando código declarativo escrito em **HCL (HashiCorp Configuration Language)**.

**Principais características:**
- Multi-cloud e multi-provider (AWS, Azure, GCP, etc.)
- Plano de execução (preview) antes de aplicar mudanças
- Gerenciamento de estado da infraestrutura
- Código versionável e reutilizável

---

## 2. Pré-requisitos

- Sistema operacional: Linux, macOS ou Windows
- Terminal/shell com acesso à internet
- **Não requer** conta em cloud provider (usaremos provider local)

---

## 3. Métodos de Instalação

### Linux (Ubuntu/Debian - APT)
```bash
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```

### Linux (RHEL/CentOS - YUM)
```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo yum install terraform
```

### macOS (Homebrew)
```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

### Windows (Chocolatey)
```powershell
choco install terraform
```

---

## 4. Comandos Essenciais

| Comando | Descrição |
|---------|-----------|
| `terraform version` | Exibe versão instalada |
| `terraform init` | Inicializa projeto, baixa providers |
| `terraform fmt` | Formata código |
| `terraform validate` | Valida sintaxe |
| `terraform plan` | Preview das mudanças |
| `terraform apply` | Aplica mudanças (cria recursos) |
| `terraform destroy` | Remove todos os recursos |
| `terraform show` | Exibe estado atual |
| `terraform output` | Exibe outputs definidos |

---

## 5. Exemplo de Projeto (Provider Local)

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
  description = "Nome do arquivo a criar"
  type        = string
  default     = "hello-terraform.txt"
}

variable "conteudo" {
  description = "Conteúdo do arquivo"
  type        = string
  default     = "Olá! Arquivo criado pelo Terraform."
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
  description = "ID do recurso"
  value       = local_file.exemplo.id
}
```

---

## 6. Verificação Pós-Instalação

```bash
# Verificar versão
terraform version

# Verificar PATH
which terraform

# Teste rápido
echo 'terraform { required_version = ">= 1.0" }' > test.tf
terraform init
terraform validate
rm -rf .terraform* test.tf
```

---

## 7. Conceitos para Iniciantes

| Conceito | Descrição |
|----------|-----------|
| **IaC** | Gerenciar infraestrutura via código, não UI |
| **HCL** | Linguagem declarativa dos arquivos `.tf` |
| **Provider** | Plugin que conecta Terraform a APIs (AWS, local, etc.) |
| **Resource** | Componente de infra gerenciado (arquivo, VM, rede) |
| **State** | Arquivo `terraform.tfstate` com mapeamento código→recursos |
| **Plan vs Apply** | Plan = preview, Apply = execução real |

---

## 8. Erros Comuns

| Erro | Solução |
|------|---------|
| `command not found` | Verificar instalação e PATH |
| `Failed to query provider` | Verificar internet |
| `Invalid character` | Rodar `terraform fmt` e `validate` |
| `Unsupported version` | Atualizar Terraform |

---

## 9. Boas Práticas

1. Sempre `terraform plan` antes de `apply`
2. Versionar `.tf` no Git (NUNCA `.tfstate`)
3. Usar `.gitignore` para `.terraform/` e `*.tfstate*`
4. Fixar versões de providers
5. Usar variáveis, não valores hardcoded
6. Formatar com `terraform fmt`
7. Organizar: `main.tf`, `variables.tf`, `outputs.tf`, `versions.tf`
