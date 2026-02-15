# Briefing: Instalação do Ansible no Ubuntu

## 1. O que é Ansible

**Ansible** é ferramenta open-source de automação de TI (Red Hat) para:
- Gerenciamento de configuração
- Provisionamento de infraestrutura  
- Orquestração e deploy

**Características:**
- **Agentless**: usa SSH, sem agente nos servidores
- **YAML**: playbooks legíveis e declarativos
- **Idempotente**: execuções repetidas = mesmo resultado
- **Modular**: biblioteca extensa de módulos

---

## 2. Pré-requisitos

**Sistemas**: Ubuntu 20.04/22.04/24.04 LTS

**Control Node (onde instala Ansible):**
- Python 3.8+
- 2 GB RAM, 500 MB disco

**Managed Nodes:**
- Python 2.7 ou 3.5+
- SSH habilitado
- Sudo para tarefas admin

---

## 3. Métodos de Instalação

### Via APT (recomendado para iniciantes)
```bash
sudo apt update
sudo apt install ansible -y
ansible --version
```

### Via PIP (versão mais recente)
```bash
sudo apt install python3-pip -y
pip3 install ansible
ansible --version
```

### Via PPA (Ansible Team - mais atual)
```bash
sudo apt install software-properties-common -y
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
```

---

## 4. Comandos Essenciais

| Comando | Descrição |
|---------|-----------|
| `ansible --version` | Versão e config |
| `ansible all -m ping` | Testa conexão com hosts |
| `ansible-playbook playbook.yml` | Executa playbook |
| `ansible-inventory --list` | Lista inventário |
| `ansible-doc <module>` | Documentação de módulo |
| `ansible-galaxy install <role>` | Instala role |

---

## 5. Projeto Básico

### ansible.cfg
```ini
[defaults]
inventory = ./inventory/hosts.ini
host_key_checking = False
```

### inventory/hosts.ini
```ini
[local]
localhost ansible_connection=local

[webservers]
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11
```

### playbook-ping.yml
```yaml
---
- name: Teste de conectividade
  hosts: all
  tasks:
    - name: Ping nos hosts
      ansible.builtin.ping:
```

### playbook-install.yml
```yaml
---
- name: Instalar pacote
  hosts: local
  become: yes
  tasks:
    - name: Instalar vim
      ansible.builtin.apt:
        name: vim
        state: present
        update_cache: yes
```

---

## 6. Verificação Pós-Instalação

```bash
# Versão
ansible --version

# Teste local
ansible localhost -m ping

# Listar módulos
ansible-doc -l | head -20
```

---

## 7. Conceitos para Iniciantes

| Conceito | Descrição |
|----------|-----------|
| **Control Node** | Máquina onde Ansible está instalado |
| **Managed Node** | Servidor gerenciado pelo Ansible |
| **Inventory** | Lista de hosts gerenciados |
| **Playbook** | Arquivo YAML com tarefas |
| **Task** | Ação individual (instalar pacote, copiar arquivo) |
| **Module** | Código reutilizável para tarefas (apt, copy, file) |
| **Idempotência** | Executar N vezes = mesmo resultado |

---

## 8. Erros Comuns

| Erro | Solução |
|------|---------|
| `command not found` | Verificar PATH, reinstalar |
| `Failed to connect` | Verificar SSH, firewall |
| `Permission denied` | Configurar sudo/become |
| `Module not found` | Instalar coleção necessária |

---

## 9. Boas Práticas

1. Use `--check` antes de executar playbooks
2. Organize inventários por ambiente (dev, prod)
3. Use variáveis para valores reutilizáveis
4. Documente playbooks com `name` descritivo
5. Versione playbooks no Git
6. Use `ansible-vault` para secrets
7. Teste em ambiente de staging primeiro
