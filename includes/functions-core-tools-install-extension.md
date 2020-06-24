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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75564707"
---
## <a name="register-extensions"></a>Extensies registreren

Met uitzonde ring van HTTP-en timer-triggers, worden function-bindingen in runtime versie 2. x en hoger geïmplementeerd als uitbreidings pakketten. In versie 2. x en voorbij de Azure Functions runtime moet u de uitbrei dingen expliciet registreren voor de bindings typen die in uw functies worden gebruikt. De uitzonde ringen hierop zijn HTTP-bindingen en timer triggers, waarvoor geen uitbrei dingen zijn vereist.

U kunt ervoor kiezen om bindings uitbreidingen afzonderlijk te installeren of u kunt een referentie voor een uitbreidings bundel toevoegen aan de host.jsvan het project bestand. Met uitbreidings bundels wordt de kans verwijderd om compatibiliteits problemen met het pakket op te lossen wanneer u meerdere bindings typen gebruikt. Het is de aanbevolen methode voor het registreren van bindings uitbreidingen. Extensie bundels verwijdert ook de vereiste van het installeren van de .NET Core 2. x SDK. 

### <a name="extension-bundles"></a>Uitbreidings bundels

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Zie [Azure functions bindings uitbreidingen registreren](../articles/azure-functions/functions-bindings-register.md#extension-bundles)voor meer informatie. U moet uitbreidings bundels toevoegen aan de host.jsop voordat u bindingen toevoegt aan het function.jsbestand.

### <a name="register-individual-extensions"></a>Afzonderlijke uitbrei dingen registreren

Als u uitbrei dingen wilt installeren die zich niet in een bundel bevinden, kunt u hand matig afzonderlijke extensie pakketten voor specifieke bindingen registreren. 

> [!NOTE]
> Als u hand matig extensies wilt registreren met `func extensions install` , moet u de .net Core 2. x SDK hebben geïnstalleerd.

Voer de volgende opdracht uit in de projectmap nadat u uw *function.js* hebt bijgewerkt met alle bindingen die uw functie nodig heeft.

```bash
func extensions install
```

Met de opdracht leest u de *function.jsin* het bestand om te zien welke pakketten u nodig hebt, installeert u ze en bouwt u het project extensies opnieuw. Er worden nieuwe bindingen aan de huidige versie toegevoegd, maar bestaande bindingen worden niet bijgewerkt. Gebruik de `--force` optie om bestaande bindingen bij te werken naar de meest recente versie bij het installeren van nieuwe.
