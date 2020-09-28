---
title: Voer een upgrade uit voor de Mongo-versie van de API van uw Azure Cosmos DB voor het MongoDB-account
description: De MongoDB wire-protocol versie voor de API van uw bestaande Azure Cosmos DB voor MongoDB-accounts naadloos bijwerken
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
ms.date: 09/22/2020
ms.author: jasonh
ms.openlocfilehash: c6369be39d0a964f07c64083e3269bb1c0c49c7f
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409660"
---
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>Voer een upgrade uit voor de MongoDB wire-protocol versie van de API van uw Azure Cosmos DB voor het MongoDB-account

In dit artikel wordt beschreven hoe u een upgrade kunt uitvoeren van de wire-protocol versie van de API van uw Azure Cosmos DB voor het MongoDB-account. Nadat u de versie van het wire-protocol hebt bijgewerkt, kunt u de nieuwste functionaliteit gebruiken in de API van Azure Cosmos DB voor MongoDB. Het upgrade proces onderbreekt niet de beschik baarheid van uw account en gebruikt geen RU/s of verkleint de capaciteit van de Data Base op elk gewenst moment. Dit proces heeft geen invloed op bestaande gegevens of indexen.

>[!Note]
> Op dit moment kunnen alleen in aanmerking komende accounts met de server versie 3,2 worden bijgewerkt naar versie 3,6. Als uw account de optie upgrade niet weergeeft, moet u [een ondersteunings ticket indienen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-from-version-32-to-36"></a>Upgrade van versie 3,2 naar 3,6

### <a name="benefits-of-upgrading-to-version-36"></a>Voordelen van het uitvoeren van een upgrade naar versie 3.6

Hieronder vindt u de nieuwe functies van versie 3,6:
- Verbeterde prestaties en stabiliteit
- Ondersteuning voor nieuwe database-opdrachten
- Standaard ondersteuning voor aggregatiepijplijn en nieuwe aggregatiefasen
- Ondersteuning voor wijzigings stromen
- Ondersteuning voor samengestelde indexen
- Ondersteuning voor meerdere partities voor de volgende bewerkingen: bijwerken, verwijderen, tellen en sorteren
- Verbeterde prestaties voor de volgende statistische bewerkingen: $count, $skip, $limit en $group
- Het indexeren van joker tekens wordt nu ondersteund

### <a name="changes-from-version-32"></a>Wijzigingen van versie 3,2

- **RequestRateIsLarge-fouten zijn verwijderd**. Aanvragen van de client toepassing zullen niet meer dan 16500 fouten retour neren. In plaats daarvan worden aanvragen hervat totdat ze zijn voltooid of voldoen aan de time-out.
- Per time-out van aanvraag is ingesteld op 60 seconden.
- MongoDB-verzamelingen die zijn gemaakt voor de nieuwe Wire-protocol versie, hebben standaard alleen de `_id` eigenschap geïndexeerd.

### <a name="action-required"></a>Vereiste actie

Voor de upgrade naar versie 3,6 wordt het eindpunt achtervoegsel van het database account bijgewerkt naar de volgende indeling:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

U moet het bestaande eind punt vervangen in uw toepassingen en stuur Programma's die verbinding maken met dit database account. **Alleen verbindingen die het nieuwe eind punt gebruiken, hebben toegang tot de functies in de MongoDb-versie 3,6**. Het voor gaande eind punt moet het achtervoegsel bevatten `.documents.azure.com` .

>[!Note]
> Dit eind punt kan een kleine verschillen hebben als uw account is gemaakt in een soevereine, overheids of beperkte Azure-Cloud.

### <a name="how-to-upgrade"></a>Een upgrade uitvoeren

1. Ga eerst naar de Azure Portal en navigeer naar uw Azure Cosmos DB-API voor de Blade overzicht van MongoDB-account. Controleer of de server versie `3.2` . 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Overzicht van Azure Portal met MongoDB-account" border="false":::

2. Selecteer de Blade in de opties aan de linkerkant `Features` . Hiermee worden de functies van het account niveau onthuld die beschikbaar zijn voor uw database account.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Azure Portal met MongoDB-account overzicht met de Blade functies gemarkeerd" border="false":::

3. Klik op de `Upgrade to Mongo server version 3.6` rij. Als u deze optie niet ziet, komt uw account mogelijk niet in aanmerking voor deze upgrade. Als dit het geval is, moet u [een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) indienen.

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="De Blade functies met opties." border="false":::

4. Bekijk de informatie die wordt weer gegeven over deze specifieke upgrade. Houd er rekening mee dat de upgrade pas wordt voltooid als uw toepassingen het bijgewerkte eind punt gebruiken, zoals in deze sectie is gemarkeerd. Klik op aan zodra `Enable` u klaar bent om het proces te starten.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Uitgebreide upgrade richtlijnen." border="false":::

5. Nadat het proces is gestart, `Features` wordt in het menu de status van de upgrade weer gegeven. De status gaat van `Pending` naar naar `In Progress` `Upgraded` . Dit proces heeft geen invloed op de bestaande functionaliteit of bewerkingen van het database account.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="Upgrade status na het initiëren." border="false":::

6. Zodra de upgrade is voltooid, wordt de status weer gegeven als `Upgraded` . Klik hierop om meer te weten te komen over de volgende stappen en acties die u moet uitvoeren om het proces te volt ooien. [Neem contact op met de ondersteuning](https://azure.microsoft.com/en-us/support/create-ticket/) als er een probleem is opgetreden bij het verwerken van uw aanvraag.

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="Bijgewerkte account status." border="false":::

7. **Als u de bijgewerkte versie van uw database account wilt gaan gebruiken**, gaat u terug naar de `Overview` Blade en kopieert u de nieuwe connection string om in uw toepassing te gebruiken. De toepassingen gaan gebruikmaken van de bijgewerkte versie zodra deze verbinding maken met het nieuwe eind punt. Bestaande verbindingen worden niet onderbroken en kunnen op uw gemak worden bijgewerkt. Voor een consistente ervaring moeten al uw toepassingen het nieuwe eind punt gebruiken.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Nieuwe blade overzicht." border="false":::

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de ondersteunde en niet-ondersteunde [functies van MongoDb versie 3,6](mongodb-feature-support-36.md).
- Zie [Mongo-functies van versie 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/) voor meer informatie
