# 🪣 Como Criar um Bucket S3 Manualmente na AWS
## 🧠 O que é um bucket?
Um bucket é como uma pasta raiz no S3. Ele armazena objetos (arquivos, imagens, logs, backups etc). Cada bucket precisa ter um nome único globalmente e está associado a uma região AWS.

## ✅ Pré-requisitos
- Ter uma conta AWS ativa
- Ter acesso ao painel (console) da AWS
- Permissões de IAM para criar recursos no S3

## 🪜 Passo a Passo – Criar Bucket S3 via Console
### 1. Acesse o Console AWS
🔗 https://s3.console.aws.amazon.com/s3

### 2. Clique em “Criar bucket”
Fica no canto superior direito.

### 3. Configure o bucket
| Campo            | Valor sugerido                             |
|------------------|---------------------------------------------|
| Nome do bucket   | `meu-bucket-exemplo-jmarcelo-2025`          |
| Região           | `us-east-1` (ou outra de sua escolha)       |
⚠️ O nome precisa ser único globalmente e sem espaços, letras maiúsculas ou acentos.

### 4. Configurações de bloqueio de acesso público
- Por padrão, o S3 bloqueia todo acesso público — isso é recomendado.
- Se quiser permitir acesso público (ex: para hospedar um site), desmarque as opções.

### 🛑 Atenção: não desmarque isso em produção, a menos que você saiba exatamente o que está fazendo]

### 5. Configurações avançadas (opcional)
Você pode:
- Habilitar versionamento
- Adicionar tags
- Ativar criptografia (SSE-S3 ou SSE-KMS)

Para esse exemplo, pode deixar tudo como padrão

### 6. Clique em “Criar bucket”
🎉 Pronto! Bucket criado com sucesso.

## ✅ Testando o Bucket
1. Clique no nome do bucket
2. Vá até “Enviar”
3. Envie um arquivo de teste (exemplo.txt)

Verifique se o upload foi feito

## 🔐 Dica: Evite acesso público
Por padrão, o S3 bloqueia isso. Só ative o acesso público se for:
- Site estático via S3 + CloudFront
- Bucket de artefatos públicos

# 🧱 Criando um bucket S3 com Terraform – Região us-east-2

## 🗂️ Estrutura dos arquivos sugeridos
bash

```
terraform-s3/
├── main.tf
├── variables.tf
├── terraform.tfvars
├── outputs.tf
```

## 🔧 main.tf
hcl

```
provider "aws" {
  region  = var.region
  profile = var.aws_profile
}

resource "aws_s3_bucket" "meu_bucket" {
  bucket = var.bucket_name
  force_destroy = true # remove arquivos automaticamente se destruir o bucket
  tags = {
    Name        = var.bucket_name
    Environment = "dev"
  }
}
```
## 📦 variables.tf
hcl

```
variable "region" {
  description = "Região padrão da AWS"
  type        = string
  default     = "us-east-2"
}

variable "aws_profile" {
  description = "Nome do perfil AWS (usado com CLI/SSO)"
  type        = string
  default     = "default"
}

variable "bucket_name" {
  description = "Nome único do bucket S3"
  type        = string
}
```

## 🧾 terraform.tfvars
hcl

```
bucket_name  = "meu-bucket-exemplo-jmarcelo-2025"
aws_profile  = "default" # ou "dev", se estiver usando SSO
region       = "us-east-2"
```

## 🔍 outputs.tf
hcl

```
output "bucket_name" {
  description = "Nome do bucket criado"
  value       = aws_s3_bucket.meu_bucket.id
}
```

## ▶️ Como rodar
### 1. Iniciar o Terraform
bash

```
terraform init
```

### 2. Ver plano de execução
bash

```
terraform plan
```

### 3. Aplicar (criar bucket)
bash

```
terraform apply
```

Confirme com `yes` quando solicitado.

## 💡 Observações
- O `force_destroy = true` permite que o bucket seja destruído mesmo que tenha objetos dentro.
- Esse código cria um bucket **privado por padrão**, igual ao que a AWS faz no console.
- Pode adicionar `acl`, `versioning`, `encryption`, `policy` e muito mais.

## ⚠️ Importante
Se você estiver usando **SSO + Assume Role**, ou o `Granted`, apenas **altere** o `profile` no `terraform.tfvars` para o nome do perfil SSO, ex:

hcl

```
aws_profile = "dev"
```

E execute os comandos sempre com:

bash
```
AWS_PROFILE=dev terraform apply
```

ou

bash
```
assume dev && terraform apply
```