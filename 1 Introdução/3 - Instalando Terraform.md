# 📥 Como Instalar o Terraform
## 🐧 Linux (Debian, Ubuntu, derivados)
### 1. Instalação via repositório oficial (recomendado)
bash
```
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl

# Adiciona a chave GPG da HashiCorp
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

# Adiciona o repositório oficial
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list

# Atualiza pacotes e instala
sudo apt-get update && sudo apt-get install terraform
```

### 2. Verifique a instalação
bash
```
terraform -v
```
## 🍎 macOS (via Homebrew)
bash
```
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```
### Verifique:
bash
```
terraform -v
```
## 🪟 Windows
### Opção 1: Via Chocolatey (mais simples)
1. Abra o PowerShell como administrador
2. Rode:

powershell
```
choco install terraform -y
```
### Opção 2: Manual (caso prefira)
1. Baixe a versão mais recente em: https://www.terraform.io/downloads
2. Extraia o .zip para um diretório de sua preferência (ex: C:\Terraform)
3. Adicione esse diretório ao PATH do sistema

Para testar:

powershell
```
terraform -v
```
## 🔁 Atualizando o Terraform
- **Linux/macOS**: basta rodar novamente o processo de instalação (ele atualiza a versão)
- **Windows (choco)**:

powershell
```
choco upgrade terraform
```

# 📌 Dica extra: Instale o `tfenv` (Gerenciador de versões do Terraform)
Se você trabalha com **múltiplos projetos**, pode usar o `tfenv` para gerenciar diferentes versões do Terraform:

bash
```
# Linux/macOS
brew install tfenv

# Instalar versão específica
tfenv install 1.5.7
tfenv use 1.5.7
```
Pronto! Com o Terraform instalado, já dá pra:
- Iniciar um projeto com terraform init
- Escrever arquivos .tf
- Automatizar sua infra com estilo 😎