---
title: Problemen met gegevens bewerkingen oplossen met diagnose en Message Analyzer
titleSuffix: Azure Storage
description: Een zelf studie waarin wordt uitgelegd hoe u end-to-end kunt oplossen met Azure Opslaganalyse, AzCopy en micro soft Message Analyzer
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: normesta
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 69983502fb7d099f474fb1c4c084f5d381a173e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76314756"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>End-to-end-probleemoplossing op basis van metrische gegevens en logboekregistratie van Azure Storage, AzCopy en Message Analyzer

[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnose en probleem oplossing is een belang rijke vaardigheid voor het maken en ondersteunen van client toepassingen met Microsoft Azure Storage. Vanwege de gedistribueerde aard van een Azure-toepassing, zijn diagnose-en probleemoplossings fouten en prestatie problemen wellicht ingewik kelder dan in traditionele omgevingen.

In deze zelf studie wordt gedemonstreerd hoe u bepaalde fouten aanduidt die van invloed kunnen zijn op de prestaties en de fouten van end-to-end met hulp middelen van micro soft en Azure Storage oplossen om de client toepassing te optimaliseren.

In deze zelf studie kunt u een end-to-end probleemoplossings scenario verkennen. Zie [Microsoft Azure Storage controleren, diagnosticeren en problemen oplossen](storage-monitoring-diagnosing-troubleshooting.md)voor meer informatie over het oplossen van problemen met Azure Storage-toepassingen.

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Hulpprogram ma's voor het oplossen van problemen met Azure Storage toepassingen

Als u client toepassingen wilt oplossen met Microsoft Azure Storage, kunt u een combi natie van hulpprogram ma's gebruiken om te bepalen wanneer er een probleem is opgetreden en wat de oorzaak van het probleem is. Tot deze hulpmiddelen behoren onder meer:

* **Azure Opslaganalyse**. [Azure Opslaganalyse](/rest/api/storageservices/Storage-Analytics) biedt metrische gegevens en logboek registratie voor Azure Storage.

  * **Metrische opslag gegevens** traceren de metrische gegevens van de trans actie en de metrische gegevens over de capaciteit voor uw opslag account. Met metrische gegevens kunt u bepalen hoe uw toepassing wordt uitgevoerd op basis van verschillende verschillende metingen. Zie [Opslaganalyse het tabel schema metrische](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) gegevens voor meer informatie over de typen metrische gegevens die worden bijgehouden door Opslaganalyse.
  * **Opslag logboek registratie** registreert elke aanvraag voor de Azure Storage services naar een logboek aan de server zijde. Het logboek traceert gedetailleerde gegevens voor elke aanvraag, met inbegrip van de uitgevoerde bewerking, de status van de bewerking en latentie-informatie. Zie [Opslaganalyse-logboek indeling](/rest/api/storageservices/Storage-Analytics-Log-Format) voor meer informatie over de aanvraag-en antwoord gegevens die naar de logboeken worden geschreven door Opslaganalyse.

* **Azure Portal**. U kunt metrische gegevens en logboek registratie configureren voor uw opslag account in de [Azure Portal](https://portal.azure.com). U kunt ook grafieken en grafieken weer geven die laten zien hoe uw toepassing in de loop van de tijd wordt uitgevoerd en waarschuwingen configureren om u te waarschuwen als uw toepassing anders werkt dan verwacht voor een opgegeven metriek.

    Zie [een opslag account bewaken in de Azure Portal](storage-monitor-storage-account.md) voor meer informatie over het configureren van bewaking in de Azure Portal.
* **AzCopy**. Server logboeken voor Azure Storage worden opgeslagen als blobs, zodat u AzCopy kunt gebruiken om de logboek-blobs te kopiëren naar een lokale map voor analyse met behulp van micro soft Message Analyzer. Zie [gegevens overdragen met het AzCopy-opdracht regel programma](storage-use-azcopy.md) voor meer informatie over AzCopy.
* **Micro soft Message Analyzer**. Message Analyzer is een hulp programma waarmee logboek bestanden worden gebruikt en logboek gegevens worden weer gegeven in een visuele indeling waarmee logboek gegevens eenvoudig kunnen worden gefilterd, gezocht en gegroepeerd in nuttige sets die u kunt gebruiken om fouten en prestatie problemen te analyseren. Zie de [micro soft Message Analyzer-bedienings handleiding](https://technet.microsoft.com/library/jj649776.aspx) voor meer informatie over Message Analyzer.

## <a name="about-the-sample-scenario"></a>Over het voorbeeld scenario

In deze zelf studie wordt een scenario onderzocht waarbij Azure Storage metrische gegevens een laag percentage slagings percentage voor een toepassing die Azure Storage aanroept. De metrische waarde voor het percentage geslaagde percentages (weer gegeven als **PercentSuccess** in de [Azure Portal](https://portal.azure.com) en in de tabel met metrische gegevens) traceert bewerkingen die zijn gelukt, maar die een HTTP-status code retour neren die groter is dan 299. In de logboek bestanden voor opslag aan de server zijde worden deze bewerkingen vastgelegd met de transactie status **ClientOtherErrors**. Voor meer informatie over de metrische gegevens van het succes volle percentage, Zie [metrische gegevens laag PercentSuccess of Analytics-logboek vermeldingen hebben bewerkingen met de transactie status ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Azure Storage bewerkingen kunnen HTTP-status codes retour neren die groter zijn dan 299 als onderdeel van hun normale functionaliteit. Maar deze fouten geven aan dat u uw client toepassing mogelijk kunt optimaliseren voor betere prestaties.

In dit scenario beschouwen we een laag percentage succes dat lager is dan 100%. U kunt echter een ander meet niveau kiezen, afhankelijk van uw behoeften. Tijdens het testen van uw toepassing wordt u aangeraden een basislijn tolerantie te maken voor uw belangrijkste prestatie gegevens. U kunt bijvoorbeeld bepalen op basis van testen dat uw toepassing een consistent percentage van 90% of 85% moet hebben. Als uw metrische gegevens tonen dat de toepassing afwijkt van dat aantal, kunt u onderzoeken wat de oorzaak van de verhoging kan zijn.

Als we in ons voorbeeld scenario hebben vastgesteld dat de waarde voor het percentage succes percentage lager is dan 100%, zullen we de logboeken onderzoeken om de fouten te vinden die overeenkomen met de metrische gegevens en ze gebruiken om erachter te komen wat het succes percentage van het lagere percentage veroorzaakt. We kijken vooral naar fouten in het 400-bereik. Daarna onderzoeken we meer dan 404 (niet gevonden) fouten.

### <a name="some-causes-of-400-range-errors"></a>Enkele oorzaken van 400-bereik fouten

In de onderstaande voor beelden ziet u een steek proef van enkele 400-bereik fouten voor aanvragen voor Azure Blob Storage en de mogelijke oorzaken. Een van deze fouten, evenals fouten in het bereik 300 en het bereik van 500, kan bijdragen aan een succes percentage van minder procent.

Houd er rekening mee dat de onderstaande lijsten ver van zijn. Zie [status-en fout codes](https://msdn.microsoft.com/library/azure/dd179382.aspx) op MSDN voor meer informatie over algemene Azure Storage fouten en over fouten die specifiek zijn voor elk van de opslag Services.

#### <a name="status-code-404-not-found-examples"></a>Voor beelden van status code 404 (niet gevonden)

Treedt op wanneer een lees bewerking voor een container of BLOB mislukt omdat de BLOB of container niet is gevonden.

* Treedt op als een container of BLOB is verwijderd door een andere client vóór deze aanvraag.
* Deze gebeurtenis treedt op als u een API-aanroep gebruikt die de container of BLOB maakt nadat u hebt gecontroleerd of deze bestaat. De CreateIfNotExists-Api's maken eerst een HEAD-oproep om te controleren of de container of BLOB bestaat. Als deze niet bestaat, wordt er een 404-fout geretourneerd, waarna een tweede PUT-aanroep wordt gedaan om de container of BLOB te schrijven.

#### <a name="status-code-409-conflict-examples"></a>Voor beelden van status code 409 (conflict)

* Deze gebeurtenis treedt op als u een Create API gebruikt om een nieuwe container of BLOB te maken, zonder eerst te controleren op bestaan en er al een container of BLOB met die naam bestaat.
* Deze gebeurtenis treedt op als een container wordt verwijderd en u probeert een nieuwe container met dezelfde naam te maken voordat de verwijderings bewerking is voltooid.
* Treedt op als u een lease opgeeft voor een container of BLOB en er al een lease aanwezig is.

#### <a name="status-code-412-precondition-failed-examples"></a>Voor beelden van status code 412 (voor waarde is mislukt)

* Deze gebeurtenis treedt op wanneer niet wordt voldaan aan de voor waarde die is opgegeven door een voorwaardelijke koptekst.
* Treedt op wanneer de opgegeven lease-ID niet overeenkomt met de lease-ID van de container of BLOB.

## <a name="generate-log-files-for-analysis"></a>Logboek bestanden genereren voor analyse

In deze zelf studie gebruiken we Message Analyzer voor het werken met drie verschillende typen logboek bestanden, maar u kunt er ook voor kiezen om met een van deze opties te werken:

* Het **server logboek**, dat wordt gemaakt wanneer u Azure Storage logboek registratie inschakelt. Het server logboek bevat gegevens over elke bewerking, die wordt aangeroepen op een van de Azure Storage services-blob, wachtrij, tabel en bestand. In het server logboek wordt aangegeven welke bewerking werd aangeroepen en welke status code is geretourneerd, evenals andere details over de aanvraag en het antwoord.
* Het **.net-client logboek**, dat wordt gemaakt wanneer u logboek registratie aan client zijde inschakelt vanuit uw .NET-toepassing. Het client logboek bevat gedetailleerde informatie over hoe de client de aanvraag voorbereidt en de respons ontvangt en verwerkt.
* Het **tracerings logboek van het http-netwerk**, waarmee gegevens worden verzameld over HTTP/HTTPS-aanvraag-en respons gegevens, met inbegrip van bewerkingen op basis van Azure Storage. In deze zelf studie genereren we de netwerk tracering via Message Analyzer.

### <a name="configure-server-side-logging-and-metrics"></a>Logboek registratie en metrische gegevens aan server zijde configureren

Eerst moet u de logboeken en metrische gegevens van Azure Storage configureren, zodat de service aan de kant van het analyseren van informatie wordt uitgevoerd. U kunt logboek registratie en metrische gegevens op verschillende manieren configureren: via de [Azure Portal](https://portal.azure.com), met behulp van Power shell of via een programma. Zie [metrische gegevens inschakelen](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) en [logboek registratie inschakelen](storage-analytics-logging.md#enable-storage-logging) voor meer informatie over het configureren van logboek registratie en metrische gegevens.

### <a name="configure-net-client-side-logging"></a>Logboek registratie voor .NET-client zijde configureren

Als u logboek registratie aan client zijde voor een .NET-toepassing wilt configureren, schakelt u .NET Diagnostics in het configuratie bestand van de toepassing in (web. config of app. config). Zie [logboek registratie aan de client zijde met de .net Storage-client bibliotheek](https://msdn.microsoft.com/library/azure/dn782839.aspx) en [logboek registratie aan client zijde met de Microsoft Azure Storage SDK voor Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) op MSDN voor meer informatie.

Het logboek aan de client zijde bevat gedetailleerde informatie over hoe de client de aanvraag voorbereidt en de respons ontvangt en verwerkt.

In de opslag-client bibliotheek worden logboek gegevens aan de client zijde opgeslagen op de locatie die is opgegeven in het configuratie bestand van de toepassing (web. config of app. config).

### <a name="collect-a-network-trace"></a>Een netwerk tracering verzamelen

U kunt Message Analyzer gebruiken om een HTTP/HTTPS-netwerk tracering te verzamelen terwijl uw client toepassing wordt uitgevoerd. Message Analyzer maakt gebruik van [Fiddler](https://www.telerik.com/fiddler) op de back-end. Voordat u de netwerk tracering verzamelt, raden we u aan om Fiddler te configureren voor het vastleggen van niet-versleuteld HTTPS-verkeer:

1. Installeer [Fiddler](https://www.telerik.com/download/fiddler).
2. Start Fiddler.
3. Selecteer **extra | Opties voor Fiddler**.
4. Controleer in het dialoog venster Opties of het **vastleggen van HTTPS-verbindingen** en **ontsleuteling van HTTPS-verkeer** beide zijn ingeschakeld, zoals hieronder wordt weer gegeven.

![Opties voor Fiddler configureren](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Voor de zelf studie moet u eerst een netwerk tracering verzamelen en opslaan in Message Analyzer en vervolgens een analyse sessie maken om de tracering en de logboeken te analyseren. Een netwerk tracering in Message Analyzer verzamelen:

1. In Message Analyzer selecteert u **bestand | Snelle tracering | Niet-versleutelde HTTPS**.
2. De tracering wordt onmiddellijk gestart. Selecteer **stoppen** om de tracering te stoppen, zodat we deze zo kunnen configureren dat alleen opslag verkeer wordt getraceerd.
3. Selecteer **bewerken** om de traceer sessie te bewerken.
4. Selecteer de koppeling **configureren** rechts van de **micro soft-PEF-webproxy etw-** provider.
5. Klik op het tabblad **provider** in het dialoog venster **Geavanceerde instellingen** .
6. Geef in het veld **hostnaam filteren** uw opslag eindpunten op, gescheiden door spaties. U kunt bijvoorbeeld uw eind punten als volgt opgeven. Wijzig `storagesample` de naam van uw opslag account:

    `storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net`

7. Sluit het dialoog venster en klik op **opnieuw opstarten** om te beginnen met het verzamelen van de tracering met het hostname-filter op locatie, zodat alleen Azure storage netwerk verkeer wordt opgenomen in de tracering.

> [!NOTE]
> Nadat u klaar bent met het verzamelen van uw netwerk tracering, wordt u ten zeerste aangeraden de instellingen die u in Fiddler hebt gewijzigd, te herstellen om HTTPS-verkeer te ontsleutelen. Schakel in het dialoog venster Opties voor Fiddler het selectie vakje **HTTPS-verbindingen vastleggen** en **HTTPS-verkeer ontsleutelen** uit.

Zie [het gebruik van de functies voor het traceren van netwerken](https://technet.microsoft.com/library/jj674819.aspx) op TechNet voor meer informatie.

## <a name="review-metrics-data-in-the-azure-portal"></a>Metrische gegevens controleren in de Azure Portal

Zodra uw toepassing gedurende een bepaalde periode actief is, kunt u de metrische grafieken bekijken die in de [Azure Portal](https://portal.azure.com) worden weer gegeven om na te gaan hoe uw service is uitgevoerd.

Ga eerst naar uw opslag account in de Azure Portal. Er wordt standaard een bewakings grafiek met de waarde voor het **percentage geslaagde** gegevens weer gegeven op de Blade account. Als u de grafiek eerder hebt gewijzigd om verschillende metrische gegevens weer te geven, voegt u het percentage voor het **slagen van succes** toe.

U ziet nu het **succes percentage** in het bewakings diagram, samen met andere metrische gegevens die u mogelijk hebt toegevoegd. In het scenario wordt het volgende onderzocht door de logboeken in Message Analyzer te analyseren. het percentage succes is lager dan 100%.

Zie [metrische grafieken aanpassen](storage-monitor-storage-account.md#customize-metrics-charts)voor meer informatie over het toevoegen en aanpassen van metrische grafieken.

> [!NOTE]
> Het kan enige tijd duren voordat uw metrische gegevens in de Azure Portal worden weer gegeven nadat u metrische opslag waarden hebt ingeschakeld. Dit komt doordat de metrische gegevens per uur voor het vorige uur niet worden weer gegeven in de Azure Portal totdat het huidige uur is verstreken. Ook worden er geen minuut gegevens weer gegeven in de Azure Portal. Afhankelijk van wanneer u metrische gegevens inschakelt, kan het tot twee uur duren voordat de metriekgegevens worden weer gegeven.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>AzCopy gebruiken om server logboeken te kopiëren naar een lokale map

Azure Storage schrijft server logboek gegevens naar blobs, terwijl metrische waarden naar tabellen worden geschreven. Logboek-blobs zijn beschikbaar in de bekende `$logs` container voor uw opslag account. Logboek-blobs hebben een hiërarchische naam van jaar, maand, dag en uur, zodat u eenvoudig het tijds bereik kunt vinden dat u wilt onderzoeken. Zo is `storagesample` `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`in het account de container voor de logboek-blobs voor 01/02/2015, van 8-9 uur. De afzonderlijke blobs in deze container worden opeenvolgend benoemd, te beginnen met `000000.log`.

U kunt het opdracht regel programma AzCopy gebruiken om deze logboek bestanden aan de server zijde te downloaden naar een locatie van uw keuze op uw lokale computer. U kunt bijvoorbeeld de volgende opdracht gebruiken om de logboek bestanden te downloaden voor BLOB-bewerkingen die plaatsvonden op 2 januari 2015 naar de map `C:\Temp\Logs\Server`. Vervang `<storageaccountname>` door de naam van uw opslag account:

```azcopy
azcopy copy 'http://<storageaccountname>.blob.core.windows.net/$logs/blob/2015/01/02' 'C:\Temp\Logs\Server'  --recursive
```

AzCopy is beschikbaar voor downloaden op de pagina [Azure-down loads](https://azure.microsoft.com/downloads/) . Zie [gegevens overdragen met het opdracht regel programma AzCopy](storage-use-azcopy.md)voor meer informatie over het gebruik van AzCopy.

Zie [logboek gegevens van opslag logboeken downloaden](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata)voor meer informatie over het downloaden van logboeken aan de server zijde.

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Micro soft Message Analyzer gebruiken om logboek gegevens te analyseren

Micro soft Message Analyzer is een hulp programma voor het vastleggen, weer geven en analyseren van verkeer van protocol berichten, gebeurtenissen en andere systeem-of toepassings berichten in probleem oplossing en diagnostische scenario's. Met Message Analyzer kunt u ook gegevens laden, verzamelen en analyseren uit logboek bestanden en opgeslagen traceringen. Zie de [micro soft Message Analyzer-bedienings handleiding](https://technet.microsoft.com/library/jj649776.aspx)voor meer informatie over Message Analyzer.

Message Analyzer bevat assets voor Azure Storage waarmee u Server-, client-en netwerk Logboeken kunt analyseren. In deze sectie wordt uitgelegd hoe u deze hulpprogram ma's gebruikt om het probleem met het succes van een laag percentage in de opslag logboeken op te lossen.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Message Analyzer en de Azure Storage-assets downloaden en installeren

1. Down load [Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) van het micro soft Download centrum en voer het installatie programma uit.
2. Start Message Analyzer.
3. Selecteer in het menu **extra** de optie **Asset Manager**. Selecteer in het dialoog venster **Asset Manager** **down loads**en filter op **Azure Storage**. U ziet de Azure Storage assets, zoals in de onderstaande afbeelding wordt weer gegeven.
4. Klik op **alle weer gegeven items synchroniseren** om de Azure Storage assets te installeren. De beschik bare assets zijn onder andere:
   * **Azure Storage kleuren regels:** Met Azure Storage kleuren regels kunt u speciale filters definiëren die gebruikmaken van kleur, tekst en teken stijlen om berichten te markeren die specifieke informatie bevatten in een tracering.
   * **Azure Storage grafieken:** Azure Storage grafieken zijn vooraf gedefinieerde grafieken die logboek gegevens van een grafiek server. Houd er rekening mee dat u op dit moment Azure Storage-grafieken kunt gebruiken, maar u kunt het server logboek alleen laden in het analyse raster.
   * **Azure Storage parsers:** De Azure Storage-parsers parseren de Azure Storage client-, server-en HTTP-Logboeken om ze weer te geven in het analyse raster.
   * **Azure Storage filters:** Azure Storage filters zijn vooraf gedefinieerde criteria die u kunt gebruiken om uw gegevens in het analyse raster op te vragen.
   * **Indelingen van Azure Storage-weer gave:** Azure Storage weer gave-indelingen zijn vooraf gedefinieerde kolom indelingen en groeperingen in het analyse raster.
5. Start Message Analyzer opnieuw nadat u de assets hebt geïnstalleerd.

![Asset Manager van Message Analyzer](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Installeer alle Azure Storage assets die voor deze zelf studie worden weer gegeven.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Uw logboek bestanden importeren in Message Analyzer

U kunt al uw opgeslagen logboek bestanden (server-side, client-side en netwerk) importeren in één sessie in micro soft Message Analyzer voor analyse.

1. Klik in het menu **bestand** van micro soft Message Analyzer op **nieuwe sessie**en klik vervolgens op **lege sessie**. Voer in het dialoog venster **nieuwe sessie** een naam in voor uw analyse sessie. Klik in het deel venster **sessie Details** op de knop **bestanden** .
2. Als u de netwerk tracerings gegevens wilt laden die zijn gegenereerd door Message Analyzer, klikt u op **bestanden toevoegen**, bladert u naar de locatie waar u het. matp-bestand hebt opgeslagen vanuit uw web-tracerings sessie, selecteert u het. matp-bestand en klikt u op **openen**.
3. Als u de logboek gegevens aan de server zijde wilt laden, klikt u op **bestanden toevoegen**, bladert u naar de locatie waar u de logboeken aan de server zijde hebt gedownload, selecteert u de logboek bestanden voor het tijds bereik dat u wilt analyseren en klikt u op **openen**. Stel vervolgens in het deel venster **sessie Details** de vervolg keuzelijst voor de **configuratie van tekst logboeken** voor elk logboek bestand aan de server zijde in op **AzureStorageLog** om ervoor te zorgen dat micro soft Message Analyzer het logboek bestand correct kan parseren.
4. Als u de logboek gegevens aan de client zijde wilt laden, klikt u op **bestanden toevoegen**, bladert u naar de locatie waar u de logboeken aan de client zijde hebt opgeslagen, selecteert u de logboek bestanden die u wilt analyseren en klikt u op **openen**. Stel vervolgens in het deel venster **sessie Details** de vervolg keuzelijst voor de **configuratie van tekst logboeken** voor elk logboek bestand aan client zijde in op **AzureStorageClientDotNetV4** om ervoor te zorgen dat micro soft Message Analyzer het logboek bestand correct kan parseren.
5. Klik in het dialoog venster **nieuwe sessie** op **starten** om de logboek gegevens te laden en te parseren. De logboek gegevens worden weer gegeven in het analyse raster Message Analyzer.

In de onderstaande afbeelding ziet u een voor beeld van een sessie die is geconfigureerd met traceer logboek bestanden voor de server, de client en het netwerk.

![Message Analyzer-sessie configureren](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Houd er rekening mee dat Message Analyzer logboek bestanden laadt in het geheugen. Als u een grote set logboek gegevens hebt, wilt u deze filteren om de beste prestaties van Message Analyzer te verkrijgen.

Bepaal eerst het tijds bestek dat u wilt controleren en bewaar deze periode zo klein mogelijk. In veel gevallen kunt u het beste een periode van minuten of uren bekijken. Importeer de kleinste set logboeken die aan uw behoeften kan voldoen.

Als u nog steeds een grote hoeveelheid logboek gegevens hebt, wilt u mogelijk een sessie filter opgeven om uw logboek gegevens te filteren voordat u deze laadt. Selecteer in het vak **sessie filter** de knop **bibliotheek** om een vooraf gedefinieerd filter te kiezen. Kies bijvoorbeeld **Algemeen tijd filter I** van de Azure Storage filters om op een tijds interval te filteren. U kunt vervolgens de filter criteria bewerken om het begin-en eind punt op te geven voor het interval dat u wilt weer geven. U kunt ook filteren op een bepaalde status code. u kunt er bijvoorbeeld voor kiezen om alleen logboek vermeldingen te laden waarin de status code 404 is.

Zie [bericht gegevens ophalen](https://technet.microsoft.com/library/dn772437.aspx) op TechNet voor meer informatie over het importeren van logboek gegevens in micro soft Message Analyzer.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>De client aanvraag-ID gebruiken voor het correleren van de gegevens van het logboek bestand

De Azure Storage-client bibliotheek genereert automatisch een unieke client aanvraag-ID voor elke aanvraag. Deze waarde wordt geschreven naar het client logboek, het server logboek en de netwerk tracering, zodat u het kunt gebruiken om gegevens in alle drie de logboeken in Message Analyzer te correleren. Zie [client aanvraag-id](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) voor aanvullende informatie over de client aanvraag-id.

In de volgende secties wordt beschreven hoe u vooraf geconfigureerde en aangepaste indelings weergaven gebruikt voor het correleren en groeperen van gegevens op basis van de client aanvraag-ID.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Een weer gave-indeling selecteren om weer te geven in het analyse raster

De opslag assets voor Message Analyzer bevatten Azure Storage weer gave-indelingen. Dit zijn vooraf geconfigureerde weer gaven die u kunt gebruiken om uw gegevens weer te geven met nuttige groeperingen en kolommen voor verschillende scenario's. U kunt ook aangepaste weer gave-indelingen maken en deze opslaan voor hergebruik.

In de onderstaande afbeelding ziet u het menu **weer gave-indeling** , dat beschikbaar is door **weer gave-indeling** te selecteren in het lint van de werk balk. De weer gave-indelingen voor Azure Storage worden gegroepeerd onder het knoop punt **Azure Storage** in het menu. U kunt zoeken `Azure Storage` in het zoekvak om alleen te filteren op Azure Storage weergave-indelingen. U kunt ook het sterretje naast een weer gave-indeling selecteren om het een favoriet te maken en boven aan het menu weer te geven.

![Menu indeling weer geven](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Selecteer **gegroepeerd op ClientRequestID en module**om te beginnen met. Met deze weer gave-indeling worden logboek gegevens van alle drie de logboeken eerst gegroepeerd op client aanvraag-ID en vervolgens op bron logboek bestand (of **module** in Message Analyzer). Met deze weer gave kunt u inzoomen op een bepaalde client aanvraag-ID en gegevens van alle drie de logboek bestanden voor die client aanvraag-ID weer geven.

In de onderstaande afbeelding ziet u deze indelings weergave die is toegepast op de voorbeeld logboek gegevens, met een subset van kolommen die worden weer gegeven. U kunt zien dat voor een bepaalde client aanvraag-ID de gegevens uit het client logboek, het server logboek en de netwerk tracering worden weer gegeven in het analyse raster.

![Indeling van Azure Storage weer gave](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Verschillende logboek bestanden hebben verschillende kolommen, dus wanneer er gegevens uit meerdere logboek bestanden worden weer gegeven in het analyse raster, bevatten sommige kolommen mogelijk geen gegevens voor een bepaalde rij. In de bovenstaande afbeelding worden bijvoorbeeld in de client logboek rijen geen gegevens weer gegeven voor de kolommen **Time Stamp**, **TimeElapsed**, **Source**en **Destination** , omdat deze kolommen niet bestaan in het client logboek, maar wel in de netwerk tracering aanwezig zijn. Op dezelfde manier wordt met de **Time Stamp** -kolom gegevens uit het server logboek weer gegeven, maar er worden geen gegevens weer gegeven voor de kolommen **TimeElapsed**, **Source**en **Destination** , die geen deel uitmaken van het server logboek.
>
>

Naast het gebruik van de Azure Storage weer gave-indelingen, kunt u ook uw eigen weer gave-indelingen definiëren en opslaan. U kunt andere gewenste velden voor het groeperen van gegevens selecteren en de groepering ook opslaan als onderdeel van uw aangepaste indeling.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Kleur regels Toep assen op het analyse raster

De opslag assets omvatten ook kleur regels, die een visueel element bieden om verschillende typen fouten te identificeren in het analyse raster. De vooraf gedefinieerde kleur regels zijn van toepassing op HTTP-fouten, zodat ze alleen worden weer gegeven voor het server logboek en netwerk tracering.

Als u kleur regels wilt Toep assen, selecteert u **kleuren regels** op het lint van de werk balk. U ziet de Azure Storage kleur regels in het menu. Selecteer voor de zelf studie **client fouten (status code tussen 400 en 499)**, zoals wordt weer gegeven in de onderstaande afbeelding.

![Indeling van Azure Storage weer gave](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Naast het gebruik van de Azure Storage kleur regels, kunt u ook uw eigen kleur regels definiëren en opslaan.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Groeps-en filter logboek gegevens om 400-bereik fouten te vinden

Vervolgens groeperen en filteren we de logboek gegevens om alle fouten in het 400-bereik te vinden.

1. Zoek de kolom **status** code in het analyse raster, klik met de rechter muisknop op de kolomkop en selecteer **groep**.
2. Vervolgens groeperen in de kolom **ClientRequestId** . U ziet dat de gegevens in het analyse raster nu zijn ingedeeld op status code en op aanvraag-ID van client.
3. Geef het venster filter programma weer als dit nog niet wordt weer gegeven. Selecteer op het lint op de werk balk de optie **Windows**en vervolgens **weergave filter**.
4. Als u de logboek gegevens wilt filteren om alleen 400-bereik fouten weer te geven, voegt u de volgende filter criteria toe aan het venster **weergave filter** en klikt u op **Toep assen**:

    `(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)`

In de onderstaande afbeelding ziet u de resultaten van deze groepering en het filter. Als u het veld **ClientRequestID** onder de groepering uitbreidt voor status code 409, wordt bijvoorbeeld een bewerking weer gegeven die heeft geresulteerd in deze status code.

![Indeling van Azure Storage weer gave](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Nadat u dit filter hebt toegepast, ziet u dat rijen uit het client logboek worden uitgesloten, omdat het client logboek geen kolom **status** code bevat. Om te beginnen, zullen we de logboeken voor Server-en netwerk tracering bekijken om 404 fouten te vinden en vervolgens teruggaan naar het client logboek om de client bewerkingen te controleren die naar hen hebben geleid.

> [!NOTE]
> U kunt filteren op de kolom **status** code en nog steeds gegevens weer geven uit alle drie de logboeken, inclusief het client logboek, als u een expressie toevoegt aan het filter dat logboek vermeldingen bevat waarvan de status code null is. Als u deze filter expressie wilt maken, gebruikt u:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Dit filter retourneert alle rijen uit het client logboek en alleen rijen uit het server logboek en het HTTP-logboek waarbij de status code groter is dan 400. Als u het toepast op de weer gave-indeling gegroepeerd op client aanvraag-ID en module, kunt u de logboek vermeldingen doorzoeken of door lopen om te zoeken naar een locatie waar alle drie de logboeken worden weer gegeven.

### <a name="filter-log-data-to-find-404-errors"></a>Logboek gegevens filteren om 404-fouten te vinden

De opslag assets bevatten vooraf gedefinieerde filters die u kunt gebruiken om logboek gegevens te beperken om de fouten of trends te vinden die u zoekt. Daarna worden twee vooraf gedefinieerde filters toegepast: één waarmee de server-en netwerk traceer logboeken voor 404-fouten worden gefilterd en één waarmee de gegevens worden gefilterd op een opgegeven tijds bereik.

1. Geef het venster filter programma weer als dit nog niet wordt weer gegeven. Selecteer op het lint op de werk balk de optie **Windows**en vervolgens **weergave filter**.
2. In het venster weergave filter selecteert `Azure Storage` u **bibliotheek**en zoekt u naar de Azure Storage filters zoeken. Selecteer het filter voor **404 (niet gevonden) berichten in alle logboeken**.
3. Open het menu **bibliotheek** opnieuw en selecteer het **globale tijd filter**.
4. Bewerk de tijds tempels die in het filter worden weer gegeven, op het bereik dat u wilt weer geven. Zo kunt u het aantal gegevens dat moet worden geanalyseerd, beperken.
5. Het filter moet er ongeveer uitzien als in het onderstaande voor beeld. Klik op **Toep assen** om het filter toe te passen op het analyse raster.

    `((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)`

    ![Indeling van Azure Storage weer gave](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Uw logboek gegevens analyseren

Nu u uw gegevens hebt gegroepeerd en gefilterd, kunt u de details van afzonderlijke aanvragen bekijken die 404-fouten hebben gegenereerd. In de huidige weer gave-indeling worden de gegevens gegroepeerd op client aanvraag-ID en vervolgens op logboek bron. Omdat we filteren op aanvragen waarbij het veld status code 404 bevat, worden alleen de server-en netwerk tracerings gegevens weer gegeven, niet de logboek gegevens van de client.

In de onderstaande afbeelding ziet u een specifieke aanvraag waarbij een Get BLOB-bewerking een 404 heeft opgeleverd, omdat de BLOB niet bestaat. Houd er rekening mee dat sommige kolommen zijn verwijderd uit de standaard weergave om de relevante gegevens weer te geven.

![Gefilterde server-en netwerk traceer logboeken](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Vervolgens correleren we deze client aanvraag-ID met de logboek gegevens van de client om te zien welke acties de client heeft ondernomen toen de fout optrad. U kunt een nieuwe analyse raster weergave voor deze sessie weer geven om de logboek gegevens van de client weer te geven die worden geopend op een tweede tabblad:

1. Kopieer eerst de waarde van het veld **ClientRequestId** naar het klem bord. U kunt dit doen door een van beide rijen te selecteren, het veld **ClientRequestId** te zoeken, met de rechter muisknop op de gegevens waarde te klikken en **kopiëren ' ClientRequestId '** te kiezen.
2. Selecteer op het lint op de werk balk de optie **Nieuw Viewer**en selecteer vervolgens **analyse raster** om een nieuw tabblad te openen. Op het tabblad Nieuw worden alle gegevens in uw logboek bestanden weer gegeven, zonder het groeperen, filteren of kleuren regels.
3. Selecteer in het lint op de werk balk de optie **indeling weer geven**en selecteer vervolgens **alle .net-client kolommen** onder het gedeelte **Azure Storage** . In deze weer gave-indeling worden gegevens uit het client logboek weer gegeven, evenals de logboeken voor Server-en netwerk tracering. Standaard wordt deze gesorteerd op de kolom **MessageNumber** .
4. Zoek vervolgens in het client logboek op de client aanvraag-ID. Selecteer op het lint op de werk balk de optie **berichten zoeken**en geef een aangepast filter op voor de client aanvraag-id in het veld **zoeken** . Gebruik deze syntaxis voor het filter, waarbij u uw eigen client aanvraag-ID opgeeft:

    `*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"`

Message Analyzer zoekt en selecteert de eerste logboek vermelding waarin de zoek criteria overeenkomen met de client aanvraag-ID. In het client logboek zijn er verschillende vermeldingen voor elke client aanvraag-ID, dus u kunt ze ook groeperen in het veld **ClientRequestId** om ze gemakkelijker te kunnen zien. In de onderstaande afbeelding worden alle berichten in het client logboek weer gegeven voor de opgegeven client aanvraag-ID.

![Client logboek met 404 fouten](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Met behulp van de gegevens die worden weer gegeven in de weer gave-indelingen op deze twee tabbladen, kunt u de gegevens van de aanvraag analyseren om te bepalen wat de fout heeft veroorzaakt. U kunt ook aanvragen bekijken die eerder zijn gegaan om te zien of een vorige gebeurtenis heeft geleid tot de 404-fout. U kunt bijvoorbeeld de logboek vermeldingen van de client voor deze client aanvraag-ID controleren om te bepalen of de blob is verwijderd, of als de fout is veroorzaakt door de client toepassing die een CreateIfNotExists-API op een container of BLOB aanroept. In het client logboek vindt u het adres van de BLOB in het veld **Beschrijving** . in de logboeken voor Server-en netwerk tracering wordt deze informatie weer gegeven in het veld **samen vatting** .

Wanneer u het adres van de BLOB weet dat de 404-fout heeft opgeleverd, kunt u verder onderzoeken. Als u de logboek vermeldingen zoekt voor andere berichten die zijn gekoppeld aan bewerkingen op dezelfde blob, kunt u controleren of de client de entiteit eerder heeft verwijderd.

## <a name="analyze-other-types-of-storage-errors"></a>Andere typen opslag fouten analyseren

Nu u bekend bent met het gebruik van Message Analyzer om uw logboek gegevens te analyseren, kunt u andere soorten fouten analyseren met behulp van weer gave-indelingen, kleur regels en zoeken/filteren. In de onderstaande tabellen worden enkele problemen vermeld die kunnen optreden en de filter criteria die u kunt gebruiken om ze te vinden. Zie [filter Message data](https://technet.microsoft.com/library/jj819365.aspx)(Engelstalig) voor meer informatie over het maken van filters en het filteren van Message Analyzer.

| Om te onderzoeken... | Filter expressie gebruiken... | Expressie is van toepassing op logboek (client, Server, netwerk, alle) |
| --- | --- | --- |
| Onverwachte vertragingen bij de levering van berichten in een wachtrij |AzureStorageClientDotNetV4. Description bevat de bewerking een nieuwe poging is mislukt. |Client |
| HTTP-toename in Percentthrottlingerror aan |HTTP. Response. status code = = 500 &#124;&#124; HTTP. Response. status code = = 503 |Netwerk |
| Toename in Percenttimeouterror aan |HTTP. Response. status code = = 500 |Netwerk |
| Toename in Percenttimeouterror aan (alle) |* Status code = = 500 |Alle |
| Toename in PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Client |
| HTTP 403-berichten (verboden) |HTTP. Response. status code = = 403 |Netwerk |
| HTTP 404-berichten (niet gevonden) |HTTP. Response. status code = = 404 |Netwerk |
| 404 (alle) |* Status code = = 404 |Alle |
| Een probleem met de verificatie van een SAS (Shared Access Signature) |AzureStorageLog. RequestStatus = = "SASAuthorizationError" |Netwerk |
| HTTP 409-berichten (conflict) |HTTP. Response. status code = = 409 |Netwerk |
| 409 (alle) |* Status code = = 409 |Alle |
| Lage PercentSuccess of logboek vermeldingen van de analyse hebben bewerkingen met de transactie status ClientOtherErrors |AzureStorageLog. RequestStatus = = "ClientOtherError" |server |
| Nagle-waarschuwing |((AzureStorageLog. EndToEndLatencyMS-AzureStorageLog. ServerLatencyMS) > (AzureStorageLog. ServerLatencyMS * 1,5)) en (AzureStorageLog. RequestPacketSize <1460) en (AzureStorageLog. EndToEndLatencyMS-AzureStorageLog. ServerLatencyMS >= 200) |server |
| Tijds bereik in server-en netwerk logboeken |#Timestamp >= 2014-10-20T16:36:38 en #Timestamp <= 2014-10-20T16:36:39 |Server, netwerk |
| Tijds bereik in Server logboeken |AzureStorageLog. time stamp >= 2014-10-20T16:36:38 en AzureStorageLog. time stamp <= 2014-10-20T16:36:39 |server |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen voor meer informatie over het oplossen van problemen met end-to-end scenario's in Azure Storage:

* [Microsoft Azure Storage bewaken, problemen opsporen en oplossen](storage-monitoring-diagnosing-troubleshooting.md)
* [Opslaganalyse](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Een Storage-account bewaken in de Azure-portal](storage-monitor-storage-account.md)
* [Gegevens overdragen met het AzCopy-opdracht regel programma](storage-use-azcopy.md)
* [Microsoft Message Analyzer Operating Guide](https://technet.microsoft.com/library/jj649776.aspx) (Operationele handleiding voor Microsoft Message Analyzer)
