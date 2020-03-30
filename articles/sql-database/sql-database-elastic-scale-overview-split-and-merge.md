---
title: Gegevens verplaatsen tussen uitgeschaalde clouddatabases
description: Legt uit hoe u scherven manipuleren en gegevens verplaatsen via een zelfgehoste service met behulp van elastische database-API's.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 8b0db4a1e55b53165e40e176834d66b62926e24b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74421554"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Gegevens verplaatsen tussen uitgeschaalde clouddatabases

Als u een Software als een Ontwikkelaar van de Dienst bent, en plotseling ondergaat uw app enorme vraag, moet u de groei aanpassen. Zo voegt u meer databases (scherven) toe. Hoe distribueert u de gegevens naar de nieuwe databases zonder de gegevensintegriteit te verstoren? Gebruik het **gereedschap splitsen om** gegevens van beperkte databases naar de nieuwe databases te verplaatsen.  

Het hulpprogramma voor splitsen samenvoegen wordt uitgevoerd als een Azure-webservice. Een beheerder of ontwikkelaar gebruikt de tool om shardlets (gegevens uit een scherf) tussen verschillende databases (shards) te verplaatsen. De tool maakt gebruik van shard mapbeheer om de database met metagegevens van de service te onderhouden en consistente toewijzingen te garanderen.

![Overzicht][1]

## <a name="download"></a>Download

