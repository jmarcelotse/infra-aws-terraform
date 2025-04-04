# 📁 O que é o terraform.tfstate?
O `terraform.tfstate` é um arquivo **JSON gerado e mantido pelo Terraform** que armazena **o estado atual da infraestrutura** criada por ele.

Pense no `tfstate` como o "cérebro" do Terraform:

É nele que o Terraform lembra o que foi criado, como foi criado e com quais atributos.

## 🧠 Por que o tfstate existe?
Porque o Terraform **não interage com a nuvem em tempo real** a cada execução. Ele compara o que está no código (`.tf`) com o que está no tfstate para decidir:
- O que precisa ser criado
- O que precisa ser atualizado
- O que precisa ser destruído

## 🧾 Exemplo simplificado de um terraform.tfstate (trecho)
json

```
{
  "resources": [
    {
      "type": "aws_s3_bucket",
      "name": "meu_bucket",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "attributes": {
            "bucket": "meu-bucket-dev-jmarcelo",
            "tags": {
              "ambiente": "dev"
            }
          }
        }
      ]
    }
  ]
}
```

## 🛠 Onde ele é salvo?
Por padrão:
📍 `./terraform.tfstate` (no mesmo diretório do projeto)

## ⚠️ CUIDADO: Nunca versionar esse arquivo no Git!
O `tfstate` pode conter:
- Dados sensíveis (senhas, IDs de acesso, chaves)
- Informações privadas da sua conta cloud
- Riscos de conflito se mais de uma pessoa alterar o mesmo arquivo

## 🔄 O que é o terraform.tfstate.backup?
O Terraform sempre cria um **backup automático** do último `tfstate` antes de modificar.

Esse backup fica salvo como:

📍 `terraform.tfstate.backup`

Se algo der errado, você pode restaurar manualmente.

## 🧠 Comandos úteis com o estado
bash

```
terraform state list                 # Lista todos os recursos gerenciados
terraform state show <resource>     # Mostra os detalhes de um recurso
terraform show                      # Mostra todo o estado formatado
```

Exemplo:

bash

```
terraform state show aws_s3_bucket.meu_bucket
```

## 📤 Exportando dados do state com output
Mesmo que os recursos estejam no `tfstate`, você pode expor valores com:

hcl

```
output "nome_bucket" {
  value = aws_s3_bucket.meu_bucket.id
}
```

E depois:

bash

```
terraform output nome_bucket
```

## ☁️ Remote Backend — Onde o tfstate deveria estar?
Em times ou produção, nunca deixe o **tfstate local**. Use **Remote Backend**, como:
- **AWS S3 + DynamoDB** (lock de concorrência)
- Terraform Cloud
- Azure Blob, GCP Storage, etc.

## 🎯 Exemplo de backend remoto (S3 + DynamoDB)
`backend.tf` **ou dentro do** `terraform block`:

hcl

