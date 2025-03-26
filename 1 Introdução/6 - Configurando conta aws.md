# 🔐 Configurando a Conta AWS para Uso com Terraform
## ✅ 1. Pré-requisitos
- AWS CLI instalada
👉 Se ainda não instalou:
https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html

Verifique a instalação:

bash
```
aws --version
```
## 🔑 2. Criação de credenciais (IAM)
Você precisa de acesso programático (chave de acesso e secreta) de um usuário com permissões adequadas.

### A. Acesse o painel IAM:
👉 https://console.aws.amazon.com/iam

### B. Crie um usuário IAM:
- Marque: Acesso programático
- Permissões: Pode usar políticas como AdministratorAccess (para testes) ou personalizadas

### C. Ao final, anote:
- AWS Access Key ID
- AWS Secret Access Key

⚠️ Importante: essa chave secreta não é mostrada novamente. Salve com cuidado (preferencialmente em um cofre de senhas ou SSM/Vault).

## ⚙️ 3. Configurando com o AWS CLI
No terminal:

bash
```
aws configure --profile meu-perfil
```
Vai pedir:

bash
```
AWS Access Key ID [None]: <sua_access_key>
AWS Secret Access Key [None]: <sua_secret_key>
Default region name [None]: us-east-1
Default output format [None]: json
```
## 🧾 4. Onde essas credenciais ficam?
Elas são salvas localmente em:

bash
```
~/.aws/credentials
~/.aws/config
```
Exemplo:

~/.aws/credentials

ini
```
[meu-perfil]
aws_access_key_id = AKIAXXXXXXXX
aws_secret_access_key = xxxxxxxxxxxxxxxxxxxxx
```
~/.aws/config

ini
```
[profile meu-perfil]
region = us-east-1
output = json
```
## 🧠 5. Usando o perfil no Terraform
No seu providers.tf:

hcl
```
provider "aws" {
  region  = var.region
  profile = var.aws_profile
}
```
No seu `variables.tf`:

hcl
```
variable "aws_profile" {
  description = "Perfil da AWS CLI"
  default     = "meu-perfil"
}
```
## 🔐 6. Boa prática: nunca exponha chaves diretamente
Evite fazer isso:

hcl
```
provider "aws" {
  access_key = "AKIA..."
  secret_key = "xxxxxxxx"
}
```
✅ Use **variáveis de ambiente**, perfis ou serviços como **AWS SSO, Vault, SSM Parameter Store**.

## 🛡️ 7. Modo avançado: autenticação com Assume Role (STS)
Se você trabalha com múltiplas contas e assume papéis IAM:
- Configure um `source_profile`
- Use `role_arn` e `mfa_serial` se necessário

Exemplo em `~/.aws/config`:

ini
```
[profile dev]
role_arn = arn:aws:iam::123456789012:role/terraform-role
source_profile = default
region = us-east-1
```

## 🧪 Testando a conexão
Teste se está tudo OK com:

bash
```
aws sts get-caller-identity --profile meu-perfil
```
Se retornar seu `UserId`, `Account` e `Arn`, tá tudo certo! 🎉

## 📦 Dica bônus: múltiplos perfis
Você pode configurar quantos perfis quiser e alternar entre eles via:

bash
```
AWS_PROFILE=meu-perfil terraform plan
```
Ou fixar no Terraform com `profile = var.aws_profile`.

# 🧩 Estrutura dos Arquivos AWS CLI
Local padrão:

bash
```
~/.aws/config
~/.aws/credentials
```
## 📁 1. TEMPLATE — ~/.aws/credentials
ini
```
[default]
aws_access_key_id = SUA_ACCESS_KEY_ID
aws_secret_access_key = SUA_SECRET_ACCESS_KEY
```
🔒 Use `default` apenas como base para autenticar-se e assumir outros papéis (roles).
Esse usuário pode ser limitado — o importante é ele ter permissão para assumir uma role.

## 📁 2. TEMPLATE — ~/.aws/config (com Assume Role + MFA)
ini
```
[default]
region = us-east-1
output = json

[profile dev]
role_arn = arn:aws:iam::123456789012:role/TerraformAdmin
source_profile = default
region = us-east-1
mfa_serial = arn:aws:iam::111111111111:mfa/seu.usuario@empresa.com

[profile prod]
role_arn = arn:aws:iam::987654321098:role/ProdTerraform
source_profile = default
region = us-east-1
mfa_serial = arn:aws:iam::111111111111:mfa/seu.usuario@empresa.com
```
## 🧪 Explicação dos campos:
| Campo           | Explicação                                                                 |
|------------------|---------------------------------------------------------------------------|
| `role_arn`       | A Role que será assumida (com `sts:AssumeRole`)                           |
| `source_profile` | O perfil base que possui a Access Key                                     |
| `mfa_serial`     | O ARN do seu MFA (virtual ou físico)                                      |
| `region`         | Região padrão (ex: `us-east-1`)                                           |
| `output`         | Formato de saída (ex: `json`, `yaml`, `table`)                            |

