# CONFIGURATION DU PROVIDER AWS ET DU BACKEND

## Configuration du provider AWS

L’objectif de ce lab étant de gérer une infrastructure sur le cloud AWS, terraform doit être en mesure de contacter l’API d’AWS pour lui indiquer ce qu’il doit créer, modifier ou supprimer. Pour cela, il est nécessaire de configurer le provider AWS dans votre code terraform.

**Configuration des credentials**

On appelle « credentials » les informations utilisées pour se connecter à un système. Dans le cadre de ce lab, les credentials que terraform utilise pour se connecter à l’API d’AWS sont une Access Key et une Secret Key :

- Créez un répertoire nommé **.aws** (n’oubliez pas le .) dans le répertoire C:\Utilisateurs\<votre-nom-d-utilisateur
- Dans le répertoire **C:\Utilisateurs\<votre-nom-d-utilisateur\.aws**, créez le fichier **credentials** et ajoutez-y le contenu suivant (remplacez *votre-access-key* et *votre-secret-key* par les access key et secret key AWS qui vous ont été communiqué) :

```
[nuumfactory-student]
aws_access_key_id = votre-access-key
aws_secret_access_key = votre-secret-key
```

**Déclaration du provider AWS**

Dans le répertoire **nuumfactory-labs/main-lab/**, créez le fichier **main.tf** et déclarez le provider AWS en ajoutant le contenu suivant :

```
provider "aws" {
  region = "eu-west-3"
  profile = "nuumfactory-student"
}
```

Lors de son exécution terraform cherche ses credentials selon l’ordre suivant :

- Les paramètres access_key et secret_key du block provider
- Les variables d’environnement AWS_ACCESS_KEY_ID et AWS_SECRET_ACCESS_KEY
- Le paramètre shared_credentials_files du block provider

La valeur par défaut du paramètre **shared_credentials_files** étant **C:\Utilisateurs\<votre-nom-d-utilisateur\.aws\credentials** sur Windows et **$HOME/.aws/credentials** sur Linux.

Dans le cas présent, terraform sera en mesure de s’authentifier sur l’API d’AWS car les credentials se trouvent bien dans le fichier **C:\Utilisateurs\<votre-nom-d-utilisateur\.aws\credentials**.

**Identification du backend**

Votre fichier .tfstate devra être stocké dans un [bucket S3](https://aws.amazon.com/s3/?nc1=h_ls) créé sur le compte AWS dédié à ce lab :

- Connectez-vous au compte AWS du lab via l’URL suivante : https://689995499512.signin.aws.amazon.com/console
- Renseignez votre adresse mail (@thenuumfactory.fr) dans le champ **Nom d’utilisateur** et votre mot de passe dans le champ **Mot de passe** puis cliquez sur **Connexion**
- Depuis la page d’accueil, sélectionnez la région **Paris** (en haut à droite), tapez **S3** dans la barre de recherche et cliquez sur le service S3
- Identifiez le bucket nommé **nuumfactory-terraform-backend** : c’est dans ce bucket que votre remote state sera stocké. Ce bucket sera donc votre backend.

**Configuration du backend dans terraform**

Dans votre fichier **main.tf**, déclarez votre backend de la manière suivante (remplacez **XX** par le numéro qui vous a été communiqué) :

```
terraform {
  backend "s3" {
    bucket  = "nuumfactory-terraform-backend"
    key     = "terraform-XX-dev.tfstate"
    region  = "eu-west-3"
    profile = "nuumfactory-student"
  }
}
```

Exécutez ensuite la commande [**terraform init**](https://developer.hashicorp.com/terraform/cli/commands/init) et constatez la présence du message **Successfully configured the backend "s3"! Terraform will automatically use this backend unless the backend configuration changes.** dans les logs.

Tous les prérequis sont maintenant en place pour que vous puissiez gérer une infrastructure sur AWS avec terraform.