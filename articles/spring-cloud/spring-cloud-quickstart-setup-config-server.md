---
title: 'Quickstart: Azure Spring Cloud-configuratieserver instellen'
description: Hierin wordt het instellen van de Azure Spring Cloud-configuratieserver voor app-implementatie beschreven.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 47f74b551919177b13f5a72d6eedeae3c77b9f14
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951659"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Quickstart: Azure Spring Cloud-configuratieserver instellen

Azure Spring Cloud-configuratieserver is een gecentraliseerde configuratieservice voor gedistribueerde systemen. Hierin wordt een pluggable opslagplaatslaag gebruikt die momenteel ondersteuning biedt voor lokale opslag, Git en Subversion.  Stel de configuratieserver in om microservice-apps te implementeren in Azure Spring Cloud.

## <a name="prerequisites"></a>Vereisten

* [JDK 8 installeren](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)
* (Optioneel) [De Azure CLI versie 2.0.67 of hoger installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) en de Azure Spring Cloud-extensie installeren met de opdracht: `az extension add --name spring-cloud`
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

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Apps bouwen en implementeren](spring-cloud-quickstart-deploy-apps.md)
