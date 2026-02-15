```markdown
# Lab 01 – Instalar e Configurar Ansible no Ubuntu

## Objetivo

- Instalar Ansible via APT no Ubuntu
- Criar arquivo de configuração `ansible.cfg`
- Configurar inventário local de hosts
- Executar playbook de teste de conectividade (ping)
- Executar playbook para instalar pacote localmente
- Limpar ambiente e remover Ansible

## Pré-requisitos

| Ferramenta | Instalação |
|------------|------------|
| **[Ubuntu 20.04/22.04/24.04](https://ubuntu.com/download/server)** | Sistema operacional base |
| **[Python 3.8+](https://www.python.org/)** | `sudo apt install python3 -y` |
| **Permissões sudo** | Usuário com acesso administrativo |
| **[Ansible Docs](https://docs.ansible.com/)** | Referência oficial |

## Estrutura de Diretórios

```
ansible-lab/
├── ansible.cfg
├── inventory/
│   └── hosts.ini
├── playbook-ping.yml
└── playbook-install.yml
```

---

## Passo 1 – Instalar Ansible via APT

Atualize o sistema e instale o Ansible:

```bash
sudo apt update
sudo apt install ansible -y
```

Verifique a instalação:

```bash
ansible --version
```

**Saída esperada:**

```
ansible [core 2.12.10]
  config file = None
  configured module search path = ['/home/usuario/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /home/usuario/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Nov 20 2023, 15:14:05) [GCC 11.4.0]
  jinja version = 3.0.3
  libyaml = True
```

> **Nota:** A versão pode variar conforme a distribuição Ubuntu utilizada.

---

## Passo 2 – Criar Estrutura de Diretórios

Crie o diretório do laboratório:

```bash
mkdir -p ~/ansible-lab/inventory
cd ~/ansible-lab
```

Verifique a estrutura:

```bash
tree ~/ansible-lab
```

**Saída esperada:**

```
/home/usuario/ansible-lab
└── inventory

1 directory, 0 files
```

---

## Passo 3 – Configurar ansible.cfg

Crie o arquivo de configuração do Ansible:

```bash
cat > ansible.cfg << 'EOF'
[defaults]
inventory = ./inventory/hosts.ini
host_key_checking = False
interpreter_python = auto_silent
EOF
```

Visualize o conteúdo:

```bash
cat ansible.cfg
```

**Saída esperada:**

```ini
[defaults]
inventory = ./inventory/hosts.ini
host_key_checking = False
interpreter_python = auto_silent
```

> **Nota:** `host_key_checking = False` desabilita a verificação de chave SSH para facilitar testes locais.

---

## Passo 4 – Criar Inventário de Hosts

Configure o inventário com apenas localhost:

```bash
cat > inventory/hosts.ini << 'EOF'
[local]
localhost ansible_connection=local

[webservers]
# Hosts remotos podem ser adicionados aqui
# web1 ansible_host=192.168.1.10 ansible_user=ubuntu
EOF
```

Visualize o inventário:

```bash
cat inventory/hosts.ini
```

Liste os hosts configurados:

```bash
ansible-inventory --list
```

**Saída esperada:**

```json
{
    "_meta": {
        "hostvars": {
            "localhost": {
                "ansible_connection": "local"
            }
        }
    },
    "all": {
        "children": [
            "local",
            "ungrouped",
            "webservers"
        ]
    },
    "local": {
        "hosts": [
            "localhost"
        ]
    },
    "webservers": {}
}
```

---

## Passo 5 – Testar Conectividade com Playbook Ping

Crie o playbook de teste:

```bash
cat > playbook-ping.yml << 'EOF'
---
- name: Teste de conectividade com hosts
  hosts: local
  gather_facts: no
  tasks:
    - name: Executar ping nos hosts locais
      ansible.builtin.ping:

    - name: Exibir mensagem de sucesso
      ansible.builtin.debug:
        msg: "Conexão com {{ inventory_hostname }} estabelecida com sucesso!"
EOF
```

Execute o playbook:

```bash
ansible-playbook playbook-ping.yml
```

**Saída esperada:**

```
PLAY [Teste de conectividade com hosts] ****************************************

TASK [Executar ping nos hosts locais] ******************************************
ok: [localhost]

TASK [Exibir mensagem de sucesso] **********************************************
ok: [localhost] => {
    "msg": "Conexão com localhost estabelecida com sucesso!"
}

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

> **Nota:** O módulo `ping` não é um ping ICMP, mas sim um teste de conectividade Ansible.

---

## Passo 6 – Criar Playbook para Instalar Pacote

Crie um playbook para instalar o `tree`:

```bash
cat > playbook-install.yml << 'EOF'
---
- name: Instalar pacote localmente
  hosts: local
  become: yes
  tasks:
    - name: Atualizar cache do APT
      ansible.builtin.apt:
        update_cache: yes
        cache_valid_time: 3600

    - name: Instalar pacote tree
      ansible.builtin.apt:
        name: tree
        state: present

    - name: Verificar instalação do tree
      ansible.builtin.command: tree --version
      register: tree_version
      changed_when: false

    - name: Exibir versão do tree
      ansible.builtin.debug:
        msg: "{{ tree_version.stdout }}"
EOF
```

Execute o playbook:

```bash
ansible-playbook playbook-install.yml
```

**Saída esperada:**

```
PLAY [Instalar pacote localmente] **********************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [Atualizar cache do APT] **************************************************
ok: [localhost]

TASK [Instalar pacote tree] ****************************************************
changed: [localhost]

TASK [Verificar instalação do tree] ********************************************
ok: [localhost]

TASK [Exibir versão do tree] ***************************************************
ok: [localhost] => {
    "msg": "tree v1.8.0 (c) 1996 - 2018 by Steve Baker, Thomas Moore, Francesc Rocher, Florian Sesser, Kyosuke Tokoro"
}

PLAY RECAP *********************************************************************
localhost                  : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Verifique manualmente:

```bash
tree --version
```

---

## Passo 7 – Testar Idempotência

Execute o playbook novamente:

```bash
ansible-playbook playbook-install.yml
```

**Saída esperada:**

```
PLAY [Instalar pacote localmente] **********************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [Atualizar cache do APT] **************************************************
ok: [localhost]

TASK [Instalar pacote tree] ****************************************************
ok: [localhost]

TASK [Verificar instalação do tree] ********************************************
ok: [localhost]

TASK [Exibir versão do tree] ***************************************************
ok: [localhost] => {
    "msg": "tree v1.8.0 (c) 1996 - 2018 by Steve Baker, Thomas Moore, Francesc Rocher, Florian Sesser, Kyosuke Tokoro"
}

PLAY RECAP *********************************************************************
localhost                  : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

> **Nota:** Observe `changed=0` – o Ansible detectou que o pacote já está instalado e não realizou alterações.

---

## Passo 8 – Explorar Documentação de Módulos

Consulte a documentação do módulo `apt`:

```bash
ansible-doc ansible.builtin.apt
```

Liste os primeiros 20 módulos disponíveis:

```bash
ansible-doc -l | head -20
```

**Saída esperada:**

```
a10_server                                           Manage A10 Networks AX/SoftAX/Thunder/vThunder devices' server object
a10_server_axapi3                                    Manage A10 Networks AX/SoftAX/Thunder/vThunder devices
a10_service_group                                    Manage A10 Networks AX/SoftAX/Thunder/vThunder devices' service groups
a10_virtual_server                                   Manage A10 Networks AX/SoftAX/Thunder/vThunder devices' virtual servers
acl                                                  Set and retrieve file ACL information
add_host                                             Add a host (and alternatively a group) to the ansible-playbook in-memory inventory
airbrake_deployment                                  Notify airbrake about app deployments
alternatives                                         Manages alternative programs for common commands
ansible.builtin.add_host                             Add a host (and alternatively a group) to the ansible-playbook in-memory inventory
ansible.builtin.apt                                  Manages apt-packages
ansible.builtin.apt_key                              Add or remove an apt key
ansible.builtin.apt_repository                       Add and remove APT repositories
ansible.builtin.assemble                             Assemble configuration files from fragments
ansible.builtin.assert                               Asserts given expressions are true
ansible.builtin.async_status                         Obtain status of asynchronous task
ansible.builtin.blockinfile                          Insert/update/remove a text block surrounded by marker lines
ansible.builtin.command                              Execute commands on targets
ansible.builtin.copy                                 Copy files to remote locations
ansible.builtin.cron                                 Manage cron.d and crontab entries
ansible.builtin.debconf                              Configure a .deb package
``````

---

## Passo 9 – Remover Pacote Instalado (Limpeza)

Crie um playbook de limpeza:

```bash
cat > playbook-cleanup.yml << 'EOF'
---
- name: Remover pacote instalado no lab
  hosts: local
  become: yes
  tasks:
    - name: Remover pacote tree
      ansible.builtin.apt:
        name: tree
        state: absent
        purge: yes

    - name: Limpar cache do APT
      ansible.builtin.apt:
        autoclean: yes
        autoremove: yes
EOF
```

Execute a limpeza:

```bash
ansible-playbook playbook-cleanup.yml
```

**Saída esperada:**

```
PLAY [Remover pacote instalado no lab] *****************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [Remover pacote tree] *****************************************************
changed: [localhost]

TASK [Limpar cache do APT] *****************************************************
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Verifique a remoção:

```bash
tree --version
```

**Saída esperada:**

```
Command 'tree' not found
```

---

## Passo 10 – Desinstalar Ansible (Opcional)

Se desejar remover completamente o Ansible:

```bash
sudo apt remove ansible -y
sudo apt autoremove -y
```

Verifique a remoção:

```bash
ansible --version
```

**Saída esperada:**

```
Command 'ansible' not found
```

Remova o diretório do laboratório:

```bash
rm -rf ~/ansible-lab
```

---

## Resultado Esperado

Ao concluir este lab, você terá:

1. **Ansible instalado** via APT e verificado com `ansible --version`
2. **Playbooks funcionais** que testam conectividade e instalam pacotes localmente com sucesso
3. **Entendimento de idempotência** demonstrado pela execução repetida sem alterações desnecessárias
4. **Ambiente limpo** com remoção de pacotes e desinstalação opcional do Ansible

---

## Dicas para Discussão

| Tópico | Pergunta |
|--------|----------|
| **Métodos de instalação** | Por que APT pode ter versão mais antiga que PIP? Quando usar PPA? |
| **ansible.cfg** | O que acontece se não configurar `host_key_checking`? |
| **Inventory** | Como organizar inventário para ambientes dev, staging e produção? |
| **Idempotência** | Por que é importante? Quais módulos não são idempotentes? |
| **Become** | Quando usar `become: yes`? Como funciona a escalação de privilégios? |
| **Módulos vs Shell** | Por que usar `ansible.builtin.apt` em vez de `shell: apt install`? |
```