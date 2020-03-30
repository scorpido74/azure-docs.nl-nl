---
title: Trainingsgegevens importeren
titleSuffix: ML Studio (classic) - Azure
description: Uw gegevens importeren in Azure Machine Learning Studio (klassiek) uit verschillende gegevensbronnen. Ontdek welke gegevenstypen en gegevensindelingen worden ondersteund.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: cee49124a7547399889e425008a8580b9b25945a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217982"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Uw trainingsgegevens importeren in Azure Machine Learning Studio (klassiek) uit verschillende gegevensbronnen

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Om uw eigen gegevens in Machine Learning Studio (klassiek) te gebruiken om een predictive analytics-oplossing te ontwikkelen en te trainen, u gegevens gebruiken van: 

* **Lokaal bestand** - Lokale gegevens van tevoren laden vanaf uw harde schijf om een gegevenssetmodule in uw werkruimte te maken
* **Online gegevensbronnen** : gebruik de module [Gegevens importeren][import-data] om toegang te krijgen tot gegevens uit een van de verschillende onlinebronnen terwijl uw experiment wordt uitgevoerd
* **Machine Learning Studio (klassiek) experiment** - Gebruik gegevens die zijn opgeslagen als gegevensset in Machine Learning Studio (klassiek)
* [**On-premises SQL Server-database**](use-data-from-an-on-premises-sql-server.md) - Gegevens uit een on-premises SQL Server-database gebruiken zonder gegevens handmatig te hoeven kopiëren

> [!NOTE]
> Er zijn een aantal voorbeeldgegevenssets beschikbaar in Machine Learning Studio (klassiek) die u gebruiken voor trainingsgegevens. Zie [De voorbeeldgegevenssets gebruiken in Azure Machine Learning Studio (klassiek)](use-sample-datasets.md)voor meer informatie hierover.

## <a name="prepare-data"></a>Gegevens voorbereiden

Machine Learning Studio (klassiek) is ontworpen om te werken met rechthoekige of tabelgegevens, zoals tekstgegevens die zijn gescheiden of gestructureerde gegevens uit een database, hoewel in sommige omstandigheden niet-rechthoekige gegevens kunnen worden gebruikt.

Het is het beste als uw gegevens relatief schoon zijn voordat u deze importeert in Studio (klassiek). U wilt bijvoorbeeld problemen oplossen, zoals niet-geciteerde tekenreeksen.

Er zijn echter modules beschikbaar in Studio (klassiek) die enige manipulatie van gegevens binnen uw experiment mogelijk maken nadat u uw gegevens hebt geïmporteerd. Afhankelijk van de machine learning-algoritmen die u zult gebruiken, moet u mogelijk beslissen hoe u omgaat met structurele problemen met gegevens, zoals ontbrekende waarden en schaarse gegevens, en er zijn modules die daarbij kunnen helpen. Kijk in de sectie **Gegevenstransformatie** van het modulepalet naar modules die deze functies uitvoeren.

Op elk moment in uw experiment u de gegevens die door een module worden geproduceerd bekijken of downloaden door op de uitvoerpoort te klikken. Afhankelijk van de module zijn er mogelijk verschillende downloadopties beschikbaar, of u de gegevens in uw webbrowser in Studio (klassiek) visualiseren.

## <a name="supported-data-formats-and-data-types"></a>Ondersteunde gegevensindelingen en gegevenstypen

U een aantal gegevenstypen importeren in uw experiment, afhankelijk van het mechanisme dat u gebruikt om gegevens te importeren en waar deze vandaan komen:

