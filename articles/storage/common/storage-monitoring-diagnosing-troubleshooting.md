---
title: Azure Storage controleren, diagnosticeren en oplossen | Microsoft Documenten
description: Gebruik functies zoals storage analytics, client-side logging en andere hulpprogramma's van derden om azure storage-gerelateerde problemen te identificeren, diagnosticeren en oplossen.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 0bbffacc0a8c47950b8637e826d1d5db9fbdb234
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605072"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Microsoft Azure Storage bewaken, problemen opsporen en oplossen
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Overzicht
Het diagnosticeren en oplossen van problemen in een gedistribueerde toepassing die wordt gehost in een cloudomgeving, kan complexer zijn dan in traditionele omgevingen. Toepassingen kunnen worden geïmplementeerd in een PaaS- of IaaS-infrastructuur, on-premises, op een mobiel apparaat of in een combinatie van deze omgevingen. Het netwerkverkeer van uw toepassing kan doorgaans openbare en particuliere netwerken doorkruisen en uw toepassing kan meerdere opslagtechnologieën gebruiken, zoals Microsoft Azure Storage Tables, Blobs, Queues of Files, naast andere gegevensopslag, zoals relationele en documentdatabases.

Om dergelijke toepassingen met succes te beheren, moet u ze proactief controleren en begrijpen hoe u alle aspecten van deze toepassingen en hun afhankelijke technologieën diagnosticeren en oplossen. Als gebruiker van Azure Storage-services moet u de opslagservices die uw toepassing gebruikt voor onverwachte gedragswijzigingen (zoals langzamer dan de gebruikelijke responstijden) continu controleren en logboekregistratie gebruiken om meer gedetailleerde gegevens te verzamelen en een probleem diepgaand te analyseren. De diagnostische informatie die u verkrijgt van zowel monitoring en logging zal u helpen om de hoofdoorzaak van het probleem van uw toepassing ondervonden te bepalen. Vervolgens u het probleem oplossen en bepalen welke stappen u nemen om het probleem te verhelpen. Azure Storage is een kern van Azure-service en vormt een belangrijk onderdeel van de meeste oplossingen die klanten implementeren voor de Azure-infrastructuur. Azure Storage bevat mogelijkheden om opslagproblemen in uw cloudtoepassingen te vereenvoudigen, te diagnosticeren en op te lossen.

> [!NOTE]
> Azure Files biedt op dit moment geen ondersteuning voor logboekregistratie.
>

Zie [End-to-End troubleshooting met Azure Storage Metrics and Logging, AzCopy en Message Analyzer](../storage-e2e-troubleshooting.md)voor een praktische handleiding voor end-to-end probleemoplossing in Azure Storage-toepassingen.

* [Inleiding]
  * [Hoe deze gids is georganiseerd]
* [Uw opslagservice bewaken]
  * [Toezicht op de status van de dienst]
  * [Controlecapaciteit]
  * [Beschikbaarheid bewaken]
  * [Prestaties monitoren]
