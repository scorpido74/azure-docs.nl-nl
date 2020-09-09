---
title: 'Quickstart: Azure Spring Cloud-service inrichten'
description: Beschrijft het maken van een Azure Spring Cloud service-exemplaar voor app-implementatie.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: c91237e3a14c60e477f58be0bf62f634b462960b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951670"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Quickstart: Azure Spring Cloud-service inrichten

U kunt Azure Spring Cloud instantiëren met behulp van de Azure-portal of de Azure CLI.  Beide methoden worden beschreven in de volgende procedures.
## <a name="prerequisites"></a>Vereisten

* [JDK 8 installeren](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)
* (Optioneel) [De Azure CLI versie 2.0.67 of hoger installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) en de Azure Spring Cloud-extensie installeren met de opdracht: `az extension add --name spring-cloud`
* (Optioneel) [De Azure-toolkit voor IntelliJ installeren](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) en [aanmelden](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Een exemplaar van Azure Spring Cloud inrichten

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

Met de volgende procedure maakt u een exemplaar van Azure Spring Cloud met behulp van de Azure Portal.

1. Open [Azure Portal](https://ms.portal.azure.com/) op een nieuw tabblad. 

2. Zoek in het bovenste zoekvak naar **Azure Spring Cloud**.

3. Selecteer **Azure Spring Cloud** in de resultaten.

    ![ASC-pictogram: starten](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Klik op **+ Toevoegen** op de Azure Spring Cloud-pagina.

    ![ASC-pictogram: toevoegen](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Vul het formulier in op de Azure Spring Cloud-pagina **Maken**.  Houd rekening met de volgende richtlijnen:
    - **Abonnement**: Selecteer het abonnement waarvoor u voor deze resource gefactureerd wilt worden.
    - **Resourcegroep**: Het is een best practice om nieuwe resourcegroepen te maken voor nieuwe resources. Dit wordt in latere stappen gebruikt als **\<resource group name\>** .
    - **Servicedetails/naam**: Geef de **\<service instance name\>** op.  De naam moet tussen de 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.  Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.
    - **Locatie**: Selecteer de locatie voor uw service-exemplaar.

    ![ASC-portal starten](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klik op **Controleren en maken**.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

De volgende procedure maakt gebruik van de Azure CLI-extensie om een exemplaar van Azure Spring Cloud in te richten.

1. Meld u aan bij de Azure CLI en kies uw actieve abonnement. Zorg ervoor dat u het actieve abonnement kiest dat is goedgekeurd voor Azure Spring Cloud

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Bedenk een naam voor uw Azure Spring Cloud-service.  De naam moet tussen de 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.  Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.

1. Maak een resourcegroep die uw Azure Spring Cloud-service bevat.

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    Meer informatie over [Azure-resourcegroepen](../azure-resource-manager/management/overview.md).

1. Open een Azure CLI-venster en voer de volgende opdrachten uit om een exemplaar van Azure Spring Cloud in te richten.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    De implementatie van de service-instantie duurt ongeveer vijf minuten.
---

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Configuratieserver instellen](spring-cloud-quickstart-setup-config-server.md)


