---
title: Gegevens bronnen die worden ondersteund in Azure Analysis Services | Microsoft Docs
description: Beschrijft gegevens bronnen die worden ondersteund voor gegevens modellen in Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 81fc73ffd61a49eae1c4f107733b6f9f53efbb4f
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993391"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Gegevens bronnen die worden ondersteund in Azure Analysis Services

Gegevens bronnen en connectors die worden weer gegeven in de wizard gegevens ophalen of importeren in Visual Studio worden weer gegeven voor zowel Azure Analysis Services als SQL Server Analysis Services. Niet alle weer gegeven gegevens bronnen en connectors worden echter ondersteund in Azure Analysis Services. De typen gegevens bronnen waarmee u verbinding kunt maken, zijn afhankelijk van veel factoren, zoals model compatibiliteits niveau, beschik bare gegevens connectors, verificatie type, providers en on-premises gegevens gateway ondersteuning. 

## <a name="azure-data-sources"></a>Azure-gegevensbronnen

|Gegevensbron  |In het geheugen  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database<sup>[2](#azsqlmanaged)</sup>     |   Ja      |    Ja      |
|Azure SQL Data Warehouse     |   Ja      |   Ja       |
|Azure Blob Storage<sup>[1](#tab1400a)</sup>     |   Ja       |    Nee      |
|Azure Table Storage<sup>[1](#tab1400a)</sup>    |   Ja       |    Nee      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Ja        |  Nee        |
|Azure Data Lake Store (gen1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   Ja       |    Nee      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     Ja     |   Nee       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   Ja       |   Nee       |
||||

<a name="tab1400a">1</a> : alleen modellen in tabel vorm 1400 en hoger.   
<a name="azsqlmanaged">2</a> -Azure SQL database beheerde instantie wordt ondersteund. Omdat beheerde exemplaren worden uitgevoerd in azure VNet met een privé-IP-adres, moet het open bare eind punt zijn ingeschakeld voor het exemplaar. Als deze niet is ingeschakeld, is een on-premises gegevens gateway vereist.    
<a name="databricks">3</a> -Azure Databricks het gebruik van de Spark-connector wordt momenteel niet ondersteund.   
<a name="gen2">4</a> -ADLS Gen2 wordt momenteel niet ondersteund.


**Provider**   
In-Memory-en DirectQuery-modellen die verbinding maken met Azure-gegevens bronnen, wordt .NET Framework gegevens provider gebruikt voor SQL Server.

## <a name="other-data-sources"></a>Andere gegevens bronnen

Voor het maken van verbinding met on-premises gegevens bronnen van en Azure als server is een on-premises gateway vereist. Wanneer u een gateway gebruikt, zijn 64-bits-providers vereist.

### <a name="in-memory-and-directquery"></a>In-Memory en DirectQuery

|Gegevensbron | In-Memory provider | DirectQuery-provider |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11,0, micro soft OLE DB provider voor SQL Server, .NET Framework gegevens provider voor SQL Server | .NET Framework gegevens provider voor SQL Server |
| SQL Server datawarehouse |SQL Server Native Client 11,0, micro soft OLE DB provider voor SQL Server, .NET Framework gegevens provider voor SQL Server | .NET Framework gegevens provider voor SQL Server |
| Oracle | OLE DB provider voor Oracle, Oracle-gegevens provider voor .NET |Oracle-gegevens provider voor .NET |
| Teradata |OLE DB provider voor Teradata, Teradata-gegevens provider voor .NET |Teradata-gegevens provider voor .NET |
| | | |

### <a name="in-memory-only"></a>Alleen in het geheugen

|Gegevensbron  |  
|---------|
|Access-Data Base     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Analytics platform systeem     |  
|CSV-bestand  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Excel-werkmap     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Map<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (bèta) |
|JSON-document<sup>[1](#tab1400b)</sup>     |  
|Regels van binair<sup>[1](#tab1400b)</sup>     | 
|MySQL-database     | 
|OData-feed<sup>[1](#tab1400b)</sup>     |  
|ODBC-query     | 
|OLE DB     |   
|PostgreSQL-Data Base<sup>[1](#tab1400b)</sup>    | 
|Sales Force-objecten<sup>[1](#tab1400b)</sup> |  
|Sales Force-rapporten<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|Share point-lijst<sup>[1](#tab1400b)</sup>, <sup>[2](#filesSP)</sup>     |   
|Sybase-database     |  
|TXT-bestand  |
|XML-tabel<sup>[1](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a> : alleen modellen in tabel vorm 1400 en hoger.   
<a name="filesSP">2</a> -bestanden in on-premises share point worden niet ondersteund.

## <a name="specifying-a-different-provider"></a>Een andere provider opgeven

Gegevens modellen in Azure Analysis Services kunnen verschillende gegevens providers vereisen bij het verbinden met bepaalde gegevens bronnen. In sommige gevallen wordt er een fout geretourneerd door tabellaire modellen die verbinding maken met gegevens bronnen die gebruikmaken van systeem eigen providers, zoals SQL Server Native Client (SQLNCLI11). Als u een andere systeem eigen provider dan SQLOLEDB gebruikt, ziet u mogelijk het volgende fout bericht: **De provider SQLNCLI/client is niet geregistreerd**. Als u een DirectQuery-model hebt om verbinding te maken met on-premises gegevens bronnen en u systeem eigen providers gebruikt, ziet u mogelijk het volgende fout bericht: **Fout bij het maken van OLE DB Row set. De syntaxis bij ' LIMIT '** is onjuist.

Bij het migreren van een on-premises SQL Server Analysis Services tabellaire model naar Azure Analysis Services, kan het nodig zijn om de provider te wijzigen.

**Een provider opgeven**

1. Klik in SSDT > **tabellaire model Verkenner** > -**gegevens bronnen**met de rechter muisknop op een gegevens bron verbinding en klik vervolgens op **gegevens bron bewerken**.
2. Klik in **verbinding bewerken**op **Geavanceerd** om het venster Geavanceerde eigenschappen te openen.
3. Selecteer bij**providers** **Geavanceerde eigenschappen** > instellen de juiste provider.

## <a name="impersonation"></a>Imitatie
In sommige gevallen kan het nodig zijn om een ander imitatie account op te geven. Het imitatie account kan worden opgegeven in Visual Studio (SSDT) of SSMS.

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

