# Relatório de Revisão — Lab 01 (Terraform Instalação Básica)

**Arquivo revisado:** `rascunhos/lab-v1.md`  
**Data da revisão:** 2026-02-14  
**Agente:** revisor-de-lab

---

## 1. Checklist do Template

| # | Item | Status | Observação |
|---|------|--------|------------|
| 1 | Título `# Lab XX – [Verbo + o que será feito]` | ✓ | `Lab 01 – Instalar o Terraform e criar seu primeiro projeto de Infrastructure as Code` — formato correto |
| 2 | Objetivo: 3–6 bullets, infinitivo, limpeza ao final | ✓ | 6 bullets, todos no infinitivo, último bullet é limpeza |
| 3 | Pré-requisitos: ferramentas + links + comandos | ✗ | Lista ferramentas mas **não inclui links** para download (Terraform, Homebrew, VS Code). O template exige formato `[Ferramenta](link)` |
| 4 | Estrutura de Diretórios | ✓ | Presente e coerente com os arquivos criados no passo a passo |
| 5 | Passo a Passo: 5–12 passos, separados por `---` | ✓ | 12 passos, todos separados por `---` |
| 6 | Código completo, linguagem correta no fence | ✓ | Todos os blocos usam `bash` corretamente; código completo sem omissões |
| 7 | Placeholders `<VALOR>` quando necessário | ✓ | Usa `<HASH>` e `<USUARIO>` adequadamente |
| 8 | Limpeza obrigatória ao final | ✓ | Passo 11 faz `terraform destroy`, Passo 12 remove diretório do lab |
| 9 | Resultado Esperado: 3–5 resultados concretos | ✓ | 5 resultados concretos e verificáveis |
| 10 | Dicas para Discussão: tabela | ✓ | Tabela com 9 conceitos relevantes, bem descrita |
| 11 | Idioma pt-BR, tom direto e prático | ✓ | Idioma consistente, tom direto, notas explicativas claras |

**Checklist: 10/11 ✓**

---

## 2. Avaliação por Critérios

### 1. Aderência ao Template
**❌ REPROVADO**

**Justificativa:** A seção de pré-requisitos não segue o formato `[Ferramenta](link)` exigido pelo template. Faltam links para:
- Terraform: `https://developer.hashicorp.com/terraform/install`
- Homebrew (macOS): `https://brew.sh`
- VS Code (opcional): `https://code.visualstudio.com`

Além disso, os comandos de verificação de versão das ferramentas pré-requisito (ex: `brew --version`) não estão na seção de pré-requisitos — estão dispersos no passo a passo.

---

### 2. Completude Técnica
**✅ APROVADO**

O lab cobre o fluxo completo do Terraform: instalação → init → fmt → validate → plan → apply → show → output → destroy. Inclui uso de variáveis, outputs e organização em múltiplos arquivos `.tf`. Nenhum conceito fundamental foi omitido para o nível proposto.

---

### 3. Clareza para Iniciantes
**✅ APROVADO**

Cada passo possui notas explicativas (`> **Nota:**`) que contextualizam os conceitos (IaC, provider, state, resource). As explicações são concisas e úteis. O uso do provider `local` elimina complexidade desnecessária de cloud para um primeiro contato.

---

### 4. Código Correto
**✅ APROVADO**

- Arquivos HCL (`versions.tf`, `variables.tf`, `main.tf`, `outputs.tf`) são sintaticamente corretos.
- Comandos bash válidos e funcionais.
- Uso de `cat <<'EOF'` evita problemas de interpolação de variáveis no shell.
- Constraint de versão `>= 1.9.0` e `~> 2.5` são válidas.

---

### 5. Saídas Esperadas
**✅ APROVADO**

Todos os comandos possuem bloco de saída esperada. Valores dinâmicos usam placeholders (`<HASH>`, `XXX`). Observação menor: o bloco de saída do `terraform fmt` está vazio — funcionalmente correto (se já formatado, não exibe nada), mas poderia conter um comentário explicativo.

---

### 6. Progressão Lógica
**✅ APROVADO**

A sequência é natural e didática: criar diretório → instalar → verificar → criar arquivos → inicializar → formatar/validar → planejar → aplicar → verificar recurso → inspecionar estado → destruir → limpar. Cada passo depende do anterior.

---

### 7. Limpeza
**✅ APROVADO**

Duas camadas de limpeza:
- Passo 11: `terraform destroy -auto-approve` remove os recursos gerenciados.
- Passo 12: `rm -rf ~/workshop/lab01-terraform` remove todos os arquivos do lab.

Ambas com verificação de confirmação.

---

### 8. Independência
**✅ APROVADO**

O lab é totalmente autocontido. Não depende de nenhum outro lab, conta de cloud ou recurso externo além do download do binário Terraform. O provider `local` garante execução offline após o `terraform init`.

---

### 9. Formatação
**✅ APROVADO**

Markdown bem estruturado: títulos hierárquicos, separadores `---`, blocos de código com linguagem, tabela formatada, notas em blockquote. Observação menor: o passo 8 repete a saída completa do plan (já mostrada no passo 7) antes do output do apply — isso é tecnicamente correto mas torna o documento verboso.

---

### 10. Nível Adequado
**✅ APROVADO**

Adequado para iniciantes absolutos em Terraform. Não assume conhecimento prévio de IaC. As notas explicam cada conceito na primeira ocorrência. A complexidade é mínima (um único resource `local_file`) sem ser trivial.

---

## 3. Resumo

| # | Critério | Resultado |
|---|----------|-----------|
| 1 | Aderência ao Template | ❌ REPROVADO |
| 2 | Completude Técnica | ✅ APROVADO |
| 3 | Clareza para Iniciantes | ✅ APROVADO |
| 4 | Código Correto | ✅ APROVADO |
| 5 | Saídas Esperadas | ✅ APROVADO |
| 6 | Progressão Lógica | ✅ APROVADO |
| 7 | Limpeza | ✅ APROVADO |
| 8 | Independência | ✅ APROVADO |
| 9 | Formatação | ✅ APROVADO |
| 10 | Nível Adequado | ✅ APROVADO |

**Resultado: 9/10 aprovados**

---

## 4. Resultado Final

### ❌ REPROVADO

---

## 5. Ações Corretivas Obrigatórias

| # | Ação | Seção Afetada | Prioridade |
|---|------|---------------|------------|
| 1 | Adicionar links de download na seção Pré-requisitos no formato `[Ferramenta](link)`: Terraform (`https://developer.hashicorp.com/terraform/install`), Homebrew (`https://brew.sh`), VS Code (`https://code.visualstudio.com`) | Pré-requisitos | Alta |

---

## 6. Melhorias Recomendadas (Não Bloqueantes)

| # | Melhoria | Seção Afetada |
|---|----------|---------------|
| 1 | Adicionar comentário no bloco vazio de saída do `terraform fmt` (ex: `# nenhum arquivo reformatado`) para evitar confusão do aluno com um bloco vazio | Passo 6 |
| 2 | Reduzir a saída esperada do passo 8 (`terraform apply`), removendo a repetição completa do plan que já aparece no passo 7 — manter apenas as linhas de `Creating...` em diante | Passo 8 |
| 3 | No passo 12, a mensagem de erro do `ls` usa `/home/<USUARIO>/...` (Linux). Adicionar variante macOS (`/Users/<USUARIO>/...`) ou usar `~` no placeholder para cobrir ambos os sistemas | Passo 12 |
