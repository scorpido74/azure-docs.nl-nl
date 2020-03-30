---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175786"
---
**1. Hoe worden klanten op de hoogte gesteld van de sdk met pensioen?**

Microsoft zal 12 maanden van tevoren kennisgeving aan het einde van de ondersteuning van de gepensioneerde SDK om een soepele overgang naar een ondersteunde SDK te vergemakkelijken. Verder worden klanten op de hoogte gebracht via verschillende communicatiekanalen: Azure Management Portal, Developer Center, blogpost en directe communicatie met toegewezen servicebeheerders.

**2. Kunnen klanten gedurende de periode van 12 maanden toepassingen maken met behulp van een "to-be" gepensioneerde Azure Cosmos DB SDK?** 

Ja, klanten hebben volledige toegang tot het schrijven, implementeren en wijzigen van toepassingen met behulp van de 'to-be' gepensioneerde Azure Cosmos DB SDK tijdens de respijtperiode van 12 maanden. Tijdens de respijtperiode van 12 maanden wordt klanten aangeraden om te migreren naar een nieuwere ondersteunde versie van Azure Cosmos DB SDK.

**3. Kunnen klanten toepassingen schrijven en wijzigen met behulp van een gepensioneerde Azure Cosmos DB SDK na de kennisgevingsperiode van 12 maanden?**

Na de aanmeldingsperiode van 12 maanden wordt de SDK buiten gebruik gesteld. Toegang tot Azure Cosmos DB door een toepassing met een gepensioneerde SDK is niet toegestaan door het Azure Cosmos DB-platform. Verder biedt Microsoft geen klantenondersteuning op de gepensioneerde SDK.

**4. Wat gebeurt er met de toepassingen die de klant uitvoert en die de niet-ondersteunde Azure Cosmos DB SDK-versie gebruiken?**

Alle pogingen die zijn gedaan om verbinding te maken met de Azure Cosmos DB-service met een gepensioneerde SDK-versie, worden afgewezen. 

**5. Worden nieuwe functies en functionaliteit toegepast op alle niet-gepensioneerde SDK's?**

Nieuwe functies en functionaliteit worden alleen toegevoegd aan nieuwe versies. Als u een oude, niet-gepensioneerde versie van de SDK gebruikt, werken uw aanvragen voor Azure Cosmos DB nog steeds als vorige, maar hebt u geen toegang tot nieuwe mogelijkheden.  

**6. Wat moet ik doen als ik mijn aanvraag niet kan bijwerken voor een cut-off datum?**

Wij raden u aan om zo vroeg mogelijk te upgraden naar de nieuwste SDK. Zodra een SDK is getagd voor uw pensioen, heeft u 12 maanden de tijd om uw aanvraag bij te werken. Als u om welke reden dan ook uw aanvraagupdate niet binnen deze termijn voltooien, neem dan contact op met het [Cosmos DB Team](mailto:askcosmosdb@microsoft.com) en vraag hun hulp vóór de cutoff-datum.

