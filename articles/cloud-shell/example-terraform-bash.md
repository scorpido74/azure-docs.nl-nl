---
title: Implementeren met Terraform vanuit Azure Cloud Shell | Microsoft Documenten
description: Implementeren met Terraform vanuit Azure Cloud Shell
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: 8bacadd8941131f608411e61cc15c120c1b2bc60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458151"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Implementeren met Terraform van Bash in Azure Cloud Shell
In dit artikel u een brongroep maken met de [Terraform AzureRM-provider.](https://www.terraform.io/docs/providers/azurerm/index.html)

[Hashicorp Terraform](https://www.terraform.io/) is een open source tool die API's codificert in declaratieve configuratiebestanden die kunnen worden gedeeld tussen teamleden die moeten worden bewerkt, beoordeeld en geversioneerd. De Microsoft AzureRM-provider wordt gebruikt om te communiceren met resources die worden ondersteund door Azure Resource Manager via de AzureRM-API's.

## <a name="automatic-authentication"></a>Automatische verificatie
Terraform is standaard geïnstalleerd in Bash in Cloud Shell. Bovendien verifieert Cloud Shell automatisch uw standaard Azure CLI-abonnement om resources te implementeren via de Terraform Azure-modules.

Terraform maakt gebruik van het standaard Azure CLI-abonnement dat is ingesteld. Voer het als instellen voor het bijwerken van standaardabonnementen uit:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Kennismaking
### <a name="launch-bash-in-cloud-shell"></a>Bash starten in Cloud Shell
1. Cloud Shell starten vanaf uw gewenste locatie
2. Controleren of uw voorkeursabonnement is ingesteld

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Een sjabloon Terraform maken
Maak een nieuwe Terraform-sjabloon met de naam main.tf met de gewenste teksteditor.

```
vim main.tf
```

Kopieer/plak de volgende code in Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Sla uw bestand op en sluit uw teksteditor af.

### <a name="terraform-init"></a>Terraform init
Begin met `terraform init`het uitvoeren van .

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

De [opdracht terraform init](https://www.terraform.io/docs/commands/init.html) wordt gebruikt om een werkmap met Terraform-configuratiebestanden te initialiseren. De `terraform init` opdracht is de eerste opdracht die moet worden uitgevoerd na het schrijven van een nieuwe Terraform-configuratie of het klonen van een bestaande opdracht van versiebeheer. Het is veilig om deze opdracht meerdere keren uit te voeren.

### <a name="terraform-plan"></a>Terraform plan
Bekijk een voorbeeld van de bronnen `terraform plan`die moeten worden gemaakt door de sjabloon Terraform met .

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

De opdracht [terraform plan](https://www.terraform.io/docs/commands/plan.html) wordt gebruikt om een uitvoeringsplan te maken. Terraform voert een vernieuwing uit, tenzij expliciet uitgeschakeld, en bepaalt vervolgens welke acties nodig zijn om de gewenste status te bereiken die is opgegeven in de configuratiebestanden. Het plan kan worden opgeslagen met -uit en vervolgens worden verstrekt aan terraform toe te passen om ervoor te zorgen dat alleen de vooraf geplande acties worden uitgevoerd.

### <a name="terraform-apply"></a>Terraform apply
Informeer de `terraform apply`Azure-resources in met .

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

De [opdracht terraform apply](https://www.terraform.io/docs/commands/apply.html) wordt gebruikt om de wijzigingen toe te passen die nodig zijn om de gewenste status van de configuratie te bereiken.

### <a name="verify-deployment-with-azure-cli"></a>Implementatie verifiëren met Azure CLI
Uitvoeren `az group show -n myRgName` om te controleren of de resource is geslaagd voor het inrichten.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Opruimen met terraform te vernietigen
Ruim de resourcegroep op die is gemaakt met het [opdracht Terraform destroy](https://www.terraform.io/docs/commands/destroy.html) om terraform-infrastructuur op te ruimen.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

U hebt een Azure-bron gemaakt via Terraform. Ga naar de volgende stappen om verder te leren over Cloud Shell.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over de Terraform Azure-provider](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash in Cloud Shell snelaan de start](quickstart.md)
