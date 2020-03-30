---
title: Aangepaste velden in Azure Monitor (Preview) | Microsoft Documenten
description: Met de functie Aangepaste velden van Azure Monitor u uw eigen doorzoekbare velden maken uit records in een Werkruimte Log Analytics die worden toegevoegd aan de eigenschappen van een verzamelde record.  In dit artikel wordt het proces beschreven om een aangepast veld te maken en wordt een gedetailleerde walkthrough met een voorbeeldgebeurtenis beschreven.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: bfb0a73631564c96a4af745fe9d7540a3a84f9c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655358"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>Aangepaste velden maken in een werkruimte Logboekanalyse in Azure Monitor (voorbeeld)

> [!NOTE]
> In dit artikel wordt beschreven hoe u tekstgegevens ontkoppelen in een log analytics-werkruimte die wordt verzameld. We raden u aan tekstgegevens in een queryfilter te ontwijn nadat deze zijn verzameld volgens de richtlijnen die zijn beschreven in [Parse-tekstgegevens in Azure Monitor.](../log-query/parse-text.md) Het biedt verschillende voordelen ten opzichte van het gebruik van aangepaste velden.

Met de functie **Aangepaste velden** van Azure Monitor u bestaande records in uw Log Analytics-werkruimte uitbreiden door uw eigen doorzoekbare velden toe te voegen.  Aangepaste velden worden automatisch ingevuld uit gegevens die zijn geëxtraheerd uit andere eigenschappen in dezelfde record.

![Overzicht](media/custom-fields/overview.png)

In de onderstaande voorbeeldrecord worden bijvoorbeeld nuttige gegevens begraven in de beschrijving van de gebeurtenis. Als u deze gegevens in een afzonderlijke eigenschap haalt, is deze beschikbaar voor acties zoals sorteren en filteren.

![Monsterextract](media/custom-fields/sample-extract.png)

> [!NOTE]
> In de preview bent u beperkt tot 100 aangepaste velden in uw werkruimte.  Deze limiet wordt uitgebreid wanneer deze functie de algemene beschikbaarheid bereikt.

## <a name="creating-a-custom-field"></a>Een aangepast veld maken
Wanneer u een aangepast veld maakt, moet Log Analytics begrijpen welke gegevens moeten worden gebruikt om de waarde ervan in te vullen.  Het maakt gebruik van een technologie van Microsoft Research genaamd FlashExtract om snel te identificeren deze gegevens.  In plaats van dat u expliciete instructies hoeft te geven, leert Azure Monitor over de gegevens die u wilt extraheren uit voorbeelden die u verstrekt.

De volgende secties bieden de procedure voor het maken van een aangepast veld.  Aan de onderkant van dit artikel is een walkthrough van een monster extractie.

> [!NOTE]
> Het aangepaste veld wordt ingevuld omdat records die overeenkomen met de opgegeven criteria worden toegevoegd aan de werkruimte Log Analytics, zodat het alleen wordt weergegeven op records die zijn verzameld nadat het aangepaste veld is gemaakt.  Het aangepaste veld wordt niet toegevoegd aan records die zich al in het gegevensarchief bevinden wanneer het wordt gemaakt.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Stap 1 – Records identificeren met het aangepaste veld
De eerste stap is het identificeren van de records die het aangepaste veld krijgen.  U begint met een [standaardlogboekquery](../log-query/log-query-overview.md) en selecteert vervolgens een record om te fungeren als het model waarvan Azure Monitor zal leren.  Wanneer u opgeeft dat u gegevens naar een aangepast veld gaat extraheren, wordt de **wizard Veldextractie** geopend waar u de criteria valideert en verfijnt.

1. Ga naar **Logboeken** en gebruik een [query om de records op te halen](../log-query/log-query-overview.md) die het aangepaste veld hebben.
2. Selecteer een record die Log Analytics gebruikt om als model te fungeren voor het extraheren van gegevens om het aangepaste veld te vullen.  U identificeert de gegevens die u uit deze record wilt halen en Log Analytics gebruikt deze informatie om de logica te bepalen om het aangepaste veld voor alle vergelijkbare records in te vullen.
3. Vouw de recordeigenschappen uit, klik op de ellips links van de bovenste eigenschap van de record en selecteer **Velden extraheren uit**.
4. De **wizard Veldextractie** wordt geopend en de geselecteerde record wordt weergegeven in de kolom **Hoofdvoorbeeld.**  Het aangepaste veld wordt gedefinieerd voor records met dezelfde waarden in de geselecteerde eigenschappen.  
5. Als de selectie niet precies is wat u wilt, selecteert u extra velden om de criteria te beperken.  Als u de veldwaarden voor de criteria wilt wijzigen, moet u een andere record annuleren en selecteren die overeenkomt met de gewenste criteria.

