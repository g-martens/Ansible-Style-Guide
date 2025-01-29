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