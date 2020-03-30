---
title: Sleutelwaardeparen ophalen uit een point-in-time
titleSuffix: Azure App Configuration
description: Oude sleutelwaardeparen ophalen met point-in-time momentopnamen in Azure App-configuratie
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523652"
---
# <a name="point-in-time-snapshot"></a>Point-in-time-snapshot

Azure App Configuration houdt een record bij van wijzigingen die zijn aangebracht in sleutelwaardeparen. Deze record bevat een tijdlijn van wijzigingen in de sleutelwaarde. U de geschiedenis van elke sleutelwaarde reconstrueren en op elk moment in de voorgaande zeven dagen de waarde ervan in het verleden geven. Met deze functie u 'tijdreizen' naar achteren en een oude sleutelwaarde ophalen. U bijvoorbeeld configuratie-instellingen herstellen die vóór de meest recente implementatie zijn gebruikt om de toepassing terug te draaien naar de vorige configuratie.

## <a name="key-value-retrieval"></a>Sleutel/waarde ophalen

U Azure PowerShell gebruiken om belangrijke waarden uit het verleden op te halen.  Gebruik, `az appconfig revision list`het toevoegen van de juiste parameters om de vereiste waarden op te halen.  Geef de instantie Azure App-configuratie op`--name {app-config-store-name}`door de winkelnaam`--connection-string {your-connection-string}`( ) op te geven of door een verbindingstekenreeks ( ) te gebruiken. Beperk de uitvoer door een specifiek`--datetime`tijdstip ( ) op te geven`--top`en door het maximum aantal items op te geven dat moet worden geretourneerd ( ).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Haal alle opgenomen wijzigingen in uw kernwaarden op.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Haal alle geregistreerde wijzigingen `environment` voor `test` de `prod`sleutel en de labels op en .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Haal alle opgenomen wijzigingen in `environment:prod`de hiërarchische sleutelruimte op.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Haal alle geregistreerde wijzigingen `color` voor de sleutel op een specifiek punt-in-time op.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Haal de laatste 10 geregistreerde wijzigingen in uw sleutelwaarden `label`op `last-modified` en retourneer alleen de waarden voor `key`en tijdstempel.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een ASP.NET Core-web-app maken](./quickstart-aspnet-core-app.md)  
