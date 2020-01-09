---
title: Trainingsgegevens importeren
titleSuffix: ML Studio (classic) - Azure
description: Informatie over het importeren van uw gegevens in Azure Machine Learning Studio (klassiek) van verschillende gegevens bronnen. Meer informatie over welke gegevens typen en gegevens indelingen worden ondersteund.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: 95938b979a90766c7e50f2560cf72266e287bfb5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454694"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Importeer uw trainings gegevens in Azure Machine Learning Studio (klassiek) van verschillende gegevens bronnen

Als u uw eigen gegevens in Machine Learning Studio (klassiek) wilt gebruiken voor het ontwikkelen en trainen van een predictive analytics oplossing, kunt u gegevens gebruiken van: 

* **Lokaal bestand** : lokale gegevens vooraf laden van de vaste schijf voor het maken van een gegevensset module in uw werk ruimte
* **Online gegevens bronnen** : gebruik de module [gegevens importeren][import-data] om toegang te krijgen tot gegevens van een van de verschillende online bronnen terwijl uw experiment wordt uitgevoerd
* **Machine learning Studio (klassiek) experiment** : gegevens gebruiken die zijn opgeslagen als een gegevensset in machine learning Studio (klassiek)
* [**On-premises SQL Server Data Base**](use-data-from-an-on-premises-sql-server.md) : gegevens van een on-premises SQL Server Data Base gebruiken zonder gegevens hand matig te hoeven kopiëren

> [!NOTE]
> Er zijn een aantal voorbeeld gegevens sets beschikbaar in Machine Learning Studio (klassiek) die u voor trainings gegevens kunt gebruiken. Zie [de voorbeeld gegevens sets gebruiken in azure machine learning Studio (klassiek)](use-sample-datasets.md)voor meer informatie.

## <a name="prepare-data"></a>Gegevens voorbereiden

Machine Learning Studio (klassiek) is ontworpen om te werken met rechthoekige of tabellaire gegevens, zoals tekst gegevens die worden gescheiden of gestructureerd van gegevens uit een Data Base, maar in sommige gevallen kunnen niet-rechthoekige gegevens worden gebruikt.

Het is het beste als uw gegevens relatief schoon zijn voordat u deze in Studio (klassiek) importeert. Stel dat u problemen ondervindt zoals teken reeksen die geen aanhalings tekens zijn.

Er zijn echter modules beschikbaar in Studio (klassiek) waarmee u gegevens in uw experiment kunt bewerken nadat u uw gegevens hebt geïmporteerd. Afhankelijk van de machine learning algoritmen die u gebruikt, moet u mogelijk bepalen hoe u gegevens structurele kwesties, zoals ontbrekende waarden en sparse gegevens, kunt afhandelen. er zijn modules die u kunnen helpen. Zoek in de sectie **gegevens transformatie** van het module palet naar modules die deze functies uitvoeren.

U kunt op elk gewenst moment in uw experiment de gegevens weer geven of downloaden die door een module worden geproduceerd door te klikken op de uitvoer poort. Afhankelijk van de module zijn er mogelijk verschillende download opties beschikbaar of kunt u de gegevens in de webbrowser in Studio (klassiek) visualiseren.

## <a name="supported-data-formats-and-data-types"></a>Ondersteunde gegevens indelingen en gegevens typen

U kunt een aantal gegevens typen importeren in uw experiment, afhankelijk van het mechanisme dat u gebruikt voor het importeren van gegevens en de herkomst van:

