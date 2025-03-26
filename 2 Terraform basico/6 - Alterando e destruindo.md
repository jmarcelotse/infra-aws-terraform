# 🔁 Alterando recursos
# 💣 Destruindo recursos
Essas ações fazem parte do que torna o Terraform tão poderoso — ele **compara o estado atual com o desejado**, identifica mudanças, e aplica só o necessário.

## 🧱 Contexto: Você já tem um bucket criado com Terraform
Vamos agora:
- **Alterar uma propriedade** do bucket (ex: adicionar tags)
- **Aplicar a mudança**
- Ver no `terraform plan` **o que será alterado**
- **Destruir o recurso quando não for mais necessário**

## 🛠️ 1. Alterando um recurso Terraform
Abra o seu `main.tf` e edite a parte do recurso:

**Antes**:

hcl

```
tags = {
  Name        = var.bucket_name
  Environment = "dev"
}
```

**Depois**:

hcl

```
tags = {
  Name        = var.bucket_name
  Environment = "dev"
  Projeto     = "Terraform Básico"
  CriadoPor   = "jmarcelo"
}
```

## 📥 2. Verificando a alteração com terraform plan
bash

```
terraform plan
```

Você verá algo como:

plaintext
```
~ resource "aws_s3_bucket" "meu_bucket" {
      tags = {
          "CriadoPor"   = "jmarcelo"
          "Environment" = "dev"
          "Name"        = "meu-bucket-terraform-com-envs"
        + "Projeto"     = "Terraform Básico"
      }
  }
```

O `~` indica uma modificação no recurso existente.

## 🚀 3. Aplicando a mudança com terraform apply
bash

```
terraform apply
```

Confirme com `yes`.

Resultado: o bucket continua o mesmo, mas com novas tags.

## 💣 4. Destruindo recursos com terraform destroy
Esse comando **remove todos os recursos definidos no** `.tfstate`.

bash

```
terraform destroy
```

Se estiver usando variáveis de ambiente:

bash

```
export TF_VAR_region=us-east-2
export TF_VAR_aws_profile=default
export TF_VAR_bucket_name=meu-bucket-terraform-com-envs

terraform destroy
```

Você verá:

plaintext

```
Plan: 0 to add, 0 to change, 1 to destroy.
```

Confirme com `yes` para remover o bucket.

## 🧠 Dica: plan -destroy
Quer ver **o que vai ser destruído** antes de rodar o `destroy`?

bash
```
terraform plan -destroy
```

## 🧹 O que acontece depois do destroy?
- O recurso (ex: bucket S3) é removido da AWS
- O `terraform.tfstate` mostra que não há mais recursos
- Se você rodar `terraform apply` de novo, o recurso será recriado

## 🔐 Cuidado em produção!
- Sempre verifique `terraform plan` antes de `apply` ou `destroy`
- Use `-target=resource.name` se quiser destruir só um recurso específico

Exemplo:

bash

```
terraform destroy -target=aws_s3_bucket.meu_bucket
```

## 💡 Dica bônus: proteja recursos contra destruição
Você pode proteger um recurso com `prevent_destroy`:

hcl

```
lifecycle {
  prevent_destroy = true
}
```

Assim, o Terraform impede a **destruição acidental**.

## ✅ Recapitulando
| Ação                         | Comando                                                        |
|------------------------------|-----------------------------------------------------------------|
| Ver mudanças                 | `terraform plan`                                               |
| Aplicar mudança              | `terraform apply`                                              |
| Ver plano de destruição      | `terraform plan -destroy`                                      |
| Destruir tudo                | `terraform destroy`                                            |
| Destruir recurso específico  | `terraform destroy -target=aws_s3_bucket.meu_bucket`           |