## 🪜 3. Como autenticar com MFA + Assume Role

### Passo 1: Gere token temporário
bash
```
aws sts get-session-token \
  --serial-number arn:aws:iam::111111111111:mfa/seu.usuario@empresa.com \
  --token-code 123456 \
  --profile default
```
### Passo 2: Ou simplesmente use:
bash
```
aws s3 ls --profile dev
```
⚠️ Ele vai pedir o código MFA automaticamente (se configurado corretamente no config).

## ✅ 4. Usando o perfil no Terraform
No `providers.tf`:

hcl
```
provider "aws" {
  profile = var.aws_profile
  region  = var.region
}
```
E em `variables.tf`:

hcl
```
variable "aws_profile" {
  description = "Nome do perfil AWS CLI"
  type        = string
  default     = "dev"
}

variable "region" {
  description = "Região da AWS"
  type        = string
  default     = "us-east-1"
}
```
### Alternativa via terminal:
bash
```
AWS_PROFILE=dev terraform plan
```

## 🔐 Dicas de Segurança
- Nunca versionar ~/.aws/credentials
- Evite usar AdministratorAccess de forma permanente — sempre prefira usar Assume Role
- Considere usar AWS Vault ou Leapp para gerenciar credenciais e MFA de forma segura

# 🧩 AWS SSO (IAM Identity Center) com Terraform
## 🧠 O que é SSO?
O AWS SSO (atualmente chamado de IAM Identity Center) permite que você acesse várias contas AWS com login federado (geralmente via SAML, Okta, AD, Google Workspace etc), sem **precisar de chaves de acesso**. Você usa o comando:

bash
```
aws sso login --profile dev
```
E ele **gera credenciais temporárias** válidas por até 1 hora.

## ✅ Pré-requisitos
- SSO configurado pela sua organização
- Você já tem um usuário com permissão a pelo menos uma conta AWS e permission set
- AWS CLI v2.2.0 ou superior
- Terraform instalado

# 🚀 Como configurar o ambiente para usar AWS SSO com Terraform

## 1. ⚙️ Configurar o ~/.aws/config
O `~/.aws/config` deve conter algo assim:

ini
```
[profile dev]
sso_start_url = https://empresa.awsapps.com/start
sso_region = us-east-1
sso_account_id = 123456789012
sso_role_name = TerraformAccess
region = us-east-1
output = json
```
🔁 Esse TerraformAccess é o **Permission Set** definido pelo admin no SSO. Ele dá acesso a recursos da AWS.

## 2. 🔐 Fazer login via SSO
Você precisa rodar o login **antes** de usar Terraform:

bash
```
aws sso login --profile dev
```
Esse comando abre o navegador, autentica e salva credenciais temporárias (de 1h) no cache local.

## 3. 🧱 Usar com Terraform
No seu `providers.tf`:

hcl
```
provider "aws" {
  region  = var.region
  profile = var.aws_profile
}
```
E nas variáveis:

hcl
```
variable "aws_profile" {
  default = "dev"
}

variable "region" {
  default = "us-east-1"
}
```
Depois, rode:

bash
```
AWS_PROFILE=dev terraform plan
```
✅ Vai funcionar se o SSO estiver logado (via `aws sso login`).

## ⚠️ Atenção: sessões expiram!
As credenciais SSO duram entre 1h a 12h (definido pelo admin). Depois disso, você verá erros como:

csharp
```
The SSO session associated with this profile has expired
```
🔄 Solução:

bash
```
aws sso login --profile dev
```
Você pode automatizar esse login via scripts ou usar ferramentas como Leapp, Granted, ou AWS Vault.

## 🛑 NÃO FUNCIONA COM ~/.aws/credentials
**Importante**: com SSO, **não se usa** `~/.aws/credentials`. As credenciais são **geradas dinamicamente** e salvas no cache da AWS CLI (`~/.aws/sso/cache`).

## 📦 Resumo
| Item                        | Status com SSO                                            |
|-----------------------------|-----------------------------------------------------------|
| `aws configure` tradicional | ❌ Não usa Access Key                                     |
| `aws sso login`             | ✅ Necessário antes de rodar Terraform                    |
| `~/.aws/credentials`        | ❌ Ignorado                                               |
| `~/.aws/config`             | ✅ Deve conter config com `sso_*`                         |
| Terraform funciona com SSO? | ✅ Sim, via perfil autenticado                            |

