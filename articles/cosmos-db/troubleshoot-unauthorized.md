---
title: Problemen met Azure Cosmos DB niet-toegestane uitzonde ringen oplossen
description: Meer informatie over het vaststellen en oplossen van niet-toegestane uitzonde ringen.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a4f51b641ca38b2b6f74bb77928537270d12f1e8
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870847"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>Azure Cosmos DB niet-toegestane uitzonde ringen vaststellen en problemen oplossen

HTTP 401: de MAC-hand tekening die in de HTTP-aanvraag is gevonden, is niet hetzelfde als de berekende hand tekening.
Als het fout bericht 401 de MAC-hand tekening in de HTTP-aanvraag niet gelijk is aan de berekende hand tekening, kan dit worden veroorzaakt door de volgende scenario's.

Voor oudere Sdk's kan de uitzonde ring als een ongeldige JSON-uitzonde ring worden weer gegeven in plaats van de juiste niet-geautoriseerde versie van 401. Nieuwere Sdk's verwerken dit scenario goed en geven een geldig fout bericht.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De volgende lijst bevat bekende oorzaken en oplossingen voor niet-toegestane uitzonde ringen.

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>De sleutel is niet juist gedraaid en is het meest voorkomende scenario
De 401 MAC-hand tekening wordt kort na het draaien van een sleutel weer gegeven en stopt uiteindelijk zonder wijzigingen. 

#### <a name="solution"></a>Oplossing:
De sleutel is gedraaid en voldoet niet aan de [Aanbevolen procedures](secure-access-to-data.md#key-rotation). De Azure Cosmos DB-account sleutel rotatie kan enkele seconden tot een aantal dagen duren, afhankelijk van de grootte van het Azure Cosmos DB-account.

### <a name="the-key-is-misconfigured"></a>De sleutel is onjuist geconfigureerd 
Het probleem met de 401 MAC-hand tekening is consistent en gebeurt voor alle aanroepen die deze sleutel gebruiken.

#### <a name="solution"></a>Oplossing:
De sleutel is onjuist geconfigureerd op de toepassing en gebruikt de verkeerde sleutel voor het account, of de volledige sleutel is niet gekopieerd.

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>De toepassing gebruikt de alleen-lezen sleutels voor schrijf bewerkingen
Het probleem met de 401 MAC-hand tekening vindt alleen plaats voor schrijf bewerkingen zoals maken of vervangen, maar lees aanvragen slagen.

#### <a name="solution"></a>Oplossing:
Schakel de toepassing in om een lees-/schrijfmachtigingen te gebruiken zodat de bewerkingen kunnen worden voltooid.

### <a name="race-condition-with-create-container"></a>Race condition met Create container
Het probleem met de 401 MAC-hand tekening wordt kort weer gegeven nadat een container is gemaakt. Dit probleem treedt alleen op wanneer het maken van de container is voltooid.

#### <a name="solution"></a>Oplossing:
Er is een race voorwaarde bij het maken van een container. Een instantie van de toepassing probeert toegang te krijgen tot de container voordat het maken van de container is voltooid. Het meest voorkomende scenario voor deze race voorwaarde is als de toepassing wordt uitgevoerd en de container wordt verwijderd en opnieuw wordt gemaakt met dezelfde naam. De SDK probeert de nieuwe container te gebruiken, maar het maken van de container wordt nog uitgevoerd, zodat deze de sleutels niet bevat.

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) wanneer u de Azure Cosmos db .NET SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md).