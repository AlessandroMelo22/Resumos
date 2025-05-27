# Comandos:

### Como criar um repositório local:

- Utilizando o comando `git init` para inicialiar um novo repositório Git em um diretório:   
 `git init`

- Clonando um repositório do GitHub para sua maquina local com o comando `git clone`:  
 `git clone <url-do-repositório-remoto> <novo-nome>(opcional)`
##  
### Conectando um repositório local à um repositório remoto:  
- `git remote add <nome-do-repositório-remoto> <url-do-repositório-remoto>` ➡ conecta um repositório local inicializado em um repositório remoto (no caso o GitHub).
Por convenção damos o nome do repositório remoto de `origin`.
##  
### Verificar os repositórios remotos que estão vinculados ao repositório Git local:
- `git remote -v` ➡ Mostra quais repositórios remotos estão conectados ao seu repositório Git.
##  
### Verificando os status dos arquivos do repositório Git:
- `git status` ➡ Mostra qual o status dos arquivos. Os arquivos podem apresentar dois status:

- **Não Rastreados**: Arquivos que o Git identificou, porém que ainda não estão sob o seu controle (arquivos novos, que não esta).  
- **Rastreados**: Arquivos que já estão sob o controle do Git (estão presentes no último commit). Os arquivos Rastreados podem ser subdivididos em:
  - **Não modificados:** arquivos que estão presentes no último commit e que não foram modificados.
  - **Modificados:** arquivos que estão no último commit porém foram modificados e esperam ser mandados para área de _stage_.
  - **Preparados:** arquivos modificados que foram adicionados a área de _stage_ e aguardam o próximo commit.
##  
### Adicionando arquivos a _Stage Area_:
- `git add` ➡ 
