---
title: Ontvangsten-formulier herkenning
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot de ontvangstbewijs analyse met de formulier Recognizer API-gebruik en limieten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 7a14b3c93a6c545648faf28c991764e990e487b1
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88724968"
---
# <a name="receipt-concepts"></a>Ontvangstbewijs concepten

AzureForm Recognizer kan ontvangst bewijzen analyseren met behulp van een van de vooraf gemaakte modellen. De ontvangst-API extraheert belang rijke informatie uit verkoop ontvangsten in het Engels, zoals de naam van de verkoper, de transactie datum, het transactie totaal, de regel items en meer. 

## <a name="understanding-receipts"></a>Over ontvangst bevestigingen 

Veel bedrijven en personen vertrouwen nog steeds op hand matige wijze van uitpakken van gegevens van hun verkoop ontvangsten, of voor zakelijke onkosten rapporten, vergoedingen, belasting doeleinden, budget tering of andere doel einden. Vaak zijn er in deze scenario's installatie kopieën van de fysieke ontvangst vereist voor validatie doeleinden.  

Het automatisch extra heren van gegevens van deze Ontvangstsen kan gecompliceerd zijn. Ontvangst bewijzen kunnen crumpled en moeilijk te lezen zijn, en smartphone-installatie kopieën van bevestigingen kunnen een lage kwaliteit hebben. Bovendien kunnen ontvangst sjablonen en-velden aanzienlijk verschillen per markt, regio en handelaar. Deze uitdagingen in zowel gegevens extractie als veld detectie maken ontvangst verwerking een uniek probleem.  

Met behulp van optische teken herkenning (OCR) en ons vooraf gegenereerde ontvangst model maakt de ontvangst-API gebruik van deze scenario's voor ontvangst verwerking. Omdat het model vooraf is getraind op onze gegevens, kunt u uw bevestigingen eenvoudig analyseren in één stap &mdash; geen model training of labels vereist.

![voorbeeld bevestiging](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>Wat doet de ontvangst-API? 

Met de vooraf samengestelde ontvangstbewijs-API wordt de inhoud van de verkoop ontvangsten uitgepakt op basis van &mdash; het type ontvangst bewijs dat u vaak bij een restaurant, een detail handelaar of een boodschappen winkel ontvangt.

### <a name="fields-extracted"></a>Geëxtraheerde velden

* Naam van handelaar 
* Bedrijfs adres 
* Telefoon nummer van handelaar 
* Transactie datum 
* Transactie tijd 
* Subtotaal 
* Btw 
* Totaal 
* Tip 
* Extractie van regel items (bijvoorbeeld artikel aantal, artikel prijs, artikel naam)

### <a name="additional-features"></a>Aanvullende functies

De kwitantie-API retourneert ook de volgende informatie:

* Ontvangstbewijs type (zoals gespecificeerd, Credit Card, enzovoort)
* Betrouwbaarheids niveau van veld (elk veld retourneert een bijbehorende betrouwbaarheids waarde)
* OCR-onbewerkte tekst (OCR: geëxtraheerde tekst uitvoer voor de volledige ontvangst)

## <a name="input-requirements"></a>Vereisten voor invoer

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Ondersteunde land instellingen 

* **Vooraf gemaakte ontvangst versie 2.0** (ga) ondersteunt verkoop ontvangsten in de land instelling en-US
* **Vooraf gemaakte bevestigingen v 2.1-Preview. 1** (open bare preview) voegt extra ondersteuning toe voor de volgende land instellingen en ontvangstbewijs: 
  * EN-AU 
  * EN-CA 
  * EN-GB 
  * EN-IN 

  > [!NOTE]
  > Taal invoer 
  >
  > Vooraf ontwikkelde ontvangst versie 2.1-Preview. 1 bevat een optionele aanvraag parameter voor het opgeven van een land instelling voor de ontvangst van extra Engelse markten. Voor verkoop ontvangsten in het Engels vanuit Australië (EN-AU), Canada (EN-CA), Groot-Brittannië (en-GB) en India (EN-IN) kunt u de land instelling opgeven om de resultaten te verbeteren. Als er geen land instelling is opgegeven in v 2.1-Preview. 1, wordt het model standaard ingesteld op het model nl-nl.

## <a name="customer-scenarios"></a>Klant scenario's  

De gegevens die zijn geëxtraheerd met de ontvangstbewijs-API kunnen worden gebruikt voor het uitvoeren van verschillende taken. Hier volgen enkele voor beelden van wat onze klanten hebben gedaan met behulp van de kwitantie-API. 

### <a name="business-expense-reporting"></a>Rapportage van zakelijke onkosten  

Vaak worden bedrijfs kosten in mindering gebracht op basis van de uitgaven voor het hand matig invoeren van gegevens uit installatie kopieën van kwitanties. Met de kassabon-API kunt u de geëxtraheerde velden gebruiken om dit proces gedeeltelijk te automatiseren en uw ontvangsten snel te analyseren.  

Omdat de bevestigings-API een eenvoudige JSON-uitvoer heeft, kunt u de geëxtraheerde veld waarden op verschillende manieren gebruiken. Integreer met interne onkosten toepassingen om onkosten rapporten vooraf in te vullen. Lees voor meer informatie over hoe Acumatica gebruikmaakt van de ontvangstbewijs-API om [onkosten rapportage een minder gemeld proces te maken](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure).  

### <a name="auditing-and-accounting"></a>Controle en accounting 

De ontvangst API-uitvoer kan ook worden gebruikt om analyses uit te voeren op verschillende punten in het onkosten rapportage-en vergoedings proces. U kunt ontvangst bewijzen verwerken om ze te sorteren voor hand matige controle of snelle goed keuringen.  

De ontvangst-uitvoer is ook nuttig voor algemeen gebruik. Gebruik de kassabon-API om alle onbewerkte bewijzen/PDF-gegevens te transformeren naar een digitale uitvoer die actie kan uitvoeren.

### <a name="consumer-behavior"></a>Consumenten gedrag 

Bevestigingen bevatten nuttige gegevens die u kunt gebruiken voor het analyseren van het gedrag van consumenten en het kopen van trends.

