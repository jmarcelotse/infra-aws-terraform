# Primeiro script com variáveis de ambiente

## 🧾 Objetivo
Criar um bucket S3 usando variáveis de ambiente no lugar de `terraform.tfvars`.

## 📂 Estrutura do Projeto
bash
```
terraform-s3-env/
├── main.tf
├── variables.tf
├── outputs.tf
```
Não usaremos `terraform.tfvars`, porque os valores virão **de fora**, via `export VARIAVEL=valor`.

## 📄 main.tf
hcl
```
provider "aws" {
  region  = var.region
  profile = var.aws_profile
}

resource "aws_s3_bucket" "meu_bucket" {
  bucket        = var.bucket_name
  force_destroy = true

  tags = {
    Name        = var.bucket_name
    Environment = "dev"
  }
}
```

## 📄 variables.tf
hcl
```
variable "region" {
  description = "Região da AWS"
  type        = string
}

variable "aws_profile" {
  description = "Perfil AWS configurado na CLI"
  type        = string
}

variable "bucket_name" {
  description = "Nome único do bucket S3"
  type        = string
}
```

## 📄 outputs.tf
hcl

```
output "bucket_name" {
  description = "Nome do bucket criado"
  value       = aws_s3_bucket.meu_bucket.id
}
```

## 🧪 Executando com variáveis de ambiente
No terminal, antes de rodar qualquer comando:

bash

```
export TF_VAR_region=us-east-2
export TF_VAR_aws_profile=default
export TF_VAR_bucket_name=meu-bucket-terraform-com-envs
Importante: o prefixo TF_VAR_ é interpretado automaticamente pelo Terraform.
```

## 👟 Rodando o ciclo completo
bash

```
terraform init
terraform plan
terraform apply
```

## 💣 Destruindo os recursos
bash

```
terraform destroy
```

## 🧠 Como isso funciona?
O Terraform lê automaticamente variáveis de ambiente com o prefixo `TF_VAR_` e associa ao nome da variável definida em `variables.tf`.
| Variável no código   | Variável no ambiente     |
|-----------------------|---------------------------|
| `var.region`          | `TF_VAR_region`           |
| `var.bucket_name`     | `TF_VAR_bucket_name`      |
| `var.aws_profile`     | `TF_VAR_aws_profile`      |

## 🔒 Segurança
- **Nunca exporte secrets** diretamente. Prefira usar SSM Parameter Store, Secrets Manager, Vault etc.
- Você pode usar `.env` com `direnv` ou scripts para automatizar o export das variáveis.

## 🧰 Dica bônus: criar um script .env.sh
bash

```
#!/bin/bash
export TF_VAR_region="us-east-2"
export TF_VAR_aws_profile="default"
export TF_VAR_bucket_name="meu-bucket-env-script"
```
E depois:

bash
```
source .env.sh
terraform apply
```

## ✅ Recapitulando
- Criamos o mesmo bucket S3 com Terraform
- Agora usando somente variáveis de ambiente
- Sem depender de terraform.tfvars
- Com código limpo, seguro e reutilizável