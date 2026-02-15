# Briefing: Instalação do Terraform

## Informações Gerais

**Tópico:** Instalação do Terraform  
**Documentação Oficial:** https://developer.hashicorp.com/terraform/install  
**Data da Pesquisa:** 15 de fevereiro de 2026

## Pré-requisitos

- Sistema operacional: Linux, macOS ou Windows
- Privilégios de administrador/sudo para instalação
- Espaço em disco: ~50-100 MB
- Conexão com internet para download
- Nenhuma dependência adicional (Terraform é um binário standalone)

## Métodos de Instalação

### 1. Linux

#### Ubuntu/Debian (via APT)
```bash
# Adicionar repositório oficial HashiCorp
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

# Atualizar e instalar
sudo apt update && sudo apt install terraform
```

#### CentOS/RHEL/Fedora (via YUM/DNF)
```bash
# Adicionar repositório HashiCorp
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo

# Instalar Terraform
sudo yum install terraform
```

#### Arch Linux (via Pacman)
```bash
sudo pacman -S terraform
```

#### Download Direto (Todas as distribuições)
```bash
# Baixar versão mais recente (exemplo: 1.7.0)
wget https://releases.hashicorp.com/terraform/1.7.0/terraform_1.7.0_linux_amd64.zip

# Descompactar
unzip terraform_1.7.0_linux_amd64.zip

# Mover para diretório no PATH
sudo mv terraform /usr/local/bin/

# Dar permissão de execução
sudo chmod +x /usr/local/bin/terraform
```

### 2. macOS

#### Homebrew (Recomendado)
```bash
# Instalar via Homebrew
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

#### Download Direto
```bash
# Baixar versão para macOS
wget https://releases.hashicorp.com/terraform/1.7.0/terraform_1.7.0_darwin_amd64.zip

# Descompactar
unzip terraform_1.7.0_darwin_amd64.zip

# Mover para diretório no PATH
sudo mv terraform /usr/local/bin/

# Dar permissão de execução
sudo chmod +x /usr/local/bin/terraform
```

**Nota para macOS:** Em chips Apple Silicon (M1/M2/M3), usar a versão `darwin_arm64`.

### 3. Windows

#### Chocolatey
```powershell
choco install terraform
```

#### Scoop
```powershell
scoop install terraform
```

#### Download Direto
1. Baixar o arquivo ZIP da versão Windows: https://releases.hashicorp.com/terraform/
2. Descompactar o arquivo em um diretório (exemplo: `C:\terraform`)
3. Adicionar o caminho ao PATH do sistema:
   - Abrir "Variáveis de Ambiente" no Painel de Controle
   - Editar a variável PATH do usuário ou do sistema
   - Adicionar o caminho completo (exemplo: `C:\terraform`)
   - Reiniciar o terminal/PowerShell

#### Windows Subsystem for Linux (WSL)
Seguir as instruções de instalação do Linux dentro do ambiente WSL.

## Verificação da Instalação

Após a instalação, verificar se o Terraform foi instalado corretamente:

```bash
# Verificar versão instalada
terraform version

# Saída esperada (exemplo):
# Terraform v1.7.0
# on linux_amd64
```

```bash
# Verificar comandos disponíveis
terraform -help
```

```bash
# Verificar autocompletar (opcional)
terraform -install-autocomplete
```

## Configuração Inicial

### Configuração Básica

1. **Nenhuma configuração obrigatória** - O Terraform funciona imediatamente após instalação

2. **Configuração de Credenciais de Provedores** (executada posteriormente, conforme necessário):
   - AWS: Configurar `~/.aws/credentials` ou variáveis de ambiente
   - Azure: `az login` ou variáveis de ambiente
   - GCP: Configurar `GOOGLE_APPLICATION_CREDENTIALS`

3. **Configuração de CLI (Opcional)**:
   - Arquivo de configuração: `~/.terraformrc` (Linux/macOS) ou `terraform.rc` (Windows)
   - Usado para configurar plugins, mirrors, credenciais de registro

### Exemplo de arquivo `.terraformrc` (opcional)
```hcl
plugin_cache_dir = "$HOME/.terraform.d/plugin-cache"
disable_checkpoint = true
```

## Versões Recomendadas

### Versão Estável Atual
- **Versão recomendada:** 1.7.x (última estável em fevereiro de 2026)
- **Suporte:** A HashiCorp mantém suporte para versões principais por ~18 meses

### Gerenciamento de Versões

**tfenv** - Gerenciador de versões do Terraform (Recomendado para ambientes com múltiplos projetos)

```bash
# Instalação do tfenv (macOS/Linux)
git clone https://github.com/tfutils/tfenv.git ~/.tfenv
echo 'export PATH="$HOME/.tfenv/bin:$PATH"' >> ~/.bashrc

# Listar versões disponíveis
tfenv list-remote

# Instalar versão específica
tfenv install 1.7.0

# Usar versão específica
tfenv use 1.7.0
```

### Compatibilidade
- Terraform 1.x mantém compatibilidade com versões 1.x anteriores
- Breaking changes ocorrem entre versões principais (0.x → 1.x → 2.x)
- Recomenda-se manter versão consistente em equipes (usar `.terraform-version` file)

## Primeiro Teste

Criar um arquivo simples para testar a instalação:

```bash
# Criar diretório de teste
mkdir terraform-test && cd terraform-test

# Criar arquivo main.tf
cat > main.tf << 'EOF'
terraform {
  required_version = ">= 1.0"
}

output "hello" {
  value = "Terraform instalado com sucesso!"
}
EOF

# Inicializar e executar
terraform init
terraform apply -auto-approve
```

Saída esperada: `hello = "Terraform instalado com sucesso!"`

## Atualização do Terraform

### Via Gerenciador de Pacotes
```bash
# Ubuntu/Debian
sudo apt update && sudo apt upgrade terraform

# macOS (Homebrew)
brew upgrade terraform

# Windows (Chocolatey)
choco upgrade terraform
```

### Download Direto
Repetir o processo de instalação com a nova versão.

## Solução de Problemas Comuns

### Comando não encontrado
- Verificar se o diretório está no PATH: `echo $PATH`
- Reiniciar o terminal após instalação

### Permissão negada (Linux/macOS)
```bash
sudo chmod +x /usr/local/bin/terraform
```

### Erro de certificado SSL
```bash
# Definir variável de ambiente (temporário)
export TF_LOG=DEBUG
```

### Versão incorreta após atualização
```bash
# Limpar cache de comandos
hash -r  # bash/zsh
rehash   # csh/tcsh
```

## Links de Referência

1. **Documentação Oficial de Instalação:** https://developer.hashicorp.com/terraform/install
2. **Downloads Oficiais:** https://releases.hashicorp.com/terraform/
3. **Changelog e Releases:** https://github.com/hashicorp/terraform/releases
4. **Guia de Início Rápido:** https://developer.hashicorp.com/terraform/tutorials/aws-get-started
5. **Repositório APT da HashiCorp:** https://apt.releases.hashicorp.com/
6. **Repositório YUM da HashiCorp:** https://rpm.releases.hashicorp.com/

## Notas Adicionais

- O Terraform é distribuído como um binário único sem dependências externas
- A instalação via gerenciadores de pacote (apt, yum, brew) facilita atualizações futuras
- Para ambientes enterprise, considere usar o Terraform Cloud ou Terraform Enterprise
- Em ambientes restritos, o download direto permite instalação offline
- Recomenda-se fixar a versão do Terraform no código (`required_version`) para garantir reprodutibilidade

---

**Briefing preparado por:** Agente Pesquisador de Documentação  
**Status:** Completo e pronto para uso