* Tekst zonder opmaak (.txt)
* Door komma's gescheiden waarden (CSV) met een header (. CSV) of zonder (. NH. CSV)
* Door tabs gescheiden waarden (TSV) met een header (. tsv) of zonder (. NH. tsv)
* Excel-bestand
* Azure Table
* Hive-tabel
* SQL database tabel
* OData-waarden
* SVMLight-gegevens (. SVMLight) (Zie de [SVMLight-definitie](http://svmlight.joachims.org/) voor indelings informatie)
* Kenmerk relatie bestands indeling (ARFF) gegevens (. ARFF) (Zie de [ARFF-definitie](https://weka.wikispaces.com/ARFF) voor indelings informatie)
* Zip-bestand (. zip)
* R-object of werkruimte bestand (. RData

Als u gegevens importeert in een indeling zoals ARFF die meta gegevens bevat, gebruikt Studio (klassiek) deze meta gegevens om de kop en het gegevens type van elke kolom te definiëren.

Als u gegevens importeert zoals een TSV-of CSV-indeling zonder deze meta gegevens, wordt door Studio (klassiek) het gegevens type voor elke kolom afgenomen door de gegevens te bemonsteren. Als de gegevens ook geen kolom koppen bevatten, biedt studio (klassiek) standaard namen.

U kunt de koppen en gegevens typen voor kolommen expliciet opgeven of wijzigen met behulp van de module [meta gegevens bewerken][edit-metadata] .

De volgende gegevens typen worden herkend door Studio (klassiek):

* Tekenreeks
* Geheel getal
* Double
* Booleaans
* Datum/tijd
* TimeSpan

Studio gebruikt een intern gegevens type met de naam ***Data Table*** voor het door geven van gegevens tussen modules. U kunt uw gegevens in de indeling van de gegevens tabel expliciet converteren met behulp van de module [converteren naar dataset][convert-to-dataset] .

Alle modules die andere indelingen dan gegevens tabel accepteren, converteren de gegevens naar de gegevens tabel op de achtergrond voordat deze aan de volgende module worden door gegeven.

Indien nodig kunt u de indeling van de gegevens tabel weer omzetten in de CSV-, TSV-, ARFF-of SVMLight-indeling met behulp van andere conversie modules.
Zoek in de sectie **gegevens indeling conversies** van het palet module voor modules die deze functies uitvoeren.

## <a name="data-capacities"></a>Gegevens capaciteit

Modules in Machine Learning Studio (klassiek) ondersteunen gegevens sets van Maxi maal 10 GB voor veelvoorkomende, gebruiks voorbeelden. Als een module meer dan één invoer heeft, is de waarde 10 GB de totale invoergrootte. U kunt een voor beeld van grotere gegevens sets maken met behulp van query's in Hive of Azure SQL Database, of u kunt de voor bereiding voor Learning by Count gebruiken voordat u de gegevens importeert.  

U kunt de volgende typen gegevens in grotere gegevenssets opnemen tijdens het normaliseren van kenmerken, tot maximaal 10 GB:

* Verspreide gegevens
* Categorische gegevens
* Tekenreeksen
* Binaire gegevens

De volgende modules zijn beperkt tot gegevenssets die kleiner zijn dan 10 GB:

* Aanbevelingsmodules
* De module Synthetic Minority Oversampling Technique (SMOTE)
* Scriptmodules: R, Python, SQL
* Modules waarbij de grootte van de uitvoer groter is dan invoergegevens, zoals Join- of hash-functies
* Kruisvalidatie, Tune Model Hyperparameters, ordinale regressie en One-vs-All-multiklasse, wanneer het aantal herhalingen groot is

Voor gegevens sets die groter zijn dan een paar GB, moet u de gegevens uploaden naar Azure Storage of Azure SQL Database of gebruikmaken van Azure HDInsight, in plaats van rechtstreeks vanuit een lokaal bestand te uploaden.

Meer informatie over afbeeldings gegevens vindt u in de module [afbeeldingen importeren](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) .

## <a name="import-from-a-local-file"></a>Importeren uit een lokaal bestand

U kunt een gegevens bestand van uw harde schijf uploaden om te gebruiken als trainings gegevens in Studio (klassiek). Wanneer u een gegevens bestand importeert, maakt u een gegevensset-module die gereed is voor gebruik in experimenten in uw werk ruimte.

Als u wilt gegevens importeren uit een lokale vaste schijf, het volgende doen:

1. Klik op **+ Nieuw** onder aan het venster Studio (klassiek).
2. Selecteer **GEGEVENSSET** en **vanuit het lokale bestand**.
3. Blader in het dialoog venster **een nieuwe gegevensset uploaden** naar het bestand dat u wilt uploaden.
4. Voer een naam, het gegevenstype te identificeren en optioneel een beschrijving invoeren. Een beschrijving wordt aanbevolen: Hiermee kunt u om vast te leggen van alle kenmerken van de gegevens die u wilt Houd er rekening mee bij het gebruik van de gegevens in de toekomst.
5. Het selectievakje **dit is de nieuwe versie van een bestaande gegevensset** kunt u een bestaande gegevensset bijwerken met nieuwe gegevens. Als u dit wilt doen, klikt u op dit selectie vakje en voert u de naam van een bestaande gegevensset in.

![Upload een nieuwe gegevensset](./media/import-data/upload-dataset-from-local-file.png)

Uploaden tijd is afhankelijk van de grootte van uw gegevens en de snelheid van de verbinding met de service. Als u weet dat het bestand lang duurt, kunt u andere dingen doen binnen Studio (klassiek) terwijl u wacht. Als de browser wordt gesloten voordat het uploaden van gegevens is voltooid, mislukt de upload echter.

Nadat uw gegevens is geüpload, wordt opgeslagen in een gegevensset-module en is beschikbaar voor een experiment in uw werkruimte.

Wanneer u een experiment bewerkt, kunt u de gegevens sets die u hebt geüpload, vinden in de lijst **mijn gegevens sets** onder de lijst **opgeslagen gegevens sets** in het module palet. U kunt slepen en neerzetten van de gegevensset naar het experimentcanvas wanneer u wilt gebruiken van de gegevensset voor verdere analyse en machine learning.

## <a name="import-from-online-data-sources"></a>Importeren uit online gegevens bronnen

Met de module [gegevens importeren][import-data] kan uw experiment gegevens importeren uit verschillende online gegevens bronnen terwijl het experiment wordt uitgevoerd.

> [!NOTE]
> Dit artikel bevat algemene informatie over de module [gegevens importeren][import-data] . Zie het onderwerp module verwijzing voor de module [gegevens importeren][import-data] voor meer informatie over de typen gegevens die u kunt openen, opmaken, para meters en antwoorden op veelgestelde vragen.

U kunt met behulp van de module [gegevens importeren][import-data] toegang krijgen tot gegevens van een van de verschillende online gegevens bronnen terwijl uw experiment wordt uitgevoerd:

* Een Web-URL met behulp van HTTP
* Hadoop met behulp van HiveQL
* Azure Blob Storage
* Azure Table
* Azure SQL database of SQL Server op Azure VM
* On-premises SQL Server-database
* Een data provider, die momenteel OData-feed
* Azure Cosmos DB

Omdat deze trainings gegevens worden geopend terwijl uw experiment actief is, is het alleen beschikbaar in dat experiment. In vergelijking worden gegevens die zijn opgeslagen in een gegevensset-module, beschikbaar voor elk experiment in uw werk ruimte.

Als u toegang wilt krijgen tot online gegevens bronnen in uw studio-experiment (klassiek), voegt u de module [gegevens importeren][import-data] toe aan uw experiment. Selecteer vervolgens **wizard Import gegevens starten** onder **Eigenschappen** voor stapsgewijze instructies om de gegevens bron te selecteren en configureren. U kunt ook hand matig **gegevens bron** selecteren onder **Eigenschappen** en de para meters opgeven die nodig zijn om toegang te krijgen tot de gegevens.

De online-gegevensbronnen die worden ondersteund in de onderstaande tabel enkele daarvan worden gespecificeerd. Deze tabel ziet u ook de bestandsindelingen die worden ondersteund en de parameters die worden gebruikt voor toegang tot de gegevens.

> [!IMPORTANT]
> Op dit moment kunnen de modules [gegevens importeren][import-data] en [exporteren][export-data] gegevens alleen lezen en schrijven vanuit Azure Storage die is gemaakt met het klassieke implementatie model. Met andere woorden, is het nieuwe type uit Azure Blob Storage-account dat een hot storage-toegangslaag of cool storage-toegangslaag biedt nog niet ondersteund.
>
> Over het algemeen een Azure storage-accounts die u mogelijk hebt gemaakt voordat deze serviceoptie is beschikbaar geworden moet niet worden beïnvloed.
> Als u een nieuw account maken wilt, selecteert u **klassieke** voor de implementatie van het model, of gebruik van resourcemanager en selecteer **algemeen gebruik** in plaats van **Blob storage** voor  **Soort account**.
>
> Zie voor meer informatie, [Azure Blob Storage: Hot en Cool Storage-lagen](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Online gegevensbronnen ondersteund
De module **import data** van Azure machine learning Studio (Classic) ondersteunt de volgende gegevens bronnen:

| Gegevensbron | Beschrijving | Parameters |
| --- | --- | --- |
| Web-URL via HTTP |Leest de gegevens in door komma's gescheiden waarden (CSV), door tabs gescheiden waarden (TSV), kenmerkrelatie bestand format (ARFF) en indelingen Support Vector Machines (SVM-licht), van een web-URL die gebruikmaakt van HTTP |<b>URL</b>: Hiermee geeft u de volledige naam van het bestand, met inbegrip van de site-URL en de bestandsnaam, met de extensie. <br/><br/><b>Gegevensindeling</b>: Hiermee geeft u een van de ondersteunde gegevens opgemaakt: CSV, TSV, ARFF of SVM licht. Als de gegevens een rij met koppen heeft, wordt deze gebruikt voor het toewijzen van de namen van kolommen. |
| Hadoop/HDFS |Leest gegevens uit gedistribueerde opslag in Hadoop. U de gegevens die u wilt met behulp van HiveQL, een SQL-achtige querytaal. HiveQL kan ook worden gebruikt om gegevens samen te voegen en gegevens te filteren voordat u de gegevens toevoegt aan Studio (klassiek). |<b>Hive-databasequery</b>: Hiermee geeft u de Hive-query die wordt gebruikt om de gegevens te genereren.<br/><br/><b>HCatalog-server URI </b> : de naam van uw cluster met behulp van de indeling opgegeven  *&lt;de clusternaam van uw&gt;. azurehdinsight.net.*<br/><br/><b>Hadoop-gebruikersaccountnaam</b>: Hiermee geeft u de accountnaam van de Hadoop gebruiker gebruikt voor het inrichten van het cluster.<br/><br/><b>Het wachtwoord voor gebruikersaccount Hadoop</b> : Hiermee geeft u de referenties die worden gebruikt bij het inrichten van het cluster. Zie voor meer informatie, [Hadoop-clusters maken in HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Locatie van de uitvoergegevens</b>: Hiermee geeft u op of de gegevens worden opgeslagen in een Hadoop distributed file system (HDFS) of in Azure. <br/><ul>Als u de uitvoergegevens in HDFS opslaat, geeft u de HDFS server-URI. (Zorg ervoor dat de naam van het HDInsight-cluster zonder het voorvoegsel HTTPS:// gebruiken). <br/><br/>Als u de uitvoergegevens in Azure opslaat, moet u de Azure storage-accountnaam, de toegangssleutel voor opslag en de naam van de opslagcontainer.</ul> |
| SQL-database |Leest de gegevens die zijn opgeslagen in een Azure SQL database of in een SQL Server-database die wordt uitgevoerd op een virtuele machine van Azure. |<b>Database-servernaam</b>: Hiermee geeft u de naam van de server waarop de database wordt uitgevoerd.<br/><ul>Voer in het geval van Azure SQL Database de naam van de server die wordt gegenereerd. Dit heeft meestal het formulier  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>In het geval van een SQL-Server die op een virtuele Azure-machine wordt gehost, voert u *TCP:&lt;DNS-naam van de virtuele machine&gt;, 1433*</ul><br/><b>De naam van database </b>: Hiermee geeft u de naam van de database op de server. <br/><br/><b>Naam van gebruikersaccount server</b>: Hiermee geeft u een gebruikersnaam voor een account met voor de database toegangsmachtigingen. <br/><br/><b>Het wachtwoord voor gebruikersaccount server</b>: Hiermee geeft u het wachtwoord voor het gebruikersaccount.<br/><br/><b>Databasequery</b>: Voer een SQL-instructie die beschrijft de gegevens die u wilt lezen. |
| On-premises SQL-database |Leest de gegevens die zijn opgeslagen in een on-premises SQL-database. |<b>Gegevensgateway</b>: Hiermee geeft u de naam van de Data Management Gateway is geïnstalleerd op een computer waarop deze toegang heeft tot uw SQL Server-database. Zie voor meer informatie over het instellen van de gateway [geavanceerde analyses uitvoeren met Azure machine learning Studio (klassiek) met behulp van gegevens van een on-premises SQL Server](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Database-servernaam</b>: Hiermee geeft u de naam van de server waarop de database wordt uitgevoerd.<br/><br/><b>De naam van database </b>: Hiermee geeft u de naam van de database op de server. <br/><br/><b>Naam van gebruikersaccount server</b>: Hiermee geeft u een gebruikersnaam voor een account met voor de database toegangsmachtigingen. <br/><br/><b>Gebruikersnaam en wachtwoord</b>: klik op <b>waarden invoeren</b> de databasereferenties van uw in te voeren. U kunt Windows geïntegreerde verificatie of SQL Server-verificatie, afhankelijk van hoe uw on-premises SQL Server is geconfigureerd.<br/><br/><b>Databasequery</b>: Voer een SQL-instructie die beschrijft de gegevens die u wilt lezen. |
| Azure Table |Leest gegevens uit de tabelservice in Azure Storage.<br/><br/>Als u grote hoeveelheden gegevens niet regelmatig worden gelezen, gebruikt u de Azure Table-Service. Het biedt een flexibel, niet-relationele (NoSQL), zeer schaalbare, goedkope en maximaal beschikbare oplossing. |De opties in de **importgegevens** veranderen, afhankelijk van of u toegang tot de gegevens van openbare of een persoonlijke opslagaccount waarvoor aanmeldingsreferenties. Dit wordt bepaald door de <b>verificatietype</b> die waarde van 'PublicOrSAS' of 'Account', die allemaal een eigen set parameters kan hebben. <br/><br/><b>Openbare of Shared Access Signature (SAS) URI</b>: de parameters zijn:<br/><br/><ul><b>Tabel URI</b>: Hiermee geeft u de openbare of SAS-URL voor de tabel.<br/><br/><b>Hiermee geeft u de rijen die u wilt zoeken naar namen van eigenschappen</b>: de waarden zijn <i>TopN</i> om te scannen op het opgegeven aantal rijen, of <i>ScanAll</i> om op te halen van alle rijen in de tabel. <br/><br/>Als de gegevens homogene en voorspelbaar is, is het raadzaam dat u selecteert *TopN* en voer een getal voor N. Voor grote tabellen, kan dit resulteren in sneller lezen tijden.<br/><br/>Als de gegevens is opgebouwd met sets van eigenschappen die op basis van de diepte variëren en de positie van de tabel, kiest u de *ScanAll* optie voor het scannen van alle rijen. Dit zorgt ervoor dat de integriteit van de resulterende eigenschap en metagegevens conversie.<br/><br/></ul><b>Private Storage-Account</b>: de parameters zijn: <br/><br/><ul><b>Accountnaam</b>: Hiermee geeft u de naam van het account dat met de tabel om te lezen.<br/><br/><b>Accountsleutel</b>: Hiermee geeft u de toegangssleutel van het opslagaccount dat is gekoppeld aan het account.<br/><br/><b>Tabelnaam</b> : Hiermee geeft u de naam van de tabel met de gegevens te lezen.<br/><br/><b>Rijen die u wilt zoeken naar namen van eigenschappen</b>: de waarden zijn <i>TopN</i> om te scannen op het opgegeven aantal rijen, of <i>ScanAll</i> om op te halen van alle rijen in de tabel.<br/><br/>Als de gegevens homogene en voorspelbare, raden wij aan dat u selecteert *TopN* en voer een getal voor N. Voor grote tabellen, kan dit resulteren in sneller lezen tijden.<br/><br/>Als de gegevens is opgebouwd met sets van eigenschappen die op basis van de diepte variëren en de positie van de tabel, kiest u de *ScanAll* optie voor het scannen van alle rijen. Dit zorgt ervoor dat de integriteit van de resulterende eigenschap en metagegevens conversie.<br/><br/> |
| Azure Blob Storage |Leest de gegevens die zijn opgeslagen in de Blob-service in Azure Storage, waaronder afbeeldingen, ongestructureerde tekst of binaire gegevens.<br/><br/>U kunt de Blob-service gebruiken om gegevens openbaar te maken en om toepassingsgegevens Privé opslaan. U kunt toegang tot uw gegevens vanaf elke locatie met behulp van HTTP of HTTPS-verbindingen. |De opties in de **importgegevens** module wijzigen, afhankelijk van of u toegang tot de gegevens van openbare of een persoonlijke opslagaccount waarvoor aanmeldingsreferenties. Dit wordt bepaald door de <b>verificatietype</b> dat kan een waarde van 'PublicOrSAS' of 'Account' hebben.<br/><br/><b>Openbare of Shared Access Signature (SAS) URI</b>: de parameters zijn:<br/><br/><ul><b>URI</b>: Hiermee geeft u de openbare of SAS-URL voor de opslag-blob.<br/><br/><b>Bestandsindeling van</b>: Hiermee geeft u de indeling van de gegevens in de Blob-service. De ondersteunde indelingen zijn CSV, TSV en ARFF.<br/><br/></ul><b>Private Storage-Account</b>: de parameters zijn: <br/><br/><ul><b>Accountnaam</b>: Hiermee geeft u de naam van het account met de blob die u wilt lezen.<br/><br/><b>Accountsleutel</b>: Hiermee geeft u de toegangssleutel van het opslagaccount dat is gekoppeld aan het account.<br/><br/><b>Pad naar de container, map of blob </b> : Hiermee geeft u de naam van de blob met de gegevens te lezen.<br/><br/><b>BLOB-bestandsindeling</b>: Hiermee geeft u de indeling van de gegevens in de blob-service. De van ondersteunde gegevensindelingen zijn CSV, TSV, ARFF, CSV met een opgegeven codering en Excel. <br/><br/><ul>Als de indeling CSV-bestand of TSV is, zorg er dan voor dat aangeeft of het bestand een rij met koppen bevat.<br/><br/>U kunt de Excel-optie gebruiken om gegevens te lezen uit Excel-werkmappen. In de <i>Excel gegevensindeling</i> optie, geef aan of de gegevens in een Excel-werkblad voor het bereik, of in een Excel-tabel is. In de <i>Excel-werkblad of ingesloten tabel </i>optie, geef de naam van het werkblad of de tabel die u wilt lezen.</ul><br/> |
| -Gegevensfeedprovider |Leest gegevens uit een ondersteunde provider van de feed. Op dit moment alleen de Open Data Protocol (OData)-indeling wordt ondersteund. |<b>Data type inhoud</b>: Hiermee geeft u de OData-indeling.<br/><br/><b>Bron-URL</b>: Hiermee geeft u de volledige URL voor de gegevensfeed. <br/>Bijvoorbeeld, leest de volgende URL van de voorbeelddatabase: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importeren uit een ander experiment

Wanneer u een tussentijds resultaat van een experiment wilt maken en dit als onderdeel van een ander experiment wilt gebruiken. Als u dit wilt doen, slaat u de module op als een gegevensset:

1. Klik op de uitvoer van de module die u wilt opslaan als een gegevensset.
2. Klik op **Opslaan als gegevensset**.
3. Wanneer u hierom wordt gevraagd, voert u een naam en een beschrijving in waarmee u de gegevensset eenvoudig kunt identificeren.
4. Klik op het selectie vakje **OK** .

Wanneer het opslaan is voltooid, is de gegevensset beschikbaar voor gebruik binnen een experiment in uw werk ruimte. U vindt deze in de lijst **opgeslagen gegevens sets** in het module palet.

## <a name="next-steps"></a>Volgende stappen

[Azure Machine Learning Studio-webservices implementeren die gebruikmaken van modules voor het importeren en exporteren van gegevens](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
