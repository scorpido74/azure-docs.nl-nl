---
title: Problemen met het gebruik van Azure-reservering oplossen
description: Dit artikel helpt u bij het begrijpen en oplossen van problemen met Azure-reserveringen die geen of nul (0) gebruik in de Azure Portal weergeven. Het gebruik dat niet overeenkomt, wordt ook uitgelegd.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: 9d5706843e8131110566ad8f955415b6db29f7ba
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207427"
---
# <a name="troubleshoot-reservation-utilization"></a>Problemen met reserveringsgebruik oplossen

Dit artikel helpt u bij het begrijpen en oplossen van problemen met Azure-reserveringen die geen of nul (0) gebruik in de Azure Portal weergeven. Het gebruik dat niet overeenkomt, wordt ook uitgelegd.

## <a name="symptoms"></a>Symptomen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar **Reserveringen** .
1. Bekijk in de lijst met reserveringen de hoeveelheid gebruik voor een reservering in de kolom **Gebruik (%)** . Dit kan 0% zijn.
1. Selecteer de reservering.
1. Op de overzichtspagina van de reservering komt uw gebruikte percentage in de grafiek mogelijk niet overeen met de waarde die wordt weergegeven in de lijst met reserveringen.

## <a name="cause"></a>Oorzaak

In de kolom **Gebruik (%)** in de Azure Portal wordt de waarde voor de huidige dag weergegeven. De waarde wordt berekend op het moment dat de gebruiksgegevens binnenkomen vanaf de plek waar resources worden uitgevoerd. Azure gebruikt het gebruik om het gebruikspercentage te berekenen.

Sommige resources rapporteren het gebruik langzamer dan andere. Daarnaast zijn sommige producttypen zoals SQL-databases langzaam bij het rapporteren van hun gebruiksgegevens.

De latentie kan ertoe leiden dat de berekening van het gebruik lagere waarden weergeeft dan het werkelijke gebruik. Het verschil is merkbaar op de daggrens. Als Azure geen gebruiksgegevens van vier tot acht uur ophaalt, wordt in dat geval de waarde 0% berekend. De waarde van 0% wordt weergegeven omdat er geen gebruiksgegevens zijn ontvangen en het lijkt erop dat de reservering geen voordeel toepast op de resources.

Wanneer de gebruiksgegevens binnenkomen, wordt de waarde gewijzigd in het juiste percentage. Wanneer alle gebruiksgegevens zijn verzameld, wordt de juiste waarde bepaald en nauwkeurig weergegeven in de grafiek.

## <a name="solution"></a>Oplossing

Als u merkt dat uw gebruikswaarden niet overeenkomen met uw verwachtingen, bekijkt u de grafiek om de beste weergave van het werkelijke gebruik te krijgen. Puntwaarden die ouder zijn dan twee dagen moeten nauwkeurig zijn. Gemiddelden over een langere periode van zeven tot 30 dagen moeten nauwkeurig zijn.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [Reserveringen voor Azure-resources beheren](manage-reserved-vm-instance.md) voor meer informatie over het beheren van reserveringen.