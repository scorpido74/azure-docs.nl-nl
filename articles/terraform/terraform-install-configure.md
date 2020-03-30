---
title: Snelstart - Terraform installeren en configureren om Azure-resources in te richten
description: In deze quicstart installeert en configureert u Terraform om Azure-bronnen te maken
keywords: azure devops terraform install configure
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 82635f59ec8165add2046a230a040b06f89d9898
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78943509"
---
# <a name="quickstart-install-and-configure-terraform-to-provision-azure-resources"></a>Snelstart: Terraform installeren en configureren om Azure-resources in te richten
 
Terraform biedt een eenvoudige manier om cloudinfrastructuur te definiëren, te bekijken en te implementeren met behulp van een [eenvoudige templatingtaal.](https://www.terraform.io/docs/configuration/syntax.html) In dit artikel worden de noodzakelijke stappen beschreven om Terraform te gebruiken voor het inrichten van resources in Azure.

Ga naar de [Terraform Hub](/azure/terraform)voor meer informatie over het gebruik van Terraform met Azure.
> [!NOTE]
> Voor terraform specifieke ondersteuning u direct contact opnemen met Terraform via een van hun communitykanalen:
>
>    * Het [Terraform gedeelte](https://discuss.hashicorp.com/c/terraform-core) van het community portal bevat vragen, use cases en nuttige patronen.
>
>    * Voor vragen over providers u terecht in de sectie [Terraform Providers](https://discuss.hashicorp.com/c/terraform-providers) van het communityportal.



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Terraform is standaard geïnstalleerd in de [Cloud Shell](/azure/terraform/terraform-cloud-shell). Als u ervoor kiest Terraform lokaal te installeren, voert u de volgende stap uit en gaat u verder [met Het instellen van Terraform-toegang tot Azure.](#set-up-terraform-access-to-azure)

## <a name="install-terraform"></a>Terraform installeren

Als u Terraform wilt installeren, [downloadt u](https://www.terraform.io/downloads.html) het juiste pakket voor uw besturingssysteem in een aparte installatiemap. De download bevat één uitvoerbaar bestand, waarvoor u ook een globaal pad moet definiëren. Ga naar [deze webpagina](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)voor instructies over het instellen van het pad op Linux en Mac. Ga naar [deze webpagina](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)voor instructies over het instellen van het pad op Windows.

Controleer uw padconfiguratie `terraform` met de opdracht. Een lijst met beschikbare Terraform-opties wordt weergegeven, zoals in de volgende voorbeelduitvoer:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Terraform-toegang tot Azure instellen

Als u Terraform wilt inschakelen om resources in Azure in te richten, maakt u een [Azure AD-serviceprincipal](/cli/azure/create-an-azure-service-principal-azure-cli). De serviceprincipal verleent uw Terraform-scripts aan het inrichten van resources in uw Azure-abonnement.

Als u meerdere Azure-abonnementen hebt, vraagt u eerst uw account op met de lijst met [AZ-accountom](/cli/azure/account#az-account-list) een lijst met abonnements-id-waarden en tenant-id-waarden op te vragen:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Als u een geselecteerd abonnement wilt gebruiken, stelt u het abonnement voor deze sessie in met [de az-accountset](/cli/azure/account#az-account-set). Stel `SUBSCRIPTION_ID` de omgevingsvariabele in om `id` de waarde van het geretourneerde veld vast te houden van het abonnement dat u wilt gebruiken:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Nu u een serviceprincipal maken voor gebruik met Terraform. Gebruik [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)en stel het *bereik* als volgt in op uw abonnement:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Uw `appId` `password`, `sp_name`, `tenant` , en worden teruggestuurd. Noteer de `appId` en. `password`

## <a name="configure-terraform-environment-variables"></a>Terraform-omgevingsvariabelen configureren

Als u Terraform wilt configureren om uw Azure AD-serviceprincipal te gebruiken, stelt u de volgende omgevingsvariabelen in, die vervolgens worden gebruikt door de [Azure Terraform-modules.](https://registry.terraform.io/modules/Azure) U de omgeving ook instellen als u werkt met een andere Azure-cloud dan Azure Public.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

U het volgende voorbeeldshellscript gebruiken om deze variabelen in te stellen:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Een voorbeeldscript uitvoeren

Maak een `test.tf` bestand in een lege map en plak in het volgende script.

```hcl
provider "azurerm" {
  # The "feature" block is required for AzureRM provider 2.x. 
  # If you are using version 1.x, the "features" block is not allowed.
  version = "~>2.0"
  features {}
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Sla het bestand op en initialiseer vervolgens de implementatie van Terraform. Met deze stap worden de Azure-modules gedownload die nodig zijn om een Azure-brongroep te maken.

```bash
terraform init
```

De uitvoer lijkt op die in het volgende voorbeeld:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

U een voorbeeld bekijken van de `terraform plan`acties die moeten worden voltooid door het Terraform-script met. Als u klaar bent om de resourcegroep te maken, past u uw Terraform-plan als volgt toe:

```bash
terraform apply
```

De uitvoer lijkt op die in het volgende voorbeeld:

```console
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u Terraform geïnstalleerd of de Cloud Shell gebruikt om Azure-referenties te configureren en resources in uw Azure-abonnement te maken. Zie het volgende artikel voor het maken van een completere Terraform-implementatie in Azure:

> [!div class="nextstepaction"]
> [Een Azure VM maken met Terraform](terraform-create-complete-vm.md)