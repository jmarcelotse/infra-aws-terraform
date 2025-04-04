# 📦 Tudo sobre Variáveis no Terraform (guia completo)

## 📖 Índice
1. O que são variáveis no Terraform?
2. Tipos de variáveis
3. Onde declarar e como usar
4. Formas de atribuir valores
5. Variáveis com validação
6. Variáveis sensíveis (secrets)
7. Variáveis condicionais e padrões
8. Exemplos práticos
9. Dicas e boas práticas
10. Comandos úteis

### 1. 💡 O que são variáveis no Terraform?
Variáveis são **valores reutilizáveis** que você pode definir para:
- Evitar repetição
- Mudar configurações sem alterar o código fonte
- Permitir reutilização de módulos
- Parametrizar ambientes (dev, staging, prod)

### 2. 🧬 Tipos de variáveis
| Tipo              | Exemplo                                                 |
|-------------------|----------------------------------------------------------|
| `string`          | `"us-east-2"`                                            |
| `number`          | `3`, `8080`                                              |
| `bool`            | `true`, `false`                                          |
| `list(string)`    | `["dev", "qa", "prod"]`                                  |
| `map(string)`     | `{ ambiente = "dev", projeto = "app" }`                  |
| `object({ ... })` | Estrutura de dados com campos nomeados                  |
| `any`             | Aceita qualquer tipo                                     |

### 3. 📄 Onde declarar variáveis?
Geralmente em um arquivo separado chamado `variables.tf`:

hcl

```
variable "region" {
  type        = string
  default     = "us-east-2"
  description = "Região da AWS"
}
```

E usa-se assim no código:

hcl

```
provider "aws" {
  region = var.region
}
```

### 4. ✍️ Formas de atribuir valores
1. `terraform.tfvars`

hcl

```
region      = "us-east-2"
bucket_name = "meu-bucket-prod"
```
2. **Linha de comando**

bash
```
terraform plan -var="region=us-east-2"
```

3. **Variáveis de ambiente**

bash

```
export TF_VAR_region=us-east-2
```

4. **Por arquivo `.tfvars` específico**

bash
```
terraform apply -var-file="dev.tfvars"
```

### 5. ✅ Validação de variáveis
Você pode validar valores com `validation`:

hcl

```
variable "bucket_name" {
  type = string
  validation {
    condition     = length(var.bucket_name) > 3
    error_message = "O nome do bucket deve ter mais de 3 caracteres."
  }
}
```

### 6. 🔐 Variáveis sensíveis (secrets)
Use `sensitive = true` para esconder valores nos planos e outputs:

hcl

```
variable "db_password" {
  type      = string
  sensitive = true
}
```

### 7. 🧠 Variáveis com padrão e condicionais
Padrão:

hcl

```
variable "env" {
  default = "dev"
}
```

**Condicional**:

hcl

```
resource "aws_s3_bucket" "bucket" {
  bucket = var.env == "prod" ? "meu-bucket-prod" : "meu-bucket-dev"
}
```

### 8. 🧪 Exemplos práticos
`variables.tf`

hcl

```
variable "region" {
  type        = string
  default     = "us-east-2"
  description = "Região padrão da AWS"
}

variable "bucket_name" {
  type        = string
  description = "Nome único do bucket"
}

variable "tags" {
  type = map(string)
  default = {
    projeto   = "terraform-bucket"
    ambiente  = "dev"
    criado_por = "jmarcelo"
  }
}
```

`main.tf`

hcl

```
resource "aws_s3_bucket" "meu_bucket" {
  bucket = var.bucket_name

  tags = var.tags
}
```

`terraform.tfvars`

hcl

```
bucket_name = "meu-bucket-variaveis"
```

### 9. 🧼 Dicas e boas práticas
- Nomeie variáveis de forma **descritiva**
- Sempre use `description` para documentação automática
- Use `type` para evitar erros de digitação
- **Nunca use dados sensíveis em `.tfvars` versionado**
- Use arquivos separados por ambiente: `dev.tfvars`, `prod.tfvars`

