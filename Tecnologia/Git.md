---
tags:
  - Tecnologia
---
O **Git** é um sistema de controle de versão distribuído, amplamente utilizado para rastrear mudanças em arquivos e coordenar o trabalho entre desenvolvedores em projetos de software. Ele permite que vários programadores trabalhem em um projeto simultaneamente sem sobrescrever as alterações uns dos outros. Além disso, o Git armazena um histórico de todas as mudanças feitas no código, facilitando o rastreamento de problemas, reverter versões antigas e trabalhar em diferentes versões do projeto (branches).
### Conceitos básicos do Git:

1. **Repositório (Repository)**: Local onde o histórico do projeto é armazenado. Pode estar na sua máquina (local) ou em um servidor (remoto), como GitHub, GitLab, etc.
2. **Branch**: Uma ramificação do projeto, permitindo que você trabalhe em diferentes partes do código simultaneamente sem afetar o branch principal (geralmente chamado de `main` ou `master`).
3. **Commit**: Registro de uma "foto" do seu código em um determinado momento, com uma mensagem explicando a mudança.
4. **Merge**: Combinar as mudanças de diferentes branches em um único branch.
### Principais comandos do Git:

 **`git init`**: Inicializa um novo repositório Git em um diretório.
```bash
git init
```

**`git clone [URL]`**: Clona (copia) um repositório remoto para sua máquina local.
```bash
git clone https://github.com/usuario/repositorio.git
```

**`git status`**: Mostra o status atual do repositório, incluindo alterações pendentes e arquivos não rastreados.
```bash
git status
```

**`git add [arquivo]`**: Adiciona arquivos ao "stage", preparando-os para o commit.
```bash
git add arquivo.txt
```

Para adicionar todos os arquivos modificados:
```bash
git add .
```

**`git commit -m "[mensagem]"`**: Faz um commit, registrando as mudanças com uma mensagem explicativa.
```bash
git commit -m "Adiciona a funcionalidade X"
```

**`git push`**: Envia (faz upload) dos commits do repositório local para o repositório remoto.
```bash
git push
```

**`git pull`**: Baixa (faz download) as atualizações do repositório remoto e mescla com o seu branch local.
```bash
git pull
```

**`git branch`**: Lista todos os branches no repositório. Também pode ser usado para criar ou deletar branches.
```bash
git branch
```

Para criar um novo branch:
```bash
git branch nome-do-branch
```

**`git checkout [branch]`**: Muda para o branch especificado.
```bash
git checkout nome-do-branch
```

Para criar e mudar para um novo branch:
```bash
git checkout -b nome-do-branch
```

 **`git merge [branch]`**: Mescla o branch especificado no branch atual.
```bash
git merge nome-do-branch
```

 **`git log`**: Exibe o histórico de commits.
```bash
git log
```

 **`git reset`**: Reverte alterações no repositório. Pode ser usado para desfazer commits e mudanças no código.
- Para reverter para o commit anterior:
```bash
git reset --hard HEAD~1
```

 **`git stash`**: Armazena temporariamente mudanças que você ainda não quer commitar.
```bash
git stash
```

 **`git remote add origin [URL]`**: Associa seu repositório local a um repositório remoto.
```bash
git remote add origin https://github.com/usuario/repositorio.git
```

**`git fetch`**: Baixa atualizações do repositório remoto, mas não faz o merge.
```bash
git fetch
```

## Fluxo básico de trabalho com Git:

1. **Clonar ou inicializar um repositório** (`git clone` ou `git init`)
2. **Criar/editar arquivos**
3. **Adicionar mudanças ao stage** (`git add`)
4. **Fazer commit das mudanças** (`git commit`)
5. **Enviar para o repositório remoto** (`git push`)
6. **Atualizar seu código com as mudanças remotas** (`git pull`)