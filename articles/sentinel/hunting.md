---
title: Jachtmogelijkheden in Azure Sentinel| Microsoft Documenten
description: In dit artikel wordt beschreven hoe u de Azure Sentinel-jachtmogelijkheden gebruiken.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: yelevin
ms.openlocfilehash: 54ddf6818b95a4037188ab222501ddfa69b28149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587895"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Zoek naar bedreigingen met Azure Sentinel

Als u een onderzoeker bent die proactief op zoek wil naar beveiligingsbedreigingen, jaagt Azure Sentinel met krachtige zoek- en querytools om te zoeken naar beveiligingsbedreigingen in de gegevensbronnen van uw organisatie. Maar uw systemen en beveiligingsapparaten genereren bergen gegevens die moeilijk te ontlopen en te filteren in zinvolle gebeurtenissen. Om beveiligingsanalisten te helpen proactief te zoeken naar nieuwe afwijkingen die niet zijn gedetecteerd door uw beveiligingsapps, leiden de ingebouwde jachtquery's van Azure Sentinel u bij het stellen van de juiste vragen om problemen te vinden in de gegevens die u al in uw netwerk hebt. 

Een ingebouwde query bevat bijvoorbeeld gegevens over de meest ongebruikelijke processen die op uw infrastructuur worden uitgevoerd - u wilt niet dat u een waarschuwing wilt over elke keer dat ze worden uitgevoerd, ze kunnen volledig onschuldig zijn, maar u wilt misschien af en toe een kijkje nemen op de query om te zien of Er is iets ongewoons. 



Met Azure Sentinel-jacht u profiteren van de volgende mogelijkheden:

- Ingebouwde query's: Om u op weg te helpen, bevat een startpagina vooraf geladen queryvoorbeelden die zijn ontworpen om u op weg te helpen en u vertrouwd te maken met de tabellen en de querytaal. Deze ingebouwde jacht query's zijn ontwikkeld door Microsoft security onderzoekers op een continue basis, het toevoegen van nieuwe query's, en fine-tuning bestaande query's om u te voorzien van een ingang om te zoeken naar nieuwe detecties en erachter te komen waar te beginnen met de jacht op de nieuwe aanvallen. 

- Krachtige querytaal met IntelliSense: gebouwd bovenop een querytaal die u de flexibiliteit geeft die u nodig hebt om de jacht naar een hoger niveau te tillen.

- Maak je eigen bladwijzers: tijdens het jachtproces kun je overeenkomsten of bevindingen, dashboards of activiteiten tegenkomen die er ongebruikelijk of verdacht uitzien. Gebruik de bladwijzerfunctionaliteit om deze items te markeren, zodat u er in de toekomst op terug komen. Met bladwijzers u items opslaan voor later, om een incident te maken voor onderzoek. Zie [Bladwijzers gebruiken bij de jacht](hunting.md)voor meer informatie over bladwijzers.
- Gebruik notitieblokken om onderzoek te automatiseren: notitieblokken zijn als stapsgewijze playbooks die u bouwen om de stappen van een onderzoek en jacht te doorlopen.  Notitieblokken bevatten alle jachtstappen in een herbruikbaar draaiboek dat kan worden gedeeld met anderen in uw organisatie. 
- De opgeslagen gegevens opvragen: de gegevens zijn toegankelijk in tabellen die u opvragen. U bijvoorbeeld procescreatie, DNS-gebeurtenissen en vele andere gebeurtenistypen opvragen.

- Links naar de community: maak gebruik van de kracht van de grotere gemeenschap om aanvullende query's en gegevensbronnen te vinden.
 
## <a name="get-started-hunting"></a>Aan de slag met jagen

1. Klik in de Azure Sentinel-portal op **Jacht**.
  ![Azure Sentinel begint met jagen](media/tutorial-hunting/hunting-start.png)

2. Wanneer u de **jachtpagina opent,** worden alle jachtquery's weergegeven in één tabel. De tabel bevat alle query's die zijn geschreven door het team van beveiligingsanalisten van Microsoft, evenals eventuele aanvullende query's die u hebt gemaakt of gewijzigd. Elke query geeft een beschrijving van wat het jaagt voor, en wat voor soort gegevens het draait op. Deze sjablonen zijn gegroepeerd door hun verschillende tactieken - de pictogrammen aan de rechterkant categoriseren het type bedreiging, zoals initiële toegang, persistentie en exfiltratie. U deze jachtquerysjablonen filteren met een van de velden. U elke query opslaan in uw favorieten. Door een query op te slaan op uw favorieten, wordt de query automatisch uitgevoerd telkens wanneer de **jachtpagina** wordt geopend. U uw eigen jachtquery of kloon maken en een bestaande sjabloon voor jachtquery's aanpassen. 
 
2. Klik **op Query uitvoeren** op de pagina met details van de jachtquery om een query uit te voeren zonder de jachtpagina te verlaten.  Het aantal overeenkomsten wordt weergegeven in de tabel. Bekijk de lijst met jachtquery's en hun wedstrijden. Kijk in welke fase de kill chain de match is gekoppeld.

