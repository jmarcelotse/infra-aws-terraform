# Primeiro script
## 🧾 Objetivo do script
- Criar um bucket S3 privado e gerenciado por código com:
- Provider AWS
- Variáveis
- Output
- Arquivos separados
- Comentários explicativos

## 📂 Estrutura do projeto
bash

```
terraform-s3/
├── main.tf
├── variables.tf
├── terraform.tfvars
├── outputs.tf
```

## 📄 main.tf
hcl

```
# Declara o provedor AWS
provider "aws" {
  region  = var.region
  profile = var.aws_profile
}

# Recurso: S3 Bucket
resource "aws_s3_bucket" "meu_bucket" {
  bucket        = var.bucket_name
  force_destroy = true  # permite deletar o bucket mesmo com objetos

  tags = {
    Name        = var.bucket_name
    Environment = "dev"
  }
}
```

## 📄 variables.tf
hcl

```
# Região padrão
variable "region" {
  description = "Região da AWS"
  type        = string
  default     = "us-east-2"
}

# Nome do perfil AWS CLI
variable "aws_profile" {
  description = "Nome do perfil AWS CLI"
  type        = string
  default     = "default"
}

# Nome do bucket S3
variable "bucket_name" {
  description = "Nome único do bucket S3"
  type        = string
}
```

## 📄 terraform.tfvars
hcl
```
bucket_name  = "meu-primeiro-bucket-terraform-2025"
aws_profile  = "default"
region       = "us-east-2"
```

## 📄 outputs.tf
hcl

```
output "bucket_name" {
  description = "Nome do bucket criado"
  value       = aws_s3_bucket.meu_bucket.id
}
```

## 🚀 Como executar
bash
```
cd terraform-s3

# 1. Inicializa o projeto
terraform init

# 2. Mostra o plano
terraform plan

# 3. Aplica o código (e cria o bucket)
terraform apply

# 4. Visualiza os outputs
terraform output
```

## 🧽 Como destruir (remover tudo)
bash
```
terraform destroy
```

## 🧠 O que você aprendeu com esse primeiro script?
✅ Usar `provider`, `resource`, `variable`, `output`
✅ Separar código por responsabilidade
✅ Aplicar boas práticas desde o início
✅ Criar seu primeiro recurso real na AWS com código