```
terraform {
  backend "s3" {
    bucket         = "terraform-states-jmarcelo"
    key            = "dev/s3-buckets/terraform.tfstate"
    region         = "us-east-2"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

Obs: esse bloco precisa ser inicializado com:

bash

```
terraform init
```

## 🔐 Segurança com o tfstate
- Use `encrypt = true` no S3
- Controle quem pode acessar o bucket do state
- Não exponha `tfstate` em outputs
- Cuidado com `terraform output -json` em pipelines

## 🛑 Problemas comuns com o tfstate
| Situação                              | Explicação                                                                                   |
|---------------------------------------|-----------------------------------------------------------------------------------------------|
| Arquivo local deletado                | Recursos ainda existem, mas o Terraform perdeu o "controle" sobre eles                        |
| Dois usuários alteram ao mesmo tempo  | Pode corromper o state local — use backend remoto com **lock** (ex: S3 + DynamoDB na AWS)     |
| Erro no `apply` e state salvo parcialmente | O `tfstate` pode ficar inconsistente com a infraestrutura real                               |
| Diferença entre código e estado       | O Terraform detecta e planeja a reconciliação durante o `terraform plan`                      |

## ✅ Boas práticas com tfstate
- Sempre use **backend remoto** em times
- Nunca versionar o arquivo `.tfstate` no Git
- Use **lock** com DynamoDB para evitar corrida de estados
- Automatize backups em bucket S3
- Valide seus states com `terraform plan` regularmente

# 📦 1. Backend Remoto com S3 (sem lock)
# 🛡️ 2. Backend Remoto com S3 + DynamoDB (com lock) ✅ Recomendado para times

## 📍 Onde configurar o `backend`?
Você deve definir o backend no bloco `terraform`, geralmente em um arquivo separado como:

bash

```
backend.tf  # ou main.tf, se preferir centralizar
```

## 📁 Estrutura de diretório recomendada
arduino

```
terraform-backend/
├── backend_s3.tf            # backend S3 simples
├── backend_s3_dynamodb.tf   # backend S3 + lock
├── main.tf                  # recursos (vazios ou exemplo)
├── variables.tf             # vars do projeto
└── terraform.tfvars         # valores reais
```

### 🧰 1. BACKEND REMOTO – S3 (sem DynamoDB)
📄 `backend_s3.tf`

hcl

```
terraform {
  backend "s3" {
    bucket = "meu-bucket-backend-tf"
    key    = "global/s3/terraform.tfstate"
    region = "us-east-2"
    encrypt = true
  }
}
```

🔒 `encrypt = true` habilita criptografia no S3

📌 `key` define o caminho do `.tfstate` dentro do bucket

### 🔁 Comando para iniciar:
bash

```
terraform init
```

### 🛡️ 2. BACKEND REMOTO – S3 + DynamoDB (com lock de concorrência)
Essa é a forma **mais segura e recomendada** para times e automações CI/CD.

### 🧾 Pré-requisitos
Você precisa criar:
- 1 bucket S3 para armazenar o `.tfstate`
- 1 tabela DynamoDB para controle de lock

Eu te ajudo com isso abaixo. 👇

### 📄 backend_s3_dynamodb.tf
hcl

```
terraform {
  backend "s3" {
    bucket         = "meu-backend-remoto-state"
    key            = "envs/dev/terraform.tfstate"
    region         = "us-east-2"
    dynamodb_table = "terraform-state-lock"
    encrypt        = true
  }
}
```

### 🛠️ Criando o bucket S3 e a tabela DynamoDB (se ainda não tiver)
hcl

```
resource "aws_s3_bucket" "state_bucket" {
  bucket = "meu-backend-remoto-state"
  acl    = "private"

  versioning {
    enabled = true
  }

  server_side_encryption_configuration {
    rule {
      apply_server_side_encryption_by_default {
        sse_algorithm = "AES256"
      }
    }
  }

  tags = {
    Name        = "Terraform State Bucket"
    Environment = "dev"
  }
}

resource "aws_dynamodb_table" "state_lock" {
  name         = "terraform-state-lock"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "LockID"

  attribute {
    name = "LockID"
    type = "S"
  }

  tags = {
    Name        = "Terraform Lock Table"
    Environment = "dev"
  }
}
```

## 🛠️ Como usar o backend remoto
**Atenção: o backend não pode usar variáveis como `var.region`, `var.bucket`, etc**.

**Passos**:
1. Substitua seu `terraform` block por um dos exemplos acima
2. Rode:

bash

```
terraform init
```

Você verá:

plaintext

```
Initializing the backend...
Successfully configured the backend "s3"! Terraform will automatically use this backend for all operations.
```

## 📦 Dica bônus: versionamento do bucket
Habilitar versionamento no bucket S3 permite:
- Histórico de `tfstate`
- Restauração em caso de erro

## ✅ Resumo
| Tipo             | Armazena o `tfstate` | Lock de concorrência | Recomendado para               |
|------------------|----------------------|-----------------------|--------------------------------|
| **S3 simples**   | ✅                   | ❌                    | Testes locais, uso pessoal     |
| **S3 + DynamoDB**| ✅                   | ✅                    | Times, produção, CI/CD         |