3. Voer een snelle revisie van de onderliggende query uit in het deelvenster querydetails of klik op **Queryresultaat weergeven** om de query in Log Analytics te openen. Bekijk onderaan de overeenkomsten voor de query.

4.  Klik op de rij en selecteer **Bladwijzer toevoegen** om de te onderzoeken rijen toe te voegen - u dit doen voor alles wat er verdacht uitziet. 

5. Ga vervolgens terug naar de hoofdpagina **Hunting** en klik op het tabblad **Bladwijzers** om alle verdachte activiteiten te bekijken. 

6. Selecteer een bladwijzer en klik op **Onderzoeken** om de onderzoekservaring te openen. U de bladwijzers filteren. Als u bijvoorbeeld een campagne onderzoekt, u een tag voor de campagne maken en vervolgens alle bladwijzers filteren op basis van de campagne.

1. Nadat u hebt ontdekt welke jachtquery waardevolle inzichten biedt in mogelijke aanvallen, u ook aangepaste detectieregels maken op basis van uw zoekopdracht en deze inzichten weergeven als waarschuwingen voor uw beveiligingsincidentresponders.

 

## <a name="query-language"></a>Querytaal 

Jagen in Azure Sentinel is gebaseerd op Kusto-querytaal. Zie [Naslaggids](https://docs.loganalytics.io/docs/Language-Reference/)voor querytalen voor meer informatie over de querytaal en ondersteunde operatoren.

## <a name="public-hunting-query-github-repository"></a>Openbare jachtquery GitHub-opslagplaats

Bekijk de [Hunting query repository](https://github.com/Azure/Orion). Bijdragen en gebruik voorbeeldquery's die door onze klanten worden gedeeld.

 

## <a name="sample-query"></a>Voorbeeldquery

Een typische query begint met een tabelnaam, \|gevolgd door een reeks operatoren, gescheiden door .

Begin in het bovenstaande voorbeeld met de tabelnaam SecurityEvent en voeg indien nodig pipeteelementen toe.

1. Definieer een tijdfilter om alleen records van de afgelopen zeven dagen te bekijken.

2. Voeg een filter toe aan de query om alleen gebeurtenis-id 4688 weer te geven.

3. Voeg een filter toe in de query op de CommandLine om alleen exemplaren van cscript.exe te bevatten.

4. Projecteer alleen de kolommen die u wilt verkennen en beperk de resultaten tot 1000 en klik op **Query uitvoeren**.
5. Klik op het groene driehoekje en voer de query uit. U de query testen en uitvoeren om te zoeken naar afwijkend gedrag.

## <a name="useful-operators"></a>Nuttige operatoren

De querytaal is krachtig en heeft veel beschikbare operators, enkele nuttige operators worden hier vermeld:

**waar** - Filter een tabel naar de subset van rijen die voldoen aan een predicaat.

**samenvatten** : maak een tabel die de inhoud van de invoertabel samenvoegt.

**join** - Voeg de rijen van twee tabellen samen om een nieuwe tabel te vormen door waarden van de opgegeven kolom(en) van elke tabel te afstemmen.

**aantal** - Het aantal records in de ingestelde invoerrecord retourneren.

**boven** - Retourneer de eerste N-records gesorteerd op de opgegeven kolommen.

**limit** - Terugnaar het opgegeven aantal rijen.

**project** - Selecteer de kolommen die u wilt opnemen, hernoemen of neerzetten en nieuwe berekende kolommen invoegen.

**uit te breiden** - Maak berekende kolommen en sluit ze toe aan de resultatenset.

**makeset** - Een dynamische (JSON)-array van de set afzonderlijke waarden retourneren die Expr in de groep neemt

**zoeken** - Rijen zoeken die overeenkomen met een predicaat in een reeks tabellen.

## <a name="save-a-query"></a>Een query opslaan

U een query maken of wijzigen en opslaan als uw eigen query of deze delen met gebruikers die zich in dezelfde tenant bevinden.

   ![Query opslaan](./media/tutorial-hunting/save-query.png)

Maak een nieuwe jachtquery:

1. Klik **op Nieuwe query** en selecteer **Opslaan**.
2. Vul alle lege velden in en selecteer **Opslaan**.

   ![Nieuwe query](./media/tutorial-hunting/new-query.png)

Kloon en wijzig een bestaande jachtquery:

1. Selecteer de jachtquery in de tabel die u wilt wijzigen.
2. Selecteer de ellips (...) in de regel van de query die u wilt wijzigen en selecteer **Kloonquery**.

   ![kloonquery](./media/tutorial-hunting/clone-query.png)
 

3. Wijzig de query en selecteer **Maken**.

   ![aangepaste query](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u een jachtonderzoek uitvoeren met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:


- [Notitieblokken gebruiken om geautomatiseerde jachtcampagnes uit te voeren](notebooks.md)
- [Gebruik bladwijzers om interessante informatie op te slaan tijdens de jacht](bookmarks.md)
