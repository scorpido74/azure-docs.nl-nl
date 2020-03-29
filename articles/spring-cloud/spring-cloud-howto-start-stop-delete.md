---
title: Uw Azure Spring Cloud-toepassing starten, stoppen en verwijderen | Microsoft Documenten
description: Uw Azure Spring Cloud-toepassing starten, stoppen en verwijderen
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276827"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Uw Azure Spring Cloud-toepassing starten, stoppen en verwijderen

In deze handleiding wordt uitgelegd hoe u de status van een toepassing in Azure Spring Cloud wijzigt met behulp van de Azure-portal of de Azure CLI.

## <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Nadat u een toepassing hebt geïmplementeerd, u deze starten, stoppen en verwijderen met behulp van de Azure-portal.

1. Ga naar uw Azure Spring Cloud-serviceinstantie in de Azure-portal.
1. Selecteer het tabblad **Toepassingsdashboard.**
1. Selecteer de toepassing waarvan u de status wilt wijzigen.
1. Selecteer **op** de pagina Overzicht voor die toepassing **Start/Stop**, **Opnieuw starten**of **Verwijderen**.

## <a name="using-the-azure-cli"></a>Azure CLI gebruiken

> [!NOTE]
> U optionele parameters gebruiken en standaardinstellingen configureren met de Azure CLI. Lees onze referentiedocumentatie over de Azure CLI door onze [referentiedocumentatie](spring-cloud-cli-reference.md)te lezen.  

Installeer eerst eerst de Azure Spring Cloud-extensie voor de Azure CLI als volgt:

```azurecli
az extension add --name spring-cloud
```

Selecteer vervolgens een van deze Azure CLI-bewerkingen:

* Ga als een startvan uw aanvraag:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Ga als u uw toepassing wilt stoppen:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Ga als volgt te werk om uw toepassing opnieuw te starten:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Ga als een nieuwe toepassing op het juiste punt:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
