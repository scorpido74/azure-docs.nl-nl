---
title: Sleutel-waardeparen ophalen van een bepaald tijdstip
titleSuffix: Azure App Configuration
description: Oude sleutel-waardeparen ophalen met behulp van punt-in-time moment opnamen in Azure-app configuratie
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523652"
---
# <a name="point-in-time-snapshot"></a>Point-in-time-snapshot

Azure-app configuratie houdt een record bij van wijzigingen die zijn aangebracht in sleutel-waardeparen. Deze record bevat een tijd lijn met belang rijke wijzigingen. U kunt de geschiedenis van een wille keurige sleutel waarde opnieuw samen stellen en de bijbehorende waarde op elk moment in de afgelopen zeven dagen opgeven. Met deze functie kunt u ' time-reizen ' achterwaarts maken en een oude sleutel waarde ophalen. U kunt bijvoorbeeld de configuratie-instellingen herstellen die voor de meest recente implementatie zijn gebruikt om de toepassing terug te zetten naar de vorige configuratie.

## <a name="key-value-retrieval"></a>Sleutel/waarde ophalen

U kunt Azure PowerShell gebruiken om vorige sleutel waarden op te halen.  Gebruik `az appconfig revision list`en voeg de juiste para meters toe om de vereiste waarden op te halen.  Geef het Azure-app configuratie-exemplaar op door de naam van de`--name {app-config-store-name}`winkel op te geven () of`--connection-string {your-connection-string}`door een Connection String () te gebruiken. De uitvoer beperken door een bepaald punt in de tijd (`--datetime`) op te geven en door het maximum aantal items op te`--top`geven dat moet worden geretourneerd ().

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Alle vastgelegde wijzigingen in uw sleutel waarden ophalen.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Alle vastgelegde wijzigingen voor de sleutel `environment` en de labels `test` en `prod`worden opgehaald.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Alle vastgelegde wijzigingen in de hiërarchische sleutel `environment:prod`ruimte ophalen.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Alle vastgelegde wijzigingen voor de sleutel `color` op een specifiek tijdstip ophalen.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

De laatste 10 vastgelegde wijzigingen in uw sleutel waarden ophalen en alleen de waarden voor `key`, `label`en `last-modified` tijds tempel retour neren.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een ASP.NET Core-web-app maken](./quickstart-aspnet-core-app.md)  
