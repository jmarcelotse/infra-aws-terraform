# 🧠 Interpolação, `locals` e `outputs` no Terraform
Esses recursos permitem:

- ✅ Reutilizar valores calculados
- ✅ Melhorar legibilidade do código
- ✅ Criar estruturas dinâmicas e customizadas
- ✅ Exibir informações úteis ao final de um terraform apply

## 📖 Índice
1. O que é interpolação?
2. O que são locals?
3. Como funcionam outputs?
4. Exemplos práticos com S3
5. Casos de uso comuns
6. Dicas e boas prática

### 🔁 1. O que é interpolação?
Interpolação é quando usamos **valores dinâmicos** dentro de strings, blocos ou expressões.

No Terraform, usamos `var.`, `local.`, `module.`, `resource.` para acessar valores.

**Sintaxe antiga (ainda funciona)**:

hcl
```
"${var.region}"
```

**Sintaxe moderna (recomendada)**:
hcl

```
var.region
```

## ✅ Exemplos de interpolação:
hcl

```
# Nome do bucket com prefixo do ambiente
bucket = "${var.env}-${var.bucket_name}"
# ou versão moderna:
bucket = "${var.env}-${var.bucket_name}"
```

hcl

```
# Tag dinâmica usando interpolação
tags = {
  criador = var.owner
  ambiente = var.env
  projeto = "s3-${var.bucket_name}"
}
```

## 🧱 2. O que são locals?
`locals` servem para **criar valores temporários ou derivados**, que não precisam ser variáveis externas. São como variáveis internas do Terraform.

## 🧪 Exemplo simples:
hcl

```
locals {
  nome_padronizado = "${lower(var.env)}-${replace(var.bucket_name, "_", "-")}"
}
```

Uso:

hcl

```
resource "aws_s3_bucket" "this" {
  bucket = local.nome_padronizado
}
```

## 🧠 Boas razões para usar locals:
| Motivo                | Exemplo                                                                 |
|------------------------|-------------------------------------------------------------------------|
| Formatação de nomes    | `replace`, `lower`, `join` – usados para gerar nomes padronizados       |
| Centralizar lógica     | Evita repetir cálculos ou expressões complexas                          |
| Legibilidade           | `local.bucket_tags` é mais claro do que 3 concatenações inline          |
| Evitar repetição       | DRY (Don’t Repeat Yourself) – facilita manutenção e reutilização        |

## 🧰 Exemplo avançado:
hcl

```
locals {
  ambiente  = var.env
  bucket_id = "${var.env}-${var.bucket_name}"
  tags_comuns = merge(
    var.tags,
    {
      provisionado_por = "Terraform"
      ambiente = var.env
    }
  )
}
```
### 📤 3. Como funcionam `outputs`?
Outputs permitem expor valores úteis depois do terraform apply. Esses valores podem ser usados para:
- Debug local
- Integração com outros módulos
- Exportação para CI/CD
- Documentação do estado

## 📄 Sintaxe básica:
hcl
```
output "nome_do_bucket" {
  description = "Nome final do bucket criado"
  value       = aws_s3_bucket.this.id
}

```

## 📌 Exemplo com locals e interpolação:
hcl

```
output "bucket_formatado" {
  value = local.nome_padronizado
}
```

## 🔒 Output sensível:
hcl

```
output "senha_db" {
  value     = var.senha
  sensitive = true
}
```

## 🧪 Exemplo completo integrando tudo
`variables.tf`

hcl

```
variable "env" {
  default = "dev"
}

variable "bucket_name" {
  default = "meu-bucket"
}

variable "tags" {
  default = {
    projeto = "infra"
    criado_por = "jmarcelo"
  }
}
```

`main.tf`

hcl
```
locals {
  nome_bucket_final = "${var.env}-${replace(var.bucket_name, "_", "-")}"

  tags_completas = merge(
    var.tags,
    {
      ambiente = var.env
      provisionado_por = "terraform"
    }
  )
}

resource "aws_s3_bucket" "exemplo" {
  bucket = local.nome_bucket_final
  tags   = local.tags_completas
}
```

`outputs.tf`

hcl

```
output "bucket_name" {
  description = "Nome final do bucket"
  value       = aws_s3_bucket.exemplo.id
}

output "tags_aplicadas" {
  value = local.tags_completas
}
```

## ⚙️ Comando para visualizar os outputs:
bash

```
terraform output
terraform output bucket_name
```

## 🔥 Casos de uso comuns para `locals` e `outputs`
| Caso                           | Solução                                                             |
|--------------------------------|----------------------------------------------------------------------|
| Padrão de nomes (naming convention) | `locals` com `replace` / `format`                                 |
| Geração de lista de buckets     | `output` com `[for b in var.buckets : b.name]`                      |
| Formatação de string            | `format("prefix-%s", var.nome)`                                     |
| Combinação de tags comuns       | `merge()` com tags do módulo                                        |
| Outputs sensíveis               | `output` com `sensitive = true`                                     |

## ✅ Boas práticas
- Use `locals` para lógica interna
- Use `outputs` apenas para o que precisa ser exposto
- Nunca faça interpolação tipo `"${var.nome}"` se `var.nome` já é string
- Use `format()`, `join()`, `replace()`, `substr()` com moderação e clareza