### <a name="step-2---perform-initial-extract"></a>Stap 2 - Eerste extract uitvoeren.
Zodra u de records hebt geïdentificeerd die het aangepaste veld hebben, identificeert u de gegevens die u wilt extraheren.  Log Analytics gebruikt deze informatie om vergelijkbare patronen in vergelijkbare records te identificeren.  In de stap hierna u de resultaten valideren en meer details geven voor Log Analytics om te gebruiken in de analyse.

1. Markeer de tekst in de voorbeeldrecord die u het aangepaste veld wilt invullen.  U krijgt dan een dialoogvenster te zien om een naam en gegevenstype voor het veld op te geven en het eerste uittreksel uit te voeren.  De ** \_** tekens CF worden automatisch toegevoegd.
2. Klik **op Extraheren** om een analyse van verzamelde records uit te voeren.  
3. In de secties **Samenvatting** en **zoekresultaten** worden de resultaten van het uittreksel weergegeven, zodat u de nauwkeurigheid ervan controleren.  **In overzicht** worden de criteria weergegeven die worden gebruikt om records te identificeren en een telling voor elk van de geïdentificeerde gegevenswaarden.  **Zoekresultaten** bevat een gedetailleerde lijst met records die overeenkomen met de criteria.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Stap 3 – Controleer de nauwkeurigheid van het extract en maak het aangepaste veld
Zodra u het eerste uittreksel hebt uitgevoerd, geeft Log Analytics de resultaten weer op basis van gegevens die al zijn verzameld.  Als de resultaten er nauwkeurig uitzien, u het aangepaste veld maken zonder verder werk.  Zo niet, dan u de resultaten verfijnen, zodat Log Analytics de logica ervan kan verbeteren.

1. Als waarden in het oorspronkelijke extract niet correct zijn, klikt u op het pictogram **Bewerken** naast een onnauwkeurige record en selecteert u **Deze markering wijzigen** om de selectie te wijzigen.
2. De vermelding wordt gekopieerd naar de sectie **Aanvullende voorbeelden** onder het **hoofdvoorbeeld**.  U het hoogtepunt hier aanpassen om Log Analytics inzicht te geven in de selectie die het had moeten maken.
3. Klik **op Extraheren** om deze nieuwe informatie te gebruiken om alle bestaande records te evalueren.  De resultaten kunnen worden gewijzigd voor andere records dan degene die u zojuist hebt gewijzigd op basis van deze nieuwe intelligentie.
4. Blijf correcties toevoegen totdat alle records in het extract de gegevens correct identificeren om het nieuwe aangepaste veld in te vullen.
5. Klik **op Extraslaan opslaan** wanneer u tevreden bent met de resultaten.  Het aangepaste veld is nu gedefinieerd, maar het wordt nog niet aan records toegevoegd.
6. Wacht tot nieuwe records overeenkomen met de opgegeven criteria die moeten worden verzameld en voer de logboekzoekopdracht opnieuw uit. Nieuwe records moeten het aangepaste veld hebben.
7. Gebruik het aangepaste veld zoals elke andere recordeigenschap.  U het gebruiken om gegevens te verzamelen en te groeperen en deze zelfs te gebruiken om nieuwe inzichten te produceren.

## <a name="viewing-custom-fields"></a>Aangepaste velden weergeven
U een lijst met alle aangepaste velden in uw beheergroep weergeven in het menu **Geavanceerde instellingen** van uw Log Analytics-werkruimte in de Azure-portal.  Selecteer **Gegevens** en vervolgens **Aangepaste velden** voor een lijst met alle aangepaste velden in uw werkruimte.  

