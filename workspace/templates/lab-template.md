# TEMPLATE PARA CRIAÇÃO DE LABS

> IA: use este template como base para gerar labs práticos de QUALQUER tecnologia. Adapte comandos, arquivos e linguagem para a tecnologia solicitada. Idioma: pt-BR. Tom: direto e prático.

---

## FORMATO DE CADA LAB

```markdown
# Lab XX – [Verbo + o que será feito]

## Objetivo

* [3 a 6 bullets com verbos no infinitivo]
* Sempre incluir limpeza dos recursos ao final

---

## Pré-requisitos (se Lab 01 ou se necessário)

- [Ferramenta](link) + comando de instalação em bloco ```bash```

---

## Estrutura de Diretórios (se aplicável)

```bash
projeto/
├── arquivo.ext
```

---

## Passo a Passo

### 1. Criar o diretório do lab

```bash
mkdir -p ~/workshop/labXX && cd ~/workshop/labXX
```

---

### 2. Criar arquivo de configuração

```bash
vi arquivo.ext
```

Conteúdo:

```yaml
# conteúdo COMPLETO — nunca usar "..." ou omitir trechos
```

> Nota: explicar conceitos-chave quando necessário

---

### 3. Aplicar/Executar

```bash
comando-principal
```

---

### 4. Verificar resultado

```bash
comando-verificacao
```

Saída esperada:

```
RECURSO    STATUS
exemplo    Running
```

---

### 5. Testar comportamento

```bash
comando-teste
```

> O que deve acontecer e por quê

---

### N. Limpeza do Lab (SEMPRE último passo)

```bash
comando-remover-tudo
```

---

## Resultado Esperado

* [3 a 5 resultados concretos e verificáveis]
* Recursos removidos ao final

---

## Dicas para Discussão (opcional)

| Conceito | Descrição |
|----------|-----------|
| [A]      | [curto]   |
```

---

## REGRAS

1. **Passos**: 5 a 12 por lab, numerados, separados por `---`
2. **Código**: sempre completo, com linguagem correta no fence (bash/yaml/hcl/python/json)
3. **Placeholders**: usar `<VALOR>` para dados que o aluno substitui
4. **Cada lab é independente** — pode ser executado isoladamente
5. **Progressão**: Lab 01 = setup → Labs intermediários = conceitos → Labs finais = cenários avançados
6. **Limpeza obrigatória** ao final de cada lab
