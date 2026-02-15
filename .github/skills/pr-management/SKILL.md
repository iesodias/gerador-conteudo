---
description: Manages Pull Request creation and configuration for lab content with structured descriptions, labels, and reviewers
---

# Pull Request Management Skill

This skill provides Pull Request management capabilities for lab generation workflow.

## Capabilities

### 1. PR Creation
- Create PR from lab branch to main
- Auto-generate structured PR description
- Set appropriate title format
- Link related issues if applicable

### 2. PR Configuration
- Add labels automatically based on status
- Assign reviewers (if configured)
- Set milestone (if applicable)
- Mark as draft if review pending

### 3. PR Description Generation
Auto-generate comprehensive PR description with:
- Lab overview and objective
- Review status and cycles
- Links to generated files
- Quality metrics
- Next steps

## Usage Instructions

When lab generation is complete and ready for PR:
1. Verify branch has been pushed to remote
2. Collect lab metadata (name, status, review cycles)
3. Generate structured PR description
4. Create PR with appropriate configuration
5. Return PR URL to user

## PR Title Format

```
[Lab] {nome-do-lab}
```

Usar kebab-case para o nome do lab (ex: kubernetes-hpa, terraform-vpc).

## PR Description Template

````markdown
# 📚 Lab: {Extrair título do lab-final.md (primeira linha # do arquivo)}

## 🎯 Objetivo
{Extrair objetivo do conteúdo do lab - não usar exemplo fixo}

## 📊 Status da Revisão
- **Status Final:** {Extrair do arquivo de revisão: APROVADO/APROVADO COM RESSALVAS/PENDENTE}
- **Ciclos de Revisão:** {Contar número de arquivos revisao-vN.md}
- **Pontuação Final:** {Extrair nota do último arquivo de revisão}/10

## 📁 Arquivos Gerados

**INSTRUÇÃO IMPORTANTE:** 
1. Liste o conteúdo do diretório workspace/[nome-do-lab]/ recursivamente
2. Organize os arquivos por subdiretório (pesquisa/, rascunhos/, revisoes/, output/)
3. Crie links markdown SOMENTE para arquivos que realmente existem
4. Use ícones apropriados (📋 📝 🔍 ✅)
5. Não inclua arquivos que não existem

**Formato sugerido:** `- [ícone] [nome-arquivo.md](caminho/relativo/completo)`

## 🔧 Tecnologias e Versões
{Extrair do conteúdo do lab - buscar seção de tecnologias ou metadados se existir}

## 📝 Próximos Passos
- [ ] Revisar conteúdo final
- [ ] Validar comandos em ambiente de teste
- [ ] Aprovar e merge para publicação
- [ ] Adicionar ao índice de labs

## 🤖 Gerado por
- **Agente:** @orquestrador-de-labs
- **Data:** {usar data atual no formato DD/MM/YYYY}
- **Workflow:** Pesquisa → Geração → Revisão → Entrega

---
_Lab gerado automaticamente pelo sistema de geração de conteúdo didático_
````

## Label Strategy

Add labels automatically based on status:

**Status Labels:**
- `✅ approved` - Review approved (≥24/30)
- `⚠️ needs-improvement` - Approved with reservations (20-23/30)
- `❌ needs-revision` - Requires significant changes (<20/30)

**Type Labels:**
- `📚 lab` - Always add for lab content
- `🔍 review-pending` - If still in review cycles
- `🎓 beginner` / `🎓 intermediate` / `🎓 advanced` - Based on lab level

**Technology Labels:**
- Extract from lab metadata or content (dynamic based on lab topic)

## Error Handling

- If branch doesn't exist: provide clear instructions to push branch first
- If PR already exists: ask user if they want to update existing PR
- If required files missing: list missing files before creating PR
- If labels don't exist in repo: create them or skip gracefully

## Examples

**Creating PR:**
```
Title: [Lab] {nome-do-lab}
Base: main
Head: lab/{nome-do-lab}
Labels: lab, approved, {technology}, {level}
Draft: false
```

Substituir {nome-do-lab}, {technology} e {level} pelos valores reais do contexto.

## Guardrails

- Never create PR to wrong base branch
- Always verify branch is pushed before creating PR
- Include all relevant files in description
- Keep description structured and readable
- Auto-assign appropriate labels based on review status