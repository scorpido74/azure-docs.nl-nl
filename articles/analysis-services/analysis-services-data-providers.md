---
title: Client bibliotheken die nodig zijn om verbinding te maken met Azure Analysis Services | Microsoft Docs
description: Beschrijft de vereiste client bibliotheken voor client toepassingen en hulpprogram ma's om verbinding te maken Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1ea14f141ed4fa698a21da8030878b04a81f1d08
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298657"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Client bibliotheken voor het maken van verbinding met Azure Analysis Services

Er zijn client bibliotheken nodig voor client toepassingen en hulpprogram ma's om verbinding te maken met Analysis Services-servers. Micro soft-client toepassingen zoals Power BI Desktop, Excel, SQL Server Management Studio (SSMS) en SQL Server Data Tools (SSDT) installeren alle drie client bibliotheken en werken ze samen met reguliere toepassings updates. In sommige gevallen moet u mogelijk nieuwere versies van de client bibliotheken installeren. Voor aangepaste client toepassingen moeten ook client bibliotheken zijn geïnstalleerd.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Down load de nieuwste client bibliotheken (Windows Installer)  

|Downloaden  |Product versie  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.5.15    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.5.15       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.0.9.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.0.9.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO en ADOMD (NuGet-pakketten)

Analysis Services-beheer objecten (AMO) en ADOMD-client bibliotheken zijn beschikbaar als Installeer bare pakketten van [NuGet.org](https://www.nuget.org/). Het is raadzaam om te migreren naar NuGet-verwijzingen in plaats van Windows Installer te gebruiken. 

|Pakket  | Product versie  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.0.9     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.0.9      |

NuGet-pakket-assemblies AssemblyVersion volgen van semantische versie: primair. Secundair. Verzenden. NuGet verwijst naar de verwachte versie, zelfs als er sprake is van een andere versie in de GAC (als gevolg van MSI-installatie). De PATCH wordt voor elke release verhoogd. AMO-en ADOMD-versies worden in-sync bewaard.

## <a name="understanding-client-libraries"></a>Informatie over client bibliotheken

Analysis Services gebruik drie client Bibliotheken, ook wel bekend als gegevens providers. ADOMD.NET en Analysis Services Management Objects (AMO) worden beheerde client bibliotheken. De Analysis Services OLE DB-provider (MSOLAP-DLL) is een systeem eigen client bibliotheek. Normaal gesp roken worden alle drie tegelijk geïnstalleerd. **Azure Analysis Services vereist de nieuwste versies van alle drie de bibliotheken**. 

Micro soft-client toepassingen zoals Power BI Desktop en Excel installeren alle drie client bibliotheken en werken ze bij wanneer er nieuwe versies beschikbaar zijn. Afhankelijk van de versie of de frequentie van updates, zijn sommige client bibliotheken mogelijk niet de nieuwste versie die wordt vereist door Azure Analysis Services. Dit geldt ook voor aangepaste toepassingen of andere interfaces, zoals AsCmd, TOM en ADOMD.NET. Voor deze toepassingen moet de bibliotheken hand matig of programmatisch worden geïnstalleerd. De client bibliotheken voor hand matige installatie zijn opgenomen in SQL Server Feature Packs als distribueer bare pakketten. Deze client bibliotheken zijn echter gekoppeld aan de SQL Server-versie en zijn mogelijk niet het meest recent.  

Client bibliotheken voor client verbindingen verschillen van gegevens providers die vereist zijn om verbinding te maken vanaf een Azure Analysis Services server met een gegevens bron. Zie [Data Source Connections](analysis-services-datasource.md)voor meer informatie over data source Connections.

## <a name="client-library-types"></a>Typen client bibliotheken

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB-provider (MSOLAP) 

 Analysis Services OLE DB-provider (MSOLAP) is de systeem eigen client bibliotheek voor Analysis Services database verbindingen. Het wordt indirect gebruikt door zowel ADOMD.NET als AMO, waarbij verbindings aanvragen worden gedelegeerd aan de gegevens provider. U kunt de OLE DB provider ook rechtstreeks vanuit toepassings code aanroepen.  
  
 De Analysis Services OLE DB-provider wordt automatisch geïnstalleerd door de meeste hulpprogram ma's en client toepassingen die worden gebruikt voor toegang tot Analysis Services-data bases. Het moet worden geïnstalleerd op computers die worden gebruikt om toegang te krijgen tot Analysis Services gegevens.  
  
 OLE DB providers worden vaak opgegeven in verbindings reeksen. Een Analysis Services connection string een andere nomenclatuur gebruikt om te verwijzen naar de OLE DB provider: MSOLAP. \<version >. dll.

### <a name="amo"></a>AMO  

 AMO is een beheerde client bibliotheek die wordt gebruikt voor Server beheer en gegevens definitie. Het wordt geïnstalleerd en gebruikt door hulpprogram ma's en client toepassingen. SQL Server Management Studio (SSMS) gebruikt bijvoorbeeld AMO om verbinding te maken met Analysis Services. Een verbinding met AMO is doorgaans mini maal, bestaande uit `"data source=\<servername>"`. Nadat een verbinding tot stand is gebracht, gebruikt u de API om met database verzamelingen en hoofd objecten te werken. Zowel SSDT als SSMS gebruiken AMO om verbinding te maken met een Analysis Services-exemplaar.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET is een beheerde gegevens-client bibliotheek die wordt gebruikt voor het opvragen van Analysis Services gegevens. Het wordt geïnstalleerd en gebruikt door hulpprogram ma's en client toepassingen. 
  
 Wanneer u verbinding maakt met een Data Base, zijn de connection string eigenschappen voor de drie bibliotheken vergelijkbaar. Bijna alle connection string die u definieert voor ADOMD.NET met behulp van [micro soft. AnalysisServices. AdomdClient. AdomdConnection. Connections Tring](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) , werkt ook voor AMO en de ANALYSIS Services OLE DB-provider (MSOLAP). Zie [eigenschappen &#40;van verbindings reeks Analysis Services&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services)voor meer informatie.  

  
##  <a name="bkmk_LibUpdate"></a>De versie van de client bibliotheek bepalen   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Ga naar `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Als u meer dan één map hebt, kiest u het hogere nummer.
  
2.  Klik met de rechter muisknop op **Msolap. dll** > **Eigenschappen** > -**gegevens**. Als de bestands naam msolap140. dll is, is deze ouder dan de meest recente versie en moet een upgrade worden uitgevoerd.
    
    ![Details van de client bibliotheek](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Ga naar `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Als u meer dan één map hebt, kiest u het hogere nummer.
2. Klik met de rechter muisknop op **micro soft. AnalysisServices** > **Eigenschappen** > **Details**.  

### <a name="adomd"></a>ADOMD

1. Ga naar `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Als u meer dan één map hebt, kiest u het hogere nummer.
2. Klik met de rechter muisknop op **micro soft. AnalysisServices. AdomdClient** > **Eigenschappen** > **Details**.  


## <a name="next-steps"></a>Volgende stappen
[Verbinding maken met Excel](analysis-services-connect-excel.md)-    
[Verbinden met Power BI](analysis-services-connect-pbi.md)
