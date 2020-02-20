---
title: 'Quick Start: een terraform-configuratie maken voor Azure'
description: Maak kennis met Terraform in Azure door een Azure Cosmos DB en Azure Container Instances te implementeren.
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 92f66fcfeac69b66eb8cdb0dc58b7a3171d45e10
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470018"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>Quick Start: een terraform-configuratie maken voor Azure

In deze Quick Start maakt u Azure-resources met behulp van terraform. De stappen in dit artikel begeleiden u bij het maken van de volgende resources:

> [!div class="checklist"]
> * Azure Cosmos DB-exemplaar
> * Azure Container Instance
> * App die werkt op deze twee resources

## <a name="create-first-configuration"></a>Eerste configuratie maken

In deze sectie maakt u de configuratie voor een Azure Cosmos DB-exemplaar.

1. Meld u aan bij de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Open de Azure Cloud Shell.

1. Start de Cloud Shell editor:

    ```bash
    code main.tf
    ```

1. De configuratie in deze stap vermodelt een aantal Azure-resources. Deze resources omvatten een Azure-resource groep en een Azure Cosmos DB-exemplaar. Een wille keurig geheel getal wordt gebruikt om een unieke Cosmos DB-exemplaar naam te maken. Er worden ook verschillende Cosmos DB-instellingen geconfigureerd. Zie de terraform-referentie voor [Cosmos DB](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html)voor meer informatie. Kopieer en plak de volgende terraform-configuratie in de editor:

    ```hcl
    resource "azurerm_resource_group" "vote-resource-group" {
      name     = "vote-resource-group"
      location = "westus"
    }

    resource "random_integer" "ri" {
      min = 10000
      max = 99999
    }

    resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
      name                = "tfex-cosmos-db-${random_integer.ri.result}"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      offer_type          = "Standard"
      kind                = "GlobalDocumentDB"

      consistency_policy {
        consistency_level       = "BoundedStaleness"
        max_interval_in_seconds = 10
        max_staleness_prefix    = 200
      }

      geo_location {
        location          = "westus"
        failover_priority = 0
      }
    }
    ```

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

## <a name="run-the-configuration"></a>De configuratie uitvoeren

In deze sectie gebruikt u verschillende terraform-opdrachten om de configuratie uit te voeren.

1. Met de opdracht [terraform init](https://www.terraform.io/docs/commands/init.html) wordt de werkmap geïnitialiseerd. Voer de volgende opdracht uit in Cloud Shell:

    ```bash
    terraform init
    ```

1. De [terraform-plan](https://www.terraform.io/docs/commands/plan.html) opdracht kan worden gebruikt om de configuratie syntaxis te valideren. De para meter `-out` stuurt de resultaten naar een bestand. Het uitvoer bestand kan later worden gebruikt om de configuratie toe te passen. Voer de volgende opdracht uit in Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. De opdracht [terraform apply](https://www.terraform.io/docs/commands/apply.html) wordt gebruikt om de configuratie toe te passen. Het uitvoer bestand van de vorige stap is opgegeven. Deze opdracht zorgt ervoor dat de Azure-resources worden gemaakt. Voer de volgende opdracht uit in Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Als u de resultaten in de Azure Portal wilt controleren, bladert u naar de nieuwe resource groep. Het nieuwe Azure Cosmos DB-exemplaar bevindt zich in de nieuwe resource groep.

## <a name="update-configuration"></a>Configuratie bijwerken

In deze sectie wordt beschreven hoe u de configuratie bijwerkt voor het toevoegen van een Azure-container exemplaar. In de container wordt een toepassing uitgevoerd die gegevens leest en schrijft naar de Cosmos DB.

1. Start de Cloud Shell editor:

    ```bash
    code main.tf
    ```

1. Met de configuratie in deze stap worden twee omgevings variabelen ingesteld: `COSMOS_DB_ENDPOINT` en `COSMOS_DB_MASTERKEY`. Deze variabelen bevatten de locatie en de sleutel voor toegang tot de database. De waarden voor deze variabelen worden opgehaald uit het data base-exemplaar dat u in de vorige stap hebt gemaakt. Dit proces wordt interpolatie genoemd. Meer informatie over Terraform-interpolatie vindt u in [Interpolation Syntax](https://www.terraform.io/docs/configuration/interpolation.html) (Interpolatiesyntaxis). De configuratie omvat ook een uitvoerblok, dat de volledig gekwalificeerde domeinnaam (FQDN) van het containerexemplaar retourneert. Kopieer en plak de volgende code in de editor:

    ```hcl
    resource "azurerm_container_group" "vote-aci" {
      name                = "vote-aci"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      ip_address_type     = "public"
      dns_name_label      = "vote-aci"
      os_type             = "linux"

      container {
        name   = "vote-aci"
        image  = "microsoft/azure-vote-front:cosmosdb"
        cpu    = "0.5"
        memory = "1.5"
        ports {
          port     = 80
          protocol = "TCP"
        }

        secure_environment_variables = {
          "COSMOS_DB_ENDPOINT"  = azurerm_cosmosdb_account.vote-cosmos-db.endpoint
          "COSMOS_DB_MASTERKEY" = azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key
          "TITLE"               = "Azure Voting App"
          "VOTE1VALUE"          = "Cats"
          "VOTE2VALUE"          = "Dogs"
        }
      }
    }

    output "dns" {
      value = azurerm_container_group.vote-aci.fqdn
    }
    ```

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

1. Zoals u in de vorige sectie hebt gedaan, voert u de volgende opdracht uit om de wijzigingen te visualiseren:

    ```bash
    terraform plan --out plan.out
    ```

1. Voer de `terraform apply` opdracht uit om de configuratie toe te passen.

    ```bash
    terraform apply plan.out
    ```

1. Noteer de FQDN van het container exemplaar.

## <a name="test-application"></a>Toepassing testen

Als u de toepassing wilt testen, gaat u naar de FQDN van het container exemplaar. De resultaten moeten er ongeveer uitzien als in de volgende uitvoer:

![Azure Voting-app](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resources die u in dit artikel hebt gemaakt.

Voer de opdracht [terraform vernietigen](https://www.terraform.io/docs/commands/destroy.html) uit om de Azure-resources die in deze zelf studie zijn gemaakt, te verwijderen:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Installeer en configureer terraform om Azure-resources in te richten](terraform-install-configure.md).