---
title: Gegevens bronnen die worden ondersteund in Azure Analysis Services | Microsoft Docs
description: Hierin worden gegevens bronnen en connectors beschreven die worden ondersteund voor tabellaire 1200 en hogere gegevens modellen in Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dc25c853a37de5c310d37e7ee64c6f762283cb0a
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077436"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Ondersteunde gegevensbronnen in Azure Analysis Services

Gegevens bronnen en connectors die worden weer gegeven in de wizard gegevens ophalen of tabel importeren in Visual Studio met Analysis Services projecten worden weer gegeven voor zowel Azure Analysis Services als SQL Server Analysis Services. Niet alle weer gegeven gegevens bronnen en connectors worden echter ondersteund in Azure Analysis Services. De typen gegevens bronnen waarmee u verbinding kunt maken, zijn afhankelijk van veel factoren, zoals model compatibiliteits niveau, beschik bare gegevens connectors, verificatie type en on-premises gegevens gateway ondersteuning. In de volgende tabellen worden de ondersteunde gegevens bronnen voor Azure Analysis Services beschreven.

## <a name="azure-data-sources"></a>Azure-gegevensbronnen

|Gegevensbron  |In het geheugen  |DirectQuery  |Notities |
|---------|---------|---------|---------|
|Azure SQL Database      |   Ja      |    Yes      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL DW)      |   Ja      |   Yes       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   Yes       |    Nee      | <sup>[1](#tab1400a)</sup> |
|Azure Table Storage     |   Yes       |    Nee      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Yes        |  Nee        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Yes       |    Nee      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Yes       |    Nee      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     Yes     |   Nee       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Yes       |   Nee       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**Opmerkingen:**

<a name="tab1400a">1</a> : alleen modellen in tabel vorm 1400 en hoger.  
<a name="azprovider">2</a> -als de gegevens bron van een *provider* in tabellaire 1200 en hoger modellen is opgegeven, is voor zowel in-Memory als DirectQuery-modellen micro soft OLE DB-stuur programma vereist voor SQL Server MSOLEDBSQL (aanbevolen), SQL Server Native Client 11,0 of .NET Framework gegevens provider voor SQL Server.  
<a name="azsqlmanaged">3</a> -Azure SQL Managed instance wordt ondersteund. Omdat het beheerde exemplaar van SQL wordt uitgevoerd in azure VNet met een privé-IP-adres, moet het open bare eind punt zijn ingeschakeld voor het exemplaar. Als deze niet is ingeschakeld, is een [on-premises gegevens gateway](analysis-services-gateway.md) vereist.  
<a name="databricks">4</a> -Azure Databricks het gebruik van de Spark-connector wordt momenteel niet ondersteund.  
<a name="gen2">5</a> -ADLS Gen2 connector wordt momenteel niet ondersteund, maar de Azure Blob Storage-connector kan worden gebruikt met een ADLS Gen2-gegevens bron.

## <a name="other-data-sources"></a>Andere gegevensbronnen

|Gegevensbron | In het geheugen | DirectQuery |Notities   |
|  --- | --- | --- | --- |
|Access-database     |  Yes | Nee |  |
|Active Directory     |  Yes | Nee | <sup>[6,5](#tab1400b)</sup>  |
|Analysis Services     |  Yes | Nee |  |
|Analytics platform systeem     |  Yes | Nee |  |
|CSV-bestand  |Yes | Nee |  |
|Dynamics 365     |  Yes | Nee | <sup>[6,5](#tab1400b)</sup> |
|Excel-werkmap     |  Yes | Nee |  |
|Exchange      |  Yes | Nee | <sup>[6,5](#tab1400b)</sup> |
|Map      |Ja | Nee | <sup>[6,5](#tab1400b)</sup> |
|IBM Informix  |Yes | Nee |  |
|JSON-document      |  Yes | Nee | <sup>[6,5](#tab1400b)</sup> |
|Regels van binair bestand      | Yes | Nee | <sup>[6,5](#tab1400b)</sup> |
|MySQL-database     | Yes | Nee |  |
|OData-feed      |  Yes | Nee | <sup>[6,5](#tab1400b)</sup> |
|ODBC-query     | Yes | Nee |  |
|OLE DB     |   Yes | Nee |  |
|Oracle  | Ja  |Yes  | <sup>[9](#oracle)</sup> |
|PostgreSQL-database   | Yes | Nee | <sup>[6,5](#tab1400b)</sup> |
|Salesforce-objecten|  Yes | Nee | <sup>[6,5](#tab1400b)</sup> |
|Salesforce-rapporten |Yes | Nee | <sup>[6,5](#tab1400b)</sup> |
|SAP HANA     |  Yes | Nee |  |
|SAP Business Warehouse    |  Yes | Nee | <sup>[6,5](#tab1400b)</sup> |
|SharePoint-lijst      |   Yes | Nee | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Ja   | Yes  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|SQL Server Data Warehouse |Ja   | Yes  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Sybase-database     |  Yes | Nee |  |
|Teradata | Ja  | Yes  | <sup>[10](#teradata)</sup> |
|TXT-bestand  |Yes | Nee |  |
|XML-tabel    |  Yes | Nee | <sup>[6,5](#tab1400b)</sup> |
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

Bij het maken van in tabel vorm 1400 en een hoger model project in Visual Studio, geeft u standaard geen gegevens provider op wanneer u verbinding maakt met een gegevens bron met behulp van **gegevens ophalen**. In tabel vorm 1400 en hogere modellen wordt gebruikgemaakt van [Power query](/power-query/power-query-what-is-power-query) connectors voor het beheren van verbindingen, gegevens query's en mashups tussen de gegevens bron en Analysis Services. Dit worden ook wel *gestructureerde* gegevens bron verbindingen genoemd in die instellingen voor de verbindings eigenschappen voor u zijn ingesteld. U kunt echter verouderde gegevens bronnen inschakelen voor een model project in Visual Studio. Als u deze functie inschakelt, kunt u de **wizard tabel importeren** gebruiken om verbinding te maken met bepaalde gegevens bronnen die traditioneel worden ondersteund in tabel 1200 en lagere modellen als *verouderde*of gegevens bronnen van *providers* . Wanneer u de gegevens bron van een provider opgeeft, kunt u een bepaalde gegevens provider en andere geavanceerde verbindings eigenschappen opgeven. U kunt bijvoorbeeld verbinding maken met een SQL Server Data Warehouse-instantie of zelfs een Azure SQL Database als een verouderde gegevens bron. U kunt vervolgens het OLE DB stuur programma voor SQL Server MSOLEDBSQL-gegevens provider selecteren. In dit geval kan het selecteren van een OLE DB gegevens provider betere prestaties bieden ten opzichte van de Power Query-connector. 

Wanneer u de wizard tabel importeren gebruikt in Visual Studio, is voor verbindingen met een gegevens bron een gegevens provider vereist. Er wordt een standaard gegevens provider voor u geselecteerd. U kunt de gegevens provider zo nodig wijzigen. Het type provider dat u kiest, kan afhankelijk zijn van de prestaties, ongeacht of het model gebruikmaakt van in-Memory opslag of DirectQuery, en op welke Analysis Services platform u uw model implementeert.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Gegevens bronnen van providers opgeven in tabellaire 1400 en hoger model projecten

Als u gegevens bronnen van providers wilt inschakelen, klikt u in Visual Studio op **extra**  >  **Opties**  >  **Analysis Services tabellaire**  >  **gegevens importeren**, selecteert u **verouderde gegevens bronnen inschakelen**.

![Verouderde gegevens bronnen inschakelen](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Als verouderde gegevens bronnen zijn ingeschakeld, klikt u in **tabellaire model Verkenner**met de rechter muisknop op **gegevens bronnen**  >  **importeren uit gegevens bron (verouderd)**.

![Verouderde gegevens bronnen in Tabellaire model Verkenner](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Net als bij tabellaire 1200 model projecten gebruikt u de **wizard tabel importeren** om verbinding te maken met een gegevens bron. Klik op de pagina verbinding maken op **Geavanceerd**. Geef de gegevens provider en andere Verbindings instellingen op in **Geavanceerde eigenschappen instellen**.

![Geavanceerde eigenschappen van verouderde gegevens bronnen](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>Imitatie
In sommige gevallen kan het nodig zijn om een ander imitatie account op te geven. Het imitatie account kan worden opgegeven in Visual Studio of SQL Server Management Studio (SSMS).

Voor on-premises gegevens bronnen:

* Als u SQL-verificatie gebruikt, moet imitatie een service account zijn.
* Als u Windows-verificatie gebruikt, stelt u Windows-gebruiker/-wacht woord in. Voor SQL Server wordt Windows-verificatie met een specifiek imitatie account alleen ondersteund voor gegevens modellen in het geheugen.

Voor gegevens bronnen in de Cloud:

* Als u SQL-verificatie gebruikt, moet imitatie een service account zijn.

## <a name="oauth-credentials"></a>OAuth-referenties

Voor modellen in tabel vorm met het compatibiliteits niveau 1400 en hoger met behulp van de modus in het geheugen, Azure SQL Database, Azure Synapse (voorheen SQL Data Warehouse), Dynamics 365 en share Point List ondersteunen OAuth-referenties. Azure Analysis Services beheert het vernieuwen van tokens voor OAuth-gegevens bronnen om time-outs voor langdurige vernieuwings bewerkingen te voor komen. Als u geldige tokens wilt genereren, stelt u referenties in met behulp van SSMS.

De direct query-modus wordt niet ondersteund met OAuth-referenties.

## <a name="next-steps"></a>Volgende stappen

* [On-premises gateway](analysis-services-gateway.md)
* [Uw server beheren](analysis-services-manage.md)
