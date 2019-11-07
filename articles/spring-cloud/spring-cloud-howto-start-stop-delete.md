---
title: Uw Azure lente-Cloud toepassing starten, stoppen en verwijderen | Microsoft Docs
description: Uw Azure lente-Cloud toepassing starten, stoppen en verwijderen
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607728"
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

Installeer de lente-Cloud extensie voor Azure CLI:

```azurecli
az extension add --name spring-cloud
```

* Uw toepassing starten:
    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Uw toepassing stoppen:
    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* De toepassing opnieuw starten:
    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Uw toepassing verwijderen:
    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