* Platte tekst (.txt)
* Door komma's gescheiden waarden (CSV) met een koptekst (.csv) of zonder (.nh.csv)
* Tab-gescheiden waarden (TSV) met een koptekst (.tsv) of zonder (.nh.tsv)
* Excel-bestand
* Azure Table
* De lijst van de bijenkorf
* SQL-databasetabel
* OData-waarden
* SVMLight-gegevens (.svmlight) (zie de [SVMLight-definitie](http://svmlight.joachims.org/) voor opmaakgegevens)
* Arff-gegevens (Attribute Relation File Format) (.arff) (zie de [ARFF-definitie](https://weka.wikispaces.com/ARFF) voor opmaakgegevens)
* Zip-bestand (.zip)
* R-object of werkruimtebestand (. RData)

Als u gegevens importeert in een indeling zoals ARFF die metagegevens bevat, gebruikt Studio (klassiek) deze metagegevens om de kop en het gegevenstype van elke kolom te definiëren.

Als u gegevens importeert, zoals de TSV- of CSV-indeling die deze metagegevens niet bevat, leidt Studio (klassiek) het gegevenstype voor elke kolom af door de gegevens te samplen. Als de gegevens ook geen kolomkoppen bevatten, biedt Studio (klassiek) standaardnamen.

U de koppen en gegevenstypen voor kolommen expliciet opgeven of wijzigen met de module [Metagegevens bewerken.][edit-metadata]

De volgende gegevenstypen worden herkend door Studio (klassiek):

* Tekenreeks
* Geheel getal
* Double
* Booleaans
* DateTime
* TimeSpan

Studio gebruikt een intern gegevenstype genaamd ***gegevenstabel*** om gegevens tussen modules door te geven. U uw gegevens expliciet omzetten in gegevenstabelindeling met de module [Converteren naar gegevensset.][convert-to-dataset]

Elke module die andere indelingen dan de gegevenstabel accepteert, converteert de gegevens in stilte naar gegevenstabel voordat deze wordt doorgestoofd naar de volgende module.

Indien nodig u de indeling van gegevenstabelen weer omzetten in CSV-, TSV-, ARFF- of SVMLight-indeling met behulp van andere conversiemodules.
Kijk in het gedeelte **Gegevensformaatconversies** van het modulepalet naar modules die deze functies uitvoeren.

## <a name="data-capacities"></a>Gegevenscapaciteiten

Modules in Machine Learning Studio (klassieke) ondersteunen gegevenssets tot 10 GB dichte numerieke gegevens voor veelvoorkomende use cases. Als een module meer dan één invoer heeft, is de waarde 10 GB de totale invoergrootte. U grotere gegevenssets bemonsteren met query's uit Hive of Azure SQL Database, of u Learning by Counts preprocessing gebruiken voordat u de gegevens importeert.  

U kunt de volgende typen gegevens in grotere gegevenssets opnemen tijdens het normaliseren van kenmerken, tot maximaal 10 GB:

* Sparse
* Categorische gegevens
* Tekenreeksen
* Binaire gegevens

De volgende modules zijn beperkt tot gegevenssets die kleiner zijn dan 10 GB:

* Aanbevelingsmodules
* De module Synthetic Minority Oversampling Technique (SMOTE)
* Scriptmodules: R, Python, SQL
* Modules waarbij de grootte van de uitvoer groter is dan invoergegevens, zoals Join- of hash-functies
* Kruisvalidatie, Tune Model Hyperparameters, ordinale regressie en One-vs-All-multiklasse, wanneer het aantal herhalingen groot is

Upload de gegevens naar Azure Storage of Azure SQL Database naar Azure Storage of Azure SQL Database of gebruikt Azure HDInsight in plaats van rechtstreeks vanuit een lokaal bestand te uploaden.

U vindt informatie over afbeeldingsgegevens in de referentie van de module [Afbeeldingen importeren.](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes)

## <a name="import-from-a-local-file"></a>Importeren uit een lokaal bestand

U een gegevensbestand uploaden vanaf uw harde schijf om te gebruiken als trainingsgegevens in Studio (klassiek). Wanneer u een gegevensbestand importeert, maakt u een gegevenssetmodule die klaar is voor gebruik in experimenten in uw werkruimte.

Ga als volgt te werk om gegevens van een lokale harde schijf te importeren:

1. Klik **op +NIEUW** onder aan het venster Studio (klassiek).
2. Selecteer **Gegevensset** en **UIT LOKAAL BESTAND**.
3. Blader in het dialoogvenster **Een nieuwe gegevensset uploaden** naar het bestand dat u wilt uploaden.
4. Voer een naam in, identificeer het gegevenstype en voer optioneel een beschrijving in. Een beschrijving wordt aanbevolen - hiermee u alle kenmerken van de gegevens vastleggen die u wilt onthouden wanneer u de gegevens in de toekomst gebruikt.
5. Het selectievakje **Met dit is de nieuwe versie van een bestaande gegevensset** u een bestaande gegevensset bijwerken met nieuwe gegevens. Klik hiervoor op dit selectievakje en voer de naam van een bestaande gegevensset in.

![Een nieuwe gegevensset uploaden](./media/import-data/upload-dataset-from-local-file.png)

De uploadtijd is afhankelijk van de grootte van uw gegevens en de snelheid van uw verbinding met de service. Als u weet dat het bestand lang zal duren, u andere dingen doen in Studio (klassiek) terwijl u wacht. Als u de browser echter sluit voordat de gegevensupload is voltooid, mislukt het uploaden.

Zodra uw gegevens zijn geüpload, worden deze opgeslagen in een gegevenssetmodule en beschikbaar voor elk experiment in uw werkruimte.

Wanneer u een experiment bewerkt, u de gegevenssets vinden die u hebt geüpload in de lijst **Mijn gegevenssets** onder de lijst **Opgeslagen gegevenssets** in het modulepalet. U de gegevensset naar het experimentcanvas slepen en neerzetten wanneer u de gegevensset wilt gebruiken voor verdere analyses en machine learning.

## <a name="import-from-online-data-sources"></a>Importeren uit online gegevensbronnen

Met de module [Gegevens importeren][import-data] kan uw experiment gegevens importeren uit verschillende online gegevensbronnen terwijl het experiment wordt uitgevoerd.

> [!NOTE]
> In dit artikel vindt u algemene informatie over de module [Gegevens importeren.][import-data] Zie het referentieonderwerp module voor de module [Gegevens importeren][import-data] voor meer gedetailleerde informatie over de typen gegevens die u openen, indelingen, parameters en antwoorden op veelgestelde vragen.

Met de module [Gegevens importeren][import-data] hebt u toegang tot gegevens uit een van de verschillende online gegevensbronnen terwijl uw experiment wordt uitgevoerd:

* Een web-URL met HTTP
* Hadoop met HiveQL
* Azure Blob Storage
* Azure Table
* Azure SQL-database of SQL Server op Azure VM
* On-premises SQL Server-database
* Een datafeedprovider, OData momenteel
* Azure Cosmos DB

Omdat deze trainingsgegevens worden geopend terwijl uw experiment wordt uitgevoerd, zijn deze alleen beschikbaar in dat experiment. Ter vergelijking: gegevens die zijn opgeslagen in een gegevenssetmodule zijn beschikbaar voor elk experiment in uw werkruimte.

Als u toegang wilt krijgen tot online gegevensbronnen in uw Studio-experiment (klassiek) experiment, voegt u de module [Gegevens importeren][import-data] toe aan uw experiment. Selecteer vervolgens **Wizard Gegevens importeren starten** onder **Eigenschappen** voor stapsgewijze instructies voor begeleide instructies om de gegevensbron te selecteren en te configureren. U ook handmatig **Gegevensbron** selecteren onder **Eigenschappen** en de parameters opgeven die nodig zijn om toegang te krijgen tot de gegevens.

De online gegevensbronnen die worden ondersteund, worden gespecificeerd in de onderstaande tabel. In deze tabel worden ook de bestandsindelingen samengevat die worden ondersteund en parameters die worden gebruikt om toegang te krijgen tot de gegevens.

> [!IMPORTANT]
> Momenteel kunnen de modules [Gegevens importeren][import-data] en [gegevens exporteren][export-data] alleen gegevens lezen en schrijven vanuit Azure-opslag die is gemaakt met behulp van het klassieke implementatiemodel. Met andere woorden, het nieuwe Azure Blob Storage-accounttype dat een hot storage-toegangslaag of koele opslagtoegangslaag biedt, wordt nog niet ondersteund.
>
> Over het algemeen mogen azure-opslagaccounts die u hebt gemaakt voordat deze serviceoptie beschikbaar kwam, niet worden beïnvloed.
> Als u een nieuw account wilt maken, selecteert u **Klassiek** voor het implementatiemodel of gebruikt u Resourcebeheer en selecteert **u Algemeen doel** in plaats van **Blob-opslag** voor **accountsoort**.
>
> Zie [Azure Blob Storage: Hot and Cool Storage Tiers](../../storage/blobs/storage-blob-storage-tiers.md)voor meer informatie.

### <a name="supported-online-data-sources"></a>Ondersteunde online gegevensbronnen
De module Azure Machine Learning Studio (klassieke) **importgegevens** ondersteunt de volgende gegevensbronnen:

| Gegevensbron | Beschrijving | Parameters |
| --- | --- | --- |
| Web-URL via HTTP |Hiermee worden gegevens gelezen in door komma's gescheiden waarden (CSV), door tabbladen gescheiden waarden (TSV), arff (attribute-relation file format) en Support Vector Machines (SVM-light) op elke web-URL die HTTP gebruikt |<b>URL</b>: Hiermee geeft u de volledige naam van het bestand op, inclusief de url van de site en de bestandsnaam, met elke extensie. <br/><br/><b>Gegevensindeling</b>: hiermee geeft u een van de ondersteunde gegevensindelingen op: CSV, TSV, ARFF of SVM-light. Als de gegevens een koptekstrij hebben, wordt deze gebruikt om kolomnamen toe te wijzen. |
| Hadoop/HDFS |Leest gegevens uit gedistribueerde opslag in Hadoop. U geeft de gewenste gegevens op met HiveQL, een SQL-achtige querytaal. HiveQL kan ook worden gebruikt om gegevens samen te voegen en gegevenste filteren uit te voeren voordat u de gegevens toevoegt aan Studio (klassiek). |<b>Hive-databasequery:</b>hiermee geeft u de hivequery op die wordt gebruikt om de gegevens te genereren.<br/><br/><b>HCatalog-server URI</b> : De naam van uw cluster opgegeven met de indeling * &lt;van uw clusternaam&gt;.azurehdinsight.net.*<br/><br/><b>Hadoop-gebruikersnaamnaam</b>: hiermee geeft u de naam van het Hadoop-gebruikersaccount op dat wordt gebruikt om het cluster in te richten.<br/><br/><b>Hadoop-gebruikersaccountwachtwoord</b> : geeft de referenties op die worden gebruikt bij het inrichten van het cluster. Zie [Hadoopclusters maken in HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)voor meer informatie.<br/><br/><b>Locatie van uitvoergegevens</b>: geeft aan of de gegevens zijn opgeslagen in een Hadoop distributed file system (HDFS) of in Azure. <br/><ul>Als u uitvoergegevens opslaat in HDFS, geeft u de Uri van de HDFS-server op. (Zorg ervoor dat u de clusternaam HDInsight gebruikt zonder het HTTPS://-voorvoegsel). <br/><br/>Als u uw uitvoergegevens opslaat in Azure, moet u de naam van het Azure-opslagaccount, de opslagtoegangssleutel en de naam van de opslagcontainer opgeven.</ul> |
| SQL-database |Hiermee worden gegevens gelezen die zijn opgeslagen in een Azure SQL-database of in een SQL Server-database die wordt uitgevoerd op een virtuele Azure-machine. |<b>Naam van de databaseserver:</b>hiermee geeft u de naam op van de server waarop de database wordt uitgevoerd.<br/><ul>Voer in het geval van Azure SQL Database de servernaam in die wordt gegenereerd. Meestal heeft het de vorm * &lt;generated_identifier&gt;.database.windows.net.* <br/><br/>In het geval van een SQL-server die wordt gehost op een Azure Virtual-machine, voert u *&lt;tcp: Virtual Machine DNS-naam&gt;in, 1433*</ul><br/><b>Databasenaam: </b>hiermee geeft u de naam van de database op de server op. <br/><br/><b>Servergebruikersnaamnaam:</b>hiermee geeft u een gebruikersnaam op voor een account met toegangsmachtigingen voor de database. <br/><br/><b>Servergebruikersaccountwachtwoord</b>: hiermee geeft u het wachtwoord voor het gebruikersaccount op.<br/><br/><b>Databasequery</b>:Voer een SQL-instructie in waarin de gegevens worden beschreven die u wilt lezen. |
| On-premises SQL-database |Hiermee leest u gegevens die zijn opgeslagen in een on-premises SQL-database. |<b>Gegevensgateway:</b>hiermee geeft u de naam op van de Data Management Gateway die is geïnstalleerd op een computer waar deze toegang heeft tot uw SQL Server-database. Zie [Geavanceerde analyses uitvoeren met Azure Machine Learning Studio (klassiek) uitvoeren met behulp van gegevens van een on-premises SQL-server](use-data-from-an-on-premises-sql-server.md)voor informatie over het instellen van de gateway.<br/><br/><b>Naam van de databaseserver:</b>hiermee geeft u de naam op van de server waarop de database wordt uitgevoerd.<br/><br/><b>Databasenaam: </b>hiermee geeft u de naam van de database op de server op. <br/><br/><b>Servergebruikersnaamnaam:</b>hiermee geeft u een gebruikersnaam op voor een account met toegangsmachtigingen voor de database. <br/><br/><b>Gebruikersnaam en wachtwoord:</b>klik op <b>Waarden invoeren</b> om uw databasereferenties in te voeren. U Windows Integrated Authentication of SQL Server Authentication gebruiken, afhankelijk van hoe uw on-premises SQL Server is geconfigureerd.<br/><br/><b>Databasequery</b>:Voer een SQL-instructie in waarin de gegevens worden beschreven die u wilt lezen. |
| Azure Table |Hiermee leest u gegevens uit de tabelservice in Azure Storage.<br/><br/>Als u zelden grote hoeveelheden gegevens leest, gebruikt u de Azure Table Service. Het biedt een flexibele, niet-relationele (NoSQL), enorm schaalbare, goedkope en zeer beschikbare opslagoplossing. |De opties in de **importgegevens** wijzigen afhankelijk van of u toegang hebt tot openbare informatie of een privé-opslagaccount waarvoor inloggegevens vereist zijn. Dit wordt bepaald door het <b>verificatietype</b> dat waarde kan hebben van "PublicOrSAS" of "Account", die elk zijn eigen set parameters hebben. <br/><br/><b>Openbare of Gedeelde Toegangshandtekening (SAS) URI:</b>De parameters zijn:<br/><br/><ul><b>TabelURI:</b>hiermee geeft u de URL Openbaar of SAS voor de tabel op.<br/><br/><b>Hiermee geeft u de rijen op die moeten worden scannen voor eigenschapsnamen:</b>de waarden zijn <i>TopN</i> om het opgegeven aantal rijen te scannen of <i>ScanAll</i> om alle rijen in de tabel te krijgen. <br/><br/>Als de gegevens homogeen en voorspelbaar zijn, wordt aanbevolen *TopN te* selecteren en een getal voor N in te voeren. Voor grote tabellen kan dit resulteren in snellere leestijden.<br/><br/>Als de gegevens zijn gestructureerd met sets eigenschappen die variëren op basis van de diepte en positie van de tabel, kiest u de optie *Scannen allen* om alle rijen te scannen. Dit garandeert de integriteit van uw resulterende eigenschap en metadata conversie.<br/><br/></ul><b>Account privéopslag:</b>De parameters zijn: <br/><br/><ul><b>Accountnaam:</b>hiermee geeft u de naam op van het account dat de te lezen tabel bevat.<br/><br/><b>Accountsleutel:</b>hiermee geeft u de opslagsleutel op die aan het account is gekoppeld.<br/><br/><b>Tabelnaam:</b> hiermee geeft u de naam op van de tabel die de te lezen gegevens bevat.<br/><br/><b>Rijen die moeten worden scannen op eigenschapnamen:</b>de waarden zijn <i>TopN</i> om het opgegeven aantal rijen te scannen of <i>ScanAll</i> om alle rijen in de tabel te krijgen.<br/><br/>Als de gegevens homogeen en voorspelbaar zijn, raden we u aan *TopN* te selecteren en een getal voor N in te voeren. Voor grote tabellen kan dit resulteren in snellere leestijden.<br/><br/>Als de gegevens zijn gestructureerd met sets eigenschappen die variëren op basis van de diepte en positie van de tabel, kiest u de optie *Scannen allen* om alle rijen te scannen. Dit garandeert de integriteit van uw resulterende eigenschap en metadata conversie.<br/><br/> |
| Azure Blob Storage |Hiermee leest u gegevens die zijn opgeslagen in de Blob-service in Azure Storage, inclusief afbeeldingen, ongestructureerde tekst of binaire gegevens.<br/><br/>U de Blob-service gebruiken om gegevens openbaar te maken of om toepassingsgegevens privé op te slaan. U uw gegevens overal openen via HTTP- of HTTPS-verbindingen. |De opties in de module **Gegevens importeren** wijzigen afhankelijk van of u toegang hebt tot openbare informatie of een privé-opslagaccount waarvoor inloggegevens vereist zijn. Dit wordt bepaald door het <b>verificatietype</b> dat een waarde kan hebben van "PublicOrSAS" of van "Account".<br/><br/><b>Openbare of Gedeelde Toegangshandtekening (SAS) URI:</b>De parameters zijn:<br/><br/><ul><b>URI:</b>hiermee geeft u de URL Openbaar of SAS voor de opslagblob op.<br/><br/><b>Bestandsindeling:</b>hiermee geeft u de indeling van de gegevens in de Blob-service op. De ondersteunde indelingen zijn CSV, TSV en ARFF.<br/><br/></ul><b>Account privéopslag:</b>De parameters zijn: <br/><br/><ul><b>Accountnaam:</b>hiermee geeft u de naam op van het account dat de blob bevat die u wilt lezen.<br/><br/><b>Accountsleutel:</b>hiermee geeft u de opslagsleutel op die aan het account is gekoppeld.<br/><br/><b>Pad naar container, map of blob:</b> hiermee geeft u de naam op van de blob die de te lezen gegevens bevat.<br/><br/><b>Blob-bestandsindeling:</b>hiermee geeft u de indeling van de gegevens in de blobservice op. De ondersteunde gegevensindelingen zijn CSV, TSV, ARFF, CSV met een opgegeven codering en Excel. <br/><br/><ul>Als de indeling CSV of TSV is, moet u aangeven of het bestand een koptekstrij bevat.<br/><br/>U de optie Excel gebruiken om gegevens uit Excel-werkmappen te lezen. Geef in de optie <i>Excel-gegevensindeling</i> aan of de gegevens zich in een Excel-werkbladbereik bevinden of in een Excel-tabel. Geef in het <i>Excel-blad of de ingesloten tabeloptie </i>de naam op van het blad of de tabel waaruit u wilt lezen.</ul><br/> |
| Leverancier van gegevensfeed |Hiermee leest u gegevens van een ondersteunde feedprovider. Momenteel wordt alleen de OData-indeling (Open Data Protocol) ondersteund. |<b>Gegevensinhoudstype</b>: Hiermee geeft u de OData-indeling op.<br/><br/><b>Bron-URL</b>: Hiermee geeft u de volledige URL voor de gegevensfeed op. <br/>De volgende URL wordt bijvoorbeeld gelezen uit de voorbeelddatabase van Northwind:https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importeren uit een ander experiment

Er zullen momenten zijn waarop u een tussenresultaat van het ene experiment wilt nemen en het wilt gebruiken als onderdeel van een ander experiment. Om dit te doen, slaat u de module op als een gegevensset:

1. Klik op de uitvoer van de module die u als gegevensset wilt opslaan.
2. Klik **op Opslaan als gegevensset**.
3. Wanneer u daarom wordt gevraagd, voert u een naam en een beschrijving in waarmee u de gegevensset eenvoudig identificeren.
4. Klik op het **ok-vinkje.**

Wanneer de opslag is voltooid, is de gegevensset beschikbaar voor gebruik binnen elk experiment in uw werkruimte. U vindt het in de lijst **Opgeslagen gegevenssets** in het modulepalet.

## <a name="next-steps"></a>Volgende stappen

[Azure Machine Learning Studio-webservices implementeren die gebruikmaken van modules voor gegevensimporteren en gegevensexporteren](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
