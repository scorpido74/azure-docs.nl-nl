---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75564707"
---
## <a name="register-extensions"></a>Extensies registreren

Met uitzondering van HTTP- en timertriggers worden functiesbindingen in runtime versie 2.x en hoger geïmplementeerd als uitbreidingspakketten. In versie 2.x en verder van de runtime van Azure-functies moet u de extensies expliciet registreren voor de bindingstypen die in uw functies worden gebruikt. De uitzonderingen hierop zijn HTTP-bindingen en timertriggers, waarvoor geen extensies nodig zijn.

U ervoor kiezen om bindende extensies afzonderlijk te installeren of u een extensiebundel toevoegen die verwijst naar het projectbestand host.json. Extensiebundels verwijdert de kans op compatibiliteitsproblemen met pakketten bij het gebruik van meerdere bindingstypen. Het is de aanbevolen aanpak voor het registreren van bindende extensies. Extensiebundels verwijdert ook de vereiste voor het installeren van de .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>Uitbreidingsbundels

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Zie [Azure Functions bindingsextensies registreren](../articles/azure-functions/functions-bindings-register.md#extension-bundles)voor meer informatie. U moet extensiebundels toevoegen aan host.json voordat u bindingen toevoegt aan het bestand function.json.

### <a name="register-individual-extensions"></a>Afzonderlijke extensies registreren

Als u extensies moet installeren die niet in een bundel zitten, u handmatig afzonderlijke uitbreidingspakketten registreren voor specifieke bindingen. 

> [!NOTE]
> Als u extensies handmatig `func extensions install`wilt registreren met behulp van , moet u de .NET Core 2.x SDK hebben geïnstalleerd.

Nadat u uw *function.json-bestand* hebt bijgewerkt om alle bindingen op te nemen die uw functie nodig heeft, voert u de volgende opdracht uit in de projectmap.

```bash
func extensions install
```

De opdracht leest het *bestand function.json* om te zien welke pakketten u nodig hebt, installeert ze en herbouwt het extensiesproject. Het voegt nieuwe bindingen toe aan de huidige versie, maar werkt bestaande bindingen niet bij. Gebruik `--force` de optie om bestaande bindingen bij te werken naar de nieuwste versie bij het installeren van nieuwe.
