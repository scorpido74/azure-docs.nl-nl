---
title: Problemen oplossen Azure Data Factory | Microsoft Docs
description: Meer informatie over het oplossen van problemen met externe controle activiteiten in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: f35a3567ae4ae7c3e2d59f776d3a3bc00ec2be3e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142387"
---
# <a name="troubleshoot-azure-data-factory"></a>Problemen met Azure Data Factory oplossen

In dit artikel worden algemene probleemoplossings methoden besproken voor externe controle activiteiten in Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Connector-en kopieer activiteit

Raadpleeg [problemen met Azure Data Factory connectors oplossen](connector-troubleshoot-guide.md)voor problemen met de connector, bijvoorbeeld wanneer er een fout optreedt.

## <a name="azure-databricks"></a>Azure Databricks

| Foutcode | Foutbericht                                          | Description                             | Aanbeveling                             |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | Fout 403.                                                    | Het Databricks-toegangs token is verlopen.                         | Het toegangs token voor Databricks is standaard 90 dagen geldig.  Maak een nieuw token en werk de gekoppelde service bij. |
| 3201           | Vereist veld ontbreekt: Settings. Task. notebook_task. notebook_path | Ongeldige ontwerpen: Het pad naar de notebook is niet juist opgegeven. | Geef het pad naar de notebook op in de Databricks-activiteit. |
| 3201           | Cluster... bestaat niet.                                 | Ontwerp fout: Het Databricks-cluster bestaat niet of is verwijderd. | Controleer of het Databricks-cluster bestaat. |
| 3201           | Ongeldige URI van python-bestand.... Ga naar de Databricks-gebruikers handleiding voor ondersteunde URI-schema's. | Ongeldige ontwerp functie.                                                | Geef een absoluut pad op voor werk ruimte-adresserings `dbfs:/folder/subfolder/foo.py` schema's of voor bestanden die zijn opgeslagen in Databricks File System. |
| 3201           | {0}LinkedService moet de vereiste eigenschappen Domain en accessToken hebben. | Ongeldige ontwerp functie.                                                | Controleer de [definitie van de gekoppelde service](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}LinkedService moet een bestaande cluster-ID of nieuwe cluster informatie opgeven om te maken. | Ongeldige ontwerp functie.                                                | Controleer de [definitie van de gekoppelde service](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Het knooppunt type Standard_D16S_v3 wordt niet ondersteund. Ondersteunde knooppunt typen:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2. | Ongeldige ontwerp functie.                                                | Raadpleeg het fout bericht.                                          |
| 3201           | Ongeldige notebook_path:... Er worden momenteel alleen absolute paden ondersteund. Paden moeten beginnen met '/'. | Ongeldige ontwerp functie.                                                | Raadpleeg het fout bericht.                                          |
| 3202           | Er zijn al 1000 taken in de afgelopen 3600 seconden gemaakt, waardoor de frequentie limiet is overschreden:   1000 aantal gemaakte taken per 3600 seconden. | Er zijn te veel Databricks-uitvoeringen in een uur.                         | Controleer alle pijp lijnen die gebruikmaken van deze Databricks-werk ruimte voor het aanmaak tempo van de taak.  Als pijp lijnen te veel Databricks-runs worden uitgevoerd, migreert u enkele pijp lijnen naar een nieuwe werk ruimte. |
| 3202           | Kan aanvraag object niet parseren: ' Key ' en ' value ' moeten worden ingesteld voor het JSON-toewijzings veld base_parameters, kreeg ' Key: '... ' ' maar. | Ontwerp fout: Er is geen waarde voor de para meter gegeven.         | Inspecteer de JSON van de pijp lijn en zorg ervoor dat alle para meters in de baseParameters-notebook een niet-lege waarde opgeven. |
| 3202           | Gebruiker: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` is niet gemachtigd om toegang te krijgen tot het cluster. | De gebruiker die het toegangs token heeft gegenereerd, heeft geen toegang tot het Databricks-cluster dat is opgegeven in de gekoppelde service. | Zorg ervoor dat de gebruiker over de vereiste machtigingen beschikt in de werk ruimte.   |
| 3203           | Het cluster heeft de status beëindigd en is niet beschikbaar voor het ontvangen van taken. Herstel het cluster of probeer het later opnieuw. | Het cluster is beëindigd.    Voor interactieve clusters kan dit een race voorwaarde zijn. | De beste manier om deze fout te vermijden, is door taak clusters te gebruiken.             |
| 3204           | De taak kan niet worden uitgevoerd.  | Fout berichten geven verschillende problemen aan, zoals een onverwachte cluster status of een specifieke activiteit. Er wordt meestal helemaal geen fout bericht weer gegeven.                                                          | N/A                                                          |



## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

De volgende tabel is van toepassing op U-SQL.

| Foutcode         | Foutbericht                                                | Description                                          | Aanbeveling                            |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | Het toegangs token is afkomstig van de verkeerde Tenant.                    | Onjuiste Azure Active Directory-Tenant (Azure AD).                                         | De service-principal die wordt gebruikt voor toegang tot Azure Data Lake Analytics hoort bij een andere Azure AD-Tenant. Maak een nieuwe Service-Principal in dezelfde Tenant als het Data Lake Analytics-account. |
| 2711,   2705,   2704 | Slag. ACL-verificatie is mislukt. De resource bestaat niet of de gebruiker is niet gemachtigd om de aangevraagde bewerking uit te voeren.<br/><br/>De gebruiker kan geen toegang krijgen tot Data Lake Store.  <br/><br/>De gebruiker is niet gemachtigd om Data Lake Analytics te gebruiken. | De service-principal of het certificaat heeft geen toegang tot het bestand in de opslag. | Zorg ervoor dat de service-principal of het certificaat dat de gebruiker biedt voor Data Lake Analytics taken toegang heeft tot het Data Lake Analytics account en de standaard Data Lake Storage instantie van de hoofdmap. |
| 2711                 | Kan het bestand of de map Azure Data Lake Store niet vinden.       | Het pad naar het U-SQL-bestand is onjuist of de referenties van de gekoppelde service hebben geen toegang. | Controleer het pad en de referenties die zijn opgenomen in de gekoppelde service. |
| 2707                 | Het account van AzureDataLakeAnalytics kan niet worden omgezet. Controleer ' AccountName ' en ' DataLakeAnalyticsUri '. | Het Data Lake Analytics-account in de gekoppelde service is onjuist.                  | Controleer of het juiste account is ingesteld.             |
| 2703                 | Fout-id: E_CQO_SYSTEM_INTERNAL_ERROR (of een fout die begint met ' fout-id: '). | De fout is van Data Lake Analytics.                                    | Een fout zoals in het voor beeld betekent dat de taak is verzonden naar Data Lake Analytics en dat het script is mislukt. Onderzoek in Data Lake Analytics. Ga in de portal naar het Data Lake Analytics-account en zoek de taak met behulp van de Data Factory-activiteit run ID (niet de pijplijn run-ID). De taak bevat meer informatie over de fout en helpt u bij het oplossen van problemen. Als de oplossing niet duidelijk is, neemt u contact op met het Data Lake Analytics ondersteunings team en geeft u de taak-URL op, die uw account naam en de taak-ID bevat. |
| 2709                 | We kunnen uw taak op dit moment niet accepteren. Het maximum aantal taken in de wachtrij voor uw account is 200. | Deze fout wordt veroorzaakt door het beperken van Data Lake Analytics.                                           | Verminder het aantal verzonden taken naar Data Lake Analytics door Data Factory triggers en gelijktijdigheids instellingen voor activiteiten te wijzigen. Of verg root de limieten voor Data Lake Analytics. |
| 2709                 | Deze taak is afgewezen omdat deze 24 AUs vereist. Door de beheerder gedefinieerd beleid van deze account voor komt dat een taak meer dan 5 AUs gebruikt. | Deze fout wordt veroorzaakt door het beperken van Data Lake Analytics.                                           | Verminder het aantal verzonden taken naar Data Lake Analytics door Data Factory triggers en gelijktijdigheids instellingen voor activiteiten te wijzigen. Of verg root de limieten voor Data Lake Analytics. |



## <a name="azure-functions"></a>Azure Functions

| Foutcode | Foutbericht                           | Description                                                  | Aanbeveling                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | De antwoord inhoud is geen geldige JObject. | De Azure-functie die is aangeroepen, heeft niet een JSON-nettolading geretourneerd in het antwoord. Azure function-activiteit in Data Factory ondersteunt alleen inhoud van een JSON-antwoord. | Werk de Azure-functie bij om een geldige JSON-nettolading te retour neren. Een functie kan bijvoorbeeld C# de volgende id `(ActionResult)new<OkObjectResult("{`\"retour neren\" \":\"123`}");`. |
| 3600         | Ongeldige HttpMethod:....               | De HTTP-methode die in de nettolading van de activiteit is opgegeven, wordt niet ondersteund door de Azure function-activiteit. | Gebruik een ondersteunde HTTP-methode, zoals plaatsen, plaatsen, ophalen, verwijderen, opties, kop of TRACERing. |



## <a name="custom"></a>Aanpassen

De volgende tabel is van toepassing op Azure Batch.

| Foutcode | Foutbericht                                                | Description                                                  | Aanbeveling                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | Onverwachte uitzonde ring en uitvoering is mislukt.             | De opdracht kan niet worden gestart of het programma heeft een fout code geretourneerd. | Controleer of het uitvoer bare bestand bestaat. Als het programma is gestart, controleert u of *stdout. txt* en *stderr. txt* zijn geüpload naar het opslag account. Het is een goed idee om copious-Logboeken in uw code te verzenden voor fout opsporing. |
| 2501         | Geen toegang tot de gebruikers batch-account; Controleer de instellingen van het batch-account. | Onjuiste batch-toegangs sleutel of groeps naam.            | Controleer de naam van de groep en de batch toegangs sleutel in de gekoppelde service. |
| 2502         | Kan geen toegang krijgen tot het opslag account van de gebruiker; Controleer de instellingen voor het opslag account. | De naam van het opslag account of de toegangs sleutel is onjuist.       | Controleer de naam van het opslag account en de toegangs sleutel in de gekoppelde service. |
| 2504         | De bewerking heeft een ongeldige status code onjuiste aanvraag geretourneerd.     | Er zijn te veel bestanden in de folderPath van de aangepaste activiteit. De totale grootte van de Resource files mag niet langer zijn dan 32.768 tekens. | Verwijder overbodige bestanden. Of zip en voeg een unzip-opdracht toe om deze uit te pakken. Gebruik bijvoorbeeld`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe` |
| 2505         | Kan geen Shared Access Signature maken, tenzij de account sleutel referenties worden gebruikt. | Aangepaste activiteiten bieden alleen ondersteuning voor opslag accounts die gebruikmaken van een toegangs sleutel. | Raadpleeg de beschrijving van de fout.                                            |
| 2507         | Het mappad bestaat niet of is leeg:....            | Er bevinden zich geen bestanden in het opslag account op het opgegeven pad.       | Het mappad moet de uitvoer bare bestanden bevatten die u wilt uitvoeren. |
| 2508         | De map Resource bevat dubbele bestanden.               | Meerdere bestanden met dezelfde naam bevinden zich in verschillende submappen van folderPath. | Aangepaste activiteiten een mapstructuur afvlakken onder folderPath.  Als u de mappen structuur wilt behouden, moet u de bestanden zip en uitpakken in Azure Batch met behulp van een unzip opdracht. Gebruik bijvoorbeeld`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe` |
| 2509         | Batch-URL... is ongeldig; Deze moet de URI-indeling hebben.         | Batch-Url's moeten overeenkomen met`https://mybatchaccount.eastus.batch.azure.com` | Raadpleeg de beschrijving van de fout.                                            |
| 2510         | Er is een fout opgetreden tijdens het verzenden van de aanvraag.               | De batch-URL is ongeldig.                                         | Controleer de batch-URL.                                            |

## <a name="hdinsight"></a>HDInsight

De volgende tabel is van toepassing op Spark-, Hive-, MapReduce-, Pig-en Hadoop-streaming.

| Fout code | Foutbericht                                                | Description                                                  | Aanbeveling                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Het verzenden van een Hadoop-taak is mislukt. Fout: De externe naam kan niet worden omgezet. <br/><br/>Het cluster is niet gevonden. | De door gegeven URI van het cluster is ongeldig.                              | Zorg ervoor dat het cluster niet is verwijderd en dat de gegeven URI juist is. Wanneer u de URI in een browser opent, ziet u de Ambari-gebruikers interface. Als het cluster zich in een virtueel netwerk bevindt, moet de URI de persoonlijke URI zijn. Als u het bestand wilt openen, gebruikt u een virtuele machine die deel uitmaakt van hetzelfde virtuele netwerk. Zie [Direct Connect to Apache Hadoop Services](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)(Engelstalig) voor meer informatie. |
| 2300         | Het verzenden van een Hadoop-taak is mislukt. Taak:..., cluster:.../. Fout: Een taak is geannuleerd. | Er is een time-out opgetreden voor de taak verzending.                         | Het probleem kan algemeen HDInsight-connectiviteit of een netwerk verbinding zijn. Controleer eerst of de gebruikers interface van HDInsight Ambari beschikbaar is vanuit elke browser. Controleer of uw referenties nog geldig zijn. Als u gebruikmaakt van zelf-hostende Integrated Runtime (IR), moet u ervoor zorgen dat u dit doet vanaf de virtuele machine of computer waarop de zelf-hostende IR is geïnstalleerd. Probeer de taak vervolgens opnieuw uit Data Factory te verzenden. Als het nog steeds niet lukt, neemt u contact op met het Data Factory-team voor ondersteuning. |
| 2300         | Gasten   De gebruikers naam of het wacht woord van Ambari is onjuist  <br/><br/>Gasten   De gebruikers beheerder is vergrendeld in Ambari.   <br/><br/>403-verboden: Toegang wordt geweigerd. | De referenties voor HDInsight zijn onjuist of verlopen. | Corrigeer de referenties en implementeer de gekoppelde service opnieuw. Controleer eerst of de referenties werken op HDInsight door de cluster-URI in een browser te openen en u aan te melden. Als de referenties niet werken, kunt u ze opnieuw instellen op de Azure Portal. |
| 2300,   2310 | 502 - De webserver heeft een ongeldig antwoord ontvangen terwijl deze fungeerde als gateway of proxyserver.       <br/>Ongeldige gateway. | Deze fout is van HDInsight.                               | Deze fout is afkomstig uit het HDInsight-cluster. Zie Ambari-fout in [UI 502](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 fouten bij het maken van verbinding met Spark Thrift server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502-fouten bij het verbinden met Spark Thrift server en het](https://hdinsight.github.io/spark/spark-thriftserver-errors.html) [oplossen van problemen met ongeldige gateway fouten in Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)voor meer informatie. |
| 2300         | Het verzenden van een Hadoop-taak is mislukt. Taak:..., cluster:... Fout: {\"error\":\"kan de aanvraag voor het verzenden van de taak niet verwerken omdat de Templeton-service bezig is met te veel verzend taak aanvragen. Wacht enige tijd voordat u de bewerking opnieuw probeert uit te voeren. Raadpleeg het bestand config Templeton. parallellism. job. Submit om gelijktijdige aanvragen te configureren.  <br/><br/>Het verzenden van een Hadoop-taak is mislukt. Taak 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, cluster: `https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/`.   Fout: {\"error\":\"java. io. IOException: org. apache. Hadoop. garens. exceptions. YarnException: Kan application_1561147195099_3730 niet verzenden naar GARENs: org. apache. Hadoop. Security. AccessControlException: De wachtrij root. joblauncher heeft al 500 toepassingen, kan de toepassing niet verzenden: application_1561147195099_3730 \ | Op hetzelfde moment worden er te veel taken verzonden naar HDInsight. | Overweeg het aantal gelijktijdige taken dat naar HDInsight moet worden verzonden te beperken. Raadpleeg Data Factory gelijktijdige activiteit als de taken worden verzonden door dezelfde activiteit. Wijzig de triggers zodat de gelijktijdige pijplijn uitvoeringen in de loop van de tijd worden verdeeld. Raadpleeg de HDInsight-documentatie om `templeton.parallellism.job.submit` de fout te corrigeren. |
| 2303,   2347 | Hadoop-taak is mislukt met afsluit code 5. Zie 'wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' voor meer informatie.  <br/><br/>Het uitvoeren van de Hive is mislukt met fout code ' UserErrorHiveOdbcCommandExecutionFailure '.   Zie 'wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' voor meer informatie. | De taak is verzonden naar HDInsight en is mislukt op HDInsight. | De taak is verzonden naar HDInsight. Het cluster is mislukt. Open de taak en de logboeken in de gebruikers interface van HDInsight Ambari of open het bestand uit de opslag als het fout bericht wordt voorgesteld. Het bestand bevat de fout Details. |
| 2328         | Er is een interne server fout opgetreden tijdens het verwerken van de aanvraag. Voer de aanvraag opnieuw uit of neem contact op met de ondersteuning. | Deze fout treedt op in HDInsight op aanvraag.                              | Deze fout is afkomstig van de HDInsight-service wanneer het HDInsight-inrichten mislukt. Neem contact op met het HDInsight-team en geef de naam op van het cluster op aanvraag. |
| 2310         | java.lang.NullPointerException                               | Deze fout treedt op wanneer de taak wordt verzonden naar een Spark-cluster.      | Deze uitzonde ring komt van HDInsight. Hiermee wordt het werkelijke probleem verborgen. Neem contact op met het HDInsight-team voor ondersteuning. Geef de cluster naam en het tijds bereik voor het uitvoeren van de activiteit op. |
|              | Alle andere fouten                                             |                                                              | Raadpleeg de [Veelgestelde vragen over](https://hdinsight.github.io/)het [oplossen van problemen met hdinsight](../hdinsight/hdinsight-troubleshoot-guide.md) en hdinsight. |



## <a name="web-activity"></a>Web Activity

| Foutcode | Foutbericht                                                | Description                                                  | Aanbeveling                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | Ongeldige HttpMethod:....                                    | Webactiviteit biedt geen ondersteuning voor de HTTP-methode die is opgegeven in de nettolading van de activiteit. | De ondersteunde HTTP-methoden zijn plaatsen, plaatsen, ophalen en verwijderen. |
| 2108         | Ongeldige server fout 500.                                     | Interne fout op het eind punt.                               | Gebruik Fiddler of postman om de functionaliteit van de URL te controleren. |
| 2108         | Niet-geautoriseerde 401.                                             | Ontbrekende geldige verificatie voor de aanvraag.                      | Het token is mogelijk verlopen. Geef een geldige verificatie methode op. Gebruik Fiddler of postman om de functionaliteit van de URL te controleren. |
| 2108         | Verboden 403.                                                | De vereiste machtigingen ontbreken.                                 | Controleer de gebruikers machtigingen voor de toegang tot de resource. Gebruik Fiddler of postman om de functionaliteit van de URL te controleren.  |
| 2108         | Ongeldige aanvraag 400.                                              | Ongeldige HTTP-aanvraag.                                         | Controleer de URL, het werk woord en de hoofd tekst van de aanvraag. Gebruik Fiddler of postman om de aanvraag te valideren.  |
| 2108         | Niet gevonden 404.                                                | De resource is niet gevonden.                                       | Gebruik Fiddler of postman om de aanvraag te valideren.  |
| 2108         | De service is niet beschikbaar.                                          | De service is niet beschikbaar.                                       | Gebruik Fiddler of postman om de aanvraag te valideren.  |
| 2108         | Het media type wordt niet ondersteund.                                       | Het inhouds type komt niet overeen met de hoofd tekst van de webactiviteit.           | Geef het inhouds type op dat overeenkomt met de indeling van de nettolading. Gebruik Fiddler of postman om de aanvraag te valideren. |
| 2108         | De bron waarnaar u op zoek bent, is verwijderd, de naam ervan is gewijzigd of is tijdelijk niet beschikbaar. | De resource is niet beschikbaar.                                | Gebruik Fiddler of postman om het eind punt te controleren. |
| 2108         | De pagina die u zoekt, kan niet worden weer gegeven omdat er een ongeldige methode (HTTP-term) wordt gebruikt. | Er is een onjuiste methode voor webactiviteiten opgegeven in de aanvraag.   | Gebruik Fiddler of postman om het eind punt te controleren. |
| 2108         | invalid_payload                                              | De hoofd tekst van de webactiviteit is onjuist.                       | Gebruik Fiddler of postman om het eind punt te controleren. |

Fiddler gebruiken om een HTTP-sessie van de bewaakte webtoepassing te maken:

1. Down load, installeer en open [Fiddler](https://www.telerik.com/download/fiddler).

1. Als uw webtoepassing gebruikmaakt van HTTPS, gaat u naar **hulpprogram ma's** > **Fiddler opties** > **https**. Selecteer **https-verbinding** maken en versleutelen van **HTTPS-verkeer**. 
   
   ![Opties voor Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Als uw toepassing gebruikmaakt van SSL-certificaten, voegt u het Fiddler-certificaat toe aan uw apparaat. Ga naar **hulpprogram ma's** > **Fiddler opties** > **https** > actiesbasis > **certificaat exporteren naar bureau blad**.

1. Vastleggen uitschakelen door te gaan naar het **bestand** > **Capture-verkeer**. Of druk op **F12**.

1. Wis de cache van uw browser zodat alle items in de cache worden verwijderd en opnieuw moet worden gedownload.

1. Een aanvraag maken: 

   a. Selecteer het tabblad Composer.

   b. Stel de HTTP-methode en URL in.

   c. Voeg indien nodig kopteksten en een aanvraag tekst toe.

   d. Selecteer **Uitvoeren**.

9. Schakel het vastleggen van verkeer in en voltooi de problematische trans actie op de pagina.

10. Ga naar **bestand** > **alle sessies** **Opslaan** > .

Zie [aan de slag met Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)



