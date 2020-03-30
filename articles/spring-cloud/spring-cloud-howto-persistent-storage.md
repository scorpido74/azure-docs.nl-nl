---
title: Permanente opslag gebruiken in Azure Spring Cloud | Microsoft Documenten
description: Persistent storage gebruiken in Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 0e49d59386b19aa8da46b8c8e6acfe50e2124541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278540"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Permanente opslag gebruiken in Azure Spring Cloud

Azure Spring Cloud biedt twee soorten opslag voor uw toepassing: persistent en tijdelijk.

Azure Spring Cloud biedt standaard tijdelijke opslag voor elk toepassingsexemplaar. Tijdelijke opslag is beperkt tot 5 GB per instantie met het standaard bevestigingspad /tmp.

> [!WARNING]
> Als u een toepassingsinstantie opnieuw start, wordt de bijbehorende tijdelijke opslag definitief verwijderd.

Permanente opslag is een file-share container beheerd door Azure en toegewezen per toepassing. Gegevens die zijn opgeslagen in permanente opslag, worden gedeeld door alle exemplaren van een toepassing. Een Azure Spring Cloud-exemplaar kan maximaal 10 toepassingen hebben met permanente opslag ingeschakeld. Elke toepassing krijgt 50 GB permanente opslag toegewezen. Het standaard bevestigingspad voor permanente opslag is /persistent.

> [!WARNING]
> Als u de permanente opslag van een toepassing uitschakelt, wordt al die opslag toegewezen en gaan alle opgeslagen gegevens verloren.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>De Azure-portal gebruiken om permanente opslag in te schakelen

1. Selecteer **alle bronnen**op de **startpagina** van uw Azure-portal .

    >![Het pictogram Alle bronnen zoeken](media/portal-all-resources.jpg)

1. Selecteer de Azure Spring Cloud-bron die permanente opslag nodig heeft. In dit voorbeeld wordt de geselecteerde toepassing **upspring**genoemd.

    > ![Selecteer uw toepassing](media/select-service.jpg)

1. Selecteer **Apps** onder **Apps**de kop Instellingen .

1. Uw Azure Spring Cloud-services worden weergegeven in een tabel.  Selecteer de service waaraan u permanente opslag wilt toevoegen. In dit voorbeeld wordt de **gatewayservice** geselecteerd.

    > ![Uw service selecteren](media/select-gateway.jpg)

1. Selecteer **Configuratie** op de configuratiepagina van de service

1. Selecteer het tabblad **Permanente opslag** en selecteer **Inschakelen**.

    > ![Permanente opslag inschakelen](media/enable-persistent-storage.jpg)

Nadat permanente opslag is ingeschakeld, worden de grootte en het pad weergegeven op de configuratiepagina.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>De Azure CLI gebruiken om permanente opslag te wijzigen

Installeer indien nodig de Spring Cloud-extensie voor de Azure CLI:

```azurecli
az extension add --name spring-cloud
```
Andere verrichtingen:

* Een app maken met permanente opslag ingeschakeld:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Ga als u over ga naar permanente opslag voor een bestaande app:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Ga als bedoeld als u permanente opslag in een bestaande app uitschakelt:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Als u de permanente opslag van een toepassing uitschakelt, wordt al die opslag toegewezen en gaan alle opgeslagen gegevens permanent verloren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [toepassings- en servicequota](spring-cloud-quotas.md).
* Meer informatie over het [handmatig schalen van uw toepassing.](spring-cloud-tutorial-scale-manual.md)
