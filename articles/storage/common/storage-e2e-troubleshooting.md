---
title: Problemen met gegevensproblemen oplossen met diagnostiek en Message Analyzer
titleSuffix: Azure Storage
description: Een zelfstudie waarin end-to-end probleemoplossing met Azure Storage Analytics, AzCopy en Microsoft Message Analyzer wordt weergegeven
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: normesta
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 69983502fb7d099f474fb1c4c084f5d381a173e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314756"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>End-to-end-probleemoplossing op basis van metrische gegevens en logboekregistratie van Azure Storage, AzCopy en Message Analyzer

[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnose en probleemoplossing is een belangrijke vaardigheid voor het bouwen en ondersteunen van clienttoepassingen met Microsoft Azure Storage. Vanwege het gedistribueerde karakter van een Azure-toepassing kunnen het diagnosticeren en oplossen van fouten en prestatieproblemen complexer zijn dan in traditionele omgevingen.

In deze zelfstudie laten we zien hoe u bepaalde fouten identificeren die van invloed kunnen zijn op de prestaties en deze fouten van end-to-end kunnen oplossen met behulp van hulpprogramma's van Microsoft en Azure Storage, om de clienttoepassing te optimaliseren.

Deze zelfstudie biedt een praktische verkenning van een end-to-end probleemoplossingsscenario. Zie [Microsoft Azure Storage controleren, diagnosticeren en oplossen voor](storage-monitoring-diagnosing-troubleshooting.md)een uitgebreide conceptgids voor het oplossen van Azure-opslagtoepassingen.

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Hulpprogramma's voor het oplossen van problemen met Azure Storage-toepassingen

Als u clienttoepassingen wilt oplossen met Microsoft Azure Storage, u een combinatie van hulpprogramma's gebruiken om te bepalen wanneer een probleem is opgetreden en wat de oorzaak van het probleem kan zijn. Tot deze hulpmiddelen behoren onder meer:

* **Azure Storage Analytics**. [Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) biedt statistieken en logboekregistratie voor Azure Storage.

  * **Opslagstatistieken houden** transactiestatistieken en capaciteitsstatistieken bij voor uw opslagaccount. Met behulp van statistieken u bepalen hoe uw toepassing presteert aan de hand van verschillende maatregelen. Zie [Tabelschema voor statistieken voor opslaganalysestatistieken](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) voor meer informatie over de typen statistieken die worden bijgehouden door Storage Analytics.
  * **Opslaglogboekregistratie** registreert elk verzoek aan de Azure Storage-services in een serverlogboek. Het logboek houdt gedetailleerde gegevens bij voor elk verzoek, inclusief de uitgevoerde bewerking, de status van de bewerking en latentiegegevens. Zie [Storage Analytics Log Format](/rest/api/storageservices/Storage-Analytics-Log-Format) voor meer informatie over de aanvraag- en antwoordgegevens die door Storage Analytics naar de logboeken zijn geschreven.

* **Azure-portal**. U metrische gegevens en logboekregistratie configureren voor uw opslagaccount in de [Azure-portal.](https://portal.azure.com) U ook grafieken en grafieken weergeven die laten zien hoe uw toepassing in de loop van de tijd presteert en waarschuwingen configureren om u op de hoogte te stellen als uw toepassing anders presteert dan verwacht voor een opgegeven statistiek.

    Zie [Een opslagaccount in de Azure-portal controleren](storage-monitor-storage-account.md) voor informatie over het configureren van bewaking in de Azure-portal.
* **AzCopy**. Serverlogboeken voor Azure Storage worden opgeslagen als blobs, zodat u AzCopy gebruiken om de logboekblobs naar een lokale map te kopiëren voor analyse met Microsoft Message Analyzer. Zie [Gegevens overzetten met het AzCopy-opdrachtregelhulpprogramma](storage-use-azcopy.md) voor meer informatie over AzCopy.
* **Microsoft Message Analyzer**. Message Analyzer is een hulpmiddel dat logbestanden verbruikt en logboekgegevens weergeeft in een visuele indeling die het eenvoudig maakt om gegevens te filteren, zoeken en groeperen in nuttige sets die u gebruiken om fouten en prestatieproblemen te analyseren. Zie [de operating guide voor Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx) voor meer informatie over Message Analyzer.

## <a name="about-the-sample-scenario"></a>Informatie over het voorbeeldscenario

Voor deze zelfstudie onderzoeken we een scenario waarin Azure Storage-statistieken een laag percentage slagingspercentage swijzen voor een toepassing die Azure-opslag aanroept. De statistiek met een laag percentage slagingspercentage (weergegeven als **PercentSuccess** in de [Azure-portal](https://portal.azure.com) en in de statistiekentabellen) houdt bewerkingen bij die slagen, maar die een HTTP-statuscode retourneren die groter is dan 299. In de opslaglogboekbestanden aan de serverzijde worden deze bewerkingen geregistreerd met een transactiestatus van **ClientOtherErrors**. Zie [Statistieken tonen laag PercentageSucces of analytics log-items hebben bewerkingen met de transactiestatus van ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success)voor meer informatie over de statistiek met succes met een laag percentage.

Azure Storage-bewerkingen kunnen HTTP-statuscodes van meer dan 299 retourneren als onderdeel van hun normale functionaliteit. Maar deze fouten geven in sommige gevallen aan dat u uw clienttoepassing optimaliseren voor betere prestaties.

In dit scenario beschouwen we een laag percentage slagingspercentage als iets onder de 100%. U echter een ander metriek niveau kiezen op basis van uw behoeften. We raden u aan tijdens het testen van uw toepassing een basislijntolerantie vast te stellen voor uw belangrijkste prestatiestatistieken. U bijvoorbeeld op basis van tests bepalen dat uw toepassing een consistent percentage slagingspercentage van 90% of 85% moet hebben. Als uit uw statistiekengegevens blijkt dat de toepassing afwijkt van dat nummer, u onderzoeken wat de oorzaak van de toename kan zijn.

Voor ons voorbeeldscenario zullen we de logboeken onderzoeken om de fouten te vinden die correleren met de statistieken en deze gebruiken om erachter te komen wat de oorzaak is van het lagere percentage slagingspercentage. We zullen specifiek kijken naar fouten in de 400 bereik. Dan zullen we meer onderzoeken 404 (Niet gevonden) fouten.

### <a name="some-causes-of-400-range-errors"></a>Enkele oorzaken van fouten in 400 bereik

De onderstaande voorbeelden tonen een steekproef van ongeveer 400-bereikfouten voor aanvragen tegen Azure Blob Storage en de mogelijke oorzaken daarvan. Elk van deze fouten, evenals fouten in de 300 bereik en de 500 bereik, kan bijdragen aan een laag percentage slagingspercentage.

Houd er rekening mee dat de onderstaande lijsten verre van compleet zijn. Zie [Status- en foutcodes](https://msdn.microsoft.com/library/azure/dd179382.aspx) op MSDN voor meer informatie over algemene Azure-opslagfouten en over fouten die specifiek zijn voor elk van de opslagservices.

#### <a name="status-code-404-not-found-examples"></a>Voorbeelden van statuscode 404 (niet gevonden)

Treedt op wanneer een leesbewerking tegen een container of blob mislukt omdat de blob of container niet wordt gevonden.

* Dit gebeurt als een container of blob vóór deze aanvraag door een andere client is verwijderd.
* Dit gebeurt als u een API-aanroep gebruikt die de container of blob maakt nadat u hebt gecontroleerd of deze bestaat. De CREATEIfNotExists API's maken eerst een HEAD-oproep om te controleren op het bestaan van de container of blob; als deze niet bestaat, wordt een fout van 404 geretourneerd en wordt een tweede PUT-oproep gedaan om de container of blob te schrijven.

#### <a name="status-code-409-conflict-examples"></a>Voorbeelden van statuscode 409 (conflict)

* Dit gebeurt als u een API maken gebruikt om een nieuwe container of blob te maken, zonder eerst te controleren of het bestaat en er al een container of blob met die naam bestaat.
* Dit gebeurt als een container wordt verwijderd en u probeert een nieuwe container met dezelfde naam te maken voordat de verwijderingsbewerking is voltooid.
* Dit gebeurt als u een lease opgeeft voor een container of blob en er al een leaseaanwezig is.

#### <a name="status-code-412-precondition-failed-examples"></a>Voorbeelden van statuscode 412 (voorwaarde mislukt)

* Treedt op wanneer niet is voldaan aan de voorwaarde die is opgegeven door een voorwaardelijke koptekst.
* Treedt op wanneer de opgegeven lease-id niet overeenkomt met de lease-id op de container of blob.

## <a name="generate-log-files-for-analysis"></a>Logboekbestanden genereren voor analyse

In deze zelfstudie gebruiken we Message Analyzer om met drie verschillende typen logbestanden te werken, hoewel u ervoor kiezen om met een van deze bestanden te werken:

* Het **serverlogboek**dat wordt gemaakt wanneer u Azure Storage-logboekregistratie inschakelt. Het serverlogboek bevat gegevens over elke bewerking die wordt aangeroepen tegen een van de Azure Storage-services - blob, wachtrij, tabel en bestand. Het serverlogboek geeft aan welke bewerking is aangeroepen en welke statuscode is geretourneerd, evenals andere details over het verzoek en antwoord.
* Het **.NET-clientlogboek**, dat wordt gemaakt wanneer u logboekregistratie aan de client zijde inschakelt vanuit uw .NET-toepassing. Het klantenlogboek bevat gedetailleerde informatie over hoe de klant de aanvraag voorbereidt en het antwoord ontvangt en verwerkt.
* Het **HTTP-netwerktraceringslogboek**, dat gegevens verzamelt over HTTP/HTTPS-aanvraag- en antwoordgegevens, ook voor bewerkingen tegen Azure Storage. In deze zelfstudie genereren we de netwerktracering via Message Analyzer.

### <a name="configure-server-side-logging-and-metrics"></a>Logboekregistratie en metrische gegevens aan de serverzijde configureren

Eerst moeten we Azure Storage-logboekregistratie en -statistieken configureren, zodat we gegevens van de servicekant moeten analyseren. U logboekregistratie en metrische gegevens op verschillende manieren configureren - via de [Azure-portal](https://portal.azure.com), door PowerShell of programmatisch te gebruiken. Zie [Statistieken inschakelen](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) en [logboekregistratie inschakelen](storage-analytics-logging.md#enable-storage-logging) voor meer informatie over het configureren van logboekregistratie en statistieken.

### <a name="configure-net-client-side-logging"></a>Logboekregistratie aan de clientzijde configureren.

Als u logboekregistratie aan de clientzijde wilt configureren voor een .NET-toepassing, schakelt u .NET-diagnose in het configuratiebestand van de toepassing in (web.config of app.config). Zie [Logboekregistratie aan clientzijde met de .NET-opslagclientbibliotheek](https://msdn.microsoft.com/library/azure/dn782839.aspx) en [logboekregistratie aan de clientzijde met de Microsoft Azure Storage SDK voor Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) op MSDN voor meer informatie.

Het client-side log bevat gedetailleerde informatie over hoe de klant de aanvraag voorbereidt en het antwoord ontvangt en verwerkt.

De opslagclientbibliotheek slaat logboekgegevens aan clientzijde op in de locatie die is opgegeven in het configuratiebestand van de toepassing (web.config of app.config).

### <a name="collect-a-network-trace"></a>Een netwerktracering verzamelen

U Message Analyzer gebruiken om een HTTP/HTTPS-netwerktracering te verzamelen terwijl uw clienttoepassing wordt uitgevoerd. Message Analyzer maakt gebruik [van Fiddler](https://www.telerik.com/fiddler) op de back-end. Voordat u de netwerktracering verzamelt, raden we u aan Fiddler te configureren om onversleuteld HTTPS-verkeer op te nemen:

1. [Fiddler](https://www.telerik.com/download/fiddler)installeren .
2. Start Fiddler.
3. **Selecteer Gereedschappen | Fiddler Opties**.
4. Controleer in het dialoogvenster Opties of **CAPTURE HTTPS CONNECTs** en **Decrypt HTTPS-verkeer** beide zijn geselecteerd, zoals hieronder wordt weergegeven.

![Fiddler-opties configureren](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Voor de zelfstudie moet u eerst een netwerktracering verzamelen en opslaan in Message Analyzer en vervolgens een analysesessie maken om de tracering en de logboeken te analyseren. Ga als lid van het netwerk op een netwerktracering in Message Analyzer:

1. Selecteer **Bestand | Snel spoor | Onversleutelde HTTPS**.
2. Het spoor zal onmiddellijk beginnen. Selecteer **Stoppen** om het spoor te stoppen, zodat we het kunnen configureren om alleen opslagverkeer te traceren.
3. Selecteer **Bewerken** om de traceringssessie te bewerken.
4. Selecteer de koppeling **Configureren** rechts van de **Microsoft-Pef-WebProxy ETW-provider.**
5. Klik in het dialoogvenster **Geavanceerde instellingen** op het tabblad **Provider.**
6. Geef in het veld **Hostnamefilter** de opslageindpunten op, gescheiden door spaties. U bijvoorbeeld uw eindpunten als volgt opgeven; de `storagesample` naam van uw opslagaccount wijzigen:

    `storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net`

7. Sluit het dialoogvenster af en klik op **Opnieuw starten** om te beginnen met het verzamelen van de tracering met het hostnamefilter op zijn plaats, zodat alleen Azure Storage-netwerkverkeer in het traceringsspoor is opgenomen.

> [!NOTE]
> Nadat u klaar bent met het verzamelen van uw netwerktracering, raden we u ten zeerste aan de instellingen die u in Fiddler hebt gewijzigd, terug te draaien om HTTPS-verkeer te decoderen. Schakel in het dialoogvenster Fiddler Options de **selectievakjes HTTPS-CONNECTs vastleggen** en **HTTPS-verkeer decoderen** uit.

Zie [Functies voor netwerktracering gebruiken](https://technet.microsoft.com/library/jj674819.aspx) op Technet voor meer informatie.

## <a name="review-metrics-data-in-the-azure-portal"></a>Metrische gegevens bekijken in de Azure-portal

Zodra uw toepassing een bepaalde tijd actief is geweest, u de metrische grafieken bekijken die in de [Azure-portal](https://portal.azure.com) worden weergegeven om te zien hoe uw service presteert.

Navigeer eerst naar uw opslagaccount in de Azure-portal. Standaard wordt een bewakingsdiagram met de statistiek **Succespercentage** weergegeven op het accountblad. Als u de grafiek eerder hebt gewijzigd om verschillende statistieken weer te geven, voegt u de statistiek **Succespercentage** toe.

U ziet nu **het percentage succes** in de bewakingsgrafiek, samen met alle andere statistieken die u hebt toegevoegd. In het scenario dat we vervolgens onderzoeken door de logboeken in Message Analyzer te analyseren, ligt het percentage slagingspercentage iets onder de 100%.

Zie [Metrische grafieken aanpassen](storage-monitor-storage-account.md#customize-metrics-charts)voor meer informatie over het toevoegen en aanpassen van metrische gegevens .

> [!NOTE]
> Het kan enige tijd duren voordat uw metrische gegevens worden weergegeven in de Azure-portal nadat u opslagstatistieken hebt ingeschakeld. Dit komt omdat uurstatistieken voor het vorige uur niet worden weergegeven in de Azure-portal totdat het huidige uur is verstreken. Ook worden minutenstatistieken momenteel niet weergegeven in de Azure-portal. Dus afhankelijk van wanneer u statistieken inschakelt, kan het tot twee uur duren voordat metrische gegevens te zien zijn.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>AzCopy gebruiken om serverlogboeken naar een lokale map te kopiëren

Azure Storage schrijft serverlogboekgegevens naar blobs, terwijl statistieken naar tabellen worden geschreven. Log blobs zijn beschikbaar in `$logs` de bekende container voor uw opslagaccount. Logboekblobs worden hiërarchisch benoemd per jaar, maand, dag en uur, zodat u eenvoudig het tijdsbereik vinden dat u wilt onderzoeken. Bijvoorbeeld, in `storagesample` het account, de container voor de log blobs voor 01/02/2015, van 8-9 uur, is `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. De afzonderlijke blobs in deze container worden `000000.log`opeenvolgend genoemd, te beginnen met .

U het azcopy-opdrachtregelgereedschap gebruiken om deze logbestanden aan de serverzijde te downloaden naar een locatie van uw keuze op uw lokale machine. U bijvoorbeeld de volgende opdracht gebruiken om de logboekbestanden voor blobbewerkingen die op `C:\Temp\Logs\Server`2 januari 2015 plaatsvonden, te downloaden naar de map; vervangen `<storageaccountname>` door de naam van uw opslagaccount:

```azcopy
azcopy copy 'http://<storageaccountname>.blob.core.windows.net/$logs/blob/2015/01/02' 'C:\Temp\Logs\Server'  --recursive
```

AzCopy is beschikbaar om te downloaden op de pagina [Azure Downloads.](https://azure.microsoft.com/downloads/) Zie Gegevens overbrengen met [het AzCopy Command-Line Utility](storage-use-azcopy.md)voor meer informatie over het gebruik van AzCopy.

Zie Logboekgegevens voor [opslaglogboekregistratie downloaden](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata)voor meer informatie over het downloaden van logboeken aan de serverzijde.

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Microsoft Message Analyzer gebruiken om logboekgegevens te analyseren

Microsoft Message Analyzer is een hulpmiddel voor het vastleggen, weergeven en analyseren van protocolberichtenverkeer, gebeurtenissen en andere systeem- of toepassingsberichten in probleemoplossings- en diagnostische scenario's. Message Analyzer stelt u ook in staat om gegevens uit logboek- en opgeslagen traceringsbestanden te laden, te aggregeren en analyseren. Zie [Operating Guide voor Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)voor meer informatie over Message Analyzer.

Message Analyzer bevat assets voor Azure Storage waarmee u server-, client- en netwerklogboeken analyseren. In deze sectie bespreken we hoe we deze tools kunnen gebruiken om het probleem van laag percentage succes in de opslaglogboeken aan te pakken.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Message Analyzer en de Azure Storage Assets downloaden en installeren

1. Download [Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) vanuit het Microsoft Download center en voer het installatieprogramma uit.
2. Start Message Analyzer.
3. Selecteer **Asset Manager**in het menu **Extra** . Selecteer in het dialoogvenster Beheer van **beheer** de optie **Downloads**en filter vervolgens op Azure **Storage**. U ziet de Azure Storage Assets, zoals in de onderstaande afbeelding.
4. Klik **op Alle weergegeven items synchroniseren** om de Azure Storage Assets te installeren. De beschikbare activa omvatten:
   * **Kleurregels voor Azure Storage:** Met de kleurregels voor Azure Storage u speciale filters definiëren die kleur-, tekst- en lettertypestijlen gebruiken om berichten te markeren die specifieke informatie in een trace bevatten.
   * **Azure-opslagdiagrammen:** Azure Storage-diagrammen zijn vooraf gedefinieerde grafieken die gegevens in grafiekserverlogboeken bevatten. Als u op dit moment Azure Storage-diagrammen wilt gebruiken, u de serverlogboek en het analyseraster alleen laden.
   * **Azure Storage Parsers:** De Azure Storage parsers ontzien de Azure Storage-client, server en HTTP-logboeken om ze weer te geven in het analyseraster.
   * **Azure-opslagfilters:** Azure Storage-filters zijn vooraf gedefinieerde criteria die u gebruiken om uw gegevens op te vragen in het analyseraster.
   * **Indelingen voor azure-opslagweergave:** Indelingen van azure storage-weergave zijn vooraf gedefinieerde kolomindelingen en groeperingen in het analyseraster.
5. Start Message Analyzer opnieuw nadat u de assets hebt geïnstalleerd.

![Message Analyzer Asset Manager](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Installeer alle Azure Storage-elementen die worden weergegeven in het kader van deze zelfstudie.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Uw logboekbestanden importeren in Message Analyzer

U al uw opgeslagen logboekbestanden (serverzijde, clientzijde en netwerk) importeren in één sessie in Microsoft Message Analyzer voor analyse.

1. Klik **in het** menu Bestand in Microsoft Message Analyzer op **Nieuwe sessie**en klik vervolgens op **Lege sessie**. Voer in het dialoogvenster **Nieuwe sessie** een naam in voor uw analysesessie. Klik in het deelvenster **Sessiedetails** op de knop **Bestanden.**
2. Als u de netwerktraceringsgegevens wilt laden die door Message Analyzer zijn gegenereerd, klikt u op **Bestanden toevoegen**, bladert u naar de locatie waar u het .matp-bestand hebt opgeslagen vanuit uw webtraceringssessie, selecteert u het .matp-bestand en klikt u op **Openen**.
3. Als u de loggegevens aan de serverzijde wilt laden, klikt u op **Bestanden toevoegen**, bladert u naar de locatie waar u uw serverlogboeken hebt gedownload, selecteert u de logboekbestanden voor het tijdsbereik dat u wilt analyseren en klikt u op **Openen**. Stel vervolgens in het deelvenster **Sessiedetails** de vervolgkeuzelijst **Tekstlogboekconfiguratie** in voor elk logbestand aan de serverzijde op **AzureStorageLog** om ervoor te zorgen dat Microsoft Message Analyzer het logboekbestand correct kan ontleden.
4. Als u de logboekgegevens aan de clientzijde wilt laden, klikt u op **Bestanden toevoegen,** bladert u naar de locatie waar u uw logboeken aan de clientzijde hebt opgeslagen, selecteert u de logboekbestanden die u wilt analyseren en klikt u op **Openen**. Stel vervolgens in het deelvenster **Sessiedetails** de vervolgkeuzelijst **Tekstlogboekconfiguratie** in voor elk logboekbestand aan de clientzijde op **AzureStorageClientDotNetV4** om ervoor te zorgen dat Microsoft Message Analyzer het logboekbestand correct kan ontleden.
5. Klik **op Start** in het dialoogvenster Nieuwe **sessie** om de logboekgegevens te laden en te ontwenen. De logboekgegevens worden weergegeven in het Analyseraster voor berichtenanalyse.

De onderstaande afbeelding toont een voorbeeldsessie die is geconfigureerd met server-, client- en netwerktraceringslogboekbestanden.

![Sessie berichtanalysesessie configureren](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Houd er rekening mee dat Message Analyzer logbestanden laadt in het geheugen. Als u een grote set logboekgegevens hebt, wilt u deze filteren om de beste prestaties van Message Analyzer te krijgen.

Bepaal eerst het tijdsbestek dat u wilt beoordelen en houd dit tijdsbestek zo klein mogelijk. In veel gevallen wilt u een periode van minuten of uren te herzien op zijn hoogst. Importeer de kleinste set logboeken die aan uw behoeften kunnen voldoen.

Als u nog steeds een grote hoeveelheid logboekgegevens hebt, u een sessiefilter opgeven om uw logboekgegevens te filteren voordat u deze laadt. Selecteer in het vak **Sessiefilter** de knop **Bibliotheek** om een vooraf gedefinieerd filter te kiezen. Kies bijvoorbeeld **Global Time Filter I** uit de Azure Storage-filters om te filteren op een tijdsinterval. Vervolgens u de filtercriteria bewerken om de begin- en eindtijdstempel op te geven voor het interval dat u wilt zien. U ook filteren op een bepaalde statuscode. U er bijvoorbeeld voor kiezen om alleen logboekvermeldingen te laden wanneer de statuscode 404 is.

Zie [Berichtengegevens](https://technet.microsoft.com/library/dn772437.aspx) ophalen op TechNet voor meer informatie over het importeren van logboekgegevens in Microsoft Message Analyzer.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>De clientaanvraag-id gebruiken om logboekbestandsgegevens te correleren

De Azure Storage Client Library genereert automatisch een unieke clientaanvraag-id voor elk verzoek. Deze waarde wordt geschreven naar het clientlogboek, het serverlogboek en de netwerktracering, zodat u deze gebruiken om gegevens te correleren in alle drie de logboeken binnen Message Analyzer. Zie [Clientaanvraag-id](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) voor aanvullende informatie over de clientaanvraag-id.

In de onderstaande secties wordt beschreven hoe u vooraf geconfigureerde en aangepaste lay-outweergaven gebruiken om gegevens te correleren en groeperen op basis van de clientaanvraag-id.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Een weergave-indeling selecteren die u wilt weergeven in het analyseraster

De opslagelementen voor Message Analyzer bevatten Indelingen voor Azure-opslagweergave, die vooraf geconfigureerde weergaven zijn die u gebruiken om uw gegevens weer te geven met nuttige groeperingen en kolommen voor verschillende scenario's. U ook aangepaste weergave-indelingen maken en deze opslaan voor hergebruik.

De afbeelding hieronder toont het menu **Indeling weergeven,** beschikbaar door **Indeling weergeven** te selecteren op het lint van de werkbalk. De weergave-indelingen voor Azure Storage worden gegroepeerd onder het Azure **Storage-knooppunt** in het menu. U `Azure Storage` zoeken in het zoekvak om alleen te filteren op indelingen van azure-opslagweergave. U de ster naast een weergave-indeling ook selecteren om deze favoriet te maken en deze boven aan het menu weer te geven.

![Menu Indeling weergeven](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Selecteer om te beginnen **Gegroepeerd op ClientRequestID en Module**. Deze weergaveindeling groepeert loggegevens uit alle drie de logboeken eerst op clientaanvraag-id, vervolgens op bronlogboekbestand (of **Module** in Message Analyzer). Met deze weergave u inzoomen op een bepaalde clientaanvraag-id en gegevens bekijken van alle drie de logboekbestanden voor die clientaanvraag-id.

De onderstaande afbeelding toont deze indelingsweergave die is toegepast op de voorbeeldlogboekgegevens, waarbij een subset van kolommen wordt weergegeven. U zien dat voor een bepaalde clientaanvraag-id het analyseraster gegevens uit het clientlogboek, het serverlogboek en de netwerktracering weergeeft.

![Indeling voor Azure-opslagweergave](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Verschillende logboekbestanden hebben verschillende kolommen, dus wanneer gegevens van meerdere logboekbestanden worden weergegeven in het analyseraster, bevatten sommige kolommen mogelijk geen gegevens voor een bepaalde rij. In de bovenstaande afbeelding worden bijvoorbeeld geen gegevens weergegeven voor de kolommen **Timestamp,** **TimeElaps,** **Source**en **Destination,** omdat deze kolommen niet in het clientlogboek voorkomen, maar wel in het netwerktracering bestaan. Op dezelfde manier worden in de kolom **Tijdstempel** tijdstempelgegevens uit het serverlogboek weergegeven, maar worden er geen gegevens weergegeven voor de kolommen **TimeElaps,** **Source**en **Destination,** die geen deel uitmaken van het serverlogboek.
>
>

Naast het gebruik van de indelingen van de Azure Storage-weergave, u ook uw eigen weergave-indelingen definiëren en opslaan. U andere gewenste velden selecteren voor het groeperen van gegevens en de groepering opslaan als onderdeel van uw aangepaste lay-out.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Kleurregels toepassen op het analyseraster

De opslagelementen bevatten ook kleurregels, die een visueel middel bieden om verschillende soorten fouten in het analyseraster te identificeren. De vooraf gedefinieerde kleurregels zijn van toepassing op HTTP-fouten, zodat ze alleen worden weergegeven voor het serverlogboek en de netwerktracering.

Als u kleurregels wilt toepassen, selecteert u **Kleurregels** op het werkbalklint. U ziet de kleurregels voor Azure Storage in het menu. Selecteer **clientfouten (StatusCode tussen 400 en 499) voor**de zelfstudie, zoals in de onderstaande afbeelding wordt weergegeven.

![Indeling voor Azure-opslagweergave](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Naast het gebruik van de kleurregels voor Azure Storage u ook uw eigen kleurregels definiëren en opslaan.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Logboekgegevens groeperen en filteren om fouten met 400 bereik te vinden

Vervolgens groeperen en filteren we de loggegevens om alle fouten in het 400-bereik te vinden.

1. Zoek de kolom **StatusCode** in het analyseraster, klik met de rechtermuisknop op de kolomkop en selecteer **Groeperen**.
2. Groepeer vervolgens de kolom **ClientRequestId.** U ziet dat de gegevens in het analyseraster nu zijn geordend op statuscode en op clientaanvraag-id.
3. Het venster van het gereedschap Weergavefilter weergeven als deze nog niet is weergegeven. Selecteer op het lint van de werkbalk **Windows,** en **vervolgens Filter weergeven**.
4. Als u de logboekgegevens wilt filteren om slechts 400 bereikfouten weer te geven, voegt u de volgende filtercriteria toe aan het venster **Weergavefilter** en klikt u op **Toepassen:**

    `(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)`

De onderstaande afbeelding toont de resultaten van deze groepering en filter. Als u bijvoorbeeld het **clientRequestID-veld** onder de groepering voor statuscode 409 uitbreidt, wordt een bewerking weergegeven die heeft geleid tot die statuscode.

![Indeling voor Azure-opslagweergave](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Nadat u dit filter hebt toegepast, ziet u dat rijen uit het clientlogboek zijn uitgesloten, omdat het clientlogboek geen **statuscodekolom** bevat. Om te beginnen bekijken we de server- en netwerktraceringslogboeken om 404 fouten te vinden en gaan we terug naar het clientlogboek om de clientbewerkingen te onderzoeken die tot hen hebben geleid.

> [!NOTE]
> U filteren op de kolom **StatusCode** en nog steeds gegevens weergeven uit alle drie de logboeken, inclusief het clientlogboek, als u een expressie toevoegt aan het filter met logboekvermeldingen waarbij de statuscode null is. Gebruik het als volgt om deze filterexpressie te construeren:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Met dit filter worden alle rijen uit het clientlogboek en alleen rijen uit het serverlogboek en het HTTP-logboek geretourneerd waar de statuscode groter is dan 400. Als u deze toepast op de weergave-indeling gegroepeerd op clientaanvraag-id en module, u zoeken of bladeren door de logboekvermeldingen om deze te vinden waar alle drie de logboeken zijn weergegeven.

### <a name="filter-log-data-to-find-404-errors"></a>Loggegevens filteren om 404 fouten te vinden

De opslagelementen bevatten vooraf gedefinieerde filters die u gebruiken om logboekgegevens te beperken om de fouten of trends te vinden die u zoekt. Vervolgens passen we twee vooraf gedefinieerde filters toe: een die de server- en netwerktraceringslogboeken filtert op 404 fouten en een filters die de gegevens op een bepaald tijdbereik filtert.

1. Het venster van het gereedschap Weergavefilter weergeven als deze nog niet is weergegeven. Selecteer op het lint van de werkbalk **Windows,** en **vervolgens Filter weergeven**.
2. Selecteer in het venster Filter weergeven `Azure Storage` de optie **Bibliotheek**en zoek op om de Azure-opslagfilters te zoeken. Selecteer het filter voor **404 (Niet gevonden) berichten in alle logboeken.**
3. Geef het menu **Bibliotheek** opnieuw weer en zoek en selecteer het **globale tijdfilter**.
4. Bewerk de tijdstempels die in het filter worden weergegeven, naar het bereik dat u wilt weergeven. Dit zal helpen om het bereik van de gegevens te analyseren te beperken.
5. Het filter moet lijken op het onderstaande voorbeeld. Klik **op Toepassen** om het filter toe te passen op het analyseraster.

    `((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)`

    ![Indeling voor Azure-opslagweergave](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Uw loggegevens analyseren

Nu u uw gegevens hebt gegroepeerd en gefilterd, u de details onderzoeken van afzonderlijke aanvragen die 404 fouten hebben gegenereerd. In de huidige weergave-indeling worden de gegevens gegroepeerd op clientaanvraag-ID en vervolgens op logboekbron. Aangezien we filteren op aanvragen waarbij het veld StatusCode 404 bevat, zien we alleen de server- en netwerktraceringsgegevens, niet de gegevens van het clientlogboek.

De onderstaande afbeelding toont een specifieke aanvraag waarbij een Get Blob-bewerking een 404 opleverde omdat de blob niet bestond. Houd er rekening mee dat sommige kolommen uit de standaardweergave zijn verwijderd om de relevante gegevens weer te geven.

![Logboeken voor gefilterde server- en netwerktracering](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Vervolgens correleren we deze clientaanvraag-id met de clientlogboekgegevens om te zien welke acties de client heeft ondernomen toen de fout is uitgevoerd. U een nieuwe analyserasterweergave voor deze sessie weergeven om de clientlogboekgegevens weer te geven, die op een tweede tabblad worden geopend:

1. Kopieer eerst de waarde van het veld **ClientRequestId** naar het klembord. U dit doen door een van beide rijs te selecteren, het veld **ClientRequestId** te lokaliseren, met de rechtermuisknop op de gegevenswaarde te klikken en **'ClientRequestId'** te kopiëren.
2. Selecteer op het lint van de werkbalk **Nieuwe viewer**en selecteer **Vervolgens Analyseraster** om een nieuw tabblad te openen. Op het nieuwe tabblad worden alle gegevens in uw logboekbestanden weergegeven, zonder groeperings-, filter- of kleurregels.
3. Selecteer op het lint van de werkbalk **Weergave-indeling**en selecteer **Alle .NET-clientkolommen** onder de sectie **Azure Storage.** Deze weergave-indeling toont gegevens uit het clientlogboek en de server- en netwerktraceringslogboeken. Standaard wordt het gesorteerd in de kolom **MessageNumber.**
4. Zoek vervolgens in het clientlogboek naar de clientaanvraag-id. Selecteer op het lint van de werkbalk **Berichten zoeken**en geef vervolgens een aangepast filter op op de clientaanvraag-id in het veld **Zoeken.** Gebruik deze syntaxis voor het filter en geef uw eigen clientaanvraag-id op:

    `*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"`

Message Analyzer zoekt en selecteert de eerste logboekvermelding waarbij de zoekcriteria overeenkomen met de clientaanvraag-id. In het clientlogboek zijn er verschillende vermeldingen voor elke clientaanvraag-id, dus u ze groeperen op het **clientRequestId-veld** om het gemakkelijker te maken om ze allemaal samen te bekijken. De onderstaande afbeelding toont alle berichten in het clientlogboek voor de opgegeven clientaanvraag-id.

![Clientlogboek met 404 fouten](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Met behulp van de gegevens die worden weergegeven in de weergave-indelingen in deze twee tabbladen, u de aanvraaggegevens analyseren om te bepalen wat de fout mogelijk heeft veroorzaakt. U ook kijken naar aanvragen die aan deze vooraf gingen om te zien of een eerdere gebeurtenis mogelijk heeft geleid tot de fout van 404. U bijvoorbeeld de clientlogboekvermeldingen voorafgaand aan deze clientaanvraag-id controleren om te bepalen of de blob mogelijk is verwijderd of dat de fout te wijten was aan de clienttoepassing die een CreateIfNotExists-API aanroept op een container of blob. In het clientlogboek u het adres van de blob vinden in het veld **Beschrijving.** in de server- en netwerktraceringslogboeken wordt deze informatie weergegeven in het veld **Overzicht.**

Zodra u het adres van de blob weet die de 404-fout heeft opgeleverd, u het verder onderzoeken. Als u in de logboekvermeldingen zoekt naar andere berichten die zijn gekoppeld aan bewerkingen op dezelfde blob, u controleren of de client de entiteit eerder heeft verwijderd.

## <a name="analyze-other-types-of-storage-errors"></a>Andere typen opslagfouten analyseren

Nu u bekend bent met het gebruik van Message Analyzer om uw logboekgegevens te analyseren, u andere typen fouten analyseren met behulp van weergave-indelingen, kleurregels en zoeken/filteren. De onderstaande tabellen bevatten een aantal problemen die u tegenkomen en de filtercriteria die u gebruiken om ze te vinden. Zie [Berichtgegevens filteren](https://technet.microsoft.com/library/jj819365.aspx)voor meer informatie over het maken van filters en de filtertaal van de Message Analyzer.

| Om te onderzoeken... | Filterexpressie gebruiken... | Expressie is van toepassing op logboek (client, server, netwerk, alles) |
| --- | --- | --- |
| Onverwachte vertragingen in de bezorging van berichten in een wachtrij |AzureStorageClientDotNetV4.Description bevat 'Mislukte bewerking opnieuw proberen'. |Client |
| HTTP-toename in PercentThrottlingError |HTTP. Response.StatusCode == 500 &#124;&#124; HTTP. Response.StatusCode == 503 |Netwerk |
| Toename van percenttime-outerror |HTTP. Response.StatusCode == 500 |Netwerk |
| Toename van PercentTimeoutError (allen) |*Statuscode == 500 |Alle |
| Toename van percentnetworkfout |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Client |
| HTTP 403-berichten (verboden) |HTTP. Response.StatusCode == 403 |Netwerk |
| HTTP 404-berichten (niet gevonden) |HTTP. Response.StatusCode == 404 |Netwerk |
| 404 (allen) |*Statuscode == 404 |Alle |
| Een probleem met de verificatie van een SAS (Shared Access Signature) |AzureStorageLog.RequestStatus == "SASAuthorizationError" |Netwerk |
| HTTP 409-berichten (conflict) |HTTP. Response.StatusCode == 409 |Netwerk |
| 409 (allen) |*Statuscode == 409 |Alle |
| Lage procentenSucces- of analytics-logboekvermeldingen hebben bewerkingen met transactiestatus van ClientOtherErrors |AzureStorageLog.RequestStatus == "ClientOtherError" |server |
| Nagle Waarschuwing |((AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1,5)) en (AzureStorageLog.RequestPacketSize <1460) en (AzureStorageLog.EndToEndlatencyMS - AzureLogStorage.ServerLatencyMS >= 200) |server |
| Tijdsbereik in server- en netwerklogboeken |#Timestamp >= 2014-10-20T16:36:38 en #Timestamp <= 2014-10-20T16:36:39 |Server, Netwerk |
| Tijdsbereik in serverlogboeken |AzureStorageLog.Timestamp >= 2014-10-20T16:36:38 en AzureStorageLog.Timestamp <= 2014-10-20T16:36:39 |server |

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over end-to-end-scenario's in Azure Storage:

* [Microsoft Azure Storage bewaken, problemen opsporen en oplossen](storage-monitoring-diagnosing-troubleshooting.md)
* [Storage Analytics](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Een Storage-account bewaken in de Azure-portal](storage-monitor-storage-account.md)
* [Gegevens overbrengen met het AzCopy-opdrachtregelhulpprogramma](storage-use-azcopy.md)
* [Microsoft Message Analyzer Operating Guide](https://technet.microsoft.com/library/jj649776.aspx) (Operationele handleiding voor Microsoft Message Analyzer)
