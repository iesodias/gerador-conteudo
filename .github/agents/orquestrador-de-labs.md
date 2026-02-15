**Passo 0: Setup Git Workflow**
- Crie uma branch para o lab usando o padrão `lab/{nome-do-lab}`
- Use a skill git-workflow para criar e verificar a branch
- Confirme que está na branch correta antes de prosseguir

---

**Passo 1: ...**

## Existing content starting from Passo 1

... (Your existing content from .github/agents/orquestrador-de-labs.md should be included here) ...



**Passo 8: Entrega Final e Pull Request**
- Quando APROVADO (ou após 3 ciclos):
  - Copie o lab final para `workspace/{nome-do-lab}/output/lab-final.md`
  - **Commit dos Arquivos:**
    - Use a skill git-workflow para fazer commit de todos os arquivos em `workspace/{nome-do-lab}/`
    - Faça push da branch para o repositório remoto
  - **Abertura do Pull Request:**
    - Use a skill pr-management para criar o PR
    - Base: main
    - Head: lab/{nome-do-lab}
    - Título: [Lab] {nome-do-lab}
    - Descrição: auto-gerada pela skill incluindo status, arquivos e métricas
    - Labels: baseadas no status de revisão
  - Notifique o usuário:
    ```
    ✅ Lab finalizado com sucesso!
    
    🔗 **Pull Request:** [link do PR]
    📦 **Branch:** lab/{nome-do-lab}
    
    📄 **Arquivos:**
    - Lab: workspace/{nome-do-lab}/output/lab-final.md
    - Revisão: workspace/{nome-do-lab}/revisoes/revisao-v{N}.md
    - Pesquisa: workspace/{nome-do-lab}/pesquisa/briefing-pesquisa.md
    
    📊 **Métricas:**
    - Ciclos de revisão: {N}
    - Status: {APROVADO/APROVADO COM RESSALVAS}
    - Pontuação: {X}/30
    ```
