# 🧠 O que é o Terraform CLI?
O **Terraform CLI** é a interface oficial de linha de comando para usar o Terraform. Com ela, você interage com o código `.tf` que declara sua infraestrutura, executa planos, aplica mudanças, e muito mais.

Se você abrir um terminal e digitar:

bash
```
terraform
```
Vai ver todos os comandos disponíveis, divididos por grupos.

## 📖 Índice dos principais comandos do Terraform CLI
1. terraform init
2. terraform plan
3. terraform apply
4. terraform destroy
5. terraform fmt
6. terraform validate
7. terraform output
8. terraform providers
9. terraform show
10. terraform state
11. terraform graph
12. terraform version
13. terraform login / logout
14. terraform import

### 1. 🔧 terraform init
Inicializa o diretório de trabalho atual.

bash
```
terraform init
```
### O que ele faz:
- Baixa os **providers** declarados (ex: aws, azurerm, etc)
- Cria o `.terraform/` e o `.terraform.lock.hcl`
- Prepara o backend (se houver) para salvar o `terraform.tfstate`

Dica: sempre rode esse comando quando:
- Criar um novo projeto
- Mudar de provider
- Clonar um repo de alguém

### 2. 🔎 terraform plan
Mostra o que o Terraform **vai fazer**, sem aplicar nada ainda.

bash

```
terraform plan
```
**O que ele exibe**:

Recursos que serão criados, atualizados ou destruídos

Detalhes do que mudou

Dica: use antes de aplicar, sempre!

Você também pode exportar o plano:

bash

```
terraform plan -out=tfplan
```

### 3. 🚀 terraform apply
Aplica as mudanças. Cria/atualiza/destroi os recursos.

bash

```
terraform apply
```

Com o plano salvo:

bash

```
terraform apply tfplan
```

Confirma com `yes` ou use:

bash

```
terraform apply -auto-approve
```
⚠️ Cuidado com `-auto-approve` em **produção**!

### 4. 💣 terraform destroy
Remove todos os recursos gerenciados pelo Terraform.

bash

```
terraform destroy
```

Também pode usar com `-auto-approve`:

bash

```
terraform destroy -auto-approve
```
⚠️ Recomendado usar `terraform plan -destroy` **antes de aplicar**.

### 5. 🧼 terraform fmt
Formata os arquivos `.tf` para seguir o padrão oficial.

bash

```
terraform fmt
```
Muito útil com GitHub Actions ou **PRs**.

### 6. ✅ terraform validate
Valida a sintaxe dos arquivos `.tf`.

bash

```
terraform validate
```
⚠️ Não checa se os recursos existem, só valida se o código está certo.

### 7. 📤 terraform output
Exibe os outputs definidos em `outputs.tf`.

bash
```
terraform output
```

Exibir um específico:

bash
```
terraform output bucket_name
```

### 8. 📦 terraform providers
Lista os providers usados no projeto e suas versões.

bash
```
terraform providers
```

### 9. 🔍 terraform show
Mostra os detalhes do **estado atual** (arquivo `.tfstate`):

bash

```
terraform show
```

### 10. 🧠 terraform state
Gerencia diretamente o estado do Terraform (avançado):

bash

```
terraform state list       # Lista todos os recursos no state
terraform state show <recurso>  # Mostra detalhes de um recurso
terraform state rm <recurso>    # Remove do state sem deletar o recurso
```
⚠️ ⚠️ ⚠️ Muito cuidado! Alterar o state pode corromper a infraestrutura.

### 11. 🕸️ terraform graph
Gera um grafo de dependência dos recursos.

bash

```
terraform graph | dot -Tpng > graph.png
```
Útil pra visualizar dependências entre recursos.

### 12. ℹ️ terraform version
Mostra a versão atual do Terraform:

bash

```
terraform version
```

E do lock de dependências:

bash

```
terraform providers lock -platform=linux_amd64
```

### 13. 🔐 terraform login / logout
Usado para logar no Terraform Cloud:

bash

```
terraform login
terraform logout
```

### 14. 🔁 terraform import
Importa recursos **manualmente criados** para o state do Terraform.

bash

```
terraform import aws_s3_bucket.exemplo meu-bucket-existente
```
Muito útil pra “adotar” recursos criados manualmente.

## 💡 Dicas de uso no dia a dia
| Ação               | Comando                                             |
|--------------------|-----------------------------------------------------|
| Iniciar projeto     | `terraform init`                                    |
| Ver mudanças        | `terraform plan`                                    |
| Aplicar             | `terraform apply`                                   |
| Remover tudo        | `terraform destroy`                                 |
| Validar código      | `terraform validate`                                |
| Formatar            | `terraform fmt`                                     |
| Ver saídas          | `terraform output`                                  |
| Inspecionar estado  | `terraform show`, `terraform state`                 |

## 🧰 Exemplo de ciclo completo
bash

```
terraform init
terraform plan
terraform apply
terraform output
terraform destroy
```

# 🛠️ Makefile para Terraform

Exemplo:

bash
```
make init
make plan
make apply
make destroy
```
Salve este conteúdo em um arquivo chamado `Makefile` na raiz do seu projeto Terraform.

makefile

