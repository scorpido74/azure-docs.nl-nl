---
title: Gegevens bronnen die worden ondersteund in Azure Analysis Services | Microsoft Docs
description: Hierin worden gegevens bronnen en connectors beschreven die worden ondersteund voor tabellaire 1200 en hogere gegevens modellen in Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b08a124ade6e2db8ca27ef61c7f5a6b3fe839885
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442767"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Gegevens bronnen die worden ondersteund in Azure Analysis Services

Gegevens bronnen en connectors die worden weer gegeven in de wizard gegevens ophalen of tabel importeren in Visual Studio met Analysis Services projecten worden weer gegeven voor zowel Azure Analysis Services als SQL Server Analysis Services. Niet alle weer gegeven gegevens bronnen en connectors worden echter ondersteund in Azure Analysis Services. De typen gegevens bronnen waarmee u verbinding kunt maken, zijn afhankelijk van veel factoren, zoals model compatibiliteits niveau, beschik bare gegevens connectors, verificatie type en on-premises gegevens gateway ondersteuning. In de volgende tabellen worden de ondersteunde gegevens bronnen voor Azure Analysis Services beschreven.

## <a name="azure-data-sources"></a>Azure-gegevensbronnen

|Gegevensbron  |In het geheugen  |DirectQuery  |Opmerkingen |
|---------|---------|---------|---------|
|Azure SQL Database      |   Ja      |    Ja      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Azure SQL Data Warehouse      |   Ja      |   Ja       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   Ja       |    Nee      | <sup>[1](#tab1400a)</sup> |
|Azure-tabelopslag     |   Ja       |    Nee      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Ja        |  Nee        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store gen1      |   Ja       |    Nee      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Ja       |    Nee      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     Ja     |   Nee       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Ja       |   Nee       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**Opmerkingen:**    
<a name="tab1400a">1</a> : alleen modellen in tabel vorm 1400 en hoger.  
<a name="azprovider">2</a> -als de gegevens bron van een *provider* in tabellaire 1200 en hoger modellen is opgegeven, is voor zowel in-Memory als DirectQuery-modellen micro soft OLE DB-stuur programma vereist voor SQL Server MSOLEDBSQL (aanbevolen), SQL Server Native Client 11,0 of .NET Framework gegevens provider voor SQL Server.    
<a name="azsqlmanaged">3</a> -Azure SQL database beheerde instantie wordt ondersteund. Omdat beheerde exemplaren worden uitgevoerd in azure VNet met een privé-IP-adres, moet het open bare eind punt zijn ingeschakeld voor het exemplaar. Als deze niet is ingeschakeld, is een [on-premises gegevens gateway](analysis-services-gateway.md) vereist.    
<a name="databricks">4</a> -Azure Databricks het gebruik van de Spark-connector wordt momenteel niet ondersteund.   
<a name="gen2">5</a> -ADLS Gen2 connector wordt momenteel niet ondersteund, maar de Azure Blob Storage-connector kan worden gebruikt met een ADLS Gen2-gegevens bron.   

## <a name="other-data-sources"></a>Andere gegevensbronnen

|Gegevensbron | In het geheugen | DirectQuery |Opmerkingen   |
|  --- | --- | --- | --- |
|Access-database     |  Ja | Nee |  |
|Active Directory     |  Ja | Nee | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  Ja | Nee |  |
|Analytics platform systeem     |  Ja | Nee |  |
|CSV-bestand  |Ja | Nee |  |
|Dynamics 365     |  Ja | Nee | <sup>[6](#tab1400b)</sup> |
|Excel-werkmap     |  Ja | Nee |  |
|Uitwisselen      |  Ja | Nee | <sup>[6](#tab1400b)</sup> |
|Map      |Ja | Nee | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Ja | Nee |  |
|JSON-document      |  Ja | Nee | <sup>[6](#tab1400b)</sup> |
|Regels van binair bestand      | Ja | Nee | <sup>[6](#tab1400b)</sup> |
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
|SharePoint-lijst      |   Ja | Nee | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Ja   | Ja  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> | 
|SQL Server datawarehouse |Ja   | Ja  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Sybase-database     |  Ja | Nee |  |
|Teradata | Ja  | Ja  | <sup>[10](#teradata)</sup> |
|TXT-bestand  |Ja | Nee |  |
|XML-tabel    |  Ja | Nee | <sup>[6](#tab1400b)</sup> |
| | | |

**Opmerkingen:**    
<a name="tab1400b">6</a> : alleen in tabel vorm 1400 en hoger.  
<a name="sqlim">7</a> -als de gegevens bron van een *provider* is opgegeven in tabellaire 1200 en hoger, geeft u micro soft OLE DB driver op voor SQL Server MSOLEDBSQL (aanbevolen), SQL Server Native Client 11,0 of .NET Framework gegevens provider voor SQL Server.  
<a name="instgw">8</a> -als MSOLEDBSQL als gegevens provider worden opgegeven, kan het nodig zijn om het [micro soft OLE DB-stuur programma voor SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) te downloaden en te installeren op dezelfde computer als de on-premises gegevens gateway.  
<a name="oracle">9</a> : Geef de Oracle 1200-gegevens provider voor .net op, of als een *provider* gegevens bron in tabellaire 1400 en-modellen.  
<a name="teradata">10</a> -voor in tabel 1200-modellen, of als gegevens bron van een *provider* in tabellaire 1400 + modellen, geeft u de Teradata-gegevens provider voor .net op.   
<a name="filesSP">11</a> -bestanden in on-premises share point worden niet ondersteund.

Voor het maken van verbinding met on-premises gegevens bronnen van een Azure Analysis Services-server is een [on-premises gateway](analysis-services-gateway.md)vereist. Wanneer u een gateway gebruikt, zijn 64-bits-providers vereist. 

## <a name="understanding-providers"></a>Wat zijn providers?

Bij het maken van in tabel vorm 1400 en een hoger model project in Visual Studio, geeft u standaard geen gegevens provider op wanneer u verbinding maakt met een gegevens bron met behulp van **gegevens ophalen**. In tabel vorm 1400 en hogere modellen wordt gebruikgemaakt van [Power query](/power-query/power-query-what-is-power-query) connectors voor het beheren van verbindingen, gegevens query's en mashups tussen de gegevens bron en Analysis Services. Dit worden ook wel *gestructureerde* gegevens bron verbindingen genoemd in die instellingen voor de verbindings eigenschappen voor u zijn ingesteld. U kunt echter oudere gegevens bronnen inschakelen. Als u deze functie inschakelt, kunt u de **wizard tabel importeren** gebruiken om verbinding te maken met bepaalde gegevens bronnen die traditioneel worden ondersteund in tabel 1200 en lagere modellen als *verouderde*of gegevens bronnen van *providers* . Wanneer u de gegevens bron van een provider opgeeft, kunt u een bepaalde gegevens provider en andere geavanceerde verbindings eigenschappen opgeven. U kunt bijvoorbeeld verbinding maken met een on-premises SQL Server Data Warehouse of zelfs een Azure SQL Database als een verouderde gegevens bron. U kunt vervolgens het OLE DB stuur programma voor SQL Server MSOLEDBSQL-gegevens provider selecteren. In dit geval kan het selecteren van een OLE DB gegevens provider betere prestaties bieden ten opzichte van de Power Query-connector. 

Wanneer u de wizard tabel importeren gebruikt in Visual Studio, is voor verbindingen met een gegevens bron een gegevens provider vereist. Er wordt een standaard gegevens provider voor u geselecteerd. U kunt de gegevens provider zo nodig wijzigen. Het type provider dat u kiest, kan afhankelijk zijn van de prestaties, ongeacht of het model gebruikmaakt van in-Memory opslag of DirectQuery, en op welke Analysis Services platform u uw model implementeert.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Gegevens bronnen van providers opgeven in tabellaire 1400 en hoger model projecten

Als u gegevens bronnen van providers wilt inschakelen, klikt u in Visual Studio op **extra** > **Opties** > Analysis Services **gegevens importeren**in **tabel vorm** > , selecteert u **verouderde gegevens bronnen inschakelen**.

![Verouderde gegevens bronnen inschakelen](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Als verouderde gegevens bronnen zijn ingeschakeld, klikt u in **Tabellaire model Verkenner**met de rechter muisknop op **gegevens bronnen** > **importeren uit gegevens bron (verouderd)** .

![Verouderde gegevens bronnen in Tabellaire model Verkenner](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Net als bij tabellaire 1200 model projecten gebruikt u de **wizard tabel importeren** om verbinding te maken met een gegevens bron. Klik op de pagina verbinding maken op **Geavanceerd**. Geef de gegevens provider en andere Verbindings instellingen op in **Geavanceerde eigenschappen instellen**.

![Geavanceerde eigenschappen van verouderde gegevens bronnen](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>Imitatie
In sommige gevallen kan het nodig zijn om een ander imitatie account op te geven. Het imitatie account kan worden opgegeven in Visual Studio of SSMS.

Voor on-premises gegevens bronnen:

* Als u SQL-verificatie gebruikt, moet imitatie een service account zijn.
* Als u Windows-verificatie gebruikt, stelt u Windows-gebruiker/-wacht woord in. Voor SQL Server wordt Windows-verificatie met een specifiek imitatie account alleen ondersteund voor gegevens modellen in het geheugen.

Voor gegevens bronnen in de Cloud:

* Als u SQL-verificatie gebruikt, moet imitatie een service account zijn.

## <a name="oauth-credentials"></a>OAuth-referenties

Voor tabellaire modellen op het compatibiliteits niveau 1400 en hoger, Azure SQL Database, Azure SQL Data Warehouse, Dynamics 365 en share point-lijst worden OAuth-referenties ondersteund. Azure Analysis Services beheert het vernieuwen van tokens voor OAuth-gegevens bronnen om time-outs voor langdurige vernieuwings bewerkingen te voor komen. Als u geldige tokens wilt genereren, stelt u referenties in met behulp van SSMS.

## <a name="next-steps"></a>Volgende stappen
[On-premises gateway](analysis-services-gateway.md)   
[Uw server beheren](analysis-services-manage.md)   