[Microsoft.azure.SqlDatabase.Elasticscale.Service.SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Documentatie

1. [Zelfstudie van het gereedschap Samenvoegbewerking van elastic database Split-Merge](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Beveiligingsconfiguratie splitsen samenvoegen](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Beveiligingsoverwegingen voor splitsen samenvoegen](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Shard-toewijzingsbeheer](sql-database-elastic-scale-shard-map-management.md)
5. [Bestaande databases migreren voor uitschaling](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Elastische databasegereedschappen](sql-database-elastic-scale-introduction.md)
7. [Woordenlijst met elastische databasegereedschappen](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Waarom het gereedschap splitsen samenvoegen gebruiken

- **Flexibiliteit**

  Toepassingen moeten flexibel uitrekken buiten de grenzen van één Azure SQL DB-database. Gebruik de tool om gegevens zo nodig naar nieuwe databases te verplaatsen met behoud van integriteit.

- **Splitsen om te groeien**

  Om de totale capaciteit om explosieve groei aan te kunnen te vergroten, creëert u extra capaciteit door de gegevens te sharden en door deze stapsgewijs meer databases te distribueren totdat aan de capaciteitsbehoeften is voldaan. Dit is een goed voorbeeld van de **gesplitste** functie.

- **Samenvoegen om te krimpen**

  De capaciteitsbehoeften krimpen door het seizoensgebonden karakter van een bedrijf. Met de tool u omlaag schalen naar minder schaaleenheden wanneer het bedrijf vertraagt. De functie 'samenvoegen' in de Elastic Scale split-merge-service dekt deze vereiste.

- **Hotspots beheren door shardlets te verplaatsen**

  Met meerdere tenants per database kan de toewijzing van shardlets aan shards leiden tot capaciteitsknelpunten op sommige scherven. Dit vereist het opnieuw toewijzen van shardlets of het verplaatsen van drukke shardlets naar nieuwe of minder gebruikte scherven.

## <a name="concepts--key-features"></a>Concepten & de belangrijkste kenmerken

- **Door de klant gehoste services**

  De split-merge wordt geleverd als een door de klant gehoste service. U moet de service implementeren en hosten in uw Microsoft Azure-abonnement. Het pakket dat u downloadt van NuGet bevat een configuratiesjabloon om te voltooien met de informatie voor uw specifieke implementatie. Zie de [zelfstudie voor het samenvoegen](sql-database-elastic-scale-configure-deploy-split-and-merge.md) voor meer informatie. Aangezien de service wordt uitgevoerd in uw Azure-abonnement, u de meeste beveiligingsaspecten van de service beheren en configureren. De standaardsjabloon bevat de opties voor het configureren van SSL, op certificaten gebaseerde clientverificatie, versleuteling voor opgeslagen referenties, DoS-bewaking en IP-beperkingen. Meer informatie over de beveiligingsaspecten vindt u in de volgende [beveiligingsconfiguratie voor het samenvoegen van](sql-database-elastic-scale-split-merge-security-configuration.md)documenten.

  De standaard geïmplementeerde service wordt uitgevoerd met één werknemer en één webrol. Elk maakt gebruik van de A1 VM-grootte in Azure Cloud Services. Hoewel u deze instellingen niet wijzigen bij het implementeren van het pakket, u deze wijzigen na een succesvolle implementatie in de lopende cloudservice (via de Azure-portal). Houd er rekening mee dat de werkrol om technische redenen niet om meer dan één exemplaar mag worden geconfigureerd.

- **Shard kaart integratie**

  De split-merge-service werkt samen met de shardkaart van de toepassing. Wanneer u de split-merge-service gebruikt om bereiken te splitsen of samen te voegen of om shardlets tussen shards te verplaatsen, houdt de service de shardkaart automatisch up-to-date. Hiervoor maakt de service verbinding met de shardmapmanagerdatabase van de toepassing en onderhoudt het bereiken en toewijzingen als voortgang van split/merge/move-aanvragen. Dit zorgt ervoor dat de shardkaart altijd een up-to-date weergave biedt wanneer gesplitste samenvoegbewerkingen gaande zijn. Splits-, merge- en shardletbewegingsbewerkingen worden geïmplementeerd door een batch shardlets van de bronscherf naar de doelshard te verplaatsen. Tijdens de shardlet-verplaatsingsbewerking worden de shardlets waarop de huidige batch van toepassing is, gemarkeerd als offline in de shardmap en zijn ze niet beschikbaar voor gegevensafhankelijke routeringsverbindingen met behulp van de **OpenConnectionForKey** API.

- **Consistente shardletverbindingen**

  Wanneer de gegevensverplaatsing begint voor een nieuwe batch shardlets, worden alle shard-map verstrekte gegevensafhankelijke routeringsverbindingen naar de shard die de shardlet opslaat, gedood en worden de daaropvolgende verbindingen van de shardkaart-API's met de shardlets geblokkeerd terwijl de gegevensverplaatsing in uitvoering om inconsistenties te voorkomen. Verbindingen met andere scherven op dezelfde scherf zal ook gedood, maar zal onmiddellijk weer slagen op opnieuw proberen. Zodra de batch is verplaatst, worden de shardlets weer online gemarkeerd voor de doelshard en worden de brongegevens uit de bronshard verwijderd. De service gaat door deze stappen voor elke batch totdat alle shardlets zijn verplaatst. Dit zal leiden tot verschillende connection kill operaties in de loop van de volledige split /merge/move operatie.  

- **Beschikbaarheid shardlet beheren**

  Het beperken van de verbinding doden met de huidige partij van shardlets zoals hierboven besproken beperkt de reikwijdte van de onbeschikbaarheid tot een partij van shardlets op een moment. Dit heeft de voorkeur boven een aanpak waarbij de volledige shard offline blijft voor al zijn shardlets tijdens een gesplitste of samenvoegbewerking. De grootte van een batch, gedefinieerd als het aantal verschillende shardlets dat tegelijk moet worden verplaatst, is een configuratieparameter. Het kan worden gedefinieerd voor elke gesplitste en samenvoegbewerking, afhankelijk van de beschikbaarheid en prestatiebehoeften van de toepassing. Houd er rekening mee dat het bereik dat wordt vergrendeld in de shardkaart groter kan zijn dan de opgegeven batchgrootte. Dit komt omdat de service de grootte van het bereik zo kiest dat het werkelijke aantal shardingssleutelwaarden in de gegevens ongeveer overeenkomt met de batchgrootte. Dit is belangrijk om te onthouden in het bijzonder voor dunbevolkte sharding sleutels.

- **Opslag van metagegevens**

  De split-merge-service gebruikt een database om de status te behouden en logboeken bij te houden tijdens de verwerking van aanvragen. De gebruiker maakt deze database in zijn abonnement en biedt de verbindingstekenreeks hiervoor in het configuratiebestand voor de service-implementatie. Beheerders van de organisatie van de gebruiker kunnen ook verbinding maken met deze database om de voortgang van de aanvragen te controleren en gedetailleerde informatie over mogelijke fouten te onderzoeken.

- **Sharding-bewustzijn**

  De gesplitste samenvoegservice maakt onderscheid tussen (1) geshardtabellen, (2) referentietabellen en (3) normale tabellen. De semantiek van een split/merge/move-bewerking is afhankelijk van het type van de gebruikte tabel en wordt als volgt gedefinieerd:

  - **Gesharde tabellen**

    Met bewerkingen splitsen, samenvoegen en verplaatsen, verplaatsen shardlets van bron naar doelscherf. Na een succesvolle voltooiing van de totale aanvraag zijn deze shardlets niet meer aanwezig op de bron. Houd er rekening mee dat de doeltabellen op de doelshard moeten bestaan en geen gegevens in het doelbereik mogen bevatten voordat de bewerking wordt verwerkt.

  - **Referentietabellen**

    Voor referentietabellen kopieert, voegt en verplaatst de bewerkingen de gegevens van de bron naar de doelshard. Houd er echter rekening mee dat er geen wijzigingen optreden op de doelshard voor een bepaalde tabel als er al een rij aanwezig is in deze tabel over het doel. De tabel moet leeg zijn voor een bewerking met een verwijzingstabel om verwerkt te worden.

  - **Andere tabellen**

    Andere tabellen kunnen aanwezig zijn op de bron of het doel van een gesplitste en samenvoegbewerking. De split-merge-service negeert deze tabellen voor gegevensverplaatsingen of kopieerbewerkingen. Houd er echter rekening mee dat ze deze bewerkingen kunnen verstoren in geval van beperkingen.

    De informatie over referentie versus geshard `SchemaInfo` tabellen wordt verstrekt door de API's op de shardkaart. In het volgende voorbeeld wordt het gebruik van deze API's op een bepaald object voor shardmapbeheer weergegeven:

    ```csharp
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));

    // publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    De tabellen 'regio' en 'nation' worden gedefinieerd als referentietabellen en worden gekopieerd met gesplitste/samenvoeg-/verplaatsingsbewerkingen. 'klant' en 'orders' worden op hun beurt gedefinieerd als geshard tabellen. `C_CUSTKEY`en `O_CUSTKEY` dienen als de sharding sleutel.

- **Referentiële integriteit**

  De split-merge-service analyseert afhankelijkheden tussen tabellen en gebruikt externe sleutelprimaire sleutelrelaties om de bewerkingen voor het verplaatsen van referentietabellen en shardlets te fasen. In het algemeen worden referentietabellen eerst gekopieerd in afhankelijkheidsvolgorde, waarna shardlets worden gekopieerd in volgorde van hun afhankelijkheden binnen elke batch. Dit is nodig om fk-PK beperkingen op de doelscherf worden geëerd als de nieuwe gegevens aankomt.

- **Shard map consistentie en uiteindelijke voltooiing**

  In aanwezigheid van fouten wordt de gesplitste samenvoegservice na een storing hervat en is het de bedoeling om lopende aanvragen te voltooien. Er kunnen echter onherstelbare situaties zijn, bijvoorbeeld wanneer de doelscherf onherstelbaar is verloren of onherstelbaar wordt aangetast. Onder die omstandigheden kunnen sommige scherven die verplaatst moesten worden, op de bronscherf blijven staan. De service zorgt ervoor dat shardletmappings pas worden bijgewerkt nadat de benodigde gegevens naar het doel zijn gekopieerd. Shardlets worden alleen verwijderd op de bron zodra al hun gegevens zijn gekopieerd naar het doel en de bijbehorende toewijzingen zijn bijgewerkt. De verwijderingsbewerking vindt plaats op de achtergrond terwijl het bereik al online is op de doelshard. De split-merge-service zorgt altijd voor de juistheid van de toewijzingen die zijn opgeslagen in de shardkaart.

## <a name="the-split-merge-user-interface"></a>De gebruikersinterface voor het splitsen

Het servicepakket voor gesplitste samenvoegen bevat een werknemersrol en een webrol. De webrol wordt gebruikt om op een interactieve manier aanvragen voor gesplitste samenvoeging in te dienen. De belangrijkste onderdelen van de gebruikersinterface zijn als volgt:

- **Type bewerking**

  Het bewerkingstype is een keuzerondje die het soort bewerking regelt dat door de service voor dit verzoek wordt uitgevoerd. U kiezen tussen de scenario's splitsen, samenvoegen en verplaatsen. U ook een eerder ingediende bewerking annuleren. U gesplitste, samenvoegende en verplaatste aanvragen voor bereikshardkaarten gebruiken. Lijstshardkaarten ondersteunen alleen verplaatsingsbewerkingen.

- **Shardkaart**

  Het volgende gedeelte van de aanvraag parameters omvat informatie over de shard kaart en de database hosting uw shard kaart. In het bijzonder moet u de naam opgeven van de Azure SQL Database-server en de database met de shardmap, referenties om verbinding te maken met de shardkaartdatabase en ten slotte de naam van de shardkaart. Momenteel accepteert de bewerking slechts één set referenties. Deze referenties moeten voldoende machtigingen hebben om wijzigingen in de shardkaart uit te voeren, evenals in de gebruikersgegevens op de shards.

- **Bronbereik (splitsen en samenvoegen)**

  Een gesplitste en samenvoegende bewerking verwerkt een bereik met behulp van de lage en hoge toets. Als u een bewerking met een niet-begrensde hoge sleutelwaarde wilt opgeven, schakelt u het selectievakje 'Hoge toets is max' in en laat u het veld met hoge toetsen leeg. De door u opgegeven reekssleutelwaarden hoeven niet precies overeen te komen met een toewijzing en de grenzen ervan in uw shardkaart. Als u helemaal geen bereikgrenzen opgeeft, stelt de service automatisch het dichtstbijzijnde bereik voor u uit. U het GetMappings.ps1 PowerShell-script gebruiken om de huidige toewijzingen in een bepaalde shardkaart op te halen.

- **Brongedrag splitsen (gesplitst)**

  Definieer voor gesplitste bewerkingen het punt om het bronbereik te splitsen. U doet dit door de shardingsleutel te verstrekken waar u de splitsing wilt laten plaatsvinden. Gebruik de keuzerondje geef op of u wilt dat het onderste deel van het bereik (met uitzondering van de gesplitste sleutel) wordt verplaatst of dat het bovenste deel moet worden verplaatst (inclusief de gesplitste toets).

- **Bron Shardlet (verplaatsen)**

  Verplaatsingsbewerkingen verschillen van gesplitste of samenvoegbewerkingen, omdat er geen bereik nodig is om de bron te beschrijven. Een bron voor verhuizing wordt eenvoudig geïdentificeerd door de sharding sleutelwaarde die u van plan bent te verplaatsen.

- **Doelshard (gesplitst)**

  Zodra u de informatie over de bron van uw gesplitste bewerking hebt verstrekt, moet u bepalen waar naar de gegevens moet worden gekopieerd door de Azure SQL Db-server en de databasenaam voor het doel op te geven.

- **Doelbereik (samenvoegen)**

  Samenvoegbewerkingen verplaatsen shardlets naar een bestaande shard. U identificeert de bestaande shard door de bereikgrenzen op te geven van het bestaande bereik waarmee u wilt fuseren.

- **Batchgrootte**

  De batchgrootte regelt het aantal shardlets dat tijdens de gegevensverplaatsing offline gaat. Dit is een gehele waarde waarbij u kleinere waarden gebruiken wanneer u gevoelig bent voor lange perioden van downtime voor shardlets. Grotere waarden verhogen de tijd dat een bepaalde shardlet offline is, maar kan de prestaties verbeteren.

- **Bedrijfs-id (annuleren)**

  Als u een lopende bewerking hebt die niet langer nodig is, u de bewerking annuleren door de bedrijfs-ID in dit veld op te geven. U de bewerkings-id ophalen uit de tabel met de aanvraagstatus (zie sectie 8.1) of uit de uitvoer in de webbrowser waar u de aanvraag hebt ingediend.

## <a name="requirements-and-limitations"></a>Vereisten en beperkingen

De huidige implementatie van de split-merge-service is onderworpen aan de volgende vereisten en beperkingen:

- De scherven moeten bestaan en worden geregistreerd in de shardkaart voordat een split-merge-bewerking op deze scherven kan worden uitgevoerd.
- De service maakt niet automatisch tabellen of andere databaseobjecten als onderdeel van de bewerkingen. Dit betekent dat het schema voor alle geshard tabellen en referentietabellen moet bestaan op de doelshard voorafgaand aan een split/merge/move-bewerking. Met name geshard tabellen moeten leeg zijn in het bereik waar nieuwe shardlets moeten worden toegevoegd door een split/merge/move-bewerking. Anders wordt de bewerking mislukt bij de eerste consistentiecontrole op de doelshard. Houd er ook rekening mee dat referentiegegevens alleen worden gekopieerd als de referentietabel leeg is en er geen consistentiegaranties zijn met betrekking tot andere gelijktijdige schrijfbewerkingen in de referentietabellen. We raden dit aan: bij het uitvoeren van gesplitste/samenvoegbewerkingen brengen geen andere schrijfbewerkingen wijzigingen aan in de referentietabellen.
- De service is gebaseerd op rijidentiteit die is vastgesteld door een unieke index of sleutel die de shardingsleutel bevat om de prestaties en betrouwbaarheid voor grote shardlets te verbeteren. Hierdoor kan de service gegevens verplaatsen met een nog fijnere granulariteit dan alleen de sharding-sleutelwaarde. Dit helpt om de maximale hoeveelheid logruimte en sloten die nodig zijn tijdens de operatie te verminderen. Overweeg een unieke index of een primaire sleutel te maken, inclusief de shardingssleutel in een bepaalde tabel als u die tabel wilt gebruiken met split/merge/move-aanvragen. Om prestatieredenen moet de shardingssleutel de hoofdkolom in de sleutel of de index zijn.
- Tijdens de verwerking van de aanvraag kunnen sommige shardletgegevens aanwezig zijn, zowel op de bron als op de doelshard. Dit is nodig om te beschermen tegen storingen tijdens de shardlet beweging. De integratie van split-merge met de shardmap zorgt ervoor dat verbindingen via de gegevensafhankelijke route-API's met behulp van de **OpenConnectionForKey-methode** op de shardkaart geen inconsistente tussenliggende toestanden zien. Wanneer u echter verbinding maakt met de bron of de doelshards zonder de **Methode OpenConnectionForKey** te gebruiken, kunnen inconsistente tussenstanden zichtbaar zijn wanneer er split/merge/move-aanvragen worden gedaan. Deze verbindingen kunnen gedeeltelijke of dubbele resultaten weergeven, afhankelijk van de timing of de shard die aan de verbinding ten grondslag ligt. Deze beperking omvat momenteel de verbindingen die zijn gemaakt door Elastic Scale Multi-Shard-Queries.
- De metagegevensdatabase voor de split-merge-service mag niet worden gedeeld tussen verschillende rollen. Een rol van de gesplitste samenvoegservice die wordt uitgevoerd in fasering, moet bijvoorbeeld wijzen op een andere metagegevensdatabase dan de productierol.

## <a name="billing"></a>Billing

De split-merge-service wordt uitgevoerd als een cloudservice in uw Microsoft Azure-abonnement. Daarom zijn er kosten verbonden aan cloudservices voor uw instantie van de service. Tenzij u vaak split/merge/move-bewerkingen uitvoert, raden we u aan uw split-merge cloudservice te verwijderen. Dat bespaart kosten voor het uitvoeren of geïmplementeerd van cloudservice-exemplaren. U uw gemakkelijk uitloopbare configuratie opnieuw implementeren en starten wanneer u split- of merge-bewerkingen moet uitvoeren.

## <a name="monitoring"></a>Bewaking

### <a name="status-tables"></a>Statustabellen

De split-merge Service biedt de tabel **RequestStatus** in de database met metagegevensarchief voor het bewaken van voltooide en lopende aanvragen. In de tabel vindt u een rij voor elke gesplitste samenvoegaanvraag die is ingediend bij deze instantie van de gesplitste samenvoegservice. Het geeft de volgende informatie voor elk verzoek:

- **Timestamp**

  De tijd en datum waarop de aanvraag is gestart.

- **OperationId (OperationId)**

  Een GUID die de aanvraag op unieke wijze identificeert. Deze aanvraag kan ook worden gebruikt om de bewerking te annuleren terwijl deze nog loopt.

- **Status**

  De huidige status van het verzoek. Voor lopende aanvragen wordt ook de huidige fase vermeld waarin de aanvraag zich bevindt.

- **Verzoek annuleren**

  Een vlag die aangeeft of de aanvraag is geannuleerd.

- **Vooruitgang**

  Een procentuele schatting van de voltooiing voor de bewerking. Een waarde van 50 geeft aan dat de bewerking ongeveer 50% voltooid is.

- **Details**

  Een XML-waarde die een gedetailleerder voortgangsrapport biedt. Het voortgangsrapport wordt periodiek bijgewerkt wanneer rijensets worden gekopieerd van bron naar doel. In het geval van fouten of uitzonderingen bevat deze kolom ook meer gedetailleerde informatie over de fout.

### <a name="azure-diagnostics"></a>Azure Diagnostics

De split-merge-service maakt gebruik van Azure Diagnostics op basis van Azure SDK 2.5 voor monitoring en diagnostiek. U beheert de configuratie van de diagnose zoals hier wordt uitgelegd: [Diagnostische gegevens inschakelen in Azure Cloud Services en virtuele machines](../cloud-services/cloud-services-dotnet-diagnostics.md). Het downloadpakket bevat twee diagnostische configuraties: een voor de webrol en een voor de werkrol. Het bevat de definities voor het registreren van prestatiemeteritems, IIS-logboeken, Windows-gebeurtenislogboeken en gebeurtenislogboeken voor gesplitste toepassingen.

## <a name="deploy-diagnostics"></a>Diagnostische gegevens implementeren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

Voer de volgende opdrachten uit met Azure PowerShell als u bewaking en diagnose wilt inschakelen met behulp van de diagnostische configuratie voor de web- en werknemersrollen die door het NuGet-pakket worden geleverd:

```powershell
$storageName = "<azureStorageAccount>"
$key = "<azureStorageAccountKey"
$storageContext = New-AzStorageContext -StorageAccountName $storageName -StorageAccountKey $key
$configPath = "<filePath>\SplitMergeWebContent.diagnostics.xml"
$serviceName = "<cloudServiceName>"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWeb"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWorker"
```

Hier vindt u meer informatie over het configureren en implementeren van diagnostische instellingen: [Diagnostische gegevens inschakelen in Azure Cloud Services en Virtuele Machines](../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Diagnostische gegevens ophalen

U hebt eenvoudig toegang tot uw diagnose vanuit de Visual Studio Server Explorer in het Azure-gedeelte van de boom Server Explorer. Open een instantie Visual Studio en klik in de menubalk op Weergave en Server Verkenner. Klik op het Azure-pictogram om verbinding te maken met uw Azure-abonnement. Navigeer vervolgens naar Azure -> Storage -> `<your storage account>` -> Tables -> WADLogsTable. Zie [Server Explorer voor](https://msdn.microsoft.com/library/x603htbk.aspx)meer informatie.

![WADLogsTable][2]

De WADLogsTable die in de bovenstaande afbeelding is gemarkeerd, bevat de gedetailleerde gebeurtenissen uit het toepassingslogboek van de gesplitste service. Houd er rekening mee dat de standaardconfiguratie van het gedownloade pakket is afgestemd op een productie-implementatie. Daarom is het interval waarbij logboeken en tellers uit de service-exemplaren worden getrokken groot (5 minuten). Voor testen en ontwikkeling verlaagt u het interval door de diagnostische instellingen van het web of de rol van de werknemer aan te passen aan uw behoeften. Klik met de rechtermuisknop op de rol in de Visual Studio Server Explorer (zie hierboven) en pas de overdrachtsperiode aan in het dialoogvenster voor de configuratie-instellingen voor diagnostiek:

![Configuratie][3]

## <a name="performance"></a>Prestaties

Over het algemeen zijn betere prestaties te verwachten van de hogere, meer performante servicelagen in Azure SQL Database. Hogere IO-, CPU- en geheugentoewijzingen voor de hogere servicelagen komen ten goede aan de bulkkopieer- en verwijderbewerkingen die de gesplitste samenvoegservice gebruikt. Verhoog daarom de servicelaag alleen voor die databases voor een bepaalde, beperkte periode.

De service voert ook validatiequery's uit als onderdeel van de normale bewerkingen. Deze validatiequery's controleren op onverwachte aanwezigheid van gegevens in het doelbereik en zorgen ervoor dat een gesplitste/samenvoeg-/verplaatsingsbewerking begint vanuit een consistente status. Deze query's werken allemaal via sharding-sleutelbereiken die zijn gedefinieerd door het bereik van de bewerking en de batchgrootte die is opgegeven als onderdeel van de aanvraagdefinitie. Deze query's presteren het best wanneer er een index aanwezig is met de shardingstoets als de hoofdkolom.

Bovendien kan de service een geoptimaliseerde benadering gebruiken die het verbruik van resources beperkt in termen van logboekruimte en geheugen, waarbij de eigenschap uniekheid met de shardingssleutel als de voorkolom een geoptimaliseerde aanpak kan gebruiken die het verbruik van resources beperkt in termen van logboekruimte en geheugen. Deze eigenschap uniciteit is vereist om grote gegevensformaten te verplaatsen (meestal meer dan 1 GB).

## <a name="how-to-upgrade"></a>Upgraden

1. Volg de stappen in [Een gesplitste samenvoegservice implementeren](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Wijzig uw configuratiebestand voor cloudservices voor de implementatie van de gesplitste samenvoeging om de nieuwe configuratieparameters weer te geven. Een nieuwe vereiste parameter is de informatie over het certificaat dat wordt gebruikt voor versleuteling. Een eenvoudige manier om dit te doen is door het nieuwe configuratiesjabloonbestand van de download te vergelijken met uw bestaande configuratie. Zorg ervoor dat u de instellingen voor "DataEncryptionPrimaryCertificateThumbprint" en "DataEncryptionPrimary" toevoegt voor zowel het web als de rol van de werknemer.
3. Voordat u de update implementeert naar Azure, moet u ervoor zorgen dat alle momenteel uitgevoerde gesplitste samenvoegbewerkingen zijn voltooid. U dit eenvoudig doen door de tabellen RequestStatus en PendingWorkflows op te vragen in de database met aalfusiemetagegevens voor lopende aanvragen.
4. Werk uw bestaande implementatie van cloudservices bij voor split-merge in uw Azure-abonnement met het nieuwe pakket en uw bijgewerkte serviceconfiguratiebestand.

U hoeft geen nieuwe metagegevensdatabase in te richten voor gesplitste samenvoeging om te upgraden. De nieuwe versie zal uw bestaande metadatadatabase automatisch upgraden naar de nieuwe versie.

## <a name="best-practices--troubleshooting"></a>Best practices en probleemoplossing

- Definieer een testtenant en oefen uw belangrijkste split/merge/move-bewerkingen uit met de testtenant op verschillende shards. Zorg ervoor dat alle metagegevens correct zijn gedefinieerd in uw shardkaart en dat de bewerkingen geen beperkingen of buitenlandse sleutels schenden.
- Houd de grootte van de testtenantgegevens boven de maximale gegevensgrootte van uw grootste tenant om ervoor te zorgen dat u geen problemen ondervindt die verband houden met gegevensgrootte. Dit helpt u een bovengrens te beoordelen op de tijd die nodig is om een enkele tenant te verplaatsen.
- Zorg ervoor dat uw schema verwijderingen toestaat. De split-merge-service vereist de mogelijkheid om gegevens uit de bronsscherf te verwijderen zodra de gegevens met succes naar het doel zijn gekopieerd. **Verwijdertriggers** kunnen bijvoorbeeld voorkomen dat de service de gegevens op de bron verwijdert en kan ervoor zorgen dat bewerkingen mislukken.
- De shardingstoets moet de hoofdkolom in uw primaire sleutel of unieke indexdefinitie zijn. Dat zorgt voor de beste prestaties voor de gesplitste of samenvoegen validatiequery's, en voor de werkelijke gegevensverplaatsings- en verwijderingsbewerkingen die altijd werken op sharding-sleutelbereiken.
- Verspan uw gesplitste samenvoegservice in de regio en het datacenter waar uw databases zich bevinden.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
