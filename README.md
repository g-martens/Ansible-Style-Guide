# Ansible-Style-Guide
On this page you can find my style Guide rules for ansible


## Topics
### Variables
When you create an variable, keep your variable names descriptive.
**Example:**
```yaml
# Good Examples
var_http_unsecure_port: 80
var_http_secure_port: 443

# Bad example
http: 80
```
### Variables precedence
Ansible does apply variable precedence, and you might have a use for it. Here is the order of precedence from least to greatest (the last listed variables override all other variables):
- command line values (for example, -u my_user, these are not variables) 1
- role defaults (as defined in Role directory structure) 1
- inventory file or script group vars 2
- inventory group_vars/all 3
- playbook group_vars/all 3
- inventory group_vars/* 3
- playbook group_vars/* 3
- inventory file or script host vars 2
- inventory host_vars/* 3
- playbook host_vars/* 3
- host facts / cached set_facts 4
- play vars
- play vars_prompt
- play vars_files
- role vars (as defined in Role directory structure)
- block vars (only for tasks in block)
- task vars (only for the task)
- include_vars
- set_facts / registered vars
- role (and include_role) params
- include params
- extra vars (for example, -e "user=my_user")(always win precedence)

#### Footnotes
- [1] Tasks in each role see their own role’s defaults. Tasks defined outside of a role see the last role’s defaults.
- [2] (1,2) Variables defined in inventory file or provided by dynamic inventory.
- [3] (1,2,3,4,5,6) Includes vars added by ‘vars plugins’ as well as host_vars and group_vars which are added by the default vars plugin shipped with Ansible.
- [4] When created with set_facts’s cacheable option, variables have the high precedence in the play, but are the same as a host facts precedence when they come from the cache.


See for more info about vars: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable

### Booleans
For Booleans you have multiple Options to set something on or of:
- True or False
- Yes or No
- Y or N
- 1 or 0

I will use always True or False

**Example:**
```yaml
enabled: True
```

### Comments
Comments are Always starting with an # and an Space. after this, the first letter always have an uppercase

**Example:**
```yaml
# This is an example of an comment
```

### Modules
#### FQCN
Always use the FQCN of an module

**Example:**
```yaml
ansible.builtin.file:
```

#### Task Description
It is important to make sure that the task name is exaclty what the task is doing.
for this, think declarative. 

**Example:**
```yaml
- name: Make sure the user james exist, his default shell is bash, and he is member of the groups admins and developers
  ansible.builtin.user:
    name: james
    shell: /bin/bash
    groups: admins,developers
    append: True
```

#### Shell or command module
Only use this module when there is no module available for your task.
If you have to use this module, make sure that you use error handling.
You can use:
- changed_when
- failed_when

**Example:**
```yaml
- name: print "Changed in screen"
  ansible.builtin.shell: | 
    echo "changed"
  register: output
  changed_when: "'changed' in output.stdout"
```




## Tips
If you use VIM to edit your YAML files, use the following line in .vimrc

**basic**
```bash
autocmd FileType yaml setlocal ai ts=2 sw=2 et
```

**Advanced**
```bash
execute pathogen#infect()

syntax on
filetype plugin indent on

autocmd FileType yaml setlocal ai et sw=2 ts=2 sts=2 cuc

set foldlevelstart=20

set nu

"" replace tabs with spaces in YAML files
let @r = ':%retab|
'

"" enable paste mode
let @p = ':set paste
'

"" disable paste mode
let @n = ':set nopaste
'

"" remove trailing spaces
let @t = ':%s/\s\+$//e
'

let g:ale_echo_msg_format = '[%linter%] %s [%severity%]'
let g:ale_sign_error = '✘'
let g:ale_sign_warning = '⚠'
let g:ale_lint_on_text_changed = 'never'

"" Remap CTRL-W (:winc[md]
noremap <Leader>w <C-w>

```