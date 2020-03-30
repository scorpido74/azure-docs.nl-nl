---
title: Continue export van telemetrie vanuit Application Insights | Microsoft Documenten
description: Exporteer diagnostische en gebruiksgegevens naar opslag in Microsoft Azure en download deze vanaf daar.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: f6afe42e483ab7ad5810169fc301946c75308c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298286"
---
# <a name="export-telemetry-from-application-insights"></a>Telemetrie exporteren vanuit Application Insights
Wilt u uw telemetrie langer bewaren dan de standaardbewaarperiode? Of verwerken op een gespecialiseerde manier? Continuous Export is hiervoor ideaal. De gebeurtenissen die u ziet in de Portal Application Insights, kunnen worden geëxporteerd naar opslag in Microsoft Azure in JSON-indeling. Van daaruit u uw gegevens downloaden en schrijven welke code u nodig hebt om het te verwerken.  

Voordat u continue export instelt, zijn er enkele alternatieven die u misschien wilt overwegen:

* Met de knop Exporteren boven aan een statistieken- of zoektabblad u tabellen en grafieken overzetten naar een Excel-spreadsheet.

* [Analytics](../../azure-monitor/app/analytics.md) biedt een krachtige querytaal voor telemetrie. Het kan ook resultaten exporteren.
* Als u uw [gegevens in Power BI](../../azure-monitor/app/export-power-bi.md )wilt verkennen, u dat doen zonder Continue export te gebruiken.
* Met [de REST API voor gegevenstoegang](https://dev.applicationinsights.io/) u programmatisch toegang krijgen tot uw telemetrie.
* U ook toegang krijgen tot [setup continuous export via Powershell.](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport)

Nadat Continuous Export uw gegevens naar opslag heeft kopieën gemaakt (waar deze zo lang kunnen blijven als u wilt), is deze nog steeds beschikbaar in Application Insights voor de gebruikelijke [bewaarperiode.](../../azure-monitor/app/data-retention-privacy.md)

## <a name="continuous-export-advanced-storage-configuration"></a>Geavanceerde opslagconfiguratie continu exporteren

Continuous Export **biedt geen ondersteuning voor** de volgende Azure-opslagfuncties/-configuraties:

* Gebruik van [VNET/Azure Storage-firewalls](https://docs.microsoft.com/azure/storage/common/storage-network-security) in combinatie met Azure Blob-opslag.

* [Onveranderlijke opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) voor Azure Blob-opslag.

* [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="create-a-continuous-export"></a><a name="setup"></a>Een continue export maken

1. Open Continue exporteren in de bron Application Insights voor uw app onder Configureren aan de linkerkant en kies **Toevoegen:**

2. Kies de telemetriegegevenstypen die u wilt exporteren.

3. Maak of selecteer een [Azure-opslagaccount](../../storage/common/storage-introduction.md) waar u de gegevens wilt opslaan. Ga voor meer informatie over opslagprijsopties naar de [officiële prijspagina.](https://azure.microsoft.com/pricing/details/storage/)

     Klik op Toevoegen, Doel exporteren, Opslagaccount en maak vervolgens een nieuwe winkel of kies een bestaande winkel.

    > [!Warning]
    > Standaard wordt de opslaglocatie ingesteld op dezelfde geografische regio als uw Application Insights-bron. Als u in een andere regio opslaat, u transferkosten maken.

4. Een container in de opslag maken of selecteren.

Zodra u uw export hebt gemaakt, begint deze te gaan. U krijgt alleen gegevens die binnenkomen nadat u de export hebt gemaakt.

Er kan een vertraging van ongeveer een uur zijn voordat gegevens in de opslag worden weergegeven.

Zodra de eerste export is voltooid, vindt u een structuur die vergelijkbaar is met de volgende structuur in uw Azure Blob-opslagcontainer: (Dit is afhankelijk van de gegevens die u verzamelt.)

|Name | Beschrijving |
|:----|:------|
| [Beschikbaarheid](export-data-model.md#availability) | Rapporten [beschikbaarheid webtests](../../azure-monitor/app/monitor-web-app-availability.md).  |
| [Gebeurtenis](export-data-model.md#events) | Aangepaste gebeurtenissen gegenereerd door [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent). 
| [Uitzonderingen](export-data-model.md#exceptions) |Rapporteert [uitzonderingen](../../azure-monitor/app/asp-net-exceptions.md) in de server en in de browser.
| [Berichten](export-data-model.md#trace-messages) | Verzonden door [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)en door de [logboekadapters](../../azure-monitor/app/asp-net-trace-logs.md).
| [Statistieken](export-data-model.md#metrics) | Gegenereerd door metrische API-aanroepen.
| [Prestatiemeteritems](export-data-model.md) | Prestatiemeteritems verzameld door Application Insights.
| [Verzoeken](export-data-model.md#requests)| Verzonden door [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). De standaardmodules gebruiken dit om de responstijd van de server, gemeten op de server, te rapporten.| 

### <a name="to-edit-continuous-export"></a>Continue export bewerken

Klik op continue export en selecteer het opslagaccount dat u wilt bewerken.

### <a name="to-stop-continuous-export"></a>Om continue export te stoppen

Als u de export wilt stoppen, klikt u op Uitschakelen. Wanneer u opnieuw op Inschakelen klikt, wordt de export opnieuw gestart met nieuwe gegevens. U krijgt de gegevens die in de portal zijn aangekomen niet terwijl de export is uitgeschakeld.

Als u de export permanent wilt stoppen, verwijdert u deze. Als u dit doet, worden uw gegevens niet uit de opslag verwijderd.

### <a name="cant-add-or-change-an-export"></a>u een export niet toevoegen of wijzigen?
* Als u export wilt toevoegen of wijzigen, hebt u toegangsrechten voor eigenaren, inzender of toepassingsstatistieken nodig. [Meer informatie over rollen][roles].

## <a name="what-events-do-you-get"></a><a name="analyze"></a>Welke evenementen krijg je?
De geëxporteerde gegevens zijn de ruwe telemetrie die we van uw toepassing ontvangen, behalve dat we locatiegegevens toevoegen, die we berekenen op basis van het IP-adres van de client.

Gegevens die door [steekproeven](../../azure-monitor/app/sampling.md) zijn verwijderd, worden niet opgenomen in de geëxporteerde gegevens.

Andere berekende statistieken zijn niet inbegrepen. We exporteren bijvoorbeeld geen gemiddeld CPU-gebruik, maar we exporteren wel de ruwe telemetrie waaruit het gemiddelde wordt berekend.

De gegevens bevatten ook de resultaten van eventuele [beschikbaarheid webtests](../../azure-monitor/app/monitor-web-app-availability.md) die u hebt ingesteld.

> [!NOTE]
> **Bemonstering.** Als uw toepassing veel gegevens verzendt, kan de samplingfunctie slechts een fractie van de gegenereerde telemetrie gebruiken en verzenden. [Meer informatie over steekproeven.](../../azure-monitor/app/sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a>De gegevens inspecteren
U de opslag direct in het portaal inspecteren. Klik op start in het meest linkse menu, bovenaan waar 'Azure-services' staan **selecteer Opslagaccounts,** selecteer de naam van het opslagaccount, selecteer blobs onder services op de **overzichtspagina** en selecteer ten slotte de containernaam.

Als u Azure-opslag wilt inspecteren in Visual Studio, opent u **Weergave**, **Cloud Explorer**. (Als u die menuopdracht niet hebt, moet u de Azure SDK installeren: open het dialoogvenster **Nieuw project,** vouw Visual C#/Cloud uit en kies **Microsoft Azure SDK voor .NET .)**

Wanneer u uw blob-winkel opent, ziet u een container met een reeks blobbestanden. De URI van elk bestand dat is afgeleid van de naam van uw Application Insights-bron, de instrumentatiesleutel, telemetrie-type/datum/tijd. (De naam van de resource is alle kleine letters en de instrumentatiesleutel laat streepjes weg.)

![Inspecteer de blob store met een geschikt gereedschap](./media/export-telemetry/04-data.png)

De datum en tijd zijn UTC en zijn wanneer de telemetrie werd gedeponeerd in de winkel - niet de tijd die het werd gegenereerd. Dus als je code schrijft om de gegevens te downloaden, kan het lineair door de gegevens gaan.

Hier is de vorm van het pad:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Waar

* `blobCreationTimeUtc`is het moment waarop blob is gemaakt in de interne faseringsopslag
* `blobDeliveryTimeUtc`is het moment waarop blob wordt gekopieerd naar de exportbestemmingsopslag

## <a name="data-format"></a><a name="format"></a>Gegevensindeling
* Elke blob is een tekstbestand dat meerdere '\n'-gescheiden rijen bevat. Het bevat de telemetrie verwerkt over een periode van ongeveer een halve minuut.
* Elke rij vertegenwoordigt een telemetriegegevenspunt, zoals een aanvraag of paginaweergave.
* Elke rij is een JSON-document zonder opmaak. Als u wilt toekijken, opent u deze in Visual Studio en kiest u Bewerken, Geavanceerd, Opmaakbestand:

![Bekijk de telemetrie met een geschikt gereedschap](./media/export-telemetry/06-json.png)

Tijdsduur zijn in teken, waar 10 000 teken = 1 ms. Deze waarden tonen bijvoorbeeld een tijd van 1 ms om een verzoek van de browser te verzenden, 3 ms om het te ontvangen en 1,8 s om de pagina in de browser te verwerken:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Gedetailleerde gegevensmodelreferentie voor de eigenschapstypen en -waarden.](export-data-model.md)

## <a name="processing-the-data"></a>Verwerking van de gegevens
Op kleine schaal u een code schrijven om uw gegevens uit elkaar te halen, deze in een spreadsheet te lezen, enzovoort. Bijvoorbeeld:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

Zie [een werkrol gebruiken voor][exportasa]een groter codevoorbeeld .

## <a name="delete-your-old-data"></a><a name="delete"></a>Uw oude gegevens verwijderen
U bent verantwoordelijk voor het beheren van uw opslagcapaciteit en het verwijderen van de oude gegevens indien nodig.

## <a name="if-you-regenerate-your-storage-key"></a>Als u uw opslagsleutel regenereert...
Als u de sleutel van uw opslag wijzigt, werkt continue export niet meer. U ziet een melding in uw Azure-account.

Open het tabblad Continu exporteren en bewerk uw export. Bewerk de exportbestemming, maar laat dezelfde opslag geselecteerd. Klik op OK om dit te bevestigen.

De continue export wordt opnieuw gestart.

## <a name="export-samples"></a>Monsters exporteren

* [Exporteren naar SQL met Stream Analytics][exportasa]
* [Voorbeeld 2 van Stream Analytics](export-stream-analytics.md)

Op grotere schaal, overwegen [HDInsight](https://azure.microsoft.com/services/hdinsight/) - Hadoop clusters in de cloud. HDInsight biedt een verscheidenheid aan technologieën voor het beheren en analyseren van big data, en u deze gebruiken om gegevens te verwerken die zijn geëxporteerd vanuit Application Insights.

## <a name="q--a"></a>Vragen en antwoorden
* *Maar alles wat ik wil is een eenmalige download van een grafiek.*  

    Ja, dat kun je doen. Klik boven aan het tabblad op **Gegevens exporteren**.
* *Ik heb een export ingesteld, maar er zijn geen gegevens in mijn winkel.*

    Heeft Application Insights telemetrie ontvangen van uw app sinds u de export hebt ingesteld? U ontvangt alleen nieuwe gegevens.
* *Ik heb geprobeerd om het opzetten van een export, maar werd de toegang geweigerd*

    Als het account eigendom is van uw organisatie, moet u lid zijn van de eigenaren of bijdragersgroepen.
* *Kan ik rechtstreeks exporteren naar mijn eigen on-premises winkel?*

    Nee, sorry. Onze exportengine werkt momenteel alleen met Azure-opslag op dit moment.  
* *Is er een limiet aan de hoeveelheid gegevens die u in mijn winkel hebt geplaatst?*

    Nee. We blijven gegevens pushen totdat u de export verwijdert. We stoppen als we de buitenste grenzen voor blob opslag raken, maar dat is vrij groot. Het is aan jou om te bepalen hoeveel opslagruimte je gebruikt.  
* *Hoeveel blobs moet ik zien in de opslag?*

  * Voor elk gegevenstype dat u hebt geselecteerd om te exporteren, wordt elke minuut een nieuwe blob gemaakt (als er gegevens beschikbaar zijn).
  * Bovendien worden voor toepassingen met veel verkeer extra partitie-eenheden toegewezen. In dit geval maakt elke eenheid elke minuut een blob.
* *Ik heb de sleutel van mijn opslag geregenereerd of de naam van de container gewijzigd, en nu werkt de export niet.*

    Bewerk de export en open het tabblad exportbestemming. Laat dezelfde opslag als voorheen en klik op OK om te bevestigen. De export wordt opnieuw gestart. Als de wijziging in de afgelopen dagen is geweest, verliest u geen gegevens.
* *Kan ik de export onderbreken?*

    Ja. Klik op Uitschakelen.

## <a name="code-samples"></a>Codevoorbeelden

* [Voorbeeld van Stream Analytics](export-stream-analytics.md)
* [Exporteren naar SQL met Stream Analytics][exportasa]
* [Gedetailleerde gegevensmodelreferentie voor de eigenschapstypen en -waarden.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
