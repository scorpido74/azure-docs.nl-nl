---
title: Snelstart - Een Terraform-configuratie maken voor Azure
description: Maak kennis met Terraform in Azure door een Azure Cosmos DB en Azure Container Instances te implementeren.
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 92f66fcfeac69b66eb8cdb0dc58b7a3171d45e10
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77470018"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>Snelstart: een Terraform-configuratie maken voor Azure

In deze quickstart maakt u Azure-resources met Terraform. De stappen in dit artikel lopen u door het maken van de volgende bronnen:

> [!div class="checklist"]
> * Azure Cosmos DB-exemplaar
> * Azure Container Instance
> * App die werkt in deze twee bronnen

## <a name="create-first-configuration"></a>Eerste configuratie maken

In deze sectie maakt u de configuratie voor een Azure Cosmos DB-exemplaar.

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Open de Azure Cloud Shell.

1. Start de Cloud Shell-editor:

    ```bash
    code main.tf
    ```

1. De configuratie in deze stap modelleert een paar Azure-bronnen. Deze bronnen omvatten een Azure-brongroep en een Azure Cosmos DB-exemplaar. Een willekeurig geheel getal wordt gebruikt om een unieke Cosmos DB-instantienaam te maken. Er worden ook verschillende Cosmos DB-instellingen geconfigureerd. Zie voor meer informatie de [cosmos DB Terraform referentie](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html). Kopieer en plak de volgende Terraform-configuratie in de editor:

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

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

## <a name="run-the-configuration"></a>De configuratie uitvoeren

In deze sectie gebruikt u verschillende Terraform-opdrachten om de configuratie uit te voeren.

1. Met de opdracht [terraform init](https://www.terraform.io/docs/commands/init.html) wordt de werkmap geïnitialiseerd. Voer de volgende opdracht uit in Cloud Shell:

    ```bash
    terraform init
    ```

1. De opdracht [terraformplan](https://www.terraform.io/docs/commands/plan.html) kan worden gebruikt om de configuratiesyntaxis te valideren. De `-out` parameter stuurt de resultaten naar een bestand. Het uitvoerbestand kan later worden gebruikt om de configuratie toe te passen. Voer de volgende opdracht uit in Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. De [opdracht terraform apply](https://www.terraform.io/docs/commands/apply.html) wordt gebruikt om de configuratie toe te passen. Het uitvoerbestand van de vorige stap is opgegeven. Met deze opdracht worden de Azure-resources gemaakt. Voer de volgende opdracht uit in Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Als u de resultaten in de Azure-portal wilt verifiëren, bladert u naar de nieuwe brongroep. De nieuwe instantie Azure Cosmos DB bevindt zich in de nieuwe brongroep.

## <a name="update-configuration"></a>Configuratie bijwerken

In deze sectie ziet u hoe u de configuratie bijwerken om een Azure Container Instance op te nemen. In de container wordt een toepassing uitgevoerd die gegevens leest en schrijft naar de Cosmos DB.

1. Start de Cloud Shell-editor:

    ```bash
    code main.tf
    ```

1. De configuratie in deze stap stelt `COSMOS_DB_ENDPOINT` `COSMOS_DB_MASTERKEY`twee omgevingsvariabelen in: en . Deze variabelen bevatten de locatie en de sleutel voor toegang tot de database. De waarden voor deze variabelen worden verkregen uit de database-instantie die in de vorige stap is gemaakt. Dit proces wordt interpolatie genoemd. Meer informatie over Terraform-interpolatie vindt u in [Interpolation Syntax](https://www.terraform.io/docs/configuration/interpolation.html) (Interpolatiesyntaxis). De configuratie omvat ook een uitvoerblok, dat de volledig gekwalificeerde domeinnaam (FQDN) van het containerexemplaar retourneert. Kopieer en plak de volgende code in de editor:

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

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

1. Net als in de vorige sectie voert u de volgende opdracht uit om de wijzigingen te visualiseren die moeten worden aangebracht:

    ```bash
    terraform plan --out plan.out
    ```

1. Voer `terraform apply` de opdracht uit om de configuratie toe te passen.

    ```bash
    terraform apply plan.out
    ```

1. Noteer de containerinstantie FQDN.

## <a name="test-application"></a>Toepassing testen

Als u de toepassing wilt testen, navigeert u naar de FQDN van de containerinstantie. U ziet resultaten die vergelijkbaar zijn met de volgende uitvoer:

![Azure Voting-app](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, verwijdert u de bronnen die in dit artikel zijn gemaakt.

Voer de opdracht [Terraform destroy uit](https://www.terraform.io/docs/commands/destroy.html) om de Azure-resources te verwijderen die in deze zelfstudie zijn gemaakt:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Installeer en configureer Terraform om Azure-resources in te richten.](terraform-install-configure.md)