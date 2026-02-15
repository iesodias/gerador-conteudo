# Lab 01 – Instalar o Terraform e criar seu primeiro projeto de Infrastructure as Code

## Objetivo

* Instalar o Terraform no ambiente local (Linux ou macOS)
* Verificar a instalação e entender os comandos básicos
* Criar um projeto Terraform completo usando o provider local (sem necessidade de cloud)
* Executar o fluxo completo: init, plan, apply e destroy
* Compreender os conceitos fundamentais de Infrastructure as Code
* Limpar todos os recursos e arquivos criados ao final do lab

---

## Pré-requisitos

- [Terraform](https://developer.hashicorp.com/terraform/install) — será instalado durante o lab
- Terminal com acesso à internet para download do Terraform e providers
- Sistema operacional: Linux (Ubuntu/Debian) ou macOS
- Editor de texto ([VS Code](https://code.visualstudio.com/), vim ou nano)

> **Nota:** Este lab **não requer** conta em nenhum provedor de cloud. Usaremos o provider `local` do Terraform, que cria recursos diretamente na sua máquina.

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

### 1. Criar o diretório do lab

```bash
mkdir -p ~/workshop/lab01-terraform && cd ~/workshop/lab01-terraform
```

---

### 2. Instalar o Terraform

Escolha o método de instalação adequado ao seu sistema operacional.

**Linux (Ubuntu/Debian via APT):**

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```

**macOS (via Homebrew):**

```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

> **Nota:** O Terraform é uma ferramenta de **Infrastructure as Code (IaC)** — permite definir infraestrutura em arquivos de código ao invés de configurar manualmente via console ou interface gráfica.

---

### 3. Verificar a instalação

Confirme que o Terraform foi instalado corretamente:

```bash
terraform version
```

Saída esperada:

```
Terraform v1.10.3
on linux_amd64
```

Verifique onde o binário foi instalado:

```bash
which terraform
```

Saída esperada:

```
/usr/bin/terraform
```

> **Nota:** A versão exata pode variar. O importante é que o comando execute sem erros. Se aparecer `terraform: command not found`, revise o passo 2.

---

### 4. Criar os arquivos do projeto Terraform

Vamos criar 4 arquivos que compõem um projeto Terraform organizado. O primeiro define as versões e providers necessários.

**Criar o arquivo versions.tf:**

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

> **Nota:** O `versions.tf` define qual versão mínima do Terraform é necessária e quais **providers** (plugins) o projeto usa. O provider `local` permite criar recursos na máquina local.

**Criar o arquivo variables.tf:**

```bash
cat <<'EOF' > variables.tf
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

> **Nota:** As **variáveis** permitem parametrizar o código. Em vez de valores fixos (hardcoded), usamos variáveis que podem ser alteradas sem modificar a lógica principal.

**Criar o arquivo main.tf:**

```bash
cat <<'EOF' > main.tf
resource "local_file" "exemplo" {
  content  = var.conteudo
  filename = "${path.module}/${var.nome_arquivo}"
}
EOF
```

> **Nota:** O `main.tf` contém os **resources** — os componentes de infraestrutura que o Terraform vai gerenciar. Aqui estamos criando um arquivo local usando o resource `local_file`.

**Criar o arquivo outputs.tf:**

```bash
cat <<'EOF' > outputs.tf
output "caminho_arquivo" {
  description = "Caminho completo do arquivo criado"
  value       = local_file.exemplo.filename
}

output "id_recurso" {
  description = "ID do recurso criado pelo Terraform"
  value       = local_file.exemplo.id
}
EOF
```

> **Nota:** Os **outputs** exibem informações sobre os recursos criados após o `terraform apply`. São úteis para consultar dados como IPs, IDs, caminhos, etc.

---

### 5. Inicializar o projeto (terraform init)

O `terraform init` é sempre o primeiro comando a executar em um projeto Terraform. Ele baixa os providers e prepara o ambiente.

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
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

> **Nota:** O `terraform init` criou o diretório `.terraform/` com o provider baixado e o arquivo `.terraform.lock.hcl` que trava as versões dos providers.

---

### 6. Formatar e validar o código

Antes de aplicar, é boa prática formatar e validar os arquivos.

Formatar o código:

```bash
terraform fmt
```

Saída esperada (se já estiver formatado, nenhum arquivo é listado):

```
(sem saída — significa que todos os arquivos já estão formatados corretamente)
```

> **Nota:** O `terraform fmt` ajusta a indentação e formatação dos arquivos `.tf` para o padrão da HashiCorp. Se algum arquivo for reformatado, o nome dele aparece na saída.

Validar a sintaxe:

```bash
terraform validate
```

Saída esperada:

```
Success! The configuration is valid.
```

> **Nota:** O `terraform validate` verifica se os arquivos `.tf` têm sintaxe correta e se as referências entre recursos, variáveis e outputs estão consistentes.

---

### 7. Visualizar o plano de execução (terraform plan)

O `terraform plan` mostra exatamente o que será criado, alterado ou destruído, sem executar nada.

```bash
terraform plan
```

Saída esperada:

```
Terraform used the selected providers to generate the following execution plan. Resource actions are
indicated with the following symbols:
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

> **Nota:** O símbolo `+` indica que o recurso será **criado**. Outros símbolos são `~` (alterado) e `-` (destruído). O plan é como uma "simulação" — nada é executado ainda.

---

### 8. Aplicar as mudanças (terraform apply)

Agora vamos executar o plano e criar o recurso de verdade.

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

> **Nota:** O `-auto-approve` pula a confirmação interativa. Em ambientes de produção, **nunca** use essa flag — sempre revise o plan antes de confirmar.

---

### 9. Verificar o recurso criado

Confirme que o arquivo foi criado pelo Terraform:

```bash
cat hello-terraform.txt
```

Saída esperada:

```
Olá! Este arquivo foi criado pelo Terraform.
```

Verifique os arquivos no diretório:

```bash
ls -la
```

Saída esperada:

```
total XX
drwxr-xr-x  X user group  XXX date .
drwxr-xr-x  X user group  XXX date ..
drwxr-xr-x  X user group  XXX date .terraform
-rw-r--r--  1 user group  XXX date .terraform.lock.hcl
-rwxrwxrwx  1 user group   46 date hello-terraform.txt
-rw-r--r--  1 user group  XXX date main.tf
-rw-r--r--  1 user group  XXX date outputs.tf
-rw-r--r--  1 user group  XXX date terraform.tfstate
-rw-r--r--  1 user group  XXX date variables.tf
-rw-r--r--  1 user group  XXX date versions.tf
```

> **Nota:** Observe que o Terraform criou o arquivo `terraform.tfstate`. Este é o **state file** — ele armazena o mapeamento entre o código e os recursos reais. **Nunca edite este arquivo manualmente.**

---

### 10. Consultar o estado e os outputs

Veja o estado atual dos recursos gerenciados:

```bash
terraform show
```

Saída esperada:

```
# local_file.exemplo:
resource "local_file" "exemplo" {
    content              = "Olá! Este arquivo foi criado pelo Terraform."
    content_base64sha256 = "<HASH>"
    content_base64sha512 = "<HASH>"
    content_md5          = "<HASH>"
    content_sha1         = "<HASH>"
    content_sha256       = "<HASH>"
    content_sha512       = "<HASH>"
    directory_permission = "0777"
    file_permission      = "0777"
    filename             = "./hello-terraform.txt"
    id                   = "<HASH>"
}


Outputs:

caminho_arquivo = "./hello-terraform.txt"
id_recurso = "<HASH>"
```

Consulte apenas os outputs:

```bash
terraform output
```

Saída esperada:

```
caminho_arquivo = "./hello-terraform.txt"
id_recurso = "<HASH>"
```

> **Nota:** O `terraform show` exibe todos os detalhes dos recursos no state. O `terraform output` mostra apenas os valores definidos no `outputs.tf`.

---

### 11. Destruir os recursos (terraform destroy)

O `terraform destroy` remove todos os recursos gerenciados pelo Terraform.

```bash
terraform destroy -auto-approve
```

Saída esperada:

```
local_file.exemplo: Refreshing state... [id=<HASH>]

Terraform used the selected providers to generate the following execution plan. Resource actions are
indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # local_file.exemplo will be destroyed
  - resource "local_file" "exemplo" {
      - content              = "Olá! Este arquivo foi criado pelo Terraform." -> null
      - content_base64sha256 = "<HASH>" -> null
      - content_base64sha512 = "<HASH>" -> null
      - content_md5          = "<HASH>" -> null
      - content_sha1         = "<HASH>" -> null
      - content_sha256       = "<HASH>" -> null
      - content_sha512       = "<HASH>" -> null
      - directory_permission = "0777" -> null
      - file_permission      = "0777" -> null
      - filename             = "./hello-terraform.txt" -> null
      - id                   = "<HASH>" -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Changes to Outputs:
  - caminho_arquivo = "./hello-terraform.txt" -> null
  - id_recurso      = "<HASH>" -> null

local_file.exemplo: Destroying... [id=<HASH>]
local_file.exemplo: Destruction complete after 0s

Destroy complete! Resources: 1 destroyed.
```

Confirme que o arquivo foi removido:

```bash
ls hello-terraform.txt
```

Saída esperada:

```
ls: cannot access 'hello-terraform.txt': No such file or directory
```

> **Nota:** O `terraform destroy` é o oposto do `terraform apply`. Ele remove todos os recursos que o Terraform criou. O state file é atualizado para refletir que não há mais recursos gerenciados.

---

### 12. Limpeza do Lab

Remova o diretório do lab e todos os arquivos criados:

```bash
cd ~ && rm -rf ~/workshop/lab01-terraform
```

Confirme que foi removido:

```bash
ls ~/workshop/lab01-terraform
```

Saída esperada:

```
ls: cannot access '/home/<USUARIO>/workshop/lab01-terraform': No such file or directory
```

> **Nota (macOS):** No macOS a mensagem será: `No such file or directory: /Users/<USUARIO>/workshop/lab01-terraform`

---

## Resultado Esperado

* Terraform instalado e funcional no ambiente local (`terraform version` retorna a versão)
* Projeto Terraform completo criado com 4 arquivos (versions.tf, variables.tf, main.tf, outputs.tf)
* Fluxo completo executado com sucesso: init → fmt → validate → plan → apply → show → output → destroy
* Arquivo `hello-terraform.txt` criado e destruído via Terraform
* Todos os arquivos e diretórios removidos ao final do lab

---

## Dicas para Discussão

| Conceito | Descrição |
|----------|-----------|
| **IaC (Infrastructure as Code)** | Prática de gerenciar infraestrutura através de código declarativo, versionável e reproduzível |
| **HCL** | HashiCorp Configuration Language — linguagem usada nos arquivos `.tf` do Terraform |
| **Provider** | Plugin que conecta o Terraform a uma API ou serviço (ex: AWS, Azure, local) |
| **Resource** | Componente de infraestrutura gerenciado pelo Terraform (ex: arquivo, VM, rede) |
| **State (terraform.tfstate)** | Arquivo que mapeia o código Terraform para os recursos reais criados |
| **Plan vs Apply** | `plan` simula as mudanças; `apply` executa de verdade. Sempre revise o plan antes de aplicar |
| **Diretório .terraform/** | Contém os providers baixados e cache. Não deve ser versionado no Git |
| **.terraform.lock.hcl** | Arquivo de lock que garante versões consistentes dos providers entre a equipe |
| **Destroy** | Remove todos os recursos gerenciados. Equivale a "desfazer" o que o apply criou |
