# 🌍 O que é o Terraform?
Terraform é uma **ferramenta de infraestrutura como código (IaC)** desenvolvida pela HashiCorp. Com ela, você pode **provisionar, configurar e gerenciar a infraestrutura de nuvem (e até on-premises)** de forma totalmente automatizada, declarando os recursos com código.

## 📦 Em outras palavras...
Imagina que ao invés de clicar em mil botões no painel da AWS para criar uma VPC, uma instância EC2, um bucket S3, uma Lambda... você simplesmente escreve isso como código em arquivos .tf, versiona isso com Git e aplica com um comando só. E se quiser destruir tudo depois? Um comando e pronto. 😎

## 🚀 Por que usar o Terraform?
| Benefício             | Descrição                                                                                      |
|-----------------------|-----------------------------------------------------------------------------------------------|
| ✅ Automação total     | Infraestrutura criada e gerenciada com código                                                 |
| 🛠️ Multi-cloud         | Funciona com AWS, Azure, GCP, Oracle, Alibaba, Kubernetes e até VMware                        |
| 💻 Controle de versão  | Toda infraestrutura pode ser versionada em Git                                                |
| 🔁 Reusabilidade       | Suporte a módulos para reaproveitar código                                                    |
| 🔄 Idempotência        | Executa múltiplas vezes e só muda o que realmente for necessário                              |
| 👥 Trabalho em equipe  | Com o Terraform Cloud ou com o backend em S3 + DynamoDB, você evita conflitos entre pessoas  |

## 🧱 Conceitos básicos do Terraform
### 1. Infraestrutura como Código (IaC)
Você descreve sua infraestrutura com arquivos `.tf`, usando a linguagem HCL (HashiCorp Configuration Language). Exemplo:

hcl
```
resource "aws_instance" "meu_servidor" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
```
### 2. Recursos (resource)
Cada coisa que você cria (bucket S3, instância EC2, VPC, Lambda, etc.) é um recurso.

hcl
```
resource "aws_s3_bucket" "meu_bucket" {
  bucket = "nome-unico-bucket"
}
```
#### 3. Provedores (provider)
É o plugin que permite o Terraform se comunicar com a API do serviço (ex: AWS, Azure, GCP...).

hcl
```
provider "aws" {
  region = "us-east-1"
}
```
### 4. Variáveis (`variable`)
Você pode parametrizar tudo, assim o código fica reutilizável.

hcl
```
variable "region" {
  default = "us-east-1"
}
```

E depois usa assim:

hcl
```
provider "aws" {
  region = var.region
}
```

### 5. Módulos (`module`)
É como uma "função" de infraestrutura. Você pode criar uma pasta com código reutilizável e chamar em diferentes ambientes.

hcl
```
module "vpc" {
  source = "./modules/vpc"
  cidr_block = "10.0.0.0/16"
}
```

### 6. Estados (`terraform.tfstate')
O Terraform mantém um **arquivo de estado**, que armazena o que já foi criado. É como se fosse a memória da infraestrutura. Por isso, **NUNCA edite esse arquivo na mão**.

## ⚙️ Como funciona o ciclo de vida do Terraform?
1. **Escreve o código** (.tf)
2. Roda terraform init para preparar o ambiente
3. Roda terraform plan para ver o que vai ser criado/modificado
4. Roda terraform apply para aplicar as mudanças
5. Se quiser destruir, roda terraform destroy

### 🧪 Exemplo simples
hcl
```
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "meu_bucket" {
  bucket = "meu-bucket-unico-teste"
  acl    = "private"
}
```

bash
```
terraform init
terraform plan
terraform apply
```

Boom! 🧨 Bucket criado na AWS com código.

## 🧰 O que dá pra criar com Terraform?
Com Terraform + AWS, você consegue criar praticamente tudo:
- VPCs, subnets, NAT, gateways
- EC2, EBS, Load Balancers
- S3, CloudFront, IAM, KMS
- Lambda, API Gateway
- RDS, DynamoDB, Aurora
- CloudWatch, Alarms, Logs
- EKS (Kubernetes)

...e muito mais

## 🔐 E a segurança?
Você pode (e deve):
- Armazenar secrets no AWS SSM ou HashiCorp Vault
- Usar módulos para aplicar políticas IAM seguras
- Integrar com GitHub Actions para CI/CD

Criar pipelines que façam validação do código Terraform (com `tflint, terraform validate, terraform fmt`)

## 🎯 Quando usar Terraform?
- Sempre que você quiser evitar o "click-ops"
- Quando quiser infra reproduzível e versionável
- Para criar ambientes multi-cloud
- Para automação CI/CD de ambientes de dev, staging, prod
- Para segurança e governança com revisão de código

## 📚 Próximos passos recomendados
- Escolher um nome pro seu projeto (já estamos nisso!)
- Criar o repositório Git
- Definir o layout dos arquivos Terraform
- Criar seu primeiro provider e resource
- Planejar os módulos
- Versionar com Git
- Automatizar com pipeline