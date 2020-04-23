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
**1. Hoe worden klanten op de hoogte gesteld van de buiten gebruik stellen van de SDK?**

Micro soft biedt een voorafgaande melding van 12 maanden aan het eind van de ondersteuning van de herstelde SDK, zodat een soepele overgang naar een ondersteunde SDK kan worden vergemakkelijkt. Verder worden klanten op de hoogte gesteld via verschillende communicatie kanalen: Azure Beheerportal, Developer Center, blog post en directe communicatie met toegewezen service beheerders.

**2. klanten kunnen in de periode van twaalf maanden toepassingen met behulp van een ' te Azure Cosmos DB SDK ' buiten gebruik stellen?** 

Ja, klanten hebben volledige toegang tot het schrijven, implementeren en aanpassen van toepassingen met behulp van de ' to-to-' buiten gebruik gestelde Azure Cosmos DB SDK tijdens de respijt periode van 12 maanden. Tijdens de evaluatie periode van twaalf maanden wordt klanten geadviseerd om naar wens naar een nieuwere, ondersteunde versie van Azure Cosmos DB SDK te migreren.

**3. kunnen klanten toepassingen ontwerpen en wijzigen met behulp van een buiten gebruik gestelde Azure Cosmos DB SDK na de meldings periode van 12 maanden?**

Na de meldings periode van 12 maanden wordt de SDK buiten gebruik gesteld. Toegang tot Azure Cosmos DB door een toepassing met behulp van een buiten gebruik gestelde SDK wordt niet toegestaan door het Azure Cosmos DB platform. Verder biedt micro soft geen klanten ondersteuning voor de buiten gebruik gestelde SDK.

**4. Wat gebeurt er met de toepassingen van de klant die niet-ondersteunde Azure Cosmos DB SDK-versie gebruiken?**

Pogingen om verbinding te maken met de Azure Cosmos DB-service met een buiten gebruik gestelde SDK-versie, worden afgewezen. 

**5. worden de nieuwe functies en functionaliteit toegepast op alle niet-ingetrokken Sdk's?**

Nieuwe functies en functionaliteit worden alleen toegevoegd aan nieuwe versies. Als u een oude, niet-ingetrokken versie van de SDK gebruikt, zullen uw aanvragen voor Azure Cosmos DB nog steeds werken als vorige, maar hebt u geen toegang tot nieuwe mogelijkheden.  

**6. Wat moet ik doen als ik mijn toepassing niet kan bijwerken vóór een afbreek datum?**

U wordt aangeraden zo snel mogelijk een upgrade naar de nieuwste SDK uit te voeren. Zodra een SDK is gelabeld voor het buiten gebruik stellen, hebt u 12 maanden de toepassing bij te werken. Als u om welke reden dan ook uw toepassings update binnen deze periode niet kunt volt ooien, neemt u contact op met het [Cosmos DB team](mailto:askcosmosdb@microsoft.com) en vraagt u om hulp voor de afsluit datum.

