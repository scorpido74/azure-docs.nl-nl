---
title: Azure Storage-services bewaken met Azure Monitor for Storage (voorbeeld)| Microsoft Documenten
description: In dit artikel wordt de functie Azure Monitor for Storage beschreven die opslagbeheerders een snelle kennis geeft van de prestatie- en gebruiksproblemen met hun Azure Storage-accounts.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: f23be7e764ad180a23c76abb7f9bb2218fd61e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662516"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>Uw opslagservice bewaken met Azure Monitor for Storage (voorbeeld)

Azure Monitor for Storage (preview) biedt uitgebreide bewaking van uw Azure Storage-accounts door een uniforme weergave te bieden van de prestaties, capaciteit en beschikbaarheid van uw Azure Storage Services. U de opslagcapaciteit en prestaties op twee manieren bekijken, rechtstreeks vanuit een opslagaccount bekijken of vanuit Azure Monitor bekijken om te zien in verschillende groepen opslagaccounts. 

Dit artikel helpt u inzicht te krijgen in de ervaring die Azure Monitor for Storage (preview) biedt om bruikbare kennis over de status en prestaties van opslagaccounts op schaal af te leiden, met de mogelijkheid om zich te concentreren op hotspots en latentie, beperking, te diagnosticeren, en beschikbaarheidsproblemen.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Inleiding tot Azure Monitor for Storage (voorbeeld)

Voordat u in de ervaring duikt, moet u begrijpen hoe het informatie presenteert en visualiseert. Of u nu de opslagfunctie rechtstreeks selecteert vanuit een opslagaccount of vanuit Azure Monitor, Azure Monitor for Storage biedt een consistente ervaring. 

Gecombineerd levert het:

* **Op schaalperspectief** met een momentopnameweergave van hun beschikbaarheid op basis van de status van de opslagservice of de API-bewerking, het gebruik dat het totale aantal aanvragen weergeeft dat de opslagservice ontvangt en de latentie die de gemiddelde tijd weergeeft die de opslagservice of het type API-bewerking neemt om aanvragen te verwerken. U ook de capaciteit bekijken per blob, bestand, tabel en wachtrij.

* **Boor de analyse** van een bepaald opslagaccount in om problemen te diagnosticeren of gedetailleerde analyses per categorie uit te voeren - beschikbaarheid, prestaties, fouten en capaciteit. Als u een van deze opties selecteert, u een diepgaand overzicht geven van metrische gegevens.  

* **Aanpasbaar** waar u wijzigen welke statistieken u wilt zien, wijzigen of instellen drempels die overeenkomen met uw limieten, en opslaan als uw eigen werkmap. Grafieken in de werkmap kunnen worden vastgemaakt aan het Azure-dashboard.  

Deze functie vereist niet dat u iets in- of configureert, de opslagstatistieken van uw opslagaccounts worden standaard verzameld. Als u niet bekend bent met statistieken die beschikbaar zijn in Azure Storage, bekijkt u de beschrijving en definitie in Azure Storage-statistieken door [azure-opslagstatistieken](../../storage/common/storage-metrics-in-azure-monitor.md)te bekijken.

>[!NOTE]
>Er zijn geen kosten verbonden aan toegang tot deze functie en er worden alleen kosten in rekening gebracht voor de essentiële functies van Azure Monitor die u configureert of inschakelt, zoals beschreven op de pagina [met prijsdetails van Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)

>[!NOTE]
>Azure Monitor for Storage biedt geen ondersteuning voor [v1-accounts voor algemene doeleinden.](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)
>

## <a name="view-from-azure-monitor"></a>Weergave vanuit Azure-monitor

Vanuit Azure Monitor u transactie-, latentie- en capaciteitsgegevens van meerdere opslagaccounts in uw abonnement bekijken en prestaties, capaciteitsproblemen en fouten identificeren.

