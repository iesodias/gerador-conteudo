---
description: Manages Pull Request creation and configuration for lab content with structured descriptions, labels, and reviewers
globs:
  - "workspace/**"
  - ".github/agents/**"
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

Examples:
- `[Lab] kubernetes-hpa-metricas-custom`
- `[Lab] terraform-aws-vpc-avancada`

## PR Description Template

````markdown
# 📚 Lab: {Nome do Lab}

## 🎯 Objetivo
{Breve descrição do objetivo do lab extraída do conteúdo}

## 📊 Status da Revisão
- **Status Final:** ✅ APROVADO / ⚠️ APROVADO COM RESSALVAS / ❌ PENDENTE
- **Ciclos de Revisão:** {N}
- **Pontuação Final:** {X}/30

## 📁 Arquivos Gerados

### Pesquisa
- 📋 [Briefing de Pesquisa](workspace/{nome-do-lab}/pesquisa/briefing-pesquisa.md)
- 🏗️ [Estrutura do Template](workspace/{nome-do-lab}/pesquisa/estrutura-template.md)

### Rascunhos
- 📝 [Lab v1](workspace/{nome-do-lab}/rascunhos/lab-v1.md)
- 📝 [Lab v2](workspace/{nome-do-lab}/rascunhos/lab-v2.md) _(se aplicável)_

### Revisões
- 🔍 [Revisão v1](workspace/{nome-do-lab}/revisoes/revisao-v1.md)
- 🔍 [Revisão v{N}](workspace/{nome-do-lab}/revisoes/revisao-v{N}.md)

### Output Final
- ✅ [Lab Final](workspace/{nome-do-lab}/output/lab-final.md)

## 🔧 Tecnologias e Versões
{Extrair da tabela de metadados do lab}

## 📝 Próximos Passos
- [ ] Revisar conteúdo final
- [ ] Validar comandos em ambiente de teste
- [ ] Aprovar e merge para publicação
- [ ] Adicionar ao índice de labs

## 🤖 Gerado por
- **Agente:** @orquestrador-de-labs
- **Data:** {data atual}
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
- Extract from lab metadata (e.g., `kubernetes`, `terraform`, `aws`)

## Error Handling

- If branch doesn't exist: provide clear instructions to push branch first
- If PR already exists: ask user if they want to update existing PR
- If required files missing: list missing files before creating PR
- If labels don't exist in repo: create them or skip gracefully

## Examples

**Creating PR:**
```
Title: [Lab] kubernetes-hpa-metricas-custom
Base: main
Head: lab/kubernetes-hpa-metricas-custom
Labels: lab, approved, kubernetes, intermediate
Draft: false
```

## Guardrails

- Never create PR to wrong base branch
- Always verify branch is pushed before creating PR
- Include all relevant files in description
- Keep description structured and readable
- Auto-assign appropriate labels based on review status