```
# ==========
# Makefile Terraform CLI Automático
# por jmarcelo 😎
# ==========

# 🧠 Variáveis padrão
PROFILE ?= default
REGION  ?= us-east-2
VARS    ?= -var-file=terraform.tfvars
ENV     ?=

# 📦 Inicialização
init:
	@echo "🔧 Inicializando Terraform..."
	AWS_PROFILE=$(PROFILE) terraform init

# 🔎 Verificar o plano
plan:
	@echo "🔍 Gerando plano..."
	AWS_PROFILE=$(PROFILE) terraform plan $(VARS)

# 🚀 Aplicar mudanças
apply:
	@echo "🚀 Aplicando infraestrutura..."
	AWS_PROFILE=$(PROFILE) terraform apply $(VARS)

# 💣 Destruir recursos
destroy:
	@echo "💥 Destruindo recursos..."
	AWS_PROFILE=$(PROFILE) terraform destroy $(VARS)

# ✅ Validar sintaxe
validate:
	@echo "✅ Validando arquivos Terraform..."
	terraform validate

# 🎨 Formatar os arquivos
fmt:
	@echo "🎨 Formatando arquivos Terraform..."
	terraform fmt -recursive

# 📤 Exibir outputs
output:
	@echo "📤 Outputs:"
	terraform output

# 📋 Listar estado
state:
	@echo "📋 Listando recursos no estado:"
	terraform state list

# 🧹 Limpeza
clean:
	@echo "🧹 Removendo arquivos temporários..."
	rm -rf .terraform terraform.tfstate* .terraform.lock.hcl

# 👀 Tudo em um
full:
	make fmt validate init plan apply output

# 🆘 Ajuda
help:
	@echo "Uso: make [comando]"
	@echo ""
	@echo "Comandos disponíveis:"
	@echo "  init        - Inicializar Terraform"
	@echo "  plan        - Gerar plano de execução"
	@echo "  apply       - Aplicar infraestrutura"
	@echo "  destroy     - Destruir recursos"
	@echo "  validate    - Validar sintaxe"
	@echo "  fmt         - Formatar arquivos .tf"
	@echo "  output      - Ver outputs definidos"
	@echo "  state       - Listar recursos no state"
	@echo "  clean       - Limpar arquivos gerados"
	@echo "  full        - Executa tudo: fmt + validate + init + plan + apply"

```
## ✅ Como usar
1. Salve o conteúdo acima como Makefile
2. No terminal, navegue até a raiz do seu projeto Terraform
3. Execute comandos assim:

bash
```
make init
make plan
make apply
make destroy
make validate
make full
```

## 🎯 Dicas
Para usar com outro perfil ou região:

bash
```
make plan PROFILE=dev REGION=us-east-1
```
- Se tiver múltiplos arquivos `.tfvars`, você pode ajustar a linha:

makefile
```
VARS ?= -var-file=terraform.dev.tfvars
```
# 🧰 Taskfile.yml para Terraform

Crie um arquivo chamado `Taskfile.yml` na raiz do seu projeto:

yaml
```
version: '3'

vars:
  PROFILE: default
  REGION: us-east-2
  VARS: "-var-file=terraform.tfvars"

tasks:
  init:
    desc: "🔧 Inicializar o Terraform"
    cmds:
      - AWS_PROFILE={{.PROFILE}} terraform init

  plan:
    desc: "🔍 Gerar plano Terraform"
    cmds:
      - AWS_PROFILE={{.PROFILE}} terraform plan {{.VARS}}

  apply:
    desc: "🚀 Aplicar mudanças"
    cmds:
      - AWS_PROFILE={{.PROFILE}} terraform apply {{.VARS}}

  destroy:
    desc: "💣 Destruir infraestrutura"
    cmds:
      - AWS_PROFILE={{.PROFILE}} terraform destroy {{.VARS}}

  validate:
    desc: "✅ Validar sintaxe dos arquivos Terraform"
    cmds:
      - terraform validate

  fmt:
    desc: "🎨 Formatar arquivos Terraform"
    cmds:
      - terraform fmt -recursive

  output:
    desc: "📤 Exibir outputs"
    cmds:
      - terraform output

  state:
    desc: "📋 Listar recursos no estado"
    cmds:
      - terraform state list

  clean:
    desc: "🧹 Limpar arquivos temporários"
    cmds:
      - rm -rf .terraform terraform.tfstate* .terraform.lock.hcl

  full:
    desc: "👷 Executar fmt, validate, init, plan e apply"
    deps: [fmt, validate, init, plan, apply
```

## 🚀 Como usar o Taskfile
### 1. Instale o Task (CLI)
Linux/macOS:

bash
```
brew install go-task/tap/go-task
```

Ou manual:

bash
```
curl -sSL https://taskfile.dev/install.sh | sh
```

**Windows**:

Use o Scoop:

powershell
```
scoop install go-task
```

### 2. Usar os comandos:
bash

```
task init
task plan
task apply
task full
```
Você também pode passar variáveis direto:

bash
```
task plan -- PROFILE=dev REGION=us-east-1
```
## 💡 Vantagens do Task
- Portável (Linux, Windows, CI/CD)
- Melhor estrutura e logs
- Tarefas com dependência (deps)
- Substitui Make + Bash + Batch scripts