![Aangepaste velden](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Een aangepast veld verwijderen
Er zijn twee manieren om een aangepast veld te verwijderen.  De eerste is de optie **Verwijderen** voor elk veld bij het bekijken van de volledige lijst zoals hierboven beschreven.  De andere methode is om een record op te halen en op de knop links van het veld te klikken.  Het menu heeft een optie om het aangepaste veld te verwijderen.

## <a name="sample-walkthrough"></a>Walkthrough voor voorbeeld
In de volgende sectie wordt een compleet voorbeeld van het maken van een aangepast veld doorlopen.  In dit voorbeeld wordt de servicenaam in Windows-gebeurtenissen geëxtraheerd die een status voor het wijzigen van de service aangeven.  Dit is afhankelijk van gebeurtenissen die zijn gemaakt door Service Control Manager tijdens het opstarten van het systeem op Windows-computers.  Als u dit voorbeeld wilt volgen, moet u [informatiegebeurtenissen verzamelen voor het systeemlogboek.](data-sources-windows-events.md)

We voeren de volgende query in om alle gebeurtenissen van Service Control Manager met een gebeurtenis-id van 7036 te retourneren, de gebeurtenis die aangeeft dat een service is gestart of gestopt.

![Query’s uitvoeren](media/custom-fields/query.png)

Vervolgens selecteren en uitbreiden we elke record met gebeurtenis-id 7036.

![Bronrecord](media/custom-fields/source-record.png)

We definiëren aangepaste velden door op de ellips naast de bovenste eigenschap te klikken.

![Velden extraheren](media/custom-fields/extract-fields.png)

De **wizard Veldextractie** wordt geopend en de velden **EventLog** en **EventID** zijn geselecteerd in de kolom **Hoofdvoorbeeld.**  Dit geeft aan dat het aangepaste veld wordt gedefinieerd voor gebeurtenissen uit het systeemlogboek met een gebeurtenis-id van 7036.  Dit is voldoende, zodat we geen andere velden hoeven te selecteren.

![Hoofdvoorbeeld](media/custom-fields/main-example.png)

We markeren de naam van de service in de eigenschap **RenderedDescription** en gebruiken **Service** om de servicenaam te identificeren.  Het aangepaste veld wordt **Service_CF**genoemd. Het veldtype is in dit geval een tekenreeks, dus dat kunnen we ongewijzigd laten.

![Veldtitel](media/custom-fields/field-title.png)

We zien dat de servicenaam goed is geïdentificeerd voor sommige records, maar niet voor anderen.   **In de zoekresultaten** wordt weergegeven dat een deel van de naam voor de **WMI-prestatieadapter** niet is geselecteerd.  De **samenvatting** toont aan dat een record geïdentificeerd **Modules Installer** in plaats van Windows Modules **Installer**.  

![Zoekresultaten](media/custom-fields/search-results-01.png)

We beginnen met de **WMI Performance** Adapter-record.  We klikken op het bewerkingspictogram en **wijzigen dit hooglicht**.  

![Markering wijzigen](media/custom-fields/modify-highlight.png)

We verhogen het hooglicht om het woord **WMI** op te nemen en vervolgens het extract opnieuw uit te voeren.  

![Extra voorbeeld](media/custom-fields/additional-example-01.png)

We kunnen zien dat de vermeldingen voor **WMI Performance Adapter** zijn gecorrigeerd en Log Analytics heeft die informatie ook gebruikt om de records voor **Windows Module Installer**te corrigeren.

![Zoekresultaten](media/custom-fields/search-results-02.png)

We kunnen nu een query uitvoeren die controleert **Service_CF** is gemaakt, maar nog niet is toegevoegd aan records. Dat komt omdat het aangepaste veld niet werkt tegen bestaande records, dus we moeten wachten tot er nieuwe records worden verzameld.

![Eerste telling](media/custom-fields/initial-count.png)

Nadat er enige tijd is verstreken, zodat nieuwe gebeurtenissen worden verzameld, kunnen we zien dat het **Service_CF** veld nu wordt toegevoegd aan records die aan onze criteria voldoen.

![Definitieve resultaten](media/custom-fields/final-results.png)

We kunnen nu het aangepaste veld gebruiken zoals elke andere recordeigenschap.  Om dit te illustreren, maken we een query die wordt groepeert door het nieuwe **Service_CF** veld om te controleren welke services het meest actief zijn.

![Groeperen op query](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboekquery's](../log-query/log-query-overview.md) om query's te maken met aangepaste velden voor criteria.
* Controleer [aangepaste logboekbestanden](data-sources-custom-logs.md) die u onteert met aangepaste velden.

