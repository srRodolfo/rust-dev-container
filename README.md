# Projeto Rust-Dev-Container - Ambiente de Desenvolvimento Docker

Este repositório contém um **ambiente de desenvolvimento completo** utilizando Docker, pronto para Rust.  

O ambiente foi configurado para ser usado com IDEs como RustRover ou Editores como VSCode.

---

### Estrutura do Projeto

- `services/` Arquivos de configuração do ambiente
- `services/rust-slim/` Dockerfile do Rust
- `.env` Arquivo contendo variáveis de ambiente (portas, usuários, senhas)
- `docker-compose.yml` Orquestração dos serviços (Rust, etc.)
- `app/` Código-fonte dos projetos (montado nos containers)
- `app/hello_cargo` Pasta de um projeto de exemplo

```
project-root/
├─ services/                      
│   └─ rust_slim/
├─ docker-compose.yml                          
├─ .env                          
└─ app/                          
    └─ hello_cargo/  
```
---

### Pré-requisitos

- Docker e Docker Compose instalados
- Sistema operacional compatível (Linux, macOS ou Windows)

---

### Configuração do Ambiente

1. Copie o conteúdo do arquivo `env.example` para `.env` e ajuste as variáveis conforme necessário:

```dotenv
# Nome base para todos os containers
CONTAINER_NAME=dev_container

# UID/GID para evitar problemas de permissão com volumes
PUID=1000
PGID=1000

# Rust
RUST_VERSION=1.98
RUST_PORT=8080
```
2. Montar o ambiente com os dados do Docker Compose:

```bash
docker compose build --no-cache
```
- `build` garante que as imagens sejam construídas ignorando o cache com a flag `--no-cache` caso haja alterações no Dockerfile.
- O Rust estará disponível na versão definida em `RUST_VERSION` e na porta definida em `RUST_PORT`.

---

### Volumes e Persistência

- Código-fonte é montado no host em `/app` para `/app` dentro do container
- O cache do Cargo persiste no volume `cargo_cache`

---

### Comandos úteis

Acessar terminal do container Rust:
```bash
docker exec -it dev_container_rust bash
```

Executar o Cargo dentro do container Rust:
```bash
docker exec dev_container_rust cargo --version
```

Executar o Rustc dentro do container Rust:
```bash
docker exec dev_container_rust rustc --version
```

Executar o Rustup dentro do container Rust:
```bash
docker exec dev_container_rust rustup --version
```

---

### Atalhos (alias) para o Terminal Bash (Opcional)

1. Abra o seu terminal e digite:
```
sudo nano ~/.bashrc
```
2. Adicione o seguinte trecho de código ao seu `~/.bashrc`:
```
# Funcao para Rust no container
docker_rust_tools() {
  local tool="$1"
  shift
  local container=$(docker ps --format '{{.Names}}' | grep '_rust$' | head -n 1)
  local rel_path="${PWD#*/app}"

  if [ -n "$container" ]; then
    docker exec -it -w "/app${rel_path}" "$container" "$tool" "$@"
  else
    echo "Nenhum container Rust em execução. Rodando '$tool' no host."
    command "$tool" "$@"
  fi
}

alias cargo='docker_rust_tools cargo'
alias rustc='docker_rust_tools rustc'
alias rustup='docker_rust_tools rustup'
```
3. Depois, recarregue o Bash:
```bash
source ~/.bashrc
```
Como usar:
```bash
# Rodar comandos do rustc direto no container
rustc --version (exemplo de comando)

# Rodar comandos do cargo direto no container
cargo --version (exemplo de comando)

# Rodar comandos do rustup direto no container
rustup --version (exemplo de comando)
```
- O comando só funciona no diretório de projetos `app` e nos diretórios filhos.
- O comando detecta automaticamente o container do projeto que utiliza este repositório.
- Caso o container não esteja rodando, o comando será executado no host.

Feito para simplificar o desenvolvimento em projetos Rust modernos em containers separados, mas trabalhando de forma integrada com a IDE.
