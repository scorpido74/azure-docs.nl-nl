---
title: Permanente opslag gebruiken in azure lente-Cloud | Microsoft Docs
description: Permanente opslag gebruiken in azure lente-Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: f14da7d8e64c58a54a9da4d851ac22fd710ea8ca
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163742"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Permanente opslag gebruiken in azure lente-Cloud

Azure lente Cloud biedt twee soorten opslag voor uw toepassing: permanente en tijdelijke.  Azure lente-Cloud maakt standaard tijdelijke opslag mogelijk voor elk toepassings exemplaar. Tijdelijke opslag is beperkt tot 5 GB met een standaardpad voor koppelen: `/tmp`.

> [!WARNING]
> Als een toepassings exemplaar opnieuw wordt gestart, wordt de bijbehorende tijdelijke opslag permanent verwijderd.

Permanente opslag is een bestands share container die wordt beheerd door Azure, toegewezen per toepassing. Gegevens die zijn opgeslagen in permanente opslag, worden gedeeld door alle exemplaren van de toepassing. Een Azure lente-Cloud service-exemplaar kan Maxi maal 10 toepassingen bevatten waarvoor permanente schijf is ingeschakeld. Elke toepassing ontvangt 50 GB aan permanente opslag. Het standaardpad voor permanente opslag is `/persistent`.

> [!WARNING]
> Als u permanente opslag *uitschakelt* , wordt de toewijzing van de opslag voor die toepassing ongedaan.  Alle gegevens in dat opslag account gaan verloren. 

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Permanente opslag inschakelen met behulp van de Azure Portal

1. Selecteer **alle resources**in het Start scherm van uw Azure Portal.

     >![Het pictogram alle resources zoeken](media/portal-all-resources.jpg)

1. Zoek en selecteer de Azure lente-Cloud resource waarvoor permanente opslag nodig is.  In dit voor beeld wordt de toepassing *jpspring*genoemd.

    > ![Uw applicationb zoeken](media/select-service.jpg)

1. Selecteer in de kop **instellingen** de optie **apps**.

1. Uw lente-Cloud Services worden weer gegeven in de tabel.  Selecteer de service waaraan u permanente opslag wilt toevoegen.  In dit voor beeld selecteren we onze **Gateway** service.

    > ![Uw service selecteren](media/select-gateway.jpg)

1. Selecteer in de Blade configuratie van de service de optie **configuratie**

1. Selecteer het tabblad **permanente opslag** en schakel permanente opslag in.

    > ![Permanente opslag inschakelen](media/enable-persistent-storage.jpg)

Wanneer permanente opslag is ingeschakeld, worden beide op deze pagina weer gegeven.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>De Azure CLI gebruiken om permanente opslag te wijzigen

Een app maken waarvoor permanente schijf is ingeschakeld:
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

Permanente opslag in een bestaande app inschakelen:

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
``` 

Permanente opslag in een bestaande app uitschakelen:

> [!WARNING]
> Als u permanente opslag uitschakelt, wordt de toewijzing van de opslag voor die toepassing ongedaan maken, waarbij de gegevens die daar zijn opgeslagen, permanent verloren gaan. 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [toepassings-en service quota's](spring-cloud-quotas.md)of over het [hand matig schalen van uw toepassing](spring-cloud-tutorial-scale-manual.md).