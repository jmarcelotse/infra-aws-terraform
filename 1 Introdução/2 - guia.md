# ✅ Mini-Guia de Comandos Terraform
| Comando                        | O que faz                                                       |
|-------------------------------|------------------------------------------------------------------|
| `terraform init`              | Inicializa o projeto e baixa os plugins (providers)             |
| `terraform plan`              | Mostra o que será criado/modificado/destruído                   |
| `terraform apply`             | Aplica as mudanças na infraestrutura                            |
| `terraform destroy`           | Remove os recursos criados                                      |
| `terraform validate`          | Valida se a sintaxe está correta                                |
| `terraform fmt`               | Formata os arquivos `.tf`                                       |
| `terraform output`            | Exibe os outputs definidos                                      |
| `terraform taint <resource>`  | Marca um recurso para ser recriado                              |
| `terraform state list`        | Lista os recursos no estado                                     |
| `terraform graph`             | Gera um grafo de dependências                                   |

# 📁 Sugestão de Estrutura de Pastas e Arquivos Terraform
Organizar o código é fundamental para escalabilidade e manutenibilidade. Aqui vai um layout recomendado:

infra/
│
├── main.tf                # Recursos principais
├── variables.tf           # Declaração de variáveis
├── outputs.tf             # Outputs do projeto
├── providers.tf           # Configurações de provider e backend
├── terraform.tfvars       # Valores das variáveis
├── versions.tf            # Versões do Terraform e providers
│
├── modules/               # Módulos reutilizáveis
│   └── s3/                # Exemplo: módulo S3
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
│
└── environments/          # Separação por ambiente (dev, staging, prod)
    ├── dev/
    │   ├── main.tf
    │   ├── terraform.tfvars
    └── prod/
        ├── main.tf
        ├── terraform.tfvars

# 📄 Exemplo de Conteúdo dos Arquivos

`providers.tf`

hcl
```
provider "aws" {
  region = var.region
  profile = var.aws_profile
}
```

`versions.tf`

hcl
```
terraform {
  required_version = ">= 1.4.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```

`variables.tf`

hcl
```
variable "region" {
  description = "Região da AWS"
  type        = string
  default     = "us-east-1"
}

variable "aws_profile" {
  description = "Perfil AWS CLI"
  type        = string
  default     = "default"
}
```

`terraform.tfvars`

hcl
```
region      = "us-east-1"
aws_profile = "minha-conta-aws"
```

`main.tf` (exemplo simples de bucket S3)

hcl
```
resource "aws_s3_bucket" "bucket_exemplo" {
  bucket = "meu-bucket-exemplo-unico"
  tags = {
    Name        = "Bucket Exemplo"
    Environment = "dev"
  }
}
```
`outputs.tf`

hcl
```
output "bucket_name" {
  description = "Nome do bucket criado"
  value       = aws_s3_bucket.bucket_exemplo.bucket
}
```
# 📝 Template Inicial de README.md para o Repositório
markdown

# 🚀 Projeto Terraform AWS - [Nome do Projeto]

Este repositório contém a infraestrutura como código (IaC) utilizando Terraform para provisionar e gerenciar recursos na AWS.

---

## 📦 Recursos Criados

- VPC e Subnets
- Buckets S3
- Instâncias EC2
- Lambda Functions
- API Gateway
- RDS / DynamoDB
- IAM Roles e Policies

---

## 📁 Estrutura de Diretórios
infra/ ├── main.tf ├── variables.tf ├── outputs.tf ├── providers.tf ├── terraform.tfvars ├── versions.tf ├── modules/ └── environments/

yaml
```

---

## 🚀 Como usar

### 1. Clonar o projeto

```bash
git clone https://github.com/seu-usuario/nome-do-projeto.git
cd nome-do-projeto/infra
```
### 2. Inicializar o Terraform
bash
```
terraform init
```
### 3. Validar a sintaxe
bash
```
terraform validate
```
### 4. Visualizar o plano
bash
```
terraform plan
```
### 5. Aplicar a infraestrutura
bash
```
terraform apply
```
### 6. Destruir a infraestrutura
bash
```
terraform destroy
```
## 📌 Requisitos
- Terraform >= 1.4
- AWS CLI configurado (aws configure)
- Acesso à conta AWS com permissões adequadas
- Git instalado

## 🔒 Segurança
- Nunca subir arquivos sensíveis (.tfstate, secrets, .pem, .env) no Git!
- Usar o AWS Systems Manager Parameter Store ou Secrets Manager para variáveis sensíveis.
- Configure backend remoto (S3 + DynamoDB) para evitar conflitos de estado.

## ✍️ Autor
- Seu Nome
- Seu LinkedIn
- Seu GitHub

## 📄 Licença
MIT License - sinta-se livre para usar e adaptar este projeto!

yaml
```
---

Se quiser, posso gerar isso tudo automaticamente como arquivos `.tf` e `.md`, prontos para colar no seu projeto!

E claro, quando quiser avançar com os primeiros recursos AWS (ex: VPC, EC2, Lambda), é só dizer que já montamos juntos. Quer que eu gere a estrutura inicial dos arquivos agora pra você colar direto no projeto?
```