* [Opslagproblemen diagnosticeren]
  * [Problemen met de status van de service]
  * [Prestatieproblemen]
  * [Fouten diagnosticeren]
  * [Problemen met opslagemulators]
  * [Hulpprogramma's voor opslaglogboekregistratie]
  * [Hulpprogramma's voor netwerklogboekregistratie gebruiken]
* [End-to-end tracering]
  * [Loggegevens correleren]
  * [Clientaanvraag-id]
  * [Serveraanvraag-id]
  * [Timestamps]
* [Richtlijnen voor probleemoplossing]
  * [Prestatiegegevens geven hoge AverageE2ELatency en lage AverageServerLatency aan]
  * [Prestatiegegevens geven lage AverageE2ELatency en lage AverageServerLatency aan, maar de client ondervindt hoge latentie]
  * [Prestatiegegevens geven hoge AverageServerLatency aan]
  * [U ervaart onverwachte vertragingen bij de levering van berichten in een wachtrij]
  * [Prestatiegegevens geven een toename in PercentThrottlingError aan]
  * [Prestatiegegevens geven een toename in PercentTimeoutError aan]
  * [Prestatiegegevens geven een toename in PercentNetworkError aan]
  * [De client ontvangt HTTP 403-meldingen (verboden)]
  * [De client ontvangt HTTP 404-meldingen (niet gevonden)]
  * [De client ontvangt HTTP 409-meldingen (conflict)]
  * [Statistieken tonen lage PercentEnsucces of analytics log items hebben bewerkingen met transactiestatus van ClientOtherErrors]
  * [Capaciteitsstatistieken laten een onverwachte toename van het gebruik van de opslagcapaciteit zien]
  * [Uw probleem ontstaat door het gebruik van de opslagemulator voor ontwikkeling of test]
  * [U ondervindt problemen met het installeren van de Azure SDK voor .NET]
  * [U hebt een ander probleem met een opslagservice]
  * [Problemen met VHD's oplossen op virtuele Windows-machines](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Problemen met VHD's op virtuele Linux-machines oplossen](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Azure Files-problemen oplossen met Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Azure Files-problemen met Linux oplossen](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Bijlagen]
  * [Bijlage 1: Fiddler gebruiken om HTTP- en HTTPS-verkeer vast te leggen]
  * [Aanhangsel 2: Wireshark gebruiken om netwerkverkeer vast te leggen]
  * [Aanhangsel 3: Microsoft Message Analyzer gebruiken om netwerkverkeer vast te leggen]
  * [Aanhangsel 4: Excel gebruiken om statistieken en logboekgegevens weer te geven]
  * [Bijlage 5: Monitoring met toepassingsinzichten voor Azure DevOps]

## <a name="introduction"></a><a name="introduction"></a>Inleiding
In deze handleiding ziet u hoe u functies zoals Azure Storage Analytics, logboekregistratie aan de clientzijde in de Azure Storage Client Library en andere hulpprogramma's van derden gebruiken om azure storage-gerelateerde problemen te identificeren, diagnosticeren en oplossen.

![][1]

Deze handleiding is bedoeld om voornamelijk te worden gelezen door ontwikkelaars van online services die gebruik maken van Azure Storage Services en IT-professionals die verantwoordelijk zijn voor het beheer van dergelijke online services. De doelstellingen van deze gids zijn:

* Om u te helpen de status en prestaties van uw Azure Storage-accounts te behouden.
* U voorzien van de nodige processen en hulpprogramma's om u te helpen beslissen of een probleem of probleem in een toepassing betrekking heeft op Azure Storage.
* U bruikbare richtlijnen bieden voor het oplossen van problemen met betrekking tot Azure Storage.

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Hoe deze gids is georganiseerd
In de sectie['Bewaken van uw opslagservice']wordt beschreven hoe u de status en prestaties van uw Azure Storage-services controleren met azure storage analytics-statistieken (storagemetrics).

In de sectie['Diagnose van opslagproblemen']wordt beschreven hoe u problemen diagnosticeren met Azure Storage Analytics Logging (Storage Logging). Het beschrijft ook hoe u logboekregistratie aan de client inschakelen met behulp van de faciliteiten in een van de clientbibliotheken, zoals de opslagclientbibliotheek voor .NET of de Azure SDK voor Java.

In de sectie[End-to-end tracing]wordt beschreven hoe u de informatie in verschillende logbestanden en metrische gegevens correleren.

De sectie['Richtlijnen voor probleemoplossing']biedt richtlijnen voor het oplossen van problemen voor enkele van de veelvoorkomende opslaggerelateerde problemen die u tegenkomen.

De "[Aanhangsels]" bevatten informatie over het gebruik van andere tools zoals Wireshark en Netmon voor het analyseren van netwerkpakketgegevens, Fiddler voor het analyseren van HTTP/HTTPS-berichten en Microsoft Message Analyzer voor het correleren van loggegevens.

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>Uw opslagservice bewaken
Als u bekend bent met Windows-prestatiebewaking, u opslagstatistieken zien als een Azure Storage-equivalent van Windows Performance Monitor-tellers. In Opslagstatistieken vindt u een uitgebreide set statistieken (tellers in de terminologie van Windows Performance Monitor), zoals beschikbaarheid van de service, totaal aantal serviceverzoeken of percentage succesvolle serviceverzoeken. Zie [Tabelschema voor opslagstatistieken voor opslaganalysestatistieken](https://msdn.microsoft.com/library/azure/hh343264.aspx)voor een volledige lijst met beschikbare statistieken. U opgeven of u wilt dat de opslagservice elk uur of elke minuut statistieken verzamelt en verzamelt. Zie [Opslagstatistieken inschakelen en statistieken weergeven](https://go.microsoft.com/fwlink/?LinkId=510865)voor meer informatie over het inschakelen van statistieken en het bewaken van uw opslagaccounts.

U kiezen welke uurstatistieken u wilt weergeven in de [Azure-portal](https://portal.azure.com) en regels configureren die beheerders per e-mail op de hoogte stellen wanneer een uurstatistiek een bepaalde drempel waardedrempel overschrijdt. Zie [Waarschuwingsmeldingen ontvangen](/azure/monitoring-and-diagnostics/monitoring-overview-alerts)voor meer informatie .

We raden u aan [Azure Monitor for Storage](../../azure-monitor/insights/storage-insights-overview.md) (voorbeeld) te controleren. Het is een functie van Azure Monitor die uitgebreide bewaking van uw Azure Storage-accounts biedt door een uniforme weergave te bieden van de prestaties, capaciteit en beschikbaarheid van uw Azure Storage Services. Het vereist niet dat u iets in- of configureert en u deze statistieken onmiddellijk bekijken vanuit de vooraf gedefinieerde interactieve grafieken en andere visualisaties die zijn opgenomen.

De opslagservice verzamelt statistieken met de beste inspanning, maar registreert mogelijk niet elke opslagbewerking.

In de Azure-portal u statistieken weergeven, zoals beschikbaarheid, totaalaantal aanvragen en gemiddelde latentienummers voor een opslagaccount. Er is ook een meldingsregel ingesteld om een beheerder te waarschuwen als de beschikbaarheid onder een bepaald niveau zakt. Van het bekijken van deze gegevens, een mogelijk gebied voor onderzoek is de tabel service succes percentage wordt onder de 100% (voor meer informatie, zie de sectie "[Metrics tonen lage PercentSuccess of analytics log items hebben bewerkingen met transactiestatus van ClientOtherErrors]").

U moet uw Azure-toepassingen continu controleren om ervoor te zorgen dat ze in orde zijn en presteren zoals verwacht door:

* Met een aantal basislijnstatistieken voor toepassingen u huidige gegevens vergelijken en belangrijke wijzigingen in het gedrag van Azure-opslag en uw toepassing identificeren. De waarden van uw basislijnstatistieken zijn in veel gevallen toepassingsspecifiek en u moet ze vaststellen wanneer u uw toepassing aan het testen bent.
* Minutenstatistieken opnemen en gebruiken om actief te controleren op onverwachte fouten en afwijkingen, zoals pieken in fouttellingen of aanvraagpercentages.
* Het registreren van uurstatistieken en deze gebruiken om gemiddelde waarden te controleren, zoals gemiddelde fouttellingen en aanvraagpercentages.
* Onderzoek naar mogelijke problemen met behulp van diagnostische tools zoals later besproken in de sectie "[Diagnose van opslag problemen]."

De grafieken in de volgende afbeelding illustreren hoe het gemiddelde dat optreedt voor uurstatistieken pieken in activiteit kan verbergen. De uurstatistieken lijken een stabiel aantal aanvragen weer te geven, terwijl de minutenstatistieken de schommelingen weergeven die echt plaatsvinden.

![][3]

In de rest van deze sectie wordt beschreven welke statistieken u moet controleren en waarom.

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>Toezicht op de status van de dienst
U de [Azure-portal](https://portal.azure.com) gebruiken om de status van de opslagservice (en andere Azure-services) weer te geven in alle Azure-regio's over de hele wereld. Met monitoring u direct zien of een probleem buiten uw controle van invloed is op de opslagservice in de regio die u voor uw toepassing gebruikt.

De [Azure-portal](https://portal.azure.com) kan ook meldingen bieden van incidenten die van invloed zijn op de verschillende Azure-services.
Opmerking: deze informatie was voorheen beschikbaar, samen met historische gegevens, op het [Azure Service Dashboard.](https://status.azure.com)

Hoewel de [Azure-portal](https://portal.azure.com) statusgegevens verzamelt vanuit de Azure-datacenters (inside-outmonitoring), u ook overwegen om een externe benadering te hanteren voor het genereren van synthetische transacties die periodiek toegang hebben tot uw door Azure gehoste webtoepassing vanaf meerdere locaties. De services van [Dynatrace](https://www.dynatrace.com/en/synthetic-monitoring) en Application Insights voor Azure DevOps zijn voorbeelden van deze aanpak. Zie de bijlage "[Bijlage 5: Monitoring met application insights voor Azure DevOps voor](#appendix-5)meer informatie over Application Insights voor Azure DevOps."

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>Controlecapaciteit
Storage Metrics slaat alleen capaciteitsstatistieken op voor de blobservice omdat blobs doorgaans het grootste deel van de opgeslagen gegevens vertegenwoordigen (op het moment van schrijven is het niet mogelijk om opslagstatistieken te gebruiken om de capaciteit van uw tabellen en wachtrijen te controleren). U deze gegevens vinden in de **tabel $MetricsCapacityBlob** als u bewaking voor de Blob-service hebt ingeschakeld. Storage Metrics registreert deze gegevens eenmaal per dag en u de waarde van de **RowKey** gebruiken om te bepalen of de rij een entiteit bevat die betrekking heeft op gebruikersgegevens **(waardegegevens)** of analysegegevens (value **analytics).** Elke opgeslagen entiteit bevat informatie over de hoeveelheid gebruikte opslagruimte **(Capaciteit** gemeten in bytes) en het huidige aantal containers (**ContainerCount**) en blobs (**ObjectCount**) in gebruik in de opslagaccount. Zie [Tabelschema voor opslaganalysestatistieken](https://msdn.microsoft.com/library/azure/hh343264.aspx)voor meer informatie over de capaciteitsstatistieken die zijn opgeslagen in de **tabel $MetricsCapacityBlob.**

> [!NOTE]
> U moet deze waarden controleren voor een vroegtijdige waarschuwing dat u de capaciteitslimieten van uw opslagaccount nadert. In de Azure-portal u waarschuwingsregels toevoegen om u op de hoogte te stellen als het totale opslaggebruik hoger is of onder de door u opgegeven drempelwaarden valt.
>
>

Zie het blogbericht [Azure Storage Billing – Bandbreedte, Transacties en capaciteit](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)voor hulp bij het schatten van de grootte van verschillende opslagobjecten, zoals blobs.

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>Beschikbaarheid bewaken
U dient de beschikbaarheid van de opslagservices in uw opslagaccount te controleren door de waarde in de **kolom Beschikbaarheid** in de tabellen voor uur- of minutenstatistieken te controleren - **$MetricsHourPrimaryTransactionsBlob,** **$MetricsHourPrimaryTransactionsTable,** **$MetricsHourPrimaryTransactionsQueue,** **$MetricsMinutePrimaryTransactionsBlob,** **$MetricsMinutePrimaryTransactionsTable,** **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. De kolom **Beschikbaarheid** bevat een percentagewaarde die de beschikbaarheid van de service of de API-bewerking aangeeft die wordt weergegeven door de rij (de **RowKey** geeft aan of de rij statistieken bevat voor de service als geheel of voor een specifieke API-bewerking).

Elke waarde van minder dan 100% geeft aan dat sommige opslagaanvragen mislukken. U zien waarom ze falen door de andere kolommen in de metrische gegevens te onderzoeken die het aantal aanvragen met verschillende fouttypen weergeven, zoals **ServerTimeoutError.** U mag verwachten dat **beschikbaarheid** tijdelijk onder de 100% daalt om redenen zoals tijdelijke servertime-outs terwijl de service partities verplaatst naar een betere aanvraag voor load-balance; de logica voor het opnieuw proberen in uw clienttoepassing moet dergelijke intermitterende voorwaarden afhandelen. In het artikel [Storage Analytics Logged Operations and Status Messages](https://msdn.microsoft.com/library/azure/hh343260.aspx) worden de transactietypen weergegeven die Storage Metrics in de berekening van **beschikbaarheid** bevatten.

In de [Azure-portal](https://portal.azure.com)u waarschuwingsregels toevoegen om u op de hoogte te stellen als **beschikbaarheid** voor een service onder een door u opgegeven drempelwaarde valt.

In het gedeelte "[Richtlijnen voor probleemoplossing]" in deze handleiding worden enkele veelvoorkomende problemen met de opslagservice beschreven met betrekking tot beschikbaarheid.

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>Prestaties monitoren
Als u de prestaties van de opslagservices wilt controleren, u de volgende statistieken gebruiken van de tabellen voor uurstatistieken en minuten.

* De waarden in de kolommen **AverageE2ELatency** en **AverageServerLatency** geven de gemiddelde tijd weer die de opslagservice of api-bewerking nodig heeft om aanvragen te verwerken. **AverageE2ELatency** is een maat voor end-to-end latentie die de tijd bevat die nodig is om het verzoek te lezen en het antwoord te verzenden naast de tijd die nodig is om de aanvraag te verwerken (omvat dus netwerklatentie zodra de aanvraag de opslagservice bereikt); **AverageServerLatency** is een maat voor alleen de verwerkingstijd en sluit daarom elke netwerklatentie met betrekking tot communicatie met de client uit. Zie de sectie "[Metrics show high AverageE2ELatency and low AverageServerLatency]" later in deze handleiding voor een discussie over waarom er mogelijk een significant verschil is tussen deze twee waarden.
* De waarden in de kolommen **TotalIngress** en **TotalUitgang geven** de totale hoeveelheid gegevens weer, in bytes, die binnenkomen naar en uit gaat van uw opslagservice of via een specifiek API-bewerkingstype.
* De waarden in de kolom **TotalRequests** geven het totale aantal aanvragen weer dat de opslagservice van API-bewerking ontvangt. **TotalRequests** is het totale aantal aanvragen dat de opslagservice ontvangt.

Doorgaans controleert u op onverwachte wijzigingen in een van deze waarden als een indicator dat u een probleem hebt waarvoor onderzoek nodig is.

In de [Azure-portal](https://portal.azure.com)u waarschuwingsregels toevoegen om u op de hoogte te stellen als een van de prestatiestatistieken voor deze service onder de drempel valt of een door u opgegeven drempel overschrijdt.

In het gedeelte "[Richtlijnen voor probleemoplossing]" in deze handleiding worden enkele veelvoorkomende problemen met de opslagservice beschreven die verband houden met de prestaties.

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Opslagproblemen diagnosticeren
Er zijn een aantal manieren waarop u zich bewust wordt van een probleem of probleem in uw toepassing, waaronder:

* Een grote fout die ervoor zorgt dat de toepassing vastloopt of stopt met werken.
* Belangrijke wijzigingen ten opzichte van basislijnwaarden in de statistieken die u controleert zoals beschreven in de vorige sectie["Monitoring your storage service."]
* Rapporten van gebruikers van uw toepassing dat een bepaalde bewerking niet is voltooid zoals verwacht of dat een bepaalde functie niet werkt.
* Fouten die zijn gegenereerd in uw toepassing die worden weergegeven in logboekbestanden of via een andere meldingsmethode.

Problemen met betrekking tot Azure-opslagservices vallen doorgaans in een van de vier brede categorieën:

* Uw toepassing heeft een prestatieprobleem, gerapporteerd door uw gebruikers, of onthuld door wijzigingen in de prestatiestatistieken.
* Er is een probleem met de Azure Storage-infrastructuur in een of meer regio's.
* Uw toepassing wordt geconfronteerd met een fout, gerapporteerd door uw gebruikers, of onthuld door een toename van een van de fout telling metrics die u controleert.
* Tijdens de ontwikkeling en test u de lokale opslagemulator gebruiken; u een aantal problemen tegenkomen die specifiek betrekking hebben op het gebruik van de opslagemulator.

In de volgende secties worden de stappen beschreven die u moet volgen om problemen in elk van deze vier categorieën te diagnosticeren en op te lossen. De sectie[Richtlijnen voor probleemoplossing]later in deze handleiding bevat meer details voor een aantal veelvoorkomende problemen die u tegenkomen.

### <a name="service-health-issues"></a><a name="service-health-issues"></a>Problemen met de status van de service
Problemen met de servicestatus vallen meestal buiten uw controle. De [Azure-portal](https://portal.azure.com) bevat informatie over lopende problemen met Azure-services, waaronder opslagservices. Als u bij het maken van uw opslagaccount voor Geo-redundante opslag voor Lezen toegang hebt gekozen, kan uw toepassing tijdelijk overschakelen naar de alleen-lezen kopie op de secundaire locatie als uw gegevens niet meer beschikbaar zijn. Als u wilt lezen vanaf de secundaire, moet uw toepassing kunnen schakelen tussen het gebruik van de primaire en secundaire opslaglocaties, en in staat zijn om te werken in een verminderde functionaliteit modus met alleen-lezen gegevens. Met de Azure Storage Client-bibliotheken u een beleid voor nieuwe try definiëren dat kan lezen uit secundaire opslag voor het geval een lezing uit primaire opslag mislukt. Uw toepassing moet zich er ook van bewust zijn dat de gegevens op de secundaire locatie uiteindelijk consistent zijn. Zie voor meer informatie de [redundantieopties voor azure storage redundantie van](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/)het blogbericht en Geo Redundant Storage Lezen.

### <a name="performance-issues"></a><a name="performance-issues"></a>Prestatieproblemen
De prestaties van een toepassing kunnen subjectief zijn, met name vanuit het perspectief van een gebruiker. Het is daarom belangrijk dat u over prestatiegegevens voor een basislijn beschikt aan de hand waarvan u kunt bepalen waar er prestatieproblemen zijn. Veel factoren kunnen de prestaties van een Azure-opslagservice beïnvloeden vanuit het perspectief van clienttoepassingen. Deze factoren kunnen werken in de opslagservice, in de client of in de netwerkinfrastructuur; Daarom is het belangrijk om een strategie te hebben om de oorsprong van het prestatieprobleem vast te stellen.

Nadat u de waarschijnlijke locatie van de oorzaak van het prestatieprobleem hebt geïdentificeerd in de statistieken, u de logboekbestanden gebruiken om gedetailleerde informatie te vinden om het probleem verder te diagnosticeren en op te lossen.

De sectie[Richtlijnen voor probleemoplossing]later in deze handleiding bevat meer informatie over enkele veelvoorkomende prestatiegerelateerde problemen die u tegenkomen.

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>Fouten diagnosticeren
Gebruikers van uw toepassing kunnen u op de hoogte stellen van fouten die door de clienttoepassing zijn gerapporteerd. Storage Metrics registreert ook het aantal punten van verschillende fouttypen uit uw opslagservices, zoals **NetworkError,** **ClientTimeoutError**of **AuthorizationError**. Hoewel Storage Metrics alleen tellingen van verschillende fouttypen registreert, u meer details over individuele aanvragen verkrijgen door server-, client-side- en netwerklogboeken te onderzoeken. De HTTP-statuscode die door de opslagservice wordt geretourneerd, geeft doorgaans een indicatie van de reden waarom de aanvraag is mislukt.

> [!NOTE]
> Vergeet niet dat u enkele intermitterende fouten verwachten: bijvoorbeeld fouten als gevolg van tijdelijke netwerkomstandigheden of toepassingsfouten.
>
>

De volgende resources kunnen worden gebruikt voor een overzicht van status- en foutcodes in verband met de opslag:

* [Foutcodes van de common rest-API](https://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Foutcodes voor blob-services](https://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Foutcodes wachtrijservice](https://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Foutcodes tabelservice](https://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Foutcodes bestandsservice](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Problemen met opslagemulators
De Azure SDK bevat een opslagemulator die u uitvoeren op een ontwikkelwerkstation. Deze emulator simuleert het grootste deel van het gedrag van de Azure-opslagservices en is handig tijdens de ontwikkeling en test, zodat u toepassingen uitvoeren die Azure-opslagservices gebruiken zonder dat u een Azure-abonnement en een Azure-opslagaccount nodig hebt.

In het gedeelte "[Troubleshooting guidance]" van deze handleiding worden enkele veelvoorkomende problemen beschreven die zich hebben ondervonden met behulp van de opslagemulator.

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>Hulpprogramma's voor opslaglogboekregistratie
Storage Logging biedt server-side logging van opslagaanvragen in uw Azure-opslagaccount. Zie [Opslaglogboekregistratie inschakelen en Logboekgegevens openen](https://go.microsoft.com/fwlink/?LinkId=510867)voor meer informatie over het inschakelen van serverlogboekregistratie en toegang tot de loggegevens.

Met de opslagclientbibliotheek voor .NET u logboekgegevens aan de clientzijde verzamelen die betrekking hebben op opslagbewerkingen die door uw toepassing worden uitgevoerd. Zie [Logboekregistratie op de client inschakelen met de .NET-opslagclientbibliotheek](https://go.microsoft.com/fwlink/?LinkId=510868) voor meer informatie.

> [!NOTE]
> In sommige omstandigheden (zoals SAS-autorisatiefouten) kan een gebruiker een fout melden waarvoor u geen aanvraaggegevens vinden in de opslaglogboeken aan de serverzijde. U de logboekmogelijkheden van de opslagclientbibliotheek gebruiken om te onderzoeken of de oorzaak van het probleem op de client ligt of netwerkbewakingstools gebruiken om het netwerk te onderzoeken.
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Hulpprogramma's voor netwerklogboekregistratie gebruiken
U het verkeer tussen de client en de server vastleggen om gedetailleerde informatie te geven over de gegevens die de client en server uitwisselen en de onderliggende netwerkvoorwaarden. Handige hulpprogramma's voor netwerklogboekregistratie zijn:

* [Fiddler](https://www.telerik.com/fiddler) is een gratis web debugging proxy waarmee u de headers en payload gegevens van HTTP en HTTPS verzoek en antwoord berichten te onderzoeken. Zie [Aanhangsel 1: Fiddler gebruiken om HTTP- en HTTPS-verkeer vast te leggen](#appendix-1)voor meer informatie.
* [Microsoft Network Monitor (Netmon)](https://www.microsoft.com/download/details.aspx?id=4865) en [Wireshark](https://www.wireshark.org/) zijn gratis netwerkprotocolanalysers waarmee u gedetailleerde pakketinformatie bekijken voor een breed scala aan netwerkprotocollen. Zie "[Bijlage 2: Wireshark gebruiken om netwerkverkeer vast te leggen " voor](#appendix-2)meer informatie over Wireshark.
* Microsoft Message Analyzer is een tool van Microsoft die Netmon vervangt en die u naast het vastleggen van netwerkpakketgegevens helpt bij het bekijken en analyseren van de logboekgegevens die zijn vastgelegd vanuit andere hulpprogramma's. Zie Bijlage[3: Microsoft Message Analyzer gebruiken om netwerkverkeer vast te leggen voor](#appendix-3)meer informatie.
* Als u een basisverbindingstest wilt uitvoeren om te controleren of uw clientmachine verbinding kan maken met de Azure-opslagservice via het netwerk, u dit niet doen met behulp van het **standaardping-hulpprogramma** op de client. U de [ **tcping-tool** ](https://www.elifulkerson.com/projects/tcping.php) echter gebruiken om de connectiviteit te controleren.

In veel gevallen zijn de logboekgegevens van Storage Logging en de Storage Client Library voldoende om een probleem te diagnosticeren, maar in sommige scenario's hebt u mogelijk de meer gedetailleerde informatie nodig die deze netwerkregistratietools kunnen bieden. Als u bijvoorbeeld Fiddler gebruikt om HTTP- en HTTPS-berichten te bekijken, u header- en payloadgegevens bekijken die van en naar de opslagservices worden verzonden, zodat u onderzoeken hoe een clienttoepassing opslagbewerkingen opnieuw probeert. Protocolanalyzers zoals Wireshark werken op pakketniveau, zodat u TCP-gegevens bekijken, waardoor u verloren pakketten en verbindingsproblemen oplossen. Message Analyzer kan zowel op HTTP- als TCP-lagen werken.

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>End-to-end tracering
End-to-end tracering met behulp van een verscheidenheid aan logbestanden is een handige techniek voor het onderzoeken van mogelijke problemen. U de datum-/tijdgegevens van uw metrische gegevens gebruiken als een indicatie van waar u in de logboekbestanden beginnen met zoeken naar de gedetailleerde informatie waarmee u het probleem oplossen.

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>Loggegevens correleren
Bij het bekijken van logboeken van clienttoepassingen, netwerksporen en opslagregistratie aan de serverzijde is het van cruciaal belang dat aanvragen in de verschillende logboekbestanden kunnen worden gecorreleerd. De logboekbestanden bevatten een aantal verschillende velden die nuttig zijn als correlatie-id. De clientaanvraag-id is het meest nuttige veld om items in de verschillende logboeken te correleren. Soms kan het echter handig zijn om de serveraanvraag-id of tijdstempels te gebruiken. In de volgende secties vindt u meer informatie over deze opties.

### <a name="client-request-id"></a><a name="client-request-id"></a>Clientaanvraag-id
De opslagclientbibliotheek genereert automatisch een unieke clientaanvraag-id voor elk verzoek.

* In het clientlogboek dat de opslagclientbibliotheek maakt, wordt de clientaanvraag-id weergegeven in het **clientaanvraag-id-veld** van elke logboekvermelding met betrekking tot de aanvraag.
* In een netwerktracering, zoals een door Fiddler vastgelegde, is de clientaanvraag-id zichtbaar in aanvraagberichten als de **http-headerwaarde x-ms-client-request-id.**
* In het logboek opslaglogboekaan de serverzijde wordt de clientaanvraag-id weergegeven in de kolom Clientaanvraag-id.

> [!NOTE]
> Het is mogelijk voor meerdere aanvragen om dezelfde clientaanvraag-id te delen omdat de client deze waarde kan toewijzen (hoewel de opslagclientbibliotheek automatisch een nieuwe waarde toewijst). Wanneer de client opnieuw wordt geprobeerd, delen alle pogingen dezelfde clientaanvraag-id. In het geval van een batch die van de client wordt verzonden, heeft de batch één clientaanvraag-id.
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>Serveraanvraag-id
De opslagservice genereert automatisch serveraanvraag-id's.

* In het logboek opslaglogboekaan de serverzijde wordt de serveraanvraag-id weergegeven, de **kolom Van de hoofdkolom Van de fout van de fout van de ato-id van de aanvraag.**
* In een netwerktracering, zoals een door Fiddler vastgelegde, wordt de serveraanvraag-id weergegeven in antwoordberichten als de **http-headerwaarde x-ms-request-id.**
* In het logboek aan de clientzijde dat de opslagclientbibliotheek maakt, wordt de serveraanvraag-id weergegeven in de kolom **Bewerkingstekst** voor de logboekvermelding met details van het serverantwoord.

> [!NOTE]
> De opslagservice kent altijd een unieke serveraanvraag-id toe aan elk verzoek dat wordt ontvangen, zodat elke poging tot opnieuw proberen van de client en elke bewerking die in een batch is opgenomen, een unieke serveraanvraag-id heeft.
>
>

Als de opslagclientbibliotheek een **StorageException** in de client gooit, bevat de eigenschap **RequestInformation** een **Object RequestResult** dat een eigenschap **ServiceRequestID** bevat. U hebt ook toegang tot een **Object RequestResult** vanuit een **instantie OperationContext.**

In het onderstaande codevoorbeeld wordt uitgelegd hoe u een aangepaste **ClientRequestId-waarde** instelt door een **Object OperationContext** de aanvraag aan de opslagservice toe te voegen. Het toont ook hoe u de **serverrequestid-waarde** uit het antwoordbericht ophalen.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

### <a name="timestamps"></a><a name="timestamps"></a>Timestamps
U ook tijdstempels gebruiken om gerelateerde logboekvermeldingen te vinden, maar wees voorzichtig met eventuele klokscheeftrekkingtussen de client en de server die mogelijk bestaan. Zoek plus of min 15 minuten voor overeenkomende server-side items op basis van de tijdstempel op de client. Houd er rekening mee dat de blobmetagegevens voor de blobs met statistieken het tijdsbereik aangeven voor de statistieken die in de blob zijn opgeslagen. Dit tijdsbereik is handig als u veel statistiekenblobs hebt voor dezelfde minuut of uur.

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Richtlijnen voor probleemoplossing
In deze sectie u de diagnose en het oplossen van problemen van enkele veelvoorkomende problemen die uw toepassing kan ondervinden bij het gebruik van de Azure-opslagservices, diagnosticeren en oplossen. Gebruik de onderstaande lijst om de informatie te vinden die relevant is voor uw specifieke probleem.

**Probleemoplossing beslissingsstructuur**

---
Heeft uw probleem betrekking op de prestaties van een van de opslagservices?

* [Prestatiegegevens geven hoge AverageE2ELatency en lage AverageServerLatency aan]
* [Prestatiegegevens geven lage AverageE2ELatency en lage AverageServerLatency aan, maar de client ondervindt hoge latentie]
* [Prestatiegegevens geven hoge AverageServerLatency aan]
* [U ervaart onverwachte vertragingen bij de levering van berichten in een wachtrij]

---
Heeft uw probleem betrekking op de beschikbaarheid van een van de opslagservices?

* [Prestatiegegevens geven een toename in PercentThrottlingError aan]
* [Prestatiegegevens geven een toename in PercentTimeoutError aan]
* [Prestatiegegevens geven een toename in PercentNetworkError aan]

---
 Ontvangt uw clientapplicatie een HTTP 4XX (zoals 404) reactie van een opslagservice?

* [De client ontvangt HTTP 403-meldingen (verboden)]
* [De client ontvangt HTTP 404-meldingen (niet gevonden)]
* [De client ontvangt HTTP 409-meldingen (conflict)]

---
[Statistieken tonen lage PercentEnsucces of analytics log items hebben bewerkingen met transactiestatus van ClientOtherErrors]

---
[Capaciteitsstatistieken laten een onverwachte toename van het gebruik van de opslagcapaciteit zien]

---
[U ondervindt onverwachte reboots van virtuele machines met een groot aantal aangesloten VHD's]

---
[Uw probleem ontstaat door het gebruik van de opslagemulator voor ontwikkeling of test]

---
[U ondervindt problemen met het installeren van de Azure SDK voor .NET]

---
[U hebt een ander probleem met een opslagservice]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Prestatiegegevens geven hoge AverageE2ELatency en lage AverageServerLatency aan
De onderstaande afbeelding van het [Azure-portalmonitoringshulpprogramma](https://portal.azure.com) toont een voorbeeld waarin de **gemiddelde e2Elatency** aanzienlijk hoger is dan de **gemiddelde serverlatentie.**

![][4]

De opslagservice berekent alleen de metrische **AverageE2ELatency** voor succesvolle aanvragen en omvat, in tegenstelling tot de gemiddelde **serverlatentie,** de tijd die de client nodig heeft om de gegevens te verzenden en bevestiging van de opslagservice te ontvangen. Daarom kan een verschil tussen **AverageE2ELatency** en **AverageServerLatency** zijn omdat de clienttoepassing traag reageert of als gevolg van omstandigheden op het netwerk.

> [!NOTE]
> U **e2ELatency** en **ServerLatency** ook bekijken voor afzonderlijke opslagbewerkingen in de logboekgegevens voor opslaglogboekregistratie.
>
>

#### <a name="investigating-client-performance-issues"></a>Problemen met de prestaties van klanten onderzoeken
Mogelijke redenen voor de client die langzaam reageert zijn het hebben van een beperkt aantal beschikbare verbindingen of threads, of het laag zijn van resources zoals CPU, geheugen of netwerkbandbreedte. Mogelijk u het probleem oplossen door de clientcode te wijzigen om efficiënter te zijn (bijvoorbeeld door asynchrone oproepen naar de opslagservice te gebruiken), of door een grotere virtuele machine te gebruiken (met meer cores en meer geheugen).

Voor de tabel- en wachtrijservices kan het Nagle-algoritme ook hoge **AverageE2ELatency** veroorzaken in vergelijking met **AverageServerLatency:** zie voor meer informatie het algoritme van de post [Nagle niet vriendelijk is voor kleine aanvragen.](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx) U het Nagle-algoritme in code uitschakelen met de klasse **ServicePointManager** in de **System.Net** naamruimte. U moet dit doen voordat u belt naar de tabel- of wachtrijservices in uw toepassing, omdat dit geen invloed heeft op verbindingen die al zijn geopend. Het volgende voorbeeld komt uit de **Application_Start** methode in een werkrol.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Controleer de logboeken aan de clientzijde om te zien hoeveel aanvragen uw clienttoepassing indient en controleer op algemene .NET-gerelateerde prestatieknelpunten in uw client, zoals CPU, .NET garbage collection, netwerkgebruik of geheugen. Zie [Foutopsporing, Tracering en Profilering](https://msdn.microsoft.com/library/7fe0dd2y)als uitgangspunt voor het oplossen van probleemoplossing voor .NET-clienttoepassingen.

#### <a name="investigating-network-latency-issues"></a>Problemen met netwerklatentie onderzoeken
Doorgaans is high-end latentie veroorzaakt door het netwerk te wijten aan tijdelijke omstandigheden. U zowel tijdelijke als permanente netwerkproblemen onderzoeken, zoals gedropte pakketten met behulp van hulpprogramma's zoals Wireshark of Microsoft Message Analyzer.

Zie "[Bijlage 2: Wireshark gebruiken om netwerkverkeer vast te leggen]voor meer informatie over het gebruik van Wireshark om netwerkproblemen op te lossen."

Zie Bijlage[3: Microsoft Message Analyzer gebruiken om netwerkverkeer vast te leggen voor]meer informatie over het gebruik van Microsoft Message Analyzer om netwerkproblemen op te lossen.

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Prestatiegegevens geven lage AverageE2ELatency en lage AverageServerLatency aan, maar de client ondervindt hoge latentie
In dit scenario is de meest waarschijnlijke oorzaak een vertraging in de opslagaanvragen die de opslagservice bereiken. U moet onderzoeken waarom aanvragen van de client de blobservice niet doorzetten.

Een mogelijke reden voor de client het uitstellen van verzoeken tot verzenden is dat er een beperkt aantal beschikbare verbindingen of threads.

Controleer ook of de client meerdere pogingen uitvoert en onderzoek de reden als dit het is. Als u wilt bepalen of de client meerdere pogingen uitvoert, u het als volgt gaan:

* Bekijk de logboeken van Storage Analytics. Als er meerdere pogingen plaatsvinden, ziet u meerdere bewerkingen met dezelfde clientaanvraag-id, maar met verschillende serveraanvraag-id's.
* Bekijk de clientlogboeken. Verbose logging geeft aan dat er een nieuwe poging heeft plaatsgevonden.
* Debuging van uw code en controleer de eigenschappen van het object **OperationContext** dat aan de aanvraag is gekoppeld. Als de bewerking opnieuw is geprobeerd, bevat de eigenschap **RequestResults** meerdere unieke serveraanvraag-id's. U ook de begin- en eindtijden voor elke aanvraag controleren. Zie het codevoorbeeld in de sectie [Serverrequest ID]voor meer informatie.

Als er geen problemen zijn in de client, moet u mogelijke netwerkproblemen zoals pakketverlies onderzoeken. U hulpprogramma's zoals Wireshark of Microsoft Message Analyzer gebruiken om netwerkproblemen te onderzoeken.

Zie "[Bijlage 2: Wireshark gebruiken om netwerkverkeer vast te leggen]voor meer informatie over het gebruik van Wireshark om netwerkproblemen op te lossen."

Zie Bijlage[3: Microsoft Message Analyzer gebruiken om netwerkverkeer vast te leggen voor]meer informatie over het gebruik van Microsoft Message Analyzer om netwerkproblemen op te lossen.

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>Prestatiegegevens geven hoge AverageServerLatency aan
In het geval van hoge **AverageServerLatency** voor blob-downloadaanvragen, moet u de logboeken voor opslaglogboekregistratie gebruiken om te zien of er herhaalde aanvragen zijn voor dezelfde blob (of set blobs). Voor blob uploadaanvragen moet u onderzoeken welke blokgrootte de client gebruikt (blokken van minder dan 64 K in grootte kunnen bijvoorbeeld leiden tot overheadkosten, tenzij de reads ook in minder dan 64 K-brokken zijn), en als meerdere clients blokken parallel uploaden naar dezelfde blob. U moet ook de statistieken per minuut controleren op pieken in het aantal aanvragen dat resulteert in het overschrijden van de schaalbaarheidsdoelen per seconde: zie ook :[Statistieken tonen een toename van PercentTimeoutError.]

Als u een hoge **AverageServerLatency** ziet voor blob-downloadaanvragen wanneer er herhaalde aanvragen zijn dezelfde blob of set blobs, moet u overwegen deze blobs te plaatsen met Azure Cache of het Azure Content Delivery Network (CDN). Voor uploadaanvragen u de doorvoer verbeteren door een groter blokformaat te gebruiken. Voor query's naar tabellen is het ook mogelijk om client-side caching te implementeren op clients die dezelfde querybewerkingen uitvoeren en waar de gegevens niet vaak worden gewijzigd.

Hoge **gemiddelde serverlatentiewaarden** kunnen ook een symptoom zijn van slecht ontworpen tabellen of query's die resulteren in scanbewerkingen of die het anti-patroon toevoegen/voorbereiden volgen. Zie " Statistieken[tonen een toename van PercentThrottlingError]" voor meer informatie.

> [!NOTE]
> Hier vindt u een uitgebreide checklist voor prestaties: [Checklist voor opslag en schaalbaarheid van Microsoft Azure.](storage-performance-checklist.md)
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>U ervaart onverwachte vertragingen bij de levering van berichten in een wachtrij
Als u een vertraging ondervindt tussen het moment dat een toepassing een bericht toevoegt aan een wachtrij en de tijd die beschikbaar is om uit de wachtrij te lezen, moet u de volgende stappen uitvoeren om het probleem te diagnosticeren:

* Controleer of de toepassing de berichten aan de wachtrij toevoegt. Controleer of de toepassing de **AddMessage-methode** niet meerdere keren opnieuw probeert voordat deze wordt geslaagd. In de logboeken van de opslagclientbibliotheek worden herhaalde pogingen van opslagbewerkingen weergegeven.
* Controleer of er geen klokscheefheid is tussen de werkrol die het bericht toevoegt aan de wachtrij en de werkrol die het bericht uit de wachtrij leest waardoor het lijkt alsof er een vertraging is in de verwerking.
* Controleer of de werkrol die de berichten uit de wachtrij leest, mislukt. Als een wachtrijclient de **GetMessage-methode** aanroept, maar niet reageert met een bevestiging, blijft het bericht onzichtbaar in de wachtrij totdat de **onzichtbaarheidstime-outperiode** is verstreken. Op dit punt wordt het bericht weer beschikbaar voor verwerking.
* Controleer of de wachtrijlengte in de loop van de tijd toeneemt. Dit kan gebeuren als u niet over voldoende werknemers beschikt om alle berichten te verwerken die andere werknemers in de wachtrij plaatsen. Controleer ook de statistieken om te zien of verwijderingsverzoeken mislukken en het aantal wachtrijen op berichten, wat kan duiden op herhaalde mislukte pogingen om het bericht te verwijderen.
* Controleer de logboeken voor opslaglogboekregistratie voor wachtrijbewerkingen met hoger dan verwachte **E2ELatency-** en **ServerLatency-waarden** over een langere periode dan normaal.

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Prestatiegegevens geven een toename in PercentThrottlingError aan
Er treden fouten op als u de schaalbaarheidsdoelen van een opslagservice overschrijdt. De opslagservice geeft gas om ervoor te zorgen dat geen enkele client of tenant de service kan gebruiken ten koste van anderen. Zie [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts](scalability-targets-standard-account.md) voor informatie over schaalbaarheidsdoelen voor opslagaccounts en prestatiedoelen voor partities binnen opslagaccounts voor meer informatie.

Als de statistiek **PercentThrottlingError** een toename laat zien van het percentage aanvragen dat niet optreedt met een beperkingsfout, moet u een van de twee scenario's onderzoeken:

* [Tijdelijke toename van PercentThrottlingError]
* [Permanente toename van percentmrottlingFout]

Een toename van **PercentThrottlingError** treedt vaak op hetzelfde moment op als een toename van het aantal opslagaanvragen of wanneer u uw toepassing in eerste instantie laadt. Dit kan zich ook manifesteren in de client als "503 Server Bezet" of "500 Operation Timeout" HTTP-statusberichten van opslagbewerkingen.

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Tijdelijke toename van PercentThrottlingError
Als u pieken ziet in de waarde van **PercentThrottlingError** die samenvallen met perioden van hoge activiteit voor de toepassing, implementeert u een exponentiële (niet lineaire) back-offstrategie voor nieuwe pogingen in uw client. Back-off retries verminderen de onmiddellijke belasting van de partitie en helpen uw toepassing om pieken in het verkeer glad te strijken. Zie de [naamruimte Microsoft.Azure.Storage.Storage.RetryPolicies](/dotnet/api/microsoft.azure.storage.retrypolicies)voor meer informatie over het implementeren van beleid voor het opnieuw proberen van de opslagclient.

> [!NOTE]
> U ook pieken zien in de waarde van **PercentThrottlingError** die niet samenvallen met perioden van hoge activiteit voor de toepassing: de meest waarschijnlijke oorzaak hier is de opslagservice bewegende partities om de taakverdeling te verbeteren.
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>Permanente toename van percentmrottlingFout
Als u een consistent hoge waarde ziet voor **PercentThrottlingError** na een permanente toename van uw transactievolumes of wanneer u uw eerste belastingstests uitvoert op uw toepassing, moet u evalueren hoe uw toepassing opslagpartities gebruikt en of deze de schaalbaarheidsdoelen voor een opslagaccount nadert. Als u bijvoorbeeld beperkingsfouten ziet in een wachtrij (die telt als één partitie), moet u overwegen extra wachtrijen te gebruiken om de transacties over meerdere partities te spreiden. Als u beperkingsfouten op een tabel ziet, moet u overwegen een ander partitieschema te gebruiken om uw transacties over meerdere partities te spreiden met behulp van een breder scala aan partitiesleutelwaarden. Een veel voorkomende oorzaak van dit probleem is het anti-patroon prepend/toevoegen waarbij u de datum als partitiesleutel selecteert en vervolgens alle gegevens op een bepaalde dag naar één partitie worden geschreven: onder belasting kan dit resulteren in een schrijfknelpunt. Overweeg een ander partitieontwerp of evalueer of het gebruik van blob-opslag een betere oplossing kan zijn. Controleer ook of beperking optreedt als gevolg van pieken in uw verkeer en onderzoek manieren om uw patroon van aanvragen glad te strijken.

Als u uw transacties over meerdere partities verdeelt, moet u nog steeds op de hoogte zijn van de schaalbaarheidslimieten die zijn ingesteld voor het opslagaccount. Als u bijvoorbeeld tien wachtrijen gebruikt die elk het maximum van 2.000 1KB-berichten per seconde verwerken, bevindt u zich op de totale limiet van 20.000 berichten per seconde voor het opslagaccount. Als u meer dan 20.000 entiteiten per seconde moet verwerken, u overwegen meerdere opslagaccounts te gebruiken. Houd er ook rekening mee dat de grootte van uw aanvragen en entiteiten van invloed is op wanneer de opslagservice uw klanten beperkt: als u grotere aanvragen en entiteiten hebt, u eerder worden beperkt.

Inefficiënt queryontwerp kan er ook toe leiden dat u de schaalbaarheidslimieten voor tabelpartities bereikt. Een query met een filter die slechts één procent van de entiteiten in een partitie selecteert, maar die alle entiteiten in een partitie scant, moet toegang krijgen tot elke entiteit. Elke gelezen entiteit telt mee voor het totale aantal transacties in die partitie; daarom u eenvoudig de schaalbaarheidsdoelstellingen bereiken.

> [!NOTE]
> Uw prestatietests moeten alle inefficiënte queryontwerpen in uw toepassing onthullen.
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Prestatiegegevens geven een toename in PercentTimeoutError aan
Uw statistieken tonen een toename van **PercentTimeoutError** voor een van uw opslagservices. Tegelijkertijd ontvangt de client een hoog volume van HTTP-statusberichten van 500 Operation Timeout van opslagbewerkingen.

> [!NOTE]
> U tijdelijk time-outfouten zien omdat de laadaanvragen van de opslagservice worden inevenwicht door een partitie naar een nieuwe server te verplaatsen.
>
>

De metric **PercentTimeoutError** is een aggregatie van de volgende statistieken: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**en **SASServerTimeoutError**.

De time-outs van de server worden veroorzaakt door een fout op de server. De time-outs van de client worden uitgevoerd omdat een bewerking op de server de door de client opgegeven time-out heeft overschreden. Een client die de opslagclientbibliotheek gebruikt, kan bijvoorbeeld een time-out voor een bewerking instellen met de eigenschap **ServerTimeout** van de klasse **QueueRequestOptions.**

Servertime-outs wijzen op een probleem met de opslagservice dat nader onderzoek vereist. U statistieken gebruiken om te zien of u de schaalbaarheidslimieten voor de service bereikt en om pieken in het verkeer te identificeren die dit probleem kunnen veroorzaken. Als het probleem met tussenpozen is, kan dit te wijten zijn aan taakverdelingsactiviteit in de service. Als het probleem aanhoudt en niet wordt veroorzaakt doordat uw toepassing de schaalbaarheidslimieten van de service bereikt, moet u een ondersteuningsprobleem opdeoren. Voor time-outs van klanten moet u beslissen of de time-out is ingesteld op een geschikte waarde in de client en de time-outwaarde in de client wijzigen of onderzoeken hoe u de prestaties van de bewerkingen in de opslagservice verbeteren, bijvoorbeeld door uw tabelquery's te optimaliseren of de grootte van uw berichten te verkleinen.

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>Prestatiegegevens geven een toename in PercentNetworkError aan
Uw statistieken tonen een toename van **PercentNetworkError** voor een van uw opslagservices. De statistiek **PercentNetworkError** is een aggregatie van de volgende statistieken: **NetworkError,** **AnonymousNetworkError**en **SASNetworkError**. Deze treden op wanneer de opslagservice een netwerkfout detecteert wanneer de client een opslagaanvraag indient.

De meest voorkomende oorzaak van deze fout is een client die de verbinding verbreekt voordat een time-out verloopt in de opslagservice. Onderzoek de code in uw client om te begrijpen waarom en wanneer de client de verbinding met de opslagservice verbreekt. U Wireshark, Microsoft Message Analyzer of Tcping ook gebruiken om problemen met de netwerkconnectiviteit van de client te onderzoeken. Deze tools worden beschreven in de [aanhangsels].

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>De client ontvangt HTTP 403-meldingen (verboden)
Als de clienttoepassing een HTTP 403-fout (verboden) weergeeft, is een vermoedelijke oorzaak dat de client gebruikmaakt van een verlopen Shared Access Signature (SAS) bij het verzenden van een opslagaanvraag (hoewel andere mogelijke oorzaken een tijdverschil, ongeldige sleutels of lege headers kunnen zijn). Als een verlopen SAS-sleutel de oorzaak is, ziet u geen vermeldingen in de logboekgegevens voor logboekregistratie voor opslag aan de serverzijde. In de volgende tabel ziet u een voorbeeld van het logboek aan de clientzijde dat is gegenereerd door de opslagclientbibliotheek, waaruit dit probleem wordt geïllustreerd:

| Bron | Uitgebreidheid | Uitgebreidheid | Clientaanvraag-id | Tekst voor bewerking |
| --- | --- | --- | --- | --- |
| Microsoft.Azure.Storage |Informatie |3 |85d077ab-... |De werking starten met locatie primair per locatiemodus PrimaryOnly. |
| Microsoft.Azure.Storage |Informatie |3 |85d077ab -... |Synchrone aanvraag starten naar<https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&sr=c&si=mypolicy&sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&api-version=2014-02-14> |
| Microsoft.Azure.Storage |Informatie |3 |85d077ab -... |Wachten op reactie. |
| Microsoft.Azure.Storage |Waarschuwing |2 |85d077ab -... |Uitzondering gegooid in afwachting van reactie: De externe server heeft een fout geretourneerd: (403) Verboden. |
| Microsoft.Azure.Storage |Informatie |3 |85d077ab -... |Reactie ontvangen. Statuscode = 403, Aanvraag-ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = . |
| Microsoft.Azure.Storage |Waarschuwing |2 |85d077ab -... |Uitzondering gegooid tijdens de bewerking: De externe server heeft een fout geretourneerd: (403) Verboden.. |
| Microsoft.Azure.Storage |Informatie |3 |85d077ab -... |Controleren of de bewerking opnieuw moet worden geprobeerd. Opnieuw proberen tellen = 0, HTTP-statuscode = 403, Uitzondering = De externe server heeft een fout geretourneerd: (403) Verboden.. |
| Microsoft.Azure.Storage |Informatie |3 |85d077ab -... |De volgende locatie is ingesteld op Primair, op basis van de locatiemodus. |
| Microsoft.Azure.Storage |Fout |1 |85d077ab -... |Het beleid opnieuw proberen stond geen nieuwe poging toe. Falen met De externe server leverde een fout op: (403) Verboden. |

In dit scenario moet u onderzoeken waarom het SAS-token verloopt voordat de client het token naar de server verzendt:

* Normaliter mag u geen begintijd instellen als u een SAS voor een client maakt voor onmiddellijk gebruik. Als er kleine tijdverschillen zijn tussen de host die de SAS genereert en die gebruikmaakt van de huidige tijd en de opslagservice, dan kan de opslagservice een SAS ontvangen die nog niet geldig is.
* Stel geen heel korte verlooptijd in voor een SAS. Kleine tijdverschillen tussen de host die de SAS genereert en de opslagservice kunnen er ook toe leiden dat een SAS kennelijk eerder verloopt dan verwacht.
* Komt de versieparameter in de SAS-toets (bijvoorbeeld **sv=2015-04-05)** overeen met de versie van de opslagclientbibliotheek die u gebruikt? We raden u aan altijd de nieuwste versie van de [opslagclientbibliotheek te](https://www.nuget.org/packages/WindowsAzure.Storage/)gebruiken.
* Als u uw opslagtoegangssleutels opnieuw genereert, kunnen eventuele SAS-tokens ongeldig worden. Dit probleem doet zich voor als u SAS-tokens genereert met een lange verlooptijd voor clienttoepassingen die in de cache worden opgeslagen.

Als u de Storage-clientbibliotheek gebruikt om SAS-tokens te genereren, dan kan er gemakkelijk een geldig token worden gemaakt. Als u echter de API Voor opslagrest gebruikt en de SAS-tokens met de hand bouwt, raadpleegt u [Het delegeren van toegang met een handtekening voor gedeelde toegang.](https://msdn.microsoft.com/library/azure/ee395415.aspx)

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>De client ontvangt HTTP 404-meldingen (niet gevonden)
Als de clienttoepassing een HTTP 404-melding (niet gevonden) van de server ontvangt, houdt dit in dat het object dat de client wilde gebruiken (zoals een entiteit, tabel, blob, container of wachtrij) niet in de opslagservice aanwezig is. Hiervoor zijn een aantal mogelijke redenen, bijvoorbeeld:

* [Het object is eerder door de client of een ander proces verwijderd]
* [Een probleem met de verificatie van een SAS (Shared Access Signature)]
* [JavaScript-code aan de clientzijde heeft geen toestemming voor toegang tot het object]
* [Netwerkfout]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>Het object is eerder door de client of een ander proces verwijderd
In scenario's waarin de client probeert gegevens in een opslagservice te lezen, bij te werken of te verwijderen, is het meestal eenvoudig om in de serverlogboeken een eerdere bewerking te identificeren die het object in kwestie uit de opslagservice heeft verwijderd. Vaak blijkt uit de loggegevens dat een andere gebruiker of proces het object heeft verwijderd. In het logboek opslaglogboekaan de serverzijde wordt in de kolommen van het type bewerking en de gevraagde objectsleutel weergegeven wanneer een client een object heeft verwijderd.

In het scenario waarin een client een object probeert in te voegen, is het mogelijk niet meteen duidelijk waarom dit resulteert in een http 404-reactie (Niet gevonden) omdat de client een nieuw object maakt. Als de client echter een blob maakt, moet deze de blobcontainer kunnen vinden, als de client een bericht maakt, moet deze een wachtrij kunnen vinden en als de client een rij toevoegt, moet deze de tabel kunnen vinden.

U het clientlogboek van de opslagclientbibliotheek gebruiken om een gedetailleerder inzicht te krijgen in wanneer de client specifieke aanvragen naar de opslagservice verzendt.

Het volgende logboek aan de clientzijde dat door de bibliotheek Opslagclient wordt gegenereerd, illustreert het probleem wanneer de client de container niet kan vinden voor de blob die wordt gemaakt. Dit logboek bevat details over de volgende opslagbewerkingen:

| Aanvraag-id | Bewerking |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists methode** om de blobcontainer te verwijderen. Houd er rekening mee dat deze bewerking een **HEAD-verzoek** bevat om te controleren op het bestaan van de container. |
| e2d06d78... |De methode **CreateIfNotExists** om de blobcontainer te maken. Houd er rekening mee dat deze bewerking een **HEAD-verzoek** bevat dat controleert op het bestaan van de container. Het **HOOFD** retourneert een 404-bericht, maar gaat verder. |
| de8b1c3c-... |**UploadFromStream-methode** om de blob te maken. Het **PUT-verzoek** mislukt met een 404-bericht |

Logboekvermeldingen:

| Aanvraag-id | Bewerkingstekst |
| --- | --- |
| 07b26a5d-... |Synchroonverzoek starten `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`naar . |
| 07b26a5d-... |StringToSign = HOOFD............ x-ms-client-request-id:07b26a5d-.... x-ms-datum:Di, 03 jun 2014 10:33:11 GMT.x-ms-versie:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Wachten op reactie. |
| 07b26a5d-... |Reactie ontvangen. Statuscode = 200, Aanvraag-id = eeead849-... Inhoud-MD5 = , &quot;ETag = 0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Antwoordkoppen zijn verwerkt en zijn doorgewerkt met de rest van de bewerking. |
| 07b26a5d-... |Het downloaden van reactie lichaam. |
| 07b26a5d-... |Bewerking voltooid. |
| 07b26a5d-... |Synchroonverzoek starten `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`naar . |
| 07b26a5d-... |StringToSign = DELETE............ x-ms-client-request-id:07b26a5d-.... x-ms-datum:Di, 03 jun 2014 10:33:12 GMT.x-ms-versie:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Wachten op reactie. |
| 07b26a5d-... |Reactie ontvangen. Statuscode = 202, Aanvraag-id = 6ab2a4cf-..., Inhoud-MD5 = , ETag = . |
| 07b26a5d-... |Antwoordkoppen zijn verwerkt en zijn doorgewerkt met de rest van de bewerking. |
| 07b26a5d-... |Het downloaden van reactie lichaam. |
| 07b26a5d-... |Bewerking voltooid. |
| e2d06d78-... |Asynchrone aanvraag `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`starten naar .</td> |
| e2d06d78-... |StringToSign = HOOFD............ x-ms-client-request-id:e2d06d78-.... x-ms-datum:Di, 03 jun 2014 10:33:12 GMT.x-ms-versie:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Wachten op reactie. |
| de8b1c3c-... |Synchroonverzoek starten `https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt`naar . |
| de8b1c3c-... |StringToSign = PUT... 64.qCmF+TQLPhq/YYK50mP9ZQ=........ x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-.... x-ms-datum:Di, 03 jun 2014 10:33:12 GMT.x-ms-versie:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Voorbereiden op het schrijven van aanvraaggegevens. |
| e2d06d78-... |Uitzondering gegooid in afwachting van reactie: De externe server heeft een fout geretourneerd: (404) Niet gevonden.. |
| e2d06d78-... |Reactie ontvangen. Statuscode = 404, aanvraag-id = 353ae3bc-..., Inhoud-MD5 = , ETag = . |
| e2d06d78-... |Antwoordkoppen zijn verwerkt en zijn doorgewerkt met de rest van de bewerking. |
| e2d06d78-... |Het downloaden van reactie lichaam. |
| e2d06d78-... |Bewerking voltooid. |
| e2d06d78-... |Asynchrone aanvraag `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`starten naar . |
| e2d06d78-... |StringToSign = PUT... 0.........x-ms-client-request-id:e2d06d78-.... x-ms-datum:Di, 03 jun 2014 10:33:12 GMT.x-ms-versie:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Wachten op reactie. |
| de8b1c3c-... |Het schrijven van aanvraaggegevens. |
| de8b1c3c-... |Wachten op reactie. |
| e2d06d78-... |Uitzondering gegooid in afwachting van reactie: De externe server heeft een fout geretourneerd: (409) Conflict.. |
| e2d06d78-... |Reactie ontvangen. Statuscode = 409, Aanvraag-id = c27da20e-..., Inhoud-MD5 = , ETag = . |
| e2d06d78-... |Foutreactiebody downloaden. |
| de8b1c3c-... |Uitzondering gegooid in afwachting van reactie: De externe server heeft een fout geretourneerd: (404) Niet gevonden.. |
| de8b1c3c-... |Reactie ontvangen. Statuscode = 404, Aanvraag-id = 0eaeab3e-..., Content-MD5 = , ETag = . |
| de8b1c3c-... |Uitzondering die tijdens de bewerking wordt gegenereerd: De externe server heeft een fout geretourneerd: (404) Niet gevonden.. |
| de8b1c3c-... |Het beleid opnieuw proberen stond geen nieuwe poging toe. Mislukt met De externe server heeft een fout geretourneerd: (404) Niet gevonden.. |
| e2d06d78-... |Het beleid opnieuw proberen stond geen nieuwe poging toe. Mislukt met De externe server heeft een fout geretourneerd: (409) Conflict.. |

In dit voorbeeld laat het logboek zien dat de client aanvragen van de **methode CreateIfNotExists** (aanvraag ID e2d06d78...) interleaving met de aanvragen van de **methode UploadFromStream** (de8b1c3c-...). Dit interleaving gebeurt omdat de clienttoepassing deze methoden asynchroon aanroept. Wijzig de asynchrone code in de client om ervoor te zorgen dat de container wordt gemaakt voordat u probeert gegevens naar een blob in die container te uploaden. Idealiter moet u al uw containers van tevoren maken.

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>Een probleem met de verificatie van een SAS (Shared Access Signature)
Als de clienttoepassing probeert een SAS-sleutel te gebruiken die niet de benodigde machtigingen voor de bewerking bevat, retourneert de opslagservice een HTTP 404-bericht (Niet gevonden) naar de client. Tegelijkertijd ziet u ook een niet-nulwaarde voor **SASAuthorizationError** in de statistieken.

In de volgende tabel ziet u een voorbeeldvan serverlogboekbericht uit het logboekbestand Opslaglogboekregistratie:

| Naam | Waarde |
| --- | --- |
| Begintijd aanvragen | 2014-05-30T06:17:48.4473697Z |
| Bewerkingstype     | GetBlobProperties            |
| Aanvraagstatus     | SASAuthorizationFout        |
| HTTP-statuscode   | 404                            |
| Verificatietype| Sas                          |
| Servicetype       | Blob                         |
| Aanvraag-URL         | `https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt` |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&;&sig=XXXXX api-version=2014-02-14 |
| Header van id aanvragen  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| Clientaanvraag-id  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Onderzoek waarom uw clienttoepassing een bewerking probeert uit te voeren waarvoor geen machtigingen zijn verleend.

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>JavaScript-code aan de clientzijde heeft geen toestemming voor toegang tot het object
Als u een JavaScript-client gebruikt en de opslagservice HTTP 404-berichten retoureert, controleert u op de volgende JavaScript-fouten in de browser:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> U de F12-hulpprogramma's voor ontwikkelaars in Internet Explorer gebruiken om de berichten te traceren die zijn uitgewisseld tussen de browser en de opslagservice wanneer u JavaScript-problemen aan de clientzijde oplost.
>
>

Deze fouten treden op omdat de webbrowser dezelfde beveiligingsbeperking voor [oorsprongsbeleid](https://www.w3.org/Security/wiki/Same_Origin_Policy) implementeert die voorkomt dat een webpagina een API aanroept in een ander domein dan het domein waar de pagina vandaan komt.

Als u het JavaScript-probleem wilt oplossen, u CorS (Cross Origin Resource Sharing) configureren voor de opslagservice die de client toegang heeft. Zie [CORS-ondersteuning (Cross-Origin Resource Sharing) voor Azure Storage Services voor](https://msdn.microsoft.com/library/azure/dn535601.aspx)meer informatie.

In het volgende codevoorbeeld ziet u hoe u uw blobservice configureert zodat JavaScript in het Contoso-domein toegang krijgt tot een blob in uw blob-opslagservice:

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

#### <a name="network-failure"></a><a name="network-failure"></a>Netwerkstoring
In sommige omstandigheden kunnen verloren netwerkpakketten ertoe leiden dat de opslagservice HTTP 404-berichten naar de client terugstuurt. Wanneer uw clienttoepassing bijvoorbeeld een entiteit uit de tabelservice verwijderd, ziet u dat de client een opslaguitzondering gooit die een statusbericht 'HTTP 404 (Niet gevonden) van de tabelservice rapporteert. Wanneer u de tabel in de tabelopslagservice onderzoekt, ziet u dat de service de entiteit op verzoek heeft verwijderd.

De uitzonderingsgegevens in de client omvatten de aanvraag-id (7e84f12d...) die door de tabelservice voor het verzoek is toegewezen: u deze informatie gebruiken om de aanvraaggegevens in de opslaglogboeken aan de serverzijde te vinden door te zoeken in de kolom **request-id-header** in het logboekbestand. U de statistieken ook gebruiken om te bepalen wanneer fouten zoals deze optreden en vervolgens de logboekbestanden doorzoeken op basis van het tijdstip waarop de statistieken deze fout hebben geregistreerd. Deze logboekvermelding laat zien dat de verwijdering is mislukt met een statusbericht 'HTTP(404) Client Other Error'. Dezelfde logvermelding bevat ook de aanvraag-ID gegenereerd door de client in de **client-request-id** kolom (813ea74f...).

Het serverloglog bevat ook een andere vermelding met dezelfde **client-request-id-waarde** (813ea74f...) voor een succesvolle verwijderingsbewerking voor dezelfde entiteit en van dezelfde client. Deze succesvolle verwijderingsbewerking vond plaats kort voordat het mislukte verwijderingsverzoek is mislukt.

De meest waarschijnlijke oorzaak van dit scenario is dat de client een verwijderverzoek voor de entiteit heeft verzonden naar de tabelservice, die is geslaagd, maar geen bevestiging van de server heeft ontvangen (mogelijk als gevolg van een tijdelijk netwerkprobleem). De client heeft de bewerking vervolgens automatisch opnieuw geprobeerd (met dezelfde **client-request-id)** en deze nieuwe poging is mislukt omdat de entiteit al was verwijderd.

Als dit probleem zich vaak voordoet, moet u onderzoeken waarom de client geen bevestigingen van de tabelservice ontvangt. Als het probleem met tussenpozen is, moet u de fout 'HTTP (404) niet gevonden' opvullen en inloggen in de client, maar de client laten doorgaan.

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>De client ontvangt HTTP 409-meldingen (conflict)
In de volgende tabel ziet u een uittreksel uit het serverlogboek voor twee clientbewerkingen: **DeleteIfExists** wordt onmiddellijk gevolgd door **CreateIfNotExists** met dezelfde blobcontainernaam. Elke clientbewerking resulteert in twee aanvragen die naar de server worden verzonden, eerst een **GetContainerProperties-verzoek** om te controleren of de container bestaat, gevolgd door de aanvraag **DeleteContainer** of **CreateContainer.**

| Tijdstempel | Bewerking | Resultaat | Containernaam | Clientaanvraag-id |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties (GetContainerProperties) |200 |mmcont |c9f52c89-... |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-... |
| 05:10:13.8987407 |GetContainerProperties (GetContainerProperties) |404 |mmcont |bc881924-... |
| 05:10:14.2147723 |Container maken |409 |mmcont |bc881924-... |

De code in de clienttoepassing verwijdert en maakt vervolgens onmiddellijk een blobcontainer met dezelfde naam opnieuw: de methode **CreateIfNotExists** (Client request ID bc881924-...) mislukt uiteindelijk met de HTTP 409 -fout (Conflict). Als een client blobcontainers, tabellen of wachtrijen verwijderd, is er een korte periode voordat de naam opnieuw beschikbaar is.

De clienttoepassing moet een unieke containernaam gebruiken wanneer er een nieuwe container wordt gemaakt als het patroon voor verwijderen/opnieuw maken algemeen is.

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>Statistieken tonen lage PercentEnsucces of analytics log items hebben bewerkingen met transactiestatus van ClientOtherErrors
De statistiek **PercentSuccess** legt het percentage bewerkingen vast dat is uitgevoerd op basis van hun HTTP-statuscode. Bewerkingen met statuscodes van 2XX worden als succesvol beschouwd, terwijl bewerkingen met statuscodes in 3XX-, 4XX- en 5XX-reeksen worden geteld als mislukt en de **percentsuccess-metrische** waarde verlagen. In de opslaglogboekbestanden aan de serverzijde worden deze bewerkingen geregistreerd met een transactiestatus van **ClientOtherErrors**.

Het is belangrijk op te merken dat deze bewerkingen met succes zijn voltooid en daarom geen invloed hebben op andere statistieken, zoals beschikbaarheid. Enkele voorbeelden van bewerkingen die succesvol worden uitgevoerd, maar die kunnen resulteren in mislukte HTTP-statuscodes zijn:

* **ResourceNotFound** (Niet gevonden 404), bijvoorbeeld van een GET-aanvraag tot een blob die niet bestaat.
* **ResourceAlreadyExists** (Conflict 409), bijvoorbeeld van een **createifnotexist-bewerking** waar de resource al bestaat.
* **ConditionNotMet** (Niet gewijzigd 304), bijvoorbeeld vanuit een voorwaardelijke bewerking, bijvoorbeeld wanneer een client een **ETag-waarde** en een HTTP **If-None-Match-header** verzendt om een afbeelding alleen aan te vragen als deze is bijgewerkt sinds de laatste bewerking.

U een lijst met algemene REST API-foutcodes vinden die de opslagservices retourneren op de pagina [Common REST API Error Codes](https://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>Capaciteitsstatistieken laten een onverwachte toename van het gebruik van de opslagcapaciteit zien
Als u plotselinge, onverwachte wijzigingen in het capaciteitsgebruik in uw opslagaccount ziet, u de redenen onderzoeken door eerst naar uw beschikbaarheidsstatistieken te kijken; Een toename van het aantal mislukte verwijderingsaanvragen kan bijvoorbeeld leiden tot een toename van de hoeveelheid blob-opslag die u gebruikt als toepassingsspecifieke opruimbewerkingen waarvan u zou verwachten dat ze ruimte vrijmaken, werken mogelijk niet zoals verwacht (bijvoorbeeld omdat de SAS-tokens die worden gebruikt voor het vrijmaken van ruimte zijn verlopen).

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>Uw probleem ontstaat door het gebruik van de opslagemulator voor ontwikkeling of test
U gebruikt de opslagemulator meestal tijdens de ontwikkeling en test om de vereiste voor een Azure-opslagaccount te voorkomen. De veelvoorkomende problemen die kunnen optreden wanneer u de opslagemulator gebruikt, zijn:

* [Functie "X" werkt niet in de opslagemulator]
* [Fout "De waarde voor een van de HTTP-headers is niet in de juiste indeling" bij het gebruik van de opslagemulator]
* [Voor het uitvoeren van de opslagemulator zijn beheerdersbevoegdheden vereist]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>Functie "X" werkt niet in de opslagemulator
De opslagemulator biedt geen ondersteuning voor alle functies van de Azure-opslagservices, zoals de bestandsservice. Zie [De Azure Storage Emulator gebruiken voor ontwikkeling en testen voor](storage-use-emulator.md)meer informatie.

Voor de functies die de opslagemulator niet ondersteunt, gebruikt u de Azure-opslagservice in de cloud.

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>Fout "De waarde voor een van de HTTP-headers is niet in de juiste indeling" bij het gebruik van de opslagemulator
U test uw toepassing die de opslagclientbibliotheek gebruikt op basis van de lokale opslagemulator en methodeaanroepen zoals **CreateIfNotExists** mislukken met het foutbericht 'De waarde voor een van de HTTP-headers is niet in de juiste indeling'. Dit geeft aan dat de versie van de opslagemulator die u gebruikt, geen ondersteuning biedt voor de versie van de opslagclientbibliotheek die u gebruikt. De Opslagclientbibliotheek voegt de **x-ms-versie** met de koptekst toe aan alle aanvragen die worden ingediend. Als de opslagemulator de waarde in de **x-ms-versiekop** niet herkent, wijst deze het verzoek af.

U de logboeken van de opslagbibliotheekclient gebruiken om de waarde te zien van de **x-ms-versiekop die** wordt verzonden. U ook de waarde van de **x-ms-versieheader** zien als u Fiddler gebruikt om de aanvragen van uw clienttoepassing te traceren.

Dit scenario treedt meestal op als u de nieuwste versie van de opslagclientbibliotheek installeert en gebruikt zonder de opslagemulator bij te werken. U moet de nieuwste versie van de opslagemulator installeren of cloudopslag gebruiken in plaats van de emulator voor ontwikkeling en test.

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>Voor het uitvoeren van de opslagemulator zijn beheerdersbevoegdheden vereist
U wordt gevraagd om beheerdersreferenties wanneer u de opslagemulator uitvoert. Dit gebeurt alleen wanneer u de opslagemulator voor de eerste keer initialiseert. Nadat u de opslagemulator hebt geïnitialiseerd, hebt u geen beheerdersbevoegdheden nodig om deze opnieuw uit te voeren.

Zie [De Azure Storage Emulator gebruiken voor ontwikkeling en testen voor](storage-use-emulator.md)meer informatie. U de opslagemulator ook initialiseren in Visual Studio, waarvoor ook beheerdersbevoegdheden nodig zijn.

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>U ondervindt problemen met het installeren van de Azure SDK voor .NET
Wanneer u de SDK probeert te installeren, wordt de opslagemulator niet op uw lokale machine geïnstalleerd. Het installatielogboek bevat een van de volgende berichten:

* CAQuietExec: Fout: kan geen toegang krijgen tot SQL-instantie
* CAQuietExec: Fout: kan geen database maken

De oorzaak is een probleem met de bestaande LocalDB-installatie. Standaard gebruikt de opslagemulator LocalDB om gegevens te blijven bestaan wanneer de Azure-opslagservices worden gesimuleerd. U uw LocalDB-exemplaar opnieuw instellen door de volgende opdrachten in een opdrachtpromptvenster uit te voeren voordat u de SDK probeert te installeren.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

Met de opdracht **Verwijderen** worden alle oude databasebestanden verwijderd uit eerdere installaties van de opslagemulator.

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>U hebt een ander probleem met een opslagservice
Als de vorige secties voor het oplossen van problemen niet het probleem bevatten dat u hebt met een opslagservice, moet u de volgende benadering hanteren voor het diagnosticeren en oplossen van uw probleem.

* Controleer uw statistieken om te zien of er een wijziging is ten opzichte van uw verwachte basislijngedrag. Aan de hand van de statistieken u mogelijk bepalen of het probleem van voorbijgaande aard of permanent is en welke opslagbewerkingen het probleem beïnvloedt.
* U de statistiekengegevens gebruiken om u te helpen bij het doorzoeken van uw loggegevens aan de serverzijde voor meer gedetailleerde informatie over eventuele fouten die zich voordoen. Met deze informatie u het probleem oplossen en oplossen.
* Als de informatie in de serverlogboeken niet voldoende is om het probleem op te lossen, u de logboeken aan de clientzijde van de opslagclientbibliotheek gebruiken om het gedrag van uw clienttoepassing te onderzoeken en hulpprogramma's zoals Fiddler, Wireshark en Microsoft Message Analyzer om uw netwerk te onderzoeken.

Zie "[Bijlage 1: Fiddler gebruiken om HTTP- en HTTPS-verkeer vast te leggen]" voor meer informatie over het gebruik van Fiddler.

Zie "[Bijlage 2: Wireshark gebruiken om netwerkverkeer vast te leggen]" voor meer informatie over het gebruik van Wireshark.

Zie[Bijlage 3: Microsoft Message Analyzer gebruiken om netwerkverkeer vast te leggen voor]meer informatie over het gebruik van Microsoft Message Analyzer.

## <a name="appendices"></a><a name="appendices"></a>Bijlagen
De bijlagen beschrijven verschillende hulpprogramma's die u nuttig vinden wanneer u problemen met Azure Storage (en andere services) diagnosticert en oplost. Deze hulpprogramma's maken geen deel uit van Azure Storage en sommige zijn producten van derden. Als zodanig vallen de hulpprogramma's die in deze bijlagen worden besproken niet onder een ondersteuningsovereenkomst die u mogelijk hebt met Microsoft Azure of Azure Storage, en daarom moet u als onderdeel van uw evaluatieproces de licentie- en ondersteuningsopties onderzoeken die beschikbaar zijn bij de providers van deze hulpprogramma's.

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>Bijlage 1: Fiddler gebruiken om HTTP- en HTTPS-verkeer vast te leggen
[Fiddler](https://www.telerik.com/fiddler) is een handig hulpmiddel voor het analyseren van het HTTP- en HTTPS-verkeer tussen uw clienttoepassing en de Azure-opslagservice die u gebruikt.

> [!NOTE]
> Fiddler kan HTTPS-verkeer decoderen; u moet de Fiddler-documentatie zorgvuldig lezen om te begrijpen hoe deze dit doet en om de gevolgen voor de beveiliging te begrijpen.
>
>

Deze bijlage biedt een korte walkthrough van hoe Fiddler te configureren om verkeer vast te leggen tussen de lokale machine waar u Fiddler en de Azure-opslagservices hebt geïnstalleerd.

Nadat u Fiddler hebt gestart, begint het met het vastleggen van HTTP- en HTTPS-verkeer op uw lokale machine. Hieronder volgen enkele handige opdrachten voor het besturen van Fiddler:

* Stop en begin met het vastleggen van verkeer. Ga in het hoofdmenu naar **Bestand** en klik vervolgens op **Verkeer vastleggen** om het vastleggen aan en uit te schakelen.
* Opgeslagen vastgelegde verkeersgegevens. Ga in het hoofdmenu naar **Bestand,** klik op **Opslaan**en klik vervolgens op **Alle sessies:** hiermee u het verkeer opslaan in een sessiearchiefbestand. U een sessiearchief later opnieuw laden voor analyse of het op verzoek naar Microsoft-ondersteuning verzenden.

Als u de hoeveelheid verkeer wilt beperken die Fiddler vastlegt, u filters gebruiken die u configureert op het tabblad **Filters.** In de volgende schermafbeelding wordt een filter weergegeven dat alleen verkeer vastlegt dat naar het eindpunt van de **opslag contosoemaildist.table.core.windows.net** wordt verzonden:

![][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>Aanhangsel 2: Wireshark gebruiken om netwerkverkeer vast te leggen
[Wireshark](https://www.wireshark.org/) is een netwerkprotocol analyzer waarmee u gedetailleerde pakketinformatie bekijken voor een breed scala aan netwerkprotocollen.

In de volgende procedure ziet u hoe u gedetailleerde pakketgegevens vastleggen voor verkeer van de lokale machine waar u Wireshark hebt geïnstalleerd op de tabelservice in uw Azure-opslagaccount.

1. Lanceer Wireshark op je lokale machine.
2. Selecteer **in** de sectie Start de lokale netwerkinterface of interfaces die zijn verbonden met internet.
3. Klik **op Opties voor vastleggen**.
4. Voeg een filter toe aan het tekstvak **Filter vastleggen.** **Host-contosoemaildist.table.core.windows.net** configureren bijvoorbeeld Wireshark om alleen pakketten vast te leggen die naar of van het eindpunt van de tabelservice worden verzonden in het opslagaccount voor **contoso-e-maildist.** Bekijk de [volledige lijst met opnamefilters.](https://wiki.wireshark.org/CaptureFilters)

   ![][6]
5. Klik op **Start**. Wireshark zal nu alle pakketten vastleggen die naar of van het eindpunt van de tafelservice worden verzonden terwijl u uw clienttoepassing op uw lokale machine gebruikt.
6. Wanneer u klaar bent, klikt u in het hoofdmenu op **Vastleggen** en **vervolgens Stoppen.**
7. Als u de vastgelegde gegevens wilt opslaan in een Wireshark Capture-bestand, klikt u in het hoofdmenu op **Bestand** en **slaat u vervolgens Op.**

WireShark zal eventuele fouten in het venster van de **pakketlijst** markeren. U ook het venster **Expertinfo** (klik op **Analyseren**, vervolgens **Expert info)** gebruiken om een overzicht van fouten en waarschuwingen weer te geven.

![][7]

U er ook voor kiezen om de TCP-gegevens te bekijken zoals de toepassingslaag het ziet door met de rechtermuisknop op de TCP-gegevens te klikken en **TCP Stream volgen**te selecteren. Dit is handig als u uw dump hebt vastgelegd zonder een capture-filter. Zie [TCP-streams volgen](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html)voor meer informatie .

![][8]

> [!NOTE]
> Zie de [Wireshark Users Guide](https://www.wireshark.org/docs/wsug_html_chunked)voor meer informatie over het gebruik van Wireshark.
>
>

### <a name="appendix-3-using-microsoft-message-analyzer-to-capture-network-traffic"></a><a name="appendix-3"></a>Aanhangsel 3: Microsoft Message Analyzer gebruiken om netwerkverkeer vast te leggen
U Microsoft Message Analyzer gebruiken om HTTP- en HTTPS-verkeer vast te leggen op een vergelijkbare manier als Fiddler en netwerkverkeer vast te leggen op een vergelijkbare manier als Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Een webtraceringssessie configureren met Microsoft Message Analyzer
Als u een webtraceringssessie wilt configureren voor HTTP- en HTTPS-verkeer met Microsoft Message Analyzer, voert u de microsoft Message Analyzer-toepassing uit en klikt u vervolgens in het menu **Bestand** op **Vastleggen/traceren**. Selecteer **Webproxy**in de lijst met beschikbare traceringsscenario's . Voeg vervolgens in het deelvenster **Configuratie van het scenario traceren** in het tekstvak **HostnameFilter** de namen van uw opslageindpunten toe (u deze namen opzoeken in de [Azure-portal).](https://portal.azure.com) Als de naam van uw Azure-opslagaccount bijvoorbeeld **contosodata**is, moet u het volgende toevoegen aan het tekstvak **HostnameFilter:**

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Een spatieteken scheidt de hostnamen.
>
>

Wanneer u klaar bent om traceergegevens te verzamelen, klikt u op de knop **Start met.**

Zie [Microsoft-PEF-WebProxy Provider](https://technet.microsoft.com/library/jj674814.aspx)voor meer informatie over de traceer van Microsoft Message **Analyzer Web Proxy.**

De ingebouwde **webproxytracer** in Microsoft Message Analyzer is gebaseerd op Fiddler; het kan HTTPS-verkeer aan clientzijde vastleggen en onversleutelde HTTPS-berichten weergeven. De **webproxytraceer** werkt door een lokale proxy te configureren voor al het HTTP- en HTTPS-verkeer dat toegang geeft tot onversleutelde berichten.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Netwerkproblemen diagnosticeren met Microsoft Message Analyzer
Naast het gebruik van de Microsoft Message Analyzer **Web Proxy** trace om details van het HTTP/HTTPs-verkeer tussen de clienttoepassing en de opslagservice vast te leggen, u ook de ingebouwde Local Link **Layer trace** gebruiken om netwerkpakketinformatie vast te leggen. Hiermee u gegevens vastleggen die vergelijkbaar zijn met die welke u vastleggen met Wireshark, en netwerkproblemen zoals gedropte pakketten diagnosticeren.

In de volgende schermafbeelding ziet u een voorbeeld **van de traceringslaag** van de lokale koppeling met enkele **informatieve** berichten in de kolom **DiagnosisTypes.** Als u op een pictogram in de kolom **DiagnosisTypes klikt,** worden de details van het bericht weergegeven. In dit voorbeeld heeft de server het bericht opnieuw verzonden #305 omdat het geen bevestiging van de client heeft ontvangen:

![][9]

Wanneer u de traceringssessie maakt in Microsoft Message Analyzer, u filters opgeven om de hoeveelheid ruis in het traceerbericht te verminderen. Klik **op** de pagina Vastleggen / traceren waar u de tracering definieert op de koppeling **Configureren** naast **Microsoft-Windows-NDIS-PacketCapture.** In de volgende schermafbeelding wordt een configuratie weergegeven die TCP-verkeer filtert voor de IP-adressen van drie opslagservices:

![][10]

Zie [Microsoft-PEF-NDIS-PacketCapture Provider](https://technet.microsoft.com/library/jj659264.aspx)voor meer informatie over de lokale koppelingslaag van Microsoft Message Analyzer.

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>Aanhangsel 4: Excel gebruiken om statistieken en logboekgegevens weer te geven
Met veel hulpprogramma's u de gegevens over opslagstatistieken van Azure-tabelopslag downloaden in een beperkte indeling, waardoor u de gegevens eenvoudig in Excel laden voor weergave en analyse. Opslaglogboekregistratiegegevens uit Azure blob-opslag bevinden zich al in een beperkte indeling die u in Excel laden. U moet echter wel de juiste kolomkoppen toevoegen op basis van de informatie bij [Storage Analytics Log Format](https://msdn.microsoft.com/library/azure/hh343259.aspx) and Storage Analytics Metrics Table [Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx).

Ga als u uw opslaglogboekregistratiegegevens in Excel wilt importeren nadat u deze hebt gedownload van blobopslag:

* Klik **in** het menu Gegevens op **Van tekst**.
* Blader naar het logboekbestand dat u wilt weergeven en klik op **Importeren**.
* Selecteer In stap 1 van de **wizard Tekst importeren**de optie **Afgebakend**.

Selecteer **puntkomma** als enige scheidingsteken in stap 1 van de **wizard Tekst importeren**en kies dubbele aanhalingsteken als de kwalificatie **tekst**. Klik vervolgens op **Voltooien** en kies waar u de gegevens in uw werkmap wilt plaatsen.

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>Bijlage 5: Monitoring met toepassingsinzichten voor Azure DevOps
U de functie Application Insights ook gebruiken voor Azure DevOps als onderdeel van uw prestatie- en beschikbaarheidsbewaking. Deze tool kan:

* Zorg ervoor dat uw webservice beschikbaar en responsief is. Of uw app nu een website is of een apparaat-app die een webservice gebruikt, deze kan uw URL om de paar minuten testen vanaf locaties over de hele wereld en u laten weten of er een probleem is.
* Diagnose van eventuele prestatieproblemen of uitzonderingen in uw webservice. Zoek uit of CPU of andere bronnen worden uitgerekt, krijg stacksporen van uitzonderingen en zoek eenvoudig door logsporen. Als de prestaties van de app onder acceptabele limieten dalen, kan Microsoft u een e-mail sturen. U zowel .NET- als Java-webservices controleren.

Meer informatie vindt u bij [Wat is Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over analyses in Azure Storage:

* [Een Storage-account bewaken in de Azure-portal](storage-monitor-storage-account.md)
* [Opslaganalyse](storage-analytics.md)
* [Statistieken voor opslaganalyses](storage-analytics-metrics.md)
* [Tabelschema voor tabelgegevens voor opslaganalyses](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Logboeken voor opslaganalyse](storage-analytics-logging.md)
* [Opslaganalyselogboekindeling](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Inleiding]: #introduction
[Hoe deze gids is georganiseerd]: #how-this-guide-is-organized

[Uw opslagservice bewaken]: #monitoring-your-storage-service
[Toezicht op de status van de dienst]: #monitoring-service-health
[Controlecapaciteit]: #monitoring-capacity
[Beschikbaarheid bewaken]: #monitoring-availability
[Prestaties monitoren]: #monitoring-performance

[Opslagproblemen diagnosticeren]: #diagnosing-storage-issues
[Problemen met de status van de service]: #service-health-issues
[Prestatieproblemen]: #performance-issues
[Fouten diagnosticeren]: #diagnosing-errors
[Problemen met opslagemulators]: #storage-emulator-issues
[Hulpprogramma's voor opslaglogboekregistratie]: #storage-logging-tools
[Hulpprogramma's voor netwerklogboekregistratie gebruiken]: #using-network-logging-tools

[End-to-end tracering]: #end-to-end-tracing
[Loggegevens correleren]: #correlating-log-data
[Clientaanvraag-id]: #client-request-id
[Serveraanvraag-id]: #server-request-id
[Timestamps]: #timestamps

[Richtlijnen voor probleemoplossing]: #troubleshooting-guidance
[Prestatiegegevens geven hoge AverageE2ELatency en lage AverageServerLatency aan]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Prestatiegegevens geven lage AverageE2ELatency en lage AverageServerLatency aan, maar de client ondervindt hoge latentie]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Prestatiegegevens geven hoge AverageServerLatency aan]: #metrics-show-high-AverageServerLatency
[U ervaart onverwachte vertragingen bij de levering van berichten in een wachtrij]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Prestatiegegevens geven een toename in PercentThrottlingError aan]: #metrics-show-an-increase-in-PercentThrottlingError
[Tijdelijke toename van PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Permanente toename van percentmrottlingFout]: #permanent-increase-in-PercentThrottlingError
[Prestatiegegevens geven een toename in PercentTimeoutError aan]: #metrics-show-an-increase-in-PercentTimeoutError
[Prestatiegegevens geven een toename in PercentNetworkError aan]: #metrics-show-an-increase-in-PercentNetworkError

[De client ontvangt HTTP 403-meldingen (verboden)]: #the-client-is-receiving-403-messages
[De client ontvangt HTTP 404-meldingen (niet gevonden)]: #the-client-is-receiving-404-messages
[Het object is eerder door de client of een ander proces verwijderd]: #client-previously-deleted-the-object
[Een probleem met de verificatie van een SAS (Shared Access Signature)]: #SAS-authorization-issue
[JavaScript-code aan de clientzijde heeft geen toestemming voor toegang tot het object]: #JavaScript-code-does-not-have-permission
[Netwerkfout]: #network-failure
[De client ontvangt HTTP 409-meldingen (conflict)]: #the-client-is-receiving-409-messages

[Statistieken tonen lage PercentEnsucces of analytics log items hebben bewerkingen met transactiestatus van ClientOtherErrors]: #metrics-show-low-percent-success
[Capaciteitsstatistieken laten een onverwachte toename van het gebruik van de opslagcapaciteit zien]: #capacity-metrics-show-an-unexpected-increase
[Uw probleem ontstaat door het gebruik van de opslagemulator voor ontwikkeling of test]: #your-issue-arises-from-using-the-storage-emulator
[Functie "X" werkt niet in de opslagemulator]: #feature-X-is-not-working
[Fout "De waarde voor een van de HTTP-headers is niet in de juiste indeling" bij het gebruik van de opslagemulator]: #error-HTTP-header-not-correct-format
[Voor het uitvoeren van de opslagemulator zijn beheerdersbevoegdheden vereist]: #storage-emulator-requires-administrative-privileges
[U ondervindt problemen met het installeren van de Azure SDK voor .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[U hebt een ander probleem met een opslagservice]: #you-have-a-different-issue-with-a-storage-service

[Bijlagen]: #appendices
[Bijlage 1: Fiddler gebruiken om HTTP- en HTTPS-verkeer vast te leggen]: #appendix-1
[Aanhangsel 2: Wireshark gebruiken om netwerkverkeer vast te leggen]: #appendix-2
[Aanhangsel 3: Microsoft Message Analyzer gebruiken om netwerkverkeer vast te leggen]: #appendix-3
[Aanhangsel 4: Excel gebruiken om statistieken en logboekgegevens weer te geven]: #appendix-4
[Bijlage 5: Monitoring met toepassingsinzichten voor Azure DevOps]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