### 10. 🔧 Comandos úteis
| Comando                                 | Função                                      |
|-----------------------------------------|---------------------------------------------|
| `terraform plan -var="env=prod"`        | Passa variável diretamente na linha de comando |
| `terraform plan -var-file=prod.tfvars`  | Usa um arquivo `.tfvars` com valores         |
| `export TF_VAR_env=dev`                 | Passa variável via variável de ambiente      |
| `terraform console`                     | Testa e inspeciona valores de forma interativa |

## 🧠 Curiosidade: variável local (não externa)
hcl

```
locals {
  nome_formatado = "${upper(var.bucket_name)}"
}
```

Usa-se como:

hcl

```
output "nome_formatado" {
  value = local.nome_formatado
}
```

## ✅ Resumo
| Recurso           | Exemplo                                                  |
|-------------------|----------------------------------------------------------|
| Declaração        | `variable "region" { ... }`                              |
| Uso               | `var.region`                                             |
| Arquivo de valor  | `terraform.tfvars`                                       |
| CLI               | `-var`, `-var-file`                                      |
| Ambiente          | `TF_VAR_<nome>`                                          |
| Validação         | `validation { condition = ... }`                         |
| Segurança         | `sensitive = true`                                       |

# 🧠 Variáveis Avançadas no Terraform

## 📖 Índice
1. Por que usar variáveis complexas?
2. Listas (`list`)
3. Mapas (`map`)
5. Objetos (`object`)
6. Listas de Objetos (`list(object)`)
7. Objetos aninhados
8. Estrutura por ambiente (`map(object)`)
9. Exemplos práticos com S3
10. Validação com tipos complexos
11. Outputs com variáveis complexas
12. Próximos passos

### 1. ❓ Por que usar variáveis complexas?
Porque elas permitem:
- Mais organização
- Mais **flexibilidade e reutilização** entre ambientes
- Separar config por módulos
- Evitar repetição de código
- **Versionar ambientes facilmente**

### 2. 🧾 Listas (`list`)
hcl

```
variable "ambientes" {
  type        = list(string)
  default     = ["dev", "staging", "prod"]
}

output "primeiro" {
  value = var.ambientes[0]
}
```

### 3. 🗺️ Mapas (`map`)
hcl

```
variable "tags" {
  type = map(string)
  default = {
    ambiente   = "dev"
    criado_por = "jmarcelo"
  }
}
```

Uso:

hcl

```
tags = var.tags
```

### 4. 🧱 Objetos (object)
hcl

```
variable "config_s3" {
  type = object({
    bucket_name  = string
    versioning   = bool
    force_destroy = bool
  })

  default = {
    bucket_name   = "meu-bucket-objeto"
    versioning    = true
    force_destroy = true
  }
}
```

Uso:

hcl

```
resource "aws_s3_bucket" "exemplo" {
  bucket        = var.config_s3.bucket_name
  force_destroy = var.config_s3.force_destroy
}
```

### 5. 📦 Lista de Objetos (`list(object)`)
hcl

```
variable "buckets" {
  type = list(object({
    name           = string
    versioning     = bool
    force_destroy  = bool
  }))

  default = [
    {
      name          = "bucket-dev"
      versioning    = true
      force_destroy = false
    },
    {
      name          = "bucket-prod"
      versioning    = false
      force_destroy = true
    }
  ]
}
```

Uso (com `for_each`):

hcl

```
resource "aws_s3_bucket" "multi" {
  for_each      = { for b in var.buckets : b.name => b }
  bucket        = each.value.name
  force_destroy = each.value.force_destroy
}
```

### 6. 🔁 Objetos aninhados
hcl