Voer de volgende stappen uit om het gebruik en de beschikbaarheid van uw opslagaccounts voor al uw abonnementen te bekijken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **Monitor** in het linkerdeelvenster in de Azure-portal en selecteer onder de sectie **Inzichten** de optie **Opslagaccounts (voorbeeld)**.

    ![Weergave Meerdere opslagaccounts](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Overzichtswerkmap

In de **werkmap Overzicht** voor het geselecteerde abonnement worden in de tabel interactieve opslagstatistieken en servicebeschikbaarheidsstatus weergegeven voor maximaal 10 opslagaccounts die zijn gegroepeerd in het abonnement. U de resultaten filteren op basis van de opties die u selecteert in de volgende vervolgkeuzelijsten:

* **Abonnementen** - alleen abonnementen met opslagaccounts worden weergegeven.  

* **Opslagaccounts** - standaard zijn 10 opslagaccounts vooraf geselecteerd. Als u alle of meerdere opslagaccounts selecteert in de scopekiezer, worden maximaal 200 opslagaccounts geretourneerd. Als u bijvoorbeeld in totaal 573 opslagaccounts had voor drie abonnementen die u hebt geselecteerd, worden slechts 200 accounts weergegeven. 

* **Time Range** - geeft standaard de laatste 4 uur informatie weer op basis van de bijbehorende selecties.

De tellertegel onder de vervolgkeuzelijsten rolt het totale aantal opslagaccounts in het abonnement op en geeft aan hoeveel van het totaal is geselecteerd. Er is voorwaardelijke kleurcodering of heatmaps voor kolommen in de werkmap die transactiestatistieken of fouten rapporteren. De diepste kleur heeft de hoogste waarde en een lichtere kleur is gebaseerd op de laagste waarden. Voor de op fouten gebaseerde kolommen is de waarde rood en voor de kolommen op basis van metrische gegevens is de waarde blauw.

Selecteer een waarde in de kolommen **Beschikbaarheid**, **E2E Latentie**, **Serverlatentie**en **transactiefouttype/Fouten** leidt u naar een rapport dat is afgestemd op het specifieke type opslagstatistieken dat overeenkomt met de kolom die is geselecteerd voor dat opslagaccount. Zie de sectie [Gedetailleerde opslagwerkmappen](#detailed-storage-workbooks) hieronder voor meer informatie over de werkmappen voor elke categorie. 

>[!NOTE]
>Zie [Het type antwoordschema](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) en zoek naar antwoordtypen zoals **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**voor meer informatie over welke fouten in het rapport kunnen worden weergegeven. Afhankelijk van de geselecteerde opslagaccounts worden alle andere fouten weergegeven onder de categorie **Overige**als er meer dan drie soorten fouten worden gerapporteerd.

De **standaardbeschikbaarheidsdrempel** is:

* Waarschuwing - 99%
* Kritiek - 90%

Als u een beschikbaarheidsdrempel wilt instellen op basis van de resultaten van uw observatie of vereisten, wijzigt u [de beschikbaarheidsdrempel](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Capaciteitswerkmap

Selecteer **Capaciteit** boven aan de pagina en de **werkmap Capaciteit** wordt geopend. Het toont u de hoeveelheid totale opslag die wordt gebruikt in de account en de capaciteit die wordt gebruikt door elke gegevensservice in het account om te helpen bij het identificeren over en onder gebruikte opslag.

![Werkmap met meerdere opslagaccounts Capaciteit](./media/storage-insights-overview/storage-account-capacity-02.png) 

Er zijn voorwaardelijke kleurcodering of heatmaps voor kolommen in de werkmap die capaciteitsstatistieken met een blauwe waarde rapporteren. De diepste kleur heeft de hoogste waarde en een lichtere kleur is gebaseerd op de laagste waarden.

Wanneer u een waarde selecteert onder een van de kolommen in de werkmap, zoomt u in op de **werkmap Capaciteit** voor het opslagaccount. Meer informatie over het drill-down rapport wordt beschreven in de sectie [Gedetailleerde opslagwerkmappen](#detailed-storage-workbooks) hieronder. 

## <a name="view-from-a-storage-account"></a>Bekijken vanuit een opslagaccount

Ga als volgt te werk om azure-monitor voor VM's rechtstreeks vanuit een opslagaccount te openen:

1. Selecteer Opslagaccounts in de Azure-portal.

2. Kies in de lijst een opslagaccount. Kies in de sectie Monitoring de optie Insights (voorbeeld).

    ![Overzichtspagina geselecteerde opslagaccount](./media/storage-insights-overview/storage-account-direct-overview-01.png)

In de **overzichtswerkmap** voor het opslagaccount worden verschillende statistieken over opslagprestaties weergegeven waarmee u snel beoordelen:

* Status van de opslagservice om onmiddellijk te zien of een probleem buiten uw besturingselement van invloed is op de opslagservice in de regio waarvoor deze is geïmplementeerd, wat wordt vermeld in de kolom **Overzicht.**

* Interactieve prestatiegrafieken met de meest essentiële details met betrekking tot opslagcapaciteit, beschikbaarheid, transacties en latentie.  

* Metrische en statustegels die de beschikbaarheid van service markeren, het totale aantal transacties voor de opslagservice, de latentie van E2E en de latentie van de server.

Als u een van de knoppen selecteert voor **Fouten,** **Prestaties,** **Beschikbaarheid**en **Capaciteit,** wordt de desbetreffende werkmap geopend. 

![Overzichtspagina geselecteerde opslagaccount](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Gedetailleerde opslagwerkmappen

Of u nu een waarde hebt geselecteerd in de kolommen **Beschikbaarheid,** **E2E Latentie**, **Serverlatentie**en **transactiefouttype/Fouten** uit de werkmap **Overzicht** van meerdere opslagaccounts, of een van de knoppen voor **fouten,** **Prestaties,** **Beschikbaarheid**en **Capaciteit** selecteert in de **werkmap Overzicht** van een specifiek opslagaccount, en elk een set interactieve opslaggerelateerde informatie leveren die is afgestemd op die categorie.  

* **Beschikbaarheid** opent de **werkmap Beschikbaarheid.** Het toont de huidige status van azure storage-service, een tabel met de beschikbare status van elk object dat is gecategoriseerd op gegevensservice die is gedefinieerd in het opslagaccount met een trendlijn die het geselecteerde tijdsbereik weergeeft, en een trenddiagram voor beschikbaarheid elke gegevensservice in het account.  

    ![Voorbeeld van beschikbaarheidsrapport](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E Latency** en **ServerLatentie** opent de **werkmap Prestaties.** Het bevat een rollupstatustegel met E2E-latentie en serverlatentie, een prestatiegrafiek van E2E versus serverlatentie en een tabel die de latentie van succesvolle aanroepen door API,, wordt afgebroken door gegevensservice die is gedefinieerd in het opslagaccount.

    ![Voorbeeld van prestatierapport](./media/storage-insights-overview/storage-account-performance-01.png)

* Als u een van de foutcategorieën in het raster selecteert, opent u de werkmap **Fout.** Het rapport toont metrische tegels van alle andere client-side fouten, behalve beschreven en succesvolle aanvragen, client-throttling fouten, een prestatiegrafiek voor de transactie **Response Type** dimensie metrische specifiek voor ClientOtherError kenmerk, en twee tabellen - **Transacties op API naam** en Transacties op antwoord **type**.

   ![Voorbeeld van foutrapport](./media/storage-insights-overview/storage-account-failures-01.png)

* **Capaciteit** opent de **werkmap Capaciteit.** Het toont de totale hoeveelheid opslagruimte die wordt gebruikt voor elk opslaggegevensobject in de account in de tegels en de grafiek en hoeveel gegevensobjecten in het account zijn opgeslagen.  

    ![Pagina Geselecteerde opslagaccountcapaciteit](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Pinnen en exporteren

U een van de metrische secties vastmaken aan een Azure-dashboard door het pushpinpictogram rechtsboven in de sectie te selecteren.

![Voorbeeld van metrische sectie die is vastmaken aan dashboard](./media/storage-insights-overview/workbook-pin-example.png)

Het **overzicht** met een multi-abonnement en opslagaccount of **capaciteitswerkmappen** ondersteunt het exporteren van de resultaten in Excel-indeling door het pijl-omlaagpictogram rechts van het pushpinpictogram te selecteren.

![Voorbeeld van werkmaprasterresultaten exporteren](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Azure Monitor voor opslag aanpassen (voorbeeld)

In deze sectie worden veelvoorkomende scenario's belicht voor het bewerken van de werkmap die u wilt aanpassen ter ondersteuning van uw behoeften op het gebied van gegevensanalyse:

* De werkmap scopen om altijd een bepaald abonnement of opslagaccount(en) te selecteren
* Statistieken in het raster wijzigen
* De beschikbaarheidsdrempel wijzigen
* De kleurweergave wijzigen

De aanpassingen worden opgeslagen in een aangepaste werkmap om te voorkomen dat de standaardconfiguratie in onze gepubliceerde werkmap wordt overschreven. Werkmappen worden opgeslagen in een resourcegroep, hetzij in de sectie **Mijn rapporten** die privé voor u is, hetzij in de sectie **Gedeelde rapporten** die toegankelijk is voor iedereen die toegang heeft tot de resourcegroep. Nadat u de aangepaste werkmap hebt opgeslagen, moet u naar de werkmapgalerie gaan om deze te starten.

![Werkmapgalerie starten vanuit opdrachtbalk](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Een abonnement of opslagaccount opgeven

U het **overzicht** van het multi-abonnement en opslagaccount of **capaciteitswerkmappen** configureren op het bereik van een bepaald abonnement(en) of opslagaccount(en) bij elke run, de volgende stappen uitvoeren.

1. Selecteer **Monitor** in de portal en selecteer **vervolgens Opslagaccounts (voorbeeld)** in het linkerdeelvenster.

2. Selecteer in de **werkmap Overzicht** in de opdrachtbalk **Bewerken**.

3. Selecteer in de vervolgkeuzelijst **Abonnementen** een of meer abonnementen waarop u deze standaard wilt aanbieden. Vergeet niet dat de werkmap maximaal 10 abonnementen selecteert.  

4. Selecteer in de vervolgkeuzelijst **Opslagaccounts** een of meer accounts waarvan u wilt dat deze standaard wordt weergegeven. Vergeet niet dat de werkmap maximaal 200 opslagaccounts selecteert. 

5. Selecteer **Opslaan als** in de opdrachtbalk om een kopie van de werkmap op te slaan met uw aanpassingen en klik vervolgens op **Gereed bewerken** om terug te keren naar de leesmodus.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Statistieken en kleuren in de werkmap wijzigen

De vooraf gebouwde werkmappen bevatten metrische gegevens en u hebt de mogelijkheid om een van de visualisaties te wijzigen of te verwijderen en aan te passen aan de specifieke behoeften van uw team.

In ons voorbeeld werken we samen met de werkmap voor de capaciteit van meerdere abonnementen en opslagaccount, om aan te tonen hoe:

* Een statistiek verwijderen
* Kleurweergave wijzigen

U dezelfde wijzigingen uitvoeren ten opzichte van **Performance**een van de vooraf gebouwde **werkmappen Voor prestaties,** **beschikbaarheid**en **capaciteit.**

1. Selecteer **Monitor** in de portal en selecteer **vervolgens Opslagaccounts (voorbeeld)** in het linkerdeelvenster.

2. Selecteer **Capaciteit** om over te schakelen naar de capaciteitswerkmap en **selecteer** bewerken op de opdrachtbalk in de opdrachtbalk.

    ![Bewerken selecteren om een werkmap te wijzigen](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Selecteer naast de sectie Statistieken de optie **Bewerken**.

    ![Selecteer Bewerken om de werkmapstatistieken voor capaciteit te wijzigen](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. We gaan de **tijdlijnkolom Account gebruikte capaciteit** verwijderen, dus selecteer **Kolominstellingen** in het statistiekenraster.

    ![Kolominstellingen bewerken](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. Selecteer in het deelvenster **Kolominstellingen bewerken** de optie onder de sectie **Kolommen** **microsoft.storage/storageaccounts-Capacity-UsedCapacity Timeline$| Account gebruikte capaciteit Tijdlijn $**, en onder de vervolgkeuzelijst Kolom **renderer** selecteer **Verborgen**.

6. Selecteer **Opslaan en dichtbij** om uw wijziging te maken.

Laten we nu het kleurthema voor de capaciteitsstatistieken in het rapport wijzigen om groen te gebruiken in plaats van blauw.

1. Selecteer **Kolominstellingen** in het statistiekenraster.

2. Selecteer in het deelvenster **Kolominstellingen bewerken** de optie onder de sectie **Kolommen** **microsoft.storage/storageaccounts-Capacity-UsedCapacity$|microsoft.storage/storageaccounts/blobservices-Capacity-BlobCapacity$|microsoft.storage/storageaccounts/fileservices-Capacity-FileCapacity$|microsoft.storage/storageaccounts/queueservices-Capacity-QueueCapacity$|microsoft.storage/storageaccounts/tableservices-Capacity-TableCapacity$**. Selecteer Onder de vervolgkeuzelijst **Kleurenpalet**de optie **Groen**.

3. Selecteer **Opslaan en dichtbij** om uw wijziging te maken.

4. Selecteer **Opslaan als** in de opdrachtbalk om een kopie van de werkmap op te slaan met uw aanpassingen en klik vervolgens op **Gereed bewerken** om terug te keren naar de leesmodus.  

### <a name="modify-the-availability-threshold"></a>De beschikbaarheidsdrempel wijzigen

In dit voorbeeld werken we met de werkmap voor opslagcapaciteit van het opslagaccount en laten we zien hoe de beschikbaarheidsdrempel kan worden gewijzigd. Standaard worden de beschikbaarheid van tegel- en netrapportagepercentage geconfigureerd met een minimumdrempel van 90 en een maximale drempelwaarde van 99. We gaan de minimumdrempelwaarde van het **beschikbaarheidspercentage** in het **api-naamraster** wijzigen in 85%, wat betekent dat de status wordt gewijzigd in kritiek als de drempelwaarde minder dan 85 procent is. 

1. Selecteer **Opslagaccounts** in de portal en selecteer vervolgens een opslagaccount in de lijst.

2. Selecteer **Insights (voorbeeld)** in het linkerdeelvenster.

3. Selecteer **beschikbaarheid** in de werkmap om over te schakelen naar de beschikbaarheidswerkmap en selecteer **Vervolgens Bewerken** in de opdrachtbalk. 

4. Schuif omlaag naar de onderkant van de pagina en selecteer aan de linkerkant naast het **API-raster Beschikbaarheid** door de **optie Bewerken**.

    ![Beschikbaarheid bewerken met api-naamrasterinstellingen](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Selecteer **Kolominstellingen** en selecteer vervolgens in het deelvenster **Kolominstellingen bewerken,** selecteer onder de sectie **Kolommen** **beschikbaarheid (%) (Drempelwaarden + opgemaakt)**.

6. Wijzig de waarde voor de status **Kritieke** status van **90** naar **85** en klik op **Opslaan en sluiten**.

    ![De waarde van de beschikbaarheidsdrempel voor kritieke status wijzigen](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Selecteer **Opslaan als** in de opdrachtbalk om een kopie van de werkmap op te slaan met uw aanpassingen en klik vervolgens op **Gereed bewerken** om terug te keren naar de leesmodus.

## <a name="troubleshooting"></a>Problemen oplossen

In deze sectie u de diagnose en het oplossen van problemen bij het probleem van enkele veelvoorkomende problemen bij het gebruik van Azure Monitor for Storage (voorbeeld) helpen. Gebruik de onderstaande lijst om de informatie te vinden die relevant is voor uw specifieke probleem.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Problemen met prestaties, capaciteit of beschikbaarheid oplossen

Zie de richtlijnen voor probleemoplossing voor [probleemoplossing](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)voor Azure Storage voor opslag voor problemen met opslag met Azure Monitor for Storage (preview.  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Waarom kan ik slechts 200 opslagaccounts zien?

Het aantal geselecteerde opslagaccounts heeft een limiet van 200, ongeacht het aantal geselecteerde abonnementen.

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Wat gebeurt er als ik op een onlangs vastgemaakte tegel in het dashboard klik?

* Als u ergens op de tegel klikt, gaat u naar het tabblad waar de tegel is vastgemaakt. Als u bijvoorbeeld een grafiek vastmaakt op het tabblad 'Overzicht van het opslagaccount' wanneer u op die tegel in het dashboard klikt, opent deze die standaardweergave, maar als u een grafiek vastmaakt van uw eigen opgeslagen exemplaar, opent deze de weergave van uw opgeslagen kopie.
* Het filterpictogram linksboven in de titel opent het tabblad Tegelinstellingen configureren.
* Het ellipspictogram rechtsboven geeft u de opties om 'titelgegevens aanpassen', 'aanpassen', 'vernieuwen' en 'verwijderen uit het dashboard'.

### <a name="what-happens-when-i-save-a-workbook"></a>Wat gebeurt er als ik een werkmap opsla?

* Wanneer u een werkmap opslaat, u hiermee een nieuwe kopie van de werkmap maken met uw bewerkingen en de titel wijzigen. Opslaan overschrijft de werkmap niet, de huidige werkmap is altijd de standaardweergave.
* Een **niet-opgeslagen** werkmap is slechts de standaardweergave.


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Waarom zie ik niet al mijn abonnementen in de portal?

De portal toont alleen gegevens voor geselecteerde abonnementen bij het starten van portalen. Als u wilt wijzigen welke abonnementen zijn geselecteerd, gaat u rechtsboven op het notitieblok met een filterpictogram. Dit toont het tabblad Directory + abonnementen.

![Map en abonnement](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Hoe wijzig je de kleuring en drempelwaarde voor beschikbaarheid?

Raadpleeg de sectie [Beschikbaarheidsdrempel wijzigen](storage-insights-overview.md#modify-the-availability-threshold) voor de gedetailleerde stappen voor het wijzigen van de kleuring en drempelwaarden voor beschikbaarheid.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>De gegevens in Azure Monitor for Storage analyseren en oplossen?

 Raadpleeg het artikel [Monitor, diagnose en probleemoplossing](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting) voor Microsoft Azure Storage voor meer informatie over het analyseren en oplossen van de Azure Storage-gegevens die worden weergegeven in Azure Monitor for Storage.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Waarom zie ik niet alle soorten fouten in statistieken?

Momenteel worden maximaal drie verschillende soorten fouten weergegeven en worden de rest van de fouten gegroepeerd in één emmer. Het wordt gecontroleerd met behulp van splitByLimit en kan worden gewijzigd. Voor het wijzigen van deze eigenschap:

1. Klik op werkmap bewerken.
2. Ga naar statistieken, klik op bewerken en selecteer **Vervolgens Transacties, Som** of welke statistieken u wilt bewerken.

    ![Ga naar statistieken en klik op bewerken en klik vervolgens op 'Transacties, sommen'](./media/storage-insights-overview/fqa7.png)

1. Wijzig vervolgens het aantal splitsingen.

    ![Selecteer Metrische parameters"](./media/storage-insights-overview/fqa7-2.png)

Als u n verschillende typen fouten wilt zien, geeft u splitByLimit op als n+1, 1 extra voor de rest van de fouten.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Ik heb mijn werkmap opgeslagen terwijl ik op een opslagaccount zat. Waarom kan ik het nu niet vinden?

Elke werkmap wordt opgeslagen in het opslagaccount waarin u deze hebt opgeslagen. Probeer het specifieke opslagaccount te vinden waarin de gebruiker de werkmap heeft opgeslagen. Anders is er geen manier om een specifieke werkmap te vinden zonder de bron (opslagaccount) te kennen.

### <a name="what-is-time-range"></a>Wat is tijdsbereik?

Het tijdsbereik toont u gegevens uit een bepaald tijdsbestek. Als het tijdsbereik bijvoorbeeld 24 uur is, worden gegevens van de afgelopen 24 uur weergegeven.

### <a name="what-is-time-granularity-time-grain"></a>Wat is tijdgranulariteit (tijdkorrel)?

Tijdgranulariteit is het tijdsverschil tussen twee gegevenspunten. Als de tijdkorrel bijvoorbeeld is ingesteld op 1 seconde betekent dit dat metrische gegevens elke seconde worden verzameld.

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Wat is de tijd granulariteit zodra we een deel van de werkmappen vastmaken aan een dashboard?

De standaardtijdgranulariteit is ingesteld op automatisch, deze kan momenteel niet worden gewijzigd.

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Hoe wijzig ik de tijdspanne van de werkmapstap op mijn dashboard?

Standaard is het tijdsverloop/tijdbereik op uw dashboardtegel ingesteld op 24 uur, om deze klik op de ellipsen rechtsboven te wijzigen, **tegelgegevens aanpassen,** het selectievakje 'de instellingen van de dashboardtijd op titelniveau overschrijven' en vervolgens een tijdspanne kiezen met behulp van het vervolgkeuzemenu.  

![Selecteer de ellips in de rechterhoek van de tegel en kies Deze gegevens aanpassen](./media/storage-insights-overview/fqa-data-settings.png)

![Selecteer in Tegelinstellingen configureren de vervolgkeuzelijst tijdsperiode om het tijdsbereik/tijdbereik te wijzigen](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Hoe wijzig ik de titel van de werkmap of een werkmapstap die ik vastgemaakt heb aan een dashboard?

De titel van de werkmap of werkmapstap die is vastgemaakt aan een dashboard, behoudt dezelfde naam als in de werkmap. Als u de titel wilt wijzigen, moet u uw eigen exemplaar van de werkmap opslaan. Dan u de werkmap een naam geven voordat u op opslaan drukt.

![Selecteer bovenaan opslaan om een kopie van de werkmap op te slaan en de naam ervan te wijzigen](./media/storage-insights-overview/fqa-change-workbook-name.png)

Als u de naam van een stap in uw opgeslagen werkmap wilt wijzigen, selecteert u bewerken onder de stap en selecteert u het tandwiel helemaal onder aan de instellingen.

![Selecteer bewerken onder aan een werkmapstap om](./media/storage-insights-overview/fqa-edit.png)
![de instellingen te openen In de instellingen selecteer het tandwiel onderaan, om de stapnaam te kunnen wijzigen](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Volgende stappen

* Configureer [metrische waarschuwingen](../platform/alerts-metric.md) en [servicestatusmeldingen](../../service-health/alerts-activity-log-service-notifications.md) om automatische waarschuwingen in te stellen om te helpen bij het detecteren van problemen.

* Lees de scenario's werkmappen zijn ontworpen om te ondersteunen, hoe u nieuwe rapporten maakt en bestaande rapporten aanpast door [interactieve rapporten maken met Azure Monitor-werkmappen](../app/usage-workbooks.md)te bekijken.

* Zie [Microsoft Azure Storage controleren, diagnosticeren en oplossen](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)van Problemen met Azure Storage voor een uitgebreide handleiding voor het gebruik van Storage Analytics en andere hulpprogramma's voor het identificeren, diagnosticeren en oplossen van azure storage.
