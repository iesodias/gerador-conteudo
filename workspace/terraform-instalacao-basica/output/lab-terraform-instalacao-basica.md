# Lab 01 – Instalar o Terraform e Provisionar um Recurso Local

## Objetivo

- Instalar o Terraform em ambiente Linux ou macOS
- Verificar a instalação e compreender a estrutura de um projeto Terraform
- Criar arquivos de configuração HCL (versions.tf, variables.tf, main.tf, outputs.tf)
- Executar o fluxo completo: init → fmt → validate → plan → apply
- Inspecionar o estado da infraestrutura com show e output
- Destruir os recursos provisionados e limpar o ambiente do lab

## Pré-requisitos

| Ferramenta | Descrição | Link |
|---|---|---|
| Terminal | Bash ou Zsh | Nativo do SO |
| Homebrew (macOS) | Gerenciador de pacotes para macOS | [https://brew.sh](https://brew.sh) |
| APT (Linux) | Gerenciador de pacotes para Ubuntu/Debian | Nativo do SO |
| wget (Linux) | Utilitário para download via linha de comando | `sudo apt install wget` |
| Editor de texto | VS Code, Vim ou Nano | [https://code.visualstudio.com](https://code.visualstudio.com) |

Verifique se você possui acesso ao terminal:

```bash
echo "Terminal funcionando!"
```

## Estrutura de Diretórios

```
terraform-lab-01/
├── versions.tf
├── variables.tf
├── main.tf
└── outputs.tf
```

Após o `terraform init`, a estrutura ficará assim:

```
terraform-lab-01/
├── .terraform/
│   └── providers/
│       └── registry.terraform.io/
│           └── hashicorp/
│               └── local/
├── .terraform.lock.hcl
├── versions.tf
├── variables.tf
├── main.tf
└── outputs.tf
```

## Passo a Passo

### Passo 1 – Criar o diretório do lab

Crie um diretório exclusivo para este laboratório e acesse-o:

```bash
mkdir -p terraform-lab-01
cd terraform-lab-01
```

Saída esperada:

```
(sem saída — o diretório foi criado silenciosamente)
```

> **Nota:** Sempre crie um diretório separado para cada projeto Terraform. O Terraform gerencia o estado localmente dentro do diretório do projeto.

---

### Passo 2 – Instalar o Terraform

Escolha **uma** das opções abaixo conforme seu sistema operacional.

**Opção A – Linux (Ubuntu/Debian via APT):**

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```

Saída esperada (resumo):

```
Hit:1 http://archive.ubuntu.com/ubuntu jammy InRelease
Get:2 https://apt.releases.hashicorp.com jammy InRelease
Reading package lists... Done
Building dependency tree... Done
The following NEW packages will be installed:
  terraform
Setting up terraform (1.10.3-1) ...
```

**Opção B – macOS (Homebrew):**

```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

Saída esperada (resumo):

```
==> Tapping hashicorp/tap
Cloning into '/opt/homebrew/Library/Taps/hashicorp/homebrew-tap'...
==> Installing terraform from hashicorp/tap
==> Downloading https://releases.hashicorp.com/terraform/1.10.3/terraform_1.10.3_darwin_arm64.zip
==> Installing hashicorp/tap/terraform
🍺  /opt/homebrew/Cellar/terraform/1.10.3: 3 files, 94.6MB, built in 10 seconds
```

> **Nota:** O Terraform é um binário único. O gerenciador de pacotes cuida de colocá-lo no PATH do sistema.

---

### Passo 3 – Verificar a instalação

Confirme que o Terraform foi instalado corretamente:

```bash
terraform version
```

Saída esperada:

```
Terraform v1.10.3
on linux_amd64
```

> **Nota:** A versão exata pode variar. O importante é que o comando retorne sem erros.

Verifique também os comandos disponíveis:

```bash
terraform -help
```

Saída esperada (resumo):

```
Usage: terraform [global options] <subcommand> [args]

The available commands for execution are listed below.

Main commands:
  init          Prepare your working directory for other commands
  validate      Check whether the configuration is valid
  plan          Show changes required by the current configuration
  apply         Create or update infrastructure
  destroy       Destroy previously-created infrastructure

All other commands:
  fmt           Reformat your configuration in the standard style
  output        Show output values from your root module
  show          Show the current state or a saved plan
  version       Show the current Terraform version
```

---

### Passo 4 – Criar os arquivos do projeto Terraform

Crie os quatro arquivos de configuração dentro do diretório `terraform-lab-01`.

**Arquivo 1 – versions.tf:**

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

```bash
cat > versions.tf <<'EOF'
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

**Arquivo 2 – variables.tf:**

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

```bash
cat > variables.tf <<'EOF'
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
EOF
```

**Arquivo 3 – main.tf:**

```hcl
resource "local_file" "exemplo" {
  content  = var.conteudo
  filename = "${path.module}/${var.nome_arquivo}"
}
```

```bash
cat > main.tf <<'EOF'
resource "local_file" "exemplo" {
  content  = var.conteudo
  filename = "${path.module}/${var.nome_arquivo}"
}
EOF
```

**Arquivo 4 – outputs.tf:**

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

```bash
cat > outputs.tf <<'EOF'
output "caminho_arquivo" {
  description = "Caminho do arquivo criado"
  value       = local_file.exemplo.filename
}

output "id_recurso" {
  description = "ID do recurso criado"
  value       = local_file.exemplo.id
}
EOF
```

Verifique que os quatro arquivos foram criados:

```bash
ls -la *.tf
```

Saída esperada:

```
-rw-r--r--  1 usuario  staff  108 14 fev 10:00 main.tf
-rw-r--r--  1 usuario  staff  185 14 fev 10:00 outputs.tf
-rw-r--r--  1 usuario  staff  230 14 fev 10:00 variables.tf
-rw-r--r--  1 usuario  staff  160 14 fev 10:00 versions.tf
```

> **Nota:** O provider `hashicorp/local` permite criar recursos no sistema de arquivos local, sem necessidade de credenciais de nuvem. Ideal para aprendizado.

---

### Passo 5 – Inicializar o projeto com terraform init

O comando `init` baixa os providers declarados em `versions.tf` e prepara o diretório de trabalho:

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
Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure.
```

Verifique os arquivos gerados pelo init:

```bash
ls -la .terraform/
ls -la .terraform.lock.hcl
```

Saída esperada:

```
drwxr-xr-x  3 usuario  staff  96 14 fev 10:01 providers

-rw-r--r--  1 usuario  staff  1234 14 fev 10:01 .terraform.lock.hcl
```

> **Nota:** O diretório `.terraform/` contém os binários dos providers baixados. O arquivo `.terraform.lock.hcl` trava as versões exatas para garantir reprodutibilidade.

---

### Passo 6 – Formatar e validar a configuração

**Formatar os arquivos HCL:**

O comando `fmt` padroniza a indentação e o estilo dos arquivos `.tf`:

```bash
terraform fmt
```

Saída esperada:

```
(sem saída — os arquivos já estão formatados corretamente)
```

> **Nota:** Se algum arquivo fosse reformatado, o nome dele apareceria na saída. Neste lab os arquivos já estão no padrão.

**Validar a configuração:**

O comando `validate` verifica se a sintaxe e a lógica da configuração estão corretas:

```bash
terraform validate
```

Saída esperada:

```
Success! The configuration is valid.
```

> **Nota:** O `validate` não acessa nenhuma API ou provider remoto. Ele faz apenas análise estática do código HCL.

---

### Passo 7 – Planejar a execução com terraform plan

O comando `plan` mostra o que o Terraform **pretende fazer** sem executar nenhuma alteração:

```bash
terraform plan
```

Saída esperada:

```
Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # local_file.exemplo will be created
  + resource "local_file" "exemplo" {
      + content              = "Olá! Este arquivo foi criado pelo Terraform."
      + content_base64sha256 = (known after apply)
      + content_base64sha512 = (known after apply)
      + content_md5          = (known after apply)
      + content_sha1         = (known after apply)
      + content_sha256       = (known after apply)
      + content_sha512       = (known after apply)
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "./hello-terraform.txt"
      + id                   = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + caminho_arquivo = "./hello-terraform.txt"
  + id_recurso      = (known after apply)
```

> **Nota:** O símbolo `+` indica que um recurso será **criado**. O `plan` é uma etapa de segurança — sempre revise o plano antes de aplicar.

---

### Passo 8 – Aplicar a configuração com terraform apply

O comando `apply` executa as alterações planejadas. Use a flag `-auto-approve` para pular a confirmação interativa:

```bash
terraform apply -auto-approve
```

Saída esperada:

```
Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # local_file.exemplo will be created
  + resource "local_file" "exemplo" {
      + content              = "Olá! Este arquivo foi criado pelo Terraform."
      + content_base64sha256 = (known after apply)
      + content_base64sha512 = (known after apply)
      + content_md5          = (known after apply)
      + content_sha1         = (known after apply)
      + content_sha256       = (known after apply)
      + content_sha512       = (known after apply)
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "./hello-terraform.txt"
      + id                   = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + caminho_arquivo = "./hello-terraform.txt"
  + id_recurso      = (known after apply)

local_file.exemplo: Creating...
local_file.exemplo: Creation complete after 0s [id=a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

caminho_arquivo = "./hello-terraform.txt"
id_recurso = "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
```

> **Nota:** O `terraform.tfstate` foi criado automaticamente. Ele armazena o estado atual da infraestrutura gerenciada pelo Terraform. **Nunca edite este arquivo manualmente.**

---

### Passo 9 – Verificar o recurso criado

Confirme que o arquivo foi criado pelo Terraform:

```bash
ls -la hello-terraform.txt
```

Saída esperada:

```
-rwxr-xr-x  1 usuario  staff  46 14 fev 10:02 hello-terraform.txt
```

Exiba o conteúdo do arquivo:

```bash
cat hello-terraform.txt
```

Saída esperada:

```
Olá! Este arquivo foi criado pelo Terraform.
```

> **Nota:** O Terraform criou o arquivo com o conteúdo definido na variável `conteudo` e o nome definido na variável `nome_arquivo`.

---

### Passo 10 – Inspecionar o estado com show e output

**Visualizar o estado completo:**

```bash
terraform show
```

Saída esperada:

```
# local_file.exemplo:
resource "local_file" "exemplo" {
    content              = "Olá! Este arquivo foi criado pelo Terraform."
    content_base64sha256 = "abc123def456..."
    content_base64sha512 = "xyz789ghi012..."
    content_md5          = "d41d8cd98f00b204e9800998ecf8427e"
    content_sha1         = "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
    content_sha256       = "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
    content_sha512       = "cf83e1357eefb8bdf1542850d66d8007d620e4050b5715dc83f4a921d36ce9ce..."
    directory_permission = "0777"
    file_permission      = "0777"
    filename             = "./hello-terraform.txt"
    id                   = "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
}

Outputs:

caminho_arquivo = "./hello-terraform.txt"
id_recurso = "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
```

**Consultar outputs específicos:**

```bash
terraform output
```

Saída esperada:

```
caminho_arquivo = "./hello-terraform.txt"
id_recurso = "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
```

Consultar um output isolado:

```bash
terraform output caminho_arquivo
```

Saída esperada:

```
"./hello-terraform.txt"
```

> **Nota:** O comando `show` exibe o estado completo dos recursos. O comando `output` exibe apenas os valores de saída definidos em `outputs.tf`.

---

### Passo 11 – Destruir os recursos com terraform destroy

Remova todos os recursos gerenciados pelo Terraform:

```bash
terraform destroy -auto-approve
```

Saída esperada:

```
local_file.exemplo: Refreshing state... [id=a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0]

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # local_file.exemplo will be destroyed
  - resource "local_file" "exemplo" {
      - content              = "Olá! Este arquivo foi criado pelo Terraform." -> null
      - content_base64sha256 = "abc123def456..." -> null
      - content_base64sha512 = "xyz789ghi012..." -> null
      - content_md5          = "d41d8cd98f00b204e9800998ecf8427e" -> null
      - content_sha1         = "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0" -> null
      - content_sha256       = "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855" -> null
      - content_sha512       = "cf83e1357eefb8bdf1542850d66d8007d620e4050b5715dc83f4a921d36ce9ce..." -> null
      - directory_permission = "0777" -> null
      - file_permission      = "0777" -> null
      - filename             = "./hello-terraform.txt" -> null
      - id                   = "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0" -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Changes to Outputs:
  - caminho_arquivo = "./hello-terraform.txt" -> null
  - id_recurso      = "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0" -> null

local_file.exemplo: Destroying... [id=a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0]
local_file.exemplo: Destruction complete after 0s

Destroy complete! Resources: 1 destroyed.
```

Confirme que o arquivo foi removido:

```bash
ls -la hello-terraform.txt
```

Saída esperada:

```
ls: hello-terraform.txt: No such file or directory
```

> **Nota:** O `destroy` remove todos os recursos rastreados no `terraform.tfstate`. O símbolo `-` indica que o recurso será **destruído**.

---

### Passo 12 – Limpeza do lab

Remova todo o diretório do laboratório para não deixar resíduos:

```bash
cd ..
rm -rf terraform-lab-01
```

Saída esperada:

```
(sem saída — o diretório foi removido silenciosamente)
```

Confirme a remoção:

```bash
ls terraform-lab-01
```

Saída esperada:

```
ls: terraform-lab-01: No such file or directory
```

> **Nota:** Sempre limpe os recursos ao final de cada lab. Em ambientes de nuvem, recursos esquecidos podem gerar custos.

---

## Resultado Esperado

Ao concluir este lab, você terá:

- **Terraform instalado e funcional** no seu sistema operacional (Linux ou macOS)
- **Projeto Terraform completo** com quatro arquivos HCL (versions.tf, variables.tf, main.tf, outputs.tf)
- **Fluxo completo executado**: init → fmt → validate → plan → apply → show → output → destroy
- **Recurso local criado e destruído** — um arquivo de texto gerenciado inteiramente pelo Terraform
- **Ambiente limpo** — sem diretórios ou arquivos residuais após a conclusão

## Dicas para Discussão

| Conceito | Descrição |
|---|---|
| **IaC (Infrastructure as Code)** | Prática de gerenciar infraestrutura por meio de arquivos de configuração versionáveis, em vez de processos manuais. |
| **HCL (HashiCorp Configuration Language)** | Linguagem declarativa usada pelo Terraform para descrever a infraestrutura desejada. Arquivos usam a extensão `.tf`. |
| **Provider** | Plugin que permite ao Terraform interagir com APIs de serviços (AWS, Azure, GCP, local). Declarado no bloco `required_providers`. |
| **Resource** | Bloco que define um componente de infraestrutura a ser gerenciado (ex: `local_file`, `aws_instance`). |
| **State (Estado)** | Representação interna que o Terraform mantém dos recursos reais. Permite calcular diferenças entre o desejado e o existente. |
| **Plan vs Apply** | `plan` mostra as mudanças sem executar; `apply` executa as mudanças. Sempre revise o plano antes de aplicar. |
| **terraform.tfstate** | Arquivo JSON que armazena o estado atual. Nunca edite manualmente. Em produção, use backend remoto (S3, GCS, etc.). |
| **.terraform/** | Diretório local que contém os binários dos providers baixados pelo `terraform init`. Não versione no Git. |
| **Destroy** | Comando que remove todos os recursos gerenciados pelo Terraform. Equivale a "desfazer" toda a infraestrutura provisionada. |
