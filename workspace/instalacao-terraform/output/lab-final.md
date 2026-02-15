# Lab 01 – Instalar e Configurar o Terraform

## Objetivo

* Instalar o Terraform via repositório oficial HashiCorp
* Verificar a instalação e versão do Terraform
* Executar um primeiro teste para validar o funcionamento
* Configurar autocompletar para melhorar a experiência de uso
* Limpar os recursos criados durante o lab

---

## Pré-requisitos

- [Terraform](https://developer.hashicorp.com/terraform/install) será instalado durante este lab
- Sistema operacional: Ubuntu/Debian (para outras distribuições, consulte a documentação oficial)
- Privilégios de sudo para instalação de pacotes
- Conexão com internet

---

## Passo a Passo

### 1. Criar o diretório do lab

```bash
mkdir -p ~/workshop/lab01 && cd ~/workshop/lab01
```

---

### 2. Adicionar o repositório oficial HashiCorp

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

```bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
```

---

### 3. Instalar o Terraform

```bash
sudo apt update && sudo apt install terraform -y
```

---

### 4. Verificar a instalação

```bash
terraform version
```

Saída esperada (versão pode variar):
```
Terraform v1.7.0
on linux_amd64
```

Verificar comandos disponíveis:

```bash
terraform -help
```

---

### 5. Criar arquivo de teste

```bash
vi main.tf
```

Conteúdo:

```hcl
terraform {
  required_version = ">= 1.0"
}

output "mensagem_sucesso" {
  value = "Terraform instalado e funcionando corretamente!"
}

output "versao_terraform" {
  value = "Você está usando o Terraform versão ${terraform.version}"
}
```

---

### 6. Inicializar e executar o teste

Inicializar o diretório Terraform:

```bash
terraform init
```

Executar o teste:

```bash
terraform apply -auto-approve
```

Saída esperada:
```
mensagem_sucesso = "Terraform instalado e funcionando corretamente!"
versao_terraform = "Você está usando o Terraform versão 1.7.0"
```

---

### 7. Configurar autocompletar (opcional)

```bash
terraform -install-autocomplete
```

Recarregar o shell:

```bash
source ~/.bashrc
```

---

### 8. Limpeza do Lab

Remover o diretório do lab:

```bash
cd ~ && rm -rf ~/workshop/lab01
```

Caso queira desinstalar o Terraform completamente:

```bash
sudo apt remove terraform -y
sudo rm /etc/apt/sources.list.d/hashicorp.list
sudo rm /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

---

## Notas Adicionais

**Para macOS (Homebrew):**
```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

**Para verificar atualizações:**
```bash
sudo apt update && sudo apt upgrade terraform
```

**Para instalar uma versão específica:**
```bash
sudo apt install terraform=1.7.0-1
```

---
