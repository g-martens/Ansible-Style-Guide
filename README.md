```
  .--.  .-. .-. .----.-..----..-.   .----.    .----.---.-.  .-.-.   .----.    .---..-. .-..-..----..----.
 / {} \ |  `| |{ {__ | || {}  } |   | {_     { {__{_   _} \/ /| |   | {_     /   __} { } || || {}  \ {_  
/  /\  \| |\  |.-._} } || {}  } `--.| {__    .-._} }| |  }  { | `--.| {__    \  {_ } {_} || ||     / {__ 
`-'  `-'`-' `-'`----'`-'`----'`----'`----'   `----' `-'  `--' `----'`----'    `---'`-----'`-'`----'`----'

```
# Topics

## Recommendations
### Overall
- keep it simple, complexity kills productivity
- optimize for readability, if something is easy to read then it's easy to understand and easy to troubleshoot
- use a good editor like VSCode, VSCodium, Sublime Text Editor etc and even try vim-ansible
- think declaratively understanding that your playbooks aren't code but the declared state that ansible will synchronize your inventory hosts to
- stay organized and use version control
- adopt a style guide or create one yourself for your own needs
- don't reinvent the wheel and use roles where you can and pick trusted or well known authors and even better use Red Hat Certified Collections
- use variables and while there are 22 different ways to use variables, you should use them in a way that makes sense for your solution
- take advantage of inventory groups and the group_vars/ and host_vars/ directories, remembering that hosts can be members of many groups structured by geography, environment, purpose, etc
- endeavour to use dynamic inventories to allow Ansible to contact an infrastructure provider to enumerate the latest list of inventory hosts known to that provider (e.g. Satellite knows which clients are currently registered with it)
- use descriptive variable names and avoid naming your variables in a way that conflicts with module options
- name your plays, blocks, tasks, handlers
- "use includes to your advantage: divide and conquer"
- use modules like command and shell when a suitable module cannot be found
- use {{ ansible_managed }} to mark files as being managed by Ansible so that users don't overwrite their content
- enable privilege escalation only when needed
- avoid automating by logging in as root as transactions are harder to audit, use sudo instead
- run your playbooks from a centralized controller
- use execution environments for frequently executed Ansible operations which require customizations in the form of collections, python dependencies, and system executables
- Use ansible-lint to verify if your code match the best practices

## Roles
- Maintain each role in its own version control repository. Ansible works well with Git-based repositories.
- Use variables to configure roles so that you can reuse the role to perform similar tasks in similar circumstances.
- Avoid storing sensitive information in a role, such as passwords or SSH keys. Configure role variables that are used to contain sensitive values when called in a play with default values   that are not sensitive. Playbooks that use the role are responsible for defining sensitive variables through Ansible Vault variable files or other methods.
- Use the ansible-galaxy role init command to start your role, and then remove any unnecessary files and directories.
- Create and maintain README.md and meta/main.yml files to document the role's purpose, author, and usage.
- Keep your role focused on a specific purpose or function. Instead of making one role do many things, write more than one role.
- Reuse roles often.
- Define dependencies in meta/main.yml

## Variables
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

1. command line values (for example, -u my_user, these are not variables) 1
2. role defaults (as defined in Role directory structure) 1
3. inventory file or script group vars 2
4. inventory group_vars/all 3
5. playbook group_vars/all 3
6. inventory group_vars/* 3
7. playbook group_vars/* 3
8. inventory file or script host vars 2
9. inventory host_vars/* 3
10. playbook host_vars/* 3
11. host facts / cached set_facts 4
12. play vars
13. play vars_prompt
14. play vars_files
15. role vars (as defined in Role directory structure)
16. block vars (only for tasks in block)
17. task vars (only for the task)
18. include_vars
19. set_facts / registered vars
20. role (and include_role) params
21. include params
22. extra vars (for example, -e "user=my_user")(always win precedence)

### Footnotes
1. Tasks in each role see their own role’s defaults. Tasks defined outside of a role see the last role’s defaults.
2. **(1,2)** Variables defined in inventory file or provided by dynamic inventory.
3. **(1,2,3,4,5,6)** Includes vars added by ‘vars plugins’ as well as host_vars and group_vars which are added by the default vars plugin shipped with Ansible.
4. When created with set_facts’s cacheable option, variables have the high precedence in the play, but are the same as a host facts precedence when they come from the cache.


See for more info about vars: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable

## Booleans
For Booleans you have multiple Options to set something on or of:
- True or False
- Yes or No
- Y or N
- 1 or 0

I will use always True or False

**Example:**
```yaml
# Bad examples
enabled: 1
enabled: Y
enabled: on

# Good example
enabled: True
```

## Comments
Comments are Always starting with an # and an Space. after this, the first letter always have an uppercase

**Example:**
```yaml
#this comment is ugly
# This is an Good example of an comment
```

## Modules
### FQCN
Always use the FQCN of an module

**Example:**
```yaml
# Bad example
file:

# Good Example
ansible.builtin.file:
```

### Task Description
It is important to make sure that the task name is exaclty what the task is doing.
for this, think declarative. 

**Example:**
```yaml
# Bad Example
- name: Create user
  ansible.builtin.user:
    name: james
    shell: /bin/bash
    groups: admins,developers
    append: True

# Good Example
- name: Make sure the user james exist, his default shell is bash, and he is member of the groups admins and developers
  ansible.builtin.user:
    name: james
    shell: /bin/bash
    groups: admins,developers
    append: True
```

### Shell or command module
Only use this module when there is no module available for your task.
If you have to use this module, make sure that you use error handling.
You can use:
- changed_when
- failed_when

**Example:**
```yaml
# Bad example
- name: print "Changed in screen"
  ansible.builtin.shell: | 
    echo "changed"

# Good example
- name: print "Changed in screen"
  ansible.builtin.shell: | 
    echo "changed"
  register: output
  changed_when: "'changed' in output.stdout"

```

### Formating
For readability, make sure that u use the module as last in the task.
This means that if you use: when, loop, failed_when, changed_when, ok_when, register etc. this become before the tasks.

```yaml
# Bad Example
- name: print "Changed in screen"
  ansible.builtin.shell: | 
    echo "changed"
  register: output
  changed_when: "'changed' in output.stdout"

# Good example
- name: print "Changed in screen"
  register: output
  changed_when: "'changed' in output.stdout"
  ansible.builtin.shell: | 
    echo "changed"

```


# Tips
If you use VIM to edit your YAML files, use the following line in .vimrc

**basic**
```bash
autocmd FileType yaml setlocal ai ts=2 sw=2 et
```