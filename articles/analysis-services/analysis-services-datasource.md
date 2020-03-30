---
title: Gegevensbronnen die worden ondersteund in Azure Analysis Services | Microsoft Documenten
description: Beschrijft gegevensbronnen en connectoren die worden ondersteund voor tabelvormige 1200- en hogere gegevensmodellen in Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f65d8fa2c2e522c718c637e32defc4c56fca8364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461654"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Ondersteunde gegevensbronnen in Azure Analysis Services

Gegevensbronnen en connectors die worden weergegeven in de wizard Gegevens of Tabelimporteren in Visual Studio met Analysis Services-projecten worden weergegeven voor zowel Azure Analysis Services als SQL Server Analysis Services. Niet alle weergegeven gegevensbronnen en connectors worden echter ondersteund in Azure Analysis Services. De typen gegevensbronnen waarmee u verbinding maken, zijn afhankelijk van vele factoren, zoals modelcompatibiliteitsniveau, beschikbare gegevensconnectoren, verificatietype en on-premises ondersteuning voor gegevensgateways. In de volgende tabellen worden ondersteunde gegevensbronnen voor Azure Analysis Services beschreven.

## <a name="azure-data-sources"></a>Azure-gegevensbronnen

|Gegevensbron  |In het geheugen  |DirectQuery  |Opmerkingen |
|---------|---------|---------|---------|
|Azure SQL Database      |   Ja      |    Ja      |<sup>[2](#azprovider)</sup>, <sup> [3.](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL Data Warehouse)      |   Ja      |   Ja       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   Ja       |    Nee      | <sup>[1](#tab1400a)</sup> |
|Azure-tabelopslag     |   Ja       |    Nee      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Ja        |  Nee        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Ja       |    Nee      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Ja       |    Nee      |<sup>[1](#tab1400a)</sup>, <sup> [5.](#gen2)</sup>|
|Azure HDInsight HDFS    |     Ja     |   Nee       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Ja       |   Nee       |<sup>[1](#tab1400a)</sup>, <sup> [4.](#databricks)</sup>|
||||

**Notities:**   
<a name="tab1400a">1</a> - Tabelvormige 1400 en hogere modellen alleen.  
<a name="azprovider">2</a> - Wanneer deze is opgegeven als *een providergegevensbron* in tabel100- en hogere modellen, vereisen zowel in-memory- als DirectQuery-modellen Microsoft OLE DB Driver voor SQL Server MSOLEDBSQL (aanbevolen), SQL Server Native Client 11.0 of .NET Framework Data Provider voor SQL Server.    
<a name="azsqlmanaged">3</a> - Azure SQL Database Managed Instance wordt ondersteund. Omdat beheerde instantie binnen Azure VNet wordt uitgevoerd met een privé-IP-adres, moet het openbare eindpunt op de instantie zijn ingeschakeld. Als dit niet is ingeschakeld, is een [on-premises gegevensgateway](analysis-services-gateway.md) vereist.    
<a name="databricks">4</a> - Azure Databricks met de Spark-connector wordt momenteel niet ondersteund.   
<a name="gen2">5</a> - ADLS Gen2-connector wordt momenteel niet ondersteund, maar Azure Blob Storage-connector kan worden gebruikt met een ADLS Gen2-gegevensbron.   

## <a name="other-data-sources"></a>Andere gegevensbronnen

|Gegevensbron | In het geheugen | DirectQuery |Opmerkingen   |
|  --- | --- | --- | --- |
|Access-database     |  Ja | Nee |  |
|Active Directory     |  Ja | Nee | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  Ja | Nee |  |
|Analytics-platformsysteem     |  Ja | Nee |  |
|CSV-bestand  |Ja | Nee |  |
|Dynamics 365     |  Ja | Nee | <sup>[6](#tab1400b)</sup> |
|Excel-werkmap     |  Ja | Nee |  |
|Exchange      |  Ja | Nee | <sup>[6](#tab1400b)</sup> |
|Map      |Ja | Nee | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Ja | Nee |  |
|JSON-document      |  Ja | Nee | <sup>[6](#tab1400b)</sup> |
|Lijnen uit binaire      | Ja | Nee | <sup>[6](#tab1400b)</sup> |
|MySQL-database     | Ja | Nee |  |
|OData-feed      |  Ja | Nee | <sup>[6](#tab1400b)</sup> |
|ODBC-query     | Ja | Nee |  |
|OLE DB     |   Ja | Nee |  |
|Oracle  | Ja  |Ja  | <sup>[9](#oracle)</sup> |
|PostgreSQL-database   | Ja | Nee | <sup>[6](#tab1400b)</sup> |
|Salesforce-objecten|  Ja | Nee | <sup>[6](#tab1400b)</sup> |
|Salesforce-rapporten |Ja | Nee | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Ja | Nee |  |
|SAP Business Warehouse    |  Ja | Nee | <sup>[6](#tab1400b)</sup> |
|SharePoint-lijst      |   Ja | Nee | <sup>[6](#tab1400b)</sup>, <sup> [11.](#filesSP)</sup> |
|SQL Server |Ja   | Ja  | <sup>[7](#sqlim)</sup>, <sup> [8.](#instgw)</sup> | 
|SQL Server-gegevensmagazijn |Ja   | Ja  | <sup>[7](#sqlim)</sup>, <sup> [8.](#instgw)</sup> |
|Sybase-database     |  Ja | Nee |  |
|Teradata | Ja  | Ja  | <sup>[10](#teradata)</sup> |
|TXT-bestand  |Ja | Nee |  |
|XML-tabel    |  Ja | Nee | <sup>[6](#tab1400b)</sup> |
| | | |

**Notities:**   
<a name="tab1400b">6</a> - Tabelvormige 1400 en hogere modellen alleen.  
<a name="sqlim">7</a> - Wanneer u als *providergegevensbron* in tabel100- en hogere modellen wordt opgegeven, geeft u Microsoft OLE DB Driver op voor SQL Server MSOLEDBSQL (aanbevolen), SQL Server Native Client 11.0 of .NET Framework Data Provider voor SQL Server.  
<a name="instgw">8</a> - Als u MSOLEDBSQL als gegevensprovider opgeeft, kan het nodig zijn om de [Microsoft OLE DB Driver voor SQL Server te](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) downloaden en te installeren op dezelfde computer als de on-premises gegevensgateway.  
<a name="oracle">9</a> - Voor tabelvormige 1200-modellen of als *gegevensbron van een provider* in tabel100+ modellen, geeft u Oracle Data Provider op voor .NET.  
<a name="teradata">10</a> - Voor tabelvormige 1200-modellen of als *gegevensbron van een provider* in tabel100+ modellen, geeft u Teradata Data Provider op voor .NET.   
<a name="filesSP">11</a> - Bestanden in on-premises SharePoint worden niet ondersteund.

Voor verbinding met on-premises gegevensbronnen van een Azure Analysis Services-server is een [on-premises gateway](analysis-services-gateway.md)vereist. Bij het gebruik van een gateway zijn 64-bits providers vereist. 

## <a name="understanding-providers"></a>Inzicht in providers

Bij het maken van tabel1400- en hogere modelprojecten in Visual Studio geeft u standaard geen gegevensprovider op wanneer u verbinding maakt met een gegevensbron met **behulp van Gegevens verzamelen**. Tabelvormige 1400- en hogere modellen gebruiken [Power Query-connectors](/power-query/power-query-what-is-power-query) om verbindingen, gegevensquery's en mashups tussen de gegevensbron en analyseservices te beheren. Deze worden soms aangeduid als *gestructureerde* gegevensbronverbindingen in die verbindingseigenschapinstellingen die voor u zijn ingesteld. U echter wel verouderde gegevensbronnen inschakelen. Wanneer u dit hebt ingeschakeld, u **de wizard Tabel importeren** gebruiken om verbinding te maken met bepaalde gegevensbronnen die traditioneel worden ondersteund in tabel1200 en lagere modellen als *oudere*of *providergegevensbronnen.* Wanneer u als gegevensbron van een provider wordt opgegeven, u een bepaalde gegevensprovider en andere geavanceerde verbindingseigenschappen opgeven. U bijvoorbeeld verbinding maken met een on-premises SQL Server Data Warehouse of zelfs een Azure SQL Database als een verouderde gegevensbron. U vervolgens de OLE DB Driver selecteren voor SQL Server MSOLEDBSQL-gegevensprovider. In dit geval kan het selecteren van een OLE DB-gegevensprovider betere prestaties bieden via de Power Query-connector. 

Wanneer u de wizard Tabel importeren in Visual Studio gebruikt, hebben verbindingen met elke gegevensbron een gegevensprovider nodig. Er is een standaardgegevensprovider voor u geselecteerd. U de gegevensprovider indien nodig wijzigen. Het type provider dat u kiest, kan afhankelijk zijn van de prestaties, of het model al dan niet gebruik maakt van in-memory storage of DirectQuery en naar welk Analysis Services-platform u uw model implementeert.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Providergegevensbronnen opgeven in tabel100- en hogere modelprojecten

Als u providergegevensbronnen wilt inschakelen, klikt u in Visual Studio op**Tabelgegevens** > **importeren** **van hulpmiddelenvoor** > **opties,** > selecteert u **Verouderde gegevensbronnen inschakelen**.

![Verouderde gegevensbronnen inschakelen](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Als verouderde gegevensbronnen zijn ingeschakeld, klikt u in **TabelmodelVerkenner**met de rechtermuisknop op **Gegevensbronnen** > **importeren uit gegevensbron (Legacy).**

![Verouderde gegevensbronnen in Tabelmodel Explorer](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Net als bij tabel1200-modelprojecten gebruikt u **wizard Tabelimporteren** om verbinding te maken met een gegevensbron. Klik op de pagina Verbinding op **Geavanceerd**. Geef gegevensprovider en andere verbindingsinstellingen op in **Geavanceerde eigenschappen instellen**.

![Geavanceerde eigenschappen van verouderde gegevensbronnen](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>Imitatie
In sommige gevallen kan het nodig zijn om een ander imitatieaccount op te geven. Impersonatie-account kan worden opgegeven in Visual Studio of SSMS.

Voor on-premises gegevensbronnen:

* Als u SQL-verificatie gebruikt, moet imitatie serviceaccount zijn.
* Als u Windows-verificatie gebruikt, stelt u Windows-gebruiker/wachtwoord in. Voor SQL Server wordt Windows-verificatie met een specifiek imitatieaccount alleen ondersteund voor gegevensmodellen in het geheugen.

Voor cloudgegevensbronnen:

* Als u SQL-verificatie gebruikt, moet imitatie serviceaccount zijn.

## <a name="oauth-credentials"></a>OAuth-referenties

Voor tabelmodellen op 1400- en hoger compatibiliteitsniveau met in-memorymodus ondersteunen Azure SQL Database, Azure Synapse Analytics (SQL Data Warehouse), Dynamics 365 en SharePoint List OAuth-referenties. Azure Analysis Services beheert tokenvernieuwing voor OAuth-gegevensbronnen om time-outs voor langdurige vernieuwingsbewerkingen te voorkomen. Als u geldige tokens wilt genereren, stelt u referenties in met Behulp van SSMS.

De modus Direct Query wordt niet ondersteund met OAuth-referenties.

## <a name="next-steps"></a>Volgende stappen
[On-premises gateway](analysis-services-gateway.md)   
[Uw server beheren](analysis-services-manage.md)   

