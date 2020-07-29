---
title: Problemen met Azure Cosmos DB niet-gemachtigde uitzonde ring oplossen
description: Een uitzonde ring voor onbevoegden vaststellen en oplossen
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 872780ff65dabe5a931ae712c0f99b6e4f3e726d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294103"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exception"></a>Azure Cosmos DB niet-gemachtigde uitzonde ring vaststellen en problemen oplossen

HTTP 401: de MAC-hand tekening die in de HTTP-aanvraag is gevonden, is niet hetzelfde als de berekende hand tekening.
Als u het volgende 401-foutbericht hebt ontvangen: 'De MAC-handtekening die in de HTTP-aanvraag is gevonden, is niet hetzelfde als de berekende handtekening.' Dit kan worden veroorzaakt door de volgende scenario's.

Oudere Sdk's de uitzonde ring kan worden weer gegeven als een ongeldige JSON-uitzonde ring in plaats van de juiste 401-niet-geautoriseerde uitsluiting. Nieuwere Sdk's verwerken dit scenario goed en geven een geldig fout bericht.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De volgende lijst bevat bekende oorzaken en oplossingen voor ongeautoriseerde uitzonde ringen.

### <a name="1-key-was-not-properly-rotated-is-the-most-common-scenario"></a>1. de sleutel is niet goed gedraaid. Dit is het meest voorkomende scenario.
De 401 MAC-handtekening wordt kort na het omwisselen van een sleutel weergegeven en stopt uiteindelijk zonder wijzigingen. 

#### <a name="solution"></a>Oplossing:
De sleutel is omgewisseld en voldoet niet aan de [best practices](secure-access-to-data.md#key-rotation). De Cosmos DB-account sleutel rotatie kan enkele seconden tot een aantal dagen duren, afhankelijk van de grootte van het Cosmos DB-account.

### <a name="2-the-key-is-misconfigured"></a>2. de sleutel is onjuist geconfigureerd 
401 MAC-handtekening probleem is consistent en gebeurt voor alle aanroepen die deze sleutel gebruiken

#### <a name="solution"></a>Oplossing:
De sleutel is onjuist geconfigureerd op de toepassing en gebruikt de verkeerde sleutel voor het account of de volledige sleutel is niet gekopieerd.

### <a name="3-the-application-is-using-the-read-only-keys-for-write-operations"></a>3. de toepassing gebruikt de alleen-lezen sleutels voor schrijf bewerkingen
401 MAC-handtekening probleem treedt alleen op bij schrijf bewerkingen als maken of vervangen, maar lees aanvraag is gelukt.

#### <a name="solution"></a>Oplossing:
Schakel de toepassing in om een lees-/schrijfmachtigingen te gebruiken zodat de bewerkingen kunnen worden voltooid.

### <a name="4-race-condition-with-create-container"></a>4. race condition met Create container
401 MAC-handtekening probleem wordt kort na het maken van een container weer gegeven. Dit gebeurt pas nadat het maken van de container is voltooid.

#### <a name="solution"></a>Oplossing:
Er is een racevoorwaarde bij het maken van een container. Een exemplaar van de toepassing probeert toegang te krijgen tot de container voordat het maken van de container is voltooid. Het meest voorkomende scenario voor deze race voorwaarde is als de toepassing wordt uitgevoerd, en de container wordt verwijderd en opnieuw gemaakt met dezelfde naam. De SDK probeert de nieuwe container te gebruiken, maar de container wordt nog gemaakt, waardoor deze de sleutels niet bevat.

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) bij het gebruik van Azure Cosmos db .NET SDK
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md)