```
variable "infra_config" {
  type = object({
    s3 = object({
      bucket_name    = string
      force_destroy  = bool
    })
    tags = map(string)
  })

  default = {
    s3 = {
      bucket_name   = "infra-aninhada-bucket"
      force_destroy = true
    }
    tags = {
      ambiente = "dev"
      projeto  = "avancado"
    }
  }
}
```

Uso:

hcl

```
bucket = var.infra_config.s3.bucket_name
tags   = var.infra_config.tags
```

### 7. 🌍 Estrutura por ambiente (map(object))
Ideal para multiambientes:

hcl

```
variable "ambientes" {
  type = map(object({
    bucket_name   = string
    versioning    = bool
  }))

  default = {
    dev = {
      bucket_name = "bucket-dev"
      versioning  = false
    }
    prod = {
      bucket_name = "bucket-prod"
      versioning  = true
    }
  }
}
```

Uso:

hcl

```
locals {
  ambiente_ativo = var.ambientes["prod"]
}

resource "aws_s3_bucket" "exemplo" {
  bucket = local.ambiente_ativo.bucket_name
}
```

### 8. 🧪 Exemplo real prático
`variables.tf`

hcl

```
variable "buckets" {
  type = list(object({
    nome   = string
    tags   = map(string)
    destroy = bool
  }))
}
```

`terraform.tfvars`

hcl

```
buckets = [
  {
    nome    = "bucket-dev-2025"
    destroy = true
    tags = {
      ambiente = "dev"
      dono     = "jmarcelo"
    }
  },
  {
    nome    = "bucket-prod-2025"
    destroy = false
    tags = {
      ambiente = "prod"
      dono     = "infra"
    }
  }
]
```

`main.tf`

hcl

```
resource "aws_s3_bucket" "buckets" {
  for_each      = { for b in var.buckets : b.nome => b }
  bucket        = each.value.nome
  force_destroy = each.value.destroy
  tags          = each.value.tags
}
```

### 9. 🛡️ Validação com tipos complexos
hcl

```
variable "config" {
  type = object({
    nome = string
    destroy = bool
  })

  validation {
    condition     = can(regex("^[a-z0-9-]+$", var.config.nome))
    error_message = "O nome do bucket deve conter apenas letras minúsculas, números e hífens."
  }
}
```

### 10. 📤 Outputs com objetos
hcl

```
output "buckets_nomes" {
  value = [for b in var.buckets : b.nome]
}
```

## ✅ Conclusão
Variáveis complexas permitem que você:
- Crie **infraestruturas parametrizadas e reutilizáveis**
- Defina vários ambientes com uma única base de código
- Trabalhe com listas, objetos, mapas e condições
- Mantenha **código limpo, DRY e escalável**

# 🧾 Template
- Usar uma variável do tipo map(`object`) com configs para `dev` e `prod`
- Criar buckets com base no ambiente selecionado
- Selecionar ambiente com uma variável simples (env = `"dev"` ou `"prod"`)

**Template completo com estrutura de ambientes** `dev` e `prod` usando `map(object)` no Terraform. Isso permite:
- ✅ Separar parâmetros por ambiente
- ✅ Aplicar lógica condicional e seleção dinâmica
- ✅ Escalar com apenas um código base para múltiplos ambientes

# 📁 Estrutura do Projeto
cpp

```
terraform-env-template/
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
```

## 📄 variables.tf
hcl

```
# Ambiente ativo (dev, prod, etc.)
variable "env" {
  type        = string
  description = "Ambiente atual (dev, prod)"
  default     = "dev"
}

# Configuração de buckets por ambiente
variable "bucket_config" {
  type = map(object({
    bucket_name    = string
    force_destroy  = bool
    enable_versioning = bool
    tags           = map(string)
  }))
  description = "Mapeamento de ambientes com configurações para buckets"
}
```

## 📄 terraform.tfvars
hcl

```
env = "dev"

bucket_config = {
  dev = {
    bucket_name        = "meu-bucket-dev-jmarcelo"
    force_destroy      = true
    enable_versioning  = false
    tags = {
      ambiente = "dev"
      criado_por = "jmarcelo"
    }
  }
  prod = {
    bucket_name        = "meu-bucket-prod-jmarcelo"
    force_destroy      = false
    enable_versioning  = true
    tags = {
      ambiente = "prod"
      criado_por = "infra"
    }
  }
}
```

## 📄 main.tf
hcl

```
provider "aws" {
  region  = "us-east-2"
  profile = "default"
}

locals {
  config = var.bucket_config[var.env]
}

resource "aws_s3_bucket" "ambiente" {
  bucket        = local.config.bucket_name
  force_destroy = local.config.force_destroy

  tags = local.config.tags
}

resource "aws_s3_bucket_versioning" "this" {
  bucket = aws_s3_bucket.ambiente.id

  versioning_configuration {
    status = local.config.enable_versioning ? "Enabled" : "Suspended"
  }
}
```

## 📄 outputs.tf
hcl

```
output "nome_do_bucket" {
  description = "Nome do bucket criado"
  value       = aws_s3_bucket.ambiente.id
}

output "versao_ativada" {
  value = local.config.enable_versioning
}
```

## ▶️ Como usar
### 1. Executar com ambiente dev (já definido no `.tfvars`)
bash

```
terraform init
terraform apply
```

### 2. Trocar para `prod`:
Altere `env = "prod"` no `terraform.tfvars` ou defina pela CLI:

bash

```
terraform apply -var='env=prod'
```

## 📌 Dica: separação por arquivos `.tfvars`
Você pode manter arquivos separados por ambiente:

bash

```
terraform apply -var-file="dev.tfvars"
terraform apply -var-file="prod.tfvars"
```

## 🛡️ Benefícios desse modelo
- Flexível para múltiplos ambientes
- Menos repetição de código
- Base unificada e fácil de versionar
- Pode ser expandido para múltiplos recursos, módulos e workspaces

# Módulo Terraform
**Módulo Terraform completo que aceita uma `list(object)` com configurações para múltiplos buckets S3**, de forma reutilizável, escalável e limpa.

Esse tipo de módulo é perfeito para:
- ✅ Criar diversos buckets de uma vez
- ✅ Aplicar regras personalizadas por bucket
- ✅ Reutilizar o módulo em qualquer ambiente

## 📁 Estrutura do projeto
bash

```
terraform-s3-multi/
├── main.tf               # chama o módulo
├── variables.tf
├── terraform.tfvars
├── modules/
│   └── s3_bucket/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
└── outputs.tf
```

## 🧠 O que vamos fazer
Criar um módulo `s3_bucket` que aceita:

hcl

```
buckets = [
  {
    name           = "bucket-1"
    force_destroy  = true
    versioning     = true
    tags = {
      ambiente = "dev"
    }
  },
  {
    name           = "bucket-2"
    force_destroy  = false
    versioning     = false
    tags = {
      ambiente = "prod"
    }
  }
]
```

### 🔧 1. `main.tf` (na raiz — consumidor do módulo)
hcl

```
provider "aws" {
  region  = "us-east-2"
  profile = "default"
}

module "buckets_s3" {
  source  = "./modules/s3_bucket"
  buckets = var.buckets
}
```

### 📄 2. `variables.tf` (raiz)
hcl

```
variable "buckets" {
  type = list(object({
    name           = string
    force_destroy  = bool
    versioning     = bool
    tags           = map(string)
  }))
  description = "Lista de buckets a serem criados"
}
```

### 📄 3. terraform.tfvars
hcl

```
buckets = [
  {
    name           = "bucket-dev-jmarcelo"
    force_destroy  = true
    versioning     = false
    tags = {
      ambiente = "dev"
      criado_por = "jmarcelo"
    }
  },
  {
    name           = "bucket-prod-jmarcelo"
    force_destroy  = false
    versioning     = true
    tags = {
      ambiente = "prod"
      criado_por = "infra"
    }
  }
]
```

### 📁 4. Módulo: modules/s3_bucket/main.tf
hcl

```
resource "aws_s3_bucket" "this" {
  for_each      = { for bucket in var.buckets : bucket.name => bucket }
  bucket        = each.value.name
  force_destroy = each.value.force_destroy
  tags          = each.value.tags
}

resource "aws_s3_bucket_versioning" "this" {
  for_each = aws_s3_bucket.this

  bucket = each.value.id

  versioning_configuration {
    status = var.buckets[lookup(keys(aws_s3_bucket.this), each.key)].versioning ? "Enabled" : "Suspended"
  }
}
```

### 📁 5. Módulo: modules/s3_bucket/variables.tf
hcl

```
variable "buckets" {
  type = list(object({
    name           = string
    force_destroy  = bool
    versioning     = bool
    tags           = map(string)
  }))
}
```

### 📁 6. Módulo: modules/s3_bucket/outputs.tf
hcl

```
output "bucket_names" {
  value = [for bucket in var.buckets : bucket.name]
}
```

### 📄 7. `outputs.tf` (raiz)
hcl

```
output "buckets_criados" {
  value = module.buckets_s3.bucket_names
}
```

## ▶️ Como usar
bash

```
terraform init
terraform plan
terraform apply
```
Resultado: dois buckets criados com configurações distintas 🎯

## 🧠 Explicações
- Usamos `for_each` no recurso, mapeando pelo nome do bucket
- O `lookup` recupera as configs da lista de objetos para o recurso versioning
- Módulo é **genérico e reutilizável**: pode ser usado por qualquer equipe

# terraform.tfvars.example
`terraform.tfvars.example` serve como **modelo/documentação** para times, pipelines ou novos membros do projeto. Ele mostra **o que precisa ser configurado, qual o formato esperado e exemplos de valores**.

Abaixo está um template completo e **comentado linha a linha**, para o módulo de múltiplos buckets S3 com `list(object)`.

## 📄 terraform.tfvars.example
hcl

```
# =======================================
# Exemplo de configuração do terraform.tfvars
# Para uso com o módulo de múltiplos buckets S3
# =======================================

# Ambiente atual (opcional, caso queira separar lógica por ambiente)
# env = "dev"

# Lista de buckets a serem criados
# Cada bucket deve conter:
# - name: Nome único do bucket (obrigatório, sem maiúsculas/acentos)
# - force_destroy: true = permite deletar o bucket com objetos dentro
# - versioning: true = habilita versionamento (útil para backups/logs)
# - tags: mapa de tags para organização/custos/ambiente/etc

buckets = [
  {
    name           = "bucket-dev-jmarcelo"    # nome do bucket S3
    force_destroy  = true                     # pode deletar com arquivos
    versioning     = false                    # versionamento desabilitado
    tags = {
      ambiente     = "dev"
      projeto      = "projeto-teste"
      criado_por   = "jmarcelo"
    }
  },
  {
    name           = "bucket-prod-jmarcelo"
    force_destroy  = false                    # evita destruir com arquivos dentro
    versioning     = true                     # versionamento habilitado
    tags = {
      ambiente     = "prod"
      projeto      = "ecommerce"
      criado_por   = "infra"
      confidencial = "sim"
    }
  }
]
```

## ✅ Como usar esse arquivo:
1. Copie para `terraform.tfvars`:

bash

```
cp terraform.tfvars.example terraform.tfvars
```

2. Ajuste os valores conforme o ambiente/projeto.
3. Rode:

bash

```
terraform plan
terraform apply
```

## 🔐 Dica de segurança
- Nunca commit esse arquivo com segredos (ex: senhas, chaves)
- Para segredos, prefira SSM Parameter Store, Secrets Manager ou Vault
