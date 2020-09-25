---
title: 'Quickstart: Azure Spring Cloud-configuratieserver instellen'
description: Hierin wordt het instellen van de Azure Spring Cloud-configuratieserver voor app-implementatie beschreven.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 639672bdeff2f833c280a041e497197286c9ff24
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885706"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Quickstart: Azure Spring Cloud-configuratieserver instellen

Azure Spring Cloud-configuratieserver is een gecentraliseerde configuratieservice voor gedistribueerde systemen. Hierin wordt een pluggable opslagplaatslaag gebruikt die momenteel ondersteuning biedt voor lokale opslag, Git en Subversion. In deze quickstart stelt u de configuratieserver in om gegevens op te halen uit een Git-opslagplaats.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Vereisten

* Voltooi de vorige quickstart in deze serie: [Azure Spring Cloud-service inrichten](spring-cloud-quickstart-provision-service-instance.md).

## <a name="azure-spring-cloud-config-server-procedures"></a>Procedures voor Azure Spring Cloud-configuratieserver

Stel uw configuratieserver in met de locatie van de Git-opslagplaats voor het project door de volgende opdracht uit te voeren. Vervang `<service instance name>` door de naam van de service die u eerder hebt gemaakt. De standaardwaarde voor de naam van het service-exemplaar die u in de voorgaande quickstart hebt ingesteld, werkt niet met deze opdracht.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

Deze opdracht vertelt de configuratieserver dat deze de configuratiegegevens moet vinden in de map [steeltoe-sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) in de opslagplaats van de voorbeeld-app. Omdat de naam van de app die de configuratiegegevens ontvangt `planet-weather-provider` is, wordt het bestand [planet-weather-provider.yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml) gebruikt.

::: zone-end

::: zone pivot="programming-language-java"
Azure Spring Cloud-configuratieserver is een gecentraliseerde configuratieservice voor gedistribueerde systemen. Hierin wordt een pluggable opslagplaatslaag gebruikt die momenteel ondersteuning biedt voor lokale opslag, Git en Subversion.  Stel de configuratieserver in om microservice-apps te implementeren in Azure Spring Cloud.

## <a name="prerequisites"></a>Vereisten

* [JDK 8 installeren](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)
* (Optioneel) [De Azure CLI versie 2.0.67 of hoger installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) en de Azure Spring Cloud-extensie installeren met de opdracht: `az extension add --name spring-cloud`
* (Optioneel) [De Azure-toolkit voor IntelliJ installeren](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) en [aanmelden](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="azure-spring-cloud-config-server-procedures"></a>Procedures voor Azure Spring Cloud-configuratieserver

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

Met de volgende procedure stelt u de configuratieserver in met behulp van de Azure-portal om het [Piggymetrics-voorbeeld](spring-cloud-quickstart-sample-app-introduction.md) te implementeren.

1. Ga naar de pagina **Overzicht** van de service en selecteer **Config Server**.

2. Stel in de sectie **Standaardopslagplaats** **URI** in op https://github.com/Azure-Samples/piggymetrics-config.

3. Selecteer **Toepassen** om uw wijzigingen op te slaan.

    ![Schermopname van ASC-portal](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

In de volgende procedure wordt de Azure CLI gebruikt om de configuratieserver in te stellen om het [Piggymetrics-voorbeeld](spring-cloud-quickstart-sample-app-introduction.md) te implementeren.

Stel uw configuratieserver in met de locatie van de Git-opslagplaats voor het project:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---
::: zone-end

## <a name="clean-up-resources"></a>Resources opschonen

Als u door wilt gaan naar de volgende quickstart van deze reeks, slaat u deze stap over.

In deze quickstarts hebt u Azure-resources gemaakt waarmee de kosten blijven toenemen als de resources in uw abonnement blijven. Als u wilt doorgaan naar de volgende quickstart en u deze resources niet meer nodig denkt te hebben, verwijdert u de resourcegroep via de portal of door de volgende opdracht in Cloud Shell uit te voeren:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

In een voorgaande quickstart hebt u ook de standaardnaam van de resourcegroep ingesteld. Als u niet van plan bent om door te gaan naar de volgende quickstart, wist u die standaardinstelling door de volgende CLI-opdracht uit te voeren:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Apps bouwen en implementeren](spring-cloud-quickstart-deploy-apps.md)
