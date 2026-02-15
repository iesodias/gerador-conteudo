# Lab 01 – Instalar o Terraform e criar infraestrutura local

## Objetivo

* Instalar o Terraform no ambiente local
* Criar um projeto Terraform com provider local
* Executar o ciclo completo: init, plan, apply, destroy
* Compreender os fundamentos de Infrastructure as Code
* Limpar todos os recursos criados

---

## Pré-requisitos

- [Terraform](https://developer.hashicorp.com/terraform/install) — será instalado no lab
- Terminal com acesso à internet
- Sistema: Linux (Ubuntu/Debian) ou macOS
- Editor: [VS Code](https://code.visualstudio.com/), vim ou nano

---

## Estrutura de Diretórios

```bash
~/workshop/lab01-terraform/
├── versions.tf
├── variables.tf
├── main.tf
└── outputs.tf
```

---

## Passo a Passo

### 1. Criar diretório do lab

```bash
mkdir -p ~/workshop/lab01-terraform && cd ~/workshop/lab01-terraform
```

---

### 2. Instalar o Terraform

**Linux (Ubuntu/Debian):**

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```

**macOS (Homebrew):**

```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

Verificar instalação:

```bash
terraform version
```

Saída esperada:

```
Terraform v1.10.3
on linux_amd64
```

---

### 3. Criar arquivos do projeto

**versions.tf:**

```bash
cat <<'EOF' > versions.tf
terraform {
  required_version = ">= 1.9.0"
  
  required_providers {
    local = {
      source  = "hashicorp/local"
      version = "~> 2.5"
    }
  }
}
EOF
```

**variables.tf:**

```bash
cat <<'EOF' > variables.tf
variable "nome_arquivo" {
  description = "Nome do arquivo"
  type        = string
  default     = "hello-terraform.txt"
}

variable "conteudo" {
  description = "Conteúdo do arquivo"
  type        = string
  default     = "Olá! Arquivo criado pelo Terraform."
}
EOF
```

**main.tf:**

```bash
cat <<'EOF' > main.tf
resource "local_file" "exemplo" {
  content  = var.conteudo
  filename = "${path.module}/${var.nome_arquivo}"
}
EOF
```

**outputs.tf:**

```bash
cat <<'EOF' > outputs.tf
output "caminho_arquivo" {
  description = "Caminho do arquivo criado"
  value       = local_file.exemplo.filename
}

output "id_recurso" {
  description = "ID do recurso"
  value       = local_file.exemplo.id
}
EOF
```

> **Nota:** O provider `local` permite criar recursos na máquina local sem necessidade de conta cloud.

---

### 4. Inicializar o projeto

```bash
terraform init
```

Saída esperada:

```
Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/local versions matching "~> 2.5"...
- Installing hashicorp/local v2.5.2...
- Installed hashicorp/local v2.5.2 (signed by HashiCorp)

Terraform has been successfully initialized!
```

> **Nota:** O `init` baixa os providers e prepara o ambiente.

---

### 5. Validar e formatar

```bash
terraform fmt
terraform validate
```

Saída esperada:

```
Success! The configuration is valid.
```

---

### 6. Visualizar o plano

```bash
terraform plan
```

Saída esperada:

```
Terraform will perform the following actions:

  # local_file.exemplo will be created
  + resource "local_file" "exemplo" {
      + content  = "Olá! Arquivo criado pelo Terraform."
      + filename = "./hello-terraform.txt"
      + id       = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```

> **Nota:** O símbolo `+` indica criação. `~` = alteração, `-` = destruição.

---

### 7. Aplicar as mudanças

```bash
terraform apply -auto-approve
```

Saída esperada:

```
local_file.exemplo: Creating...
local_file.exemplo: Creation complete after 0s [id=<HASH>]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

caminho_arquivo = "./hello-terraform.txt"
id_recurso = "<HASH>"
```

---

### 8. Verificar o recurso criado

```bash
cat hello-terraform.txt
```

Saída esperada:

```
Olá! Arquivo criado pelo Terraform.
```

Listar arquivos:

```bash
ls -la
```

> **Nota:** O arquivo `terraform.tfstate` mapeia código para recursos reais. Nunca edite manualmente.

---

### 9. Consultar estado e outputs

```bash
terraform show
terraform output
```

Saída de `terraform output`:

```
caminho_arquivo = "./hello-terraform.txt"
id_recurso = "<HASH>"
```

---

### 10. Destruir recursos

```bash
terraform destroy -auto-approve
```

Saída esperada:

```
local_file.exemplo: Destroying... [id=<HASH>]
local_file.exemplo: Destruction complete after 0s

Destroy complete! Resources: 1 destroyed.
```

Confirmar remoção:

```bash
ls hello-terraform.txt
```

Saída esperada:

```
ls: cannot access 'hello-terraform.txt': No such file or directory
```

---

### 11. Limpeza do lab

```bash
cd ~ && rm -rf ~/workshop/lab01-terraform
```

---

## Resultado Esperado

* Terraform instalado e funcional
* Projeto completo com 4 arquivos `.tf`
* Fluxo executado: init → fmt → validate → plan → apply → show → output → destroy
* Arquivo criado e destruído via Terraform
* Todos os recursos removidos

---

## Dicas para Discussão

| Conceito | Descrição |
|----------|-----------|
| **IaC** | Infraestrutura como código, versionável e reproduzível |
| **HCL** | Linguagem dos arquivos `.tf` |
| **Provider** | Plugin que conecta Terraform a APIs |
| **Resource** | Componente de infraestrutura gerenciado |
| **State** | Arquivo que mapeia código para recursos |
| **Plan vs Apply** | Plan = preview, Apply = execução |
