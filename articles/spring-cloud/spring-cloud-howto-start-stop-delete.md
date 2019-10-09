---
title: Uw Azure lente-Cloud toepassing starten, stoppen en verwijderen | Microsoft Docs
description: Uw Azure lente-Cloud toepassing starten, stoppen en verwijderen
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: f7f76644d13c20704d2c3bd908176ac452df2a20
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039082"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Uw Azure lente-Cloud toepassing starten, stoppen en verwijderen

In deze hand leiding wordt uitgelegd hoe u de status van een toepassing in azure lente-Cloud kunt wijzigen met behulp van de Azure Portal of CLI.

## <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Zodra u een toepassing hebt geïmplementeerd, kunt u deze **starten**, **stoppen**en **verwijderen** met behulp van de Azure Portal.

1. Ga naar het Azure veer Cloud service-exemplaar in het Azure Portal.
1. Selecteer het tabblad **toepassings dashboard** .
1. Selecteer de toepassing waarvan u de status wilt wijzigen.
2. Zoek op de pagina **overzicht** voor die toepassing naar knoppen om de toepassing te **starten/stoppen**, **opnieuw**op te starten en te **verwijderen** .

## <a name="using-the-azure-cli"></a>Azure CLI gebruiken

> [!NOTE]
> U kunt optionele para meters gebruiken en standaard instellingen configureren met de Azure CLI. Lees onze [referentie documentatie](spring-cloud-cli-reference.md)voor meer informatie.

* Uw toepassing starten:
    ```Azure CLI
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Uw toepassing stoppen:
    ```Azure CLI
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* De toepassing opnieuw starten:
    ```Azure CLI
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Uw toepassing verwijderen:
    ```Azure CLI
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
