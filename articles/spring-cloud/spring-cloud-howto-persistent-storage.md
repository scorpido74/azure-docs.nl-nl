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
ms.openlocfilehash: 8c57698471d1363438c10e5806f9ed6f1da5333f
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039089"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Permanente opslag gebruiken in azure lente-Cloud

Azure lente Cloud biedt twee soorten opslag voor uw toepassing: permanente en tijdelijke.  Azure lente-Cloud maakt standaard tijdelijke opslag mogelijk voor elk toepassings exemplaar. Tijdelijke opslag is beperkt tot 5 GB en het standaard koppelingspad is `/tmp`.

> [!WARNING]
> Als u een exemplaar van een toepassing opnieuw start, wordt de bijbehorende tijdelijke opslag permanent verwijderd.

Permanente opslag is een bestands share container die wordt beheerd door Azure en die wordt toegewezen aan een per toepassings bereik. Gegevens die zijn opgeslagen in permanente opslag, worden gedeeld door alle exemplaren van de toepassing. Een Azure lente-Cloud service-exemplaar kan Maxi maal 10 toepassingen bevatten waarvoor een permanente schijf is ingeschakeld en elke toepassing krijgt 50 GB aan permanente opslag. Het standaardpad voor permanente opslag is `/persistent`.

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Permanente opslag inschakelen met behulp van de Azure Portal

1. Selecteer op de pagina Azure lente-Cloud service de optie **toepassings dashboard**en selecteer vervolgens de toepassing waarvoor permanente opslag vereist is.
1. Op het tabblad **overzicht** gaat u naar het kenmerk **persistente opslag** en selecteert u **uitgeschakeld**.
1. Klik op **inschakelen** om permanente opslag in te scha kelen en selecteer **OK** om de wijziging toe te passen.

Wanneer permanente opslag is ingeschakeld, worden de grootte en het pad ervan weer gegeven in het kenmerk **persistente opslag** van de pagina **overzicht** .

> [!WARNING]
> Als u permanente opslag *uitschakelt* , wordt de toewijzing van de opslag voor die toepassing ongedaan.  Alle gegevens in dat opslag account gaan verloren. 

## <a name="enable-persistent-storage-using-the-azure-cli"></a>Permanente opslag inschakelen met behulp van de Azure CLI

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