## 🎁 Bônus: exemplo completo de .aws/config
ini
```
[profile dev]
sso_start_url = https://empresa.awsapps.com/start
sso_region = us-east-1
sso_account_id = 123456789012
sso_role_name = TerraformAccess
region = us-east-1
output = json

[profile prod]
sso_start_url = https://empresa.awsapps.com/start
sso_region = us-east-1
sso_account_id = 987654321098
sso_role_name = TerraformAdmin
region = us-east-1
output = json
```

# Script
## 🧠 OBJETIVO
Você vai conseguir:
- Logar via SSO com um comando só (./login.sh dev)
- Usar múltiplas contas e perfis com facilidade via Granted
- Rodar Terraform com SSO ativo sem stress de sessão expirada

## 🧾 login.sh – Script de Login SSO
bash
```
#!/bin/bash

PROFILE=$1

if [ -z "$PROFILE" ]; then
  echo "❌ Informe o nome do profile como argumento:"
  echo "./login.sh <profile>"
  exit 1
fi

echo "🔐 Verificando status do AWS SSO para o profile '$PROFILE'..."

EXPIRY=$(aws configure list-sso --profile "$PROFILE" 2>/dev/null | grep 'accessToken expires at' | awk -F': ' '{print $2}')

if [ -z "$EXPIRY" ]; then
  echo "🔄 Nenhuma sessão ativa encontrada. Iniciando login via SSO..."
  aws sso login --profile "$PROFILE"
else
  NOW=$(date -u +%s)
  EXPIRY_TS=$(date -u -d "$EXPIRY" +%s)

  if [ "$NOW" -gt "$EXPIRY_TS" ]; then
    echo "🔄 Sessão expirada. Reautenticando via SSO..."
    aws sso login --profile "$PROFILE"
  else
    echo "✅ Sessão ainda válida até: $EXPIRY"
  fi
fi

echo "✅ Login finalizado. Você pode usar: AWS_PROFILE=$PROFILE terraform plan"
```
### 💡 Uso:

bash
```
chmod +x login.sh
./login.sh dev
```
## 🚀 Instalar e Configurar o Granted (Common Fate)
### 🧰 O que é?
Granted é uma ferramenta CLI que facilita o uso de perfis SSO e AssumeRole com AWS. Ele:
- Cria sessões automaticamente
- Se integra com SSO
- Permite usar assume <perfil> ao invés de aws sso login
- Funciona super bem com Terraform e múltiplas contas

## 💻 Instalação do Granted
### Linux / macOS:

bash
```
curl -s https://raw.githubusercontent.com/common-fate/granted/main/install.sh | bash
```
Isso instala os binários: assume e granted.

💡 O comando principal que você vai usar é:
```
assume dev
```
### Windows (PowerShell):
powershell
```
iwr -useb https://raw.githubusercontent.com/common-fate/granted/main/install.ps1 | iex
```
## ⚙️ Configuração do `.aws/config` compatível com Granted
Granted usa os mesmos perfis SSO definidos no seu `~/.aws/config`.

Exemplo:

ini
```
[profile dev]
sso_start_url = https://empresa.awsapps.com/start
sso_region = us-east-1
sso_account_id = 123456789012
sso_role_name = TerraformAccess
region = us-east-1
output = json
```
## 🧪 Como usar o Granted
### Autenticar e ativar o perfil:

bash
```
assume dev
```
Isso cria uma sessão temporária e já exporta as variáveis de ambiente. O terminal entra numa subshell com as credenciais válidas.

### Usar com Terraform:
bash
```
assume dev
terraform plan
```
🧨 Quando a sessão expirar, basta repetir `assume dev`.

## ⚡ Dica avançada: usar assume exec
Se quiser rodar um comando direto, sem entrar em subshell:

bash
```
assume exec dev -- terraform apply
```

## 🎁 Extras
### Criar alias para facilitar:
Adicione no seu `.bashrc` ou `.zshrc`:

bash
```
alias tdev='assume dev && terraform plan'
alias tprod='assume prod && terraform plan'
```
# ✅ RESUMO
| Ferramenta / Comando         | Função                                                              |
|------------------------------|---------------------------------------------------------------------|
| `login.sh`                   | Script simples de login SSO direto                                  |
| **Granted / assume**         | Autenticação e troca de perfis automatizada                         |
| `assume exec <perfil>`       | Executa comandos já autenticado                                     |
| `assume <perfil>`            | Entra em subshell com sessão válida                                 |
