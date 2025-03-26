# 🔁 Instalando o TFEnv (Terraform Version Manager)

## 🐧 Linux & 🍎 macOS
### ✅ Requisitos:
- Git
- Curl ou Wget

### 1. Clonar o repositório
bash
```
git clone https://github.com/tfutils/tfenv.git ~/.tfenv
```

## 1. Clonar o repositório
bash
```
git clone https://github.com/tfutils/tfenv.git ~/.tfenv
```
## 2. Adicionar ao PATH
Adicione ao seu `.bashrc`, `.zshrc` ou `.bash_profile`:

bash
```
export PATH="$HOME/.tfenv/bin:$PATH"
```
Depois rode:

bash
```
source ~/.bashrc   # ou ~/.zshrc dependendo do seu shell
```
## 3. Testar instalação
bash
```
tfenv --version
```

## 🪟 Windows
O tfenv é feito pra Unix/Linux/macOS, mas no Windows você pode:
- Usar via WSL (Windows Subsystem for Linux)
- Usar Scoop (não oficial para tfenv)
- Ou gerenciar versões manualmente

Se você tiver o WSL:

bash
```
sudo apt install git
git clone https://github.com/tfutils/tfenv.git ~/.tfenv

echo 'export PATH="$HOME/.tfenv/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```
## 📦 Comandos úteis do TFEnv
| Comando                        | O que faz                                                              |
|-------------------------------|-------------------------------------------------------------------------|
| `tfenv list-remote`           | Lista todas as versões do Terraform disponíveis                        |
| `tfenv install <versão>`      | Instala uma versão específica (ex: `tfenv install 1.5.7`)              |
| `tfenv use <versão>`          | Ativa uma versão para o terminal atual                                 |
| `tfenv install latest`        | Instala a versão mais recente                                          |
| `tfenv use latest`            | Usa a versão mais recente                                              |
| `tfenv uninstall <versão>`    | Remove uma versão instalada                                            |

## 📁 Arquivo .terraform-version (opcional)
Você pode criar um arquivo no seu projeto para **fixar a versão do Terraform** a ser usada:

bash
```
echo "1.5.7" > .terraform-version
```
Assim, sempre que você entrar nesse projeto e rodar `tfenv use`, ele ativa automaticamente essa versão.

## 🧪 Exemplo completo
bash
```
tfenv list-remote             # Ver versões disponíveis
tfenv install 1.5.7           # Instala a versão desejada
tfenv use 1.5.7               # Usa essa versão agora
terraform -v                  # Confirma se está na versão certa
```