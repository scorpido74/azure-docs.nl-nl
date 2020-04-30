---
title: Azure Analysis Services-client bibliotheken | Microsoft Docs
description: Beschrijft de vereiste client bibliotheken voor client toepassingen en hulpprogram ma's om verbinding te maken Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5ee3de593e7795f155e8ca38ffac02cfaa80d171
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232952"
---
# <a name="client-libraries-for-connecting-to-analysis-services"></a>Client bibliotheken voor het maken van verbinding met Analysis Services

Er zijn client bibliotheken nodig voor client toepassingen en hulpprogram ma's om verbinding te maken met Analysis Services-servers. Micro soft-client toepassingen als Power BI Desktop, Excel, SQL Server Management Studio (SSMS) en Analysis Services projects-extensie voor Visual Studio installeren alle drie client bibliotheken en werken ze samen met reguliere toepassings updates. In sommige gevallen moet u mogelijk nieuwere versies van de client bibliotheken installeren. Voor aangepaste client toepassingen moeten ook client bibliotheken zijn geïnstalleerd.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Down load de nieuwste client bibliotheken (Windows Installer)  

|Download  |Productversie  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.37.40    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.37.40       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.7.0.1    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.7.0.1     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO en ADOMD (NuGet-pakketten)

Analysis Services-beheer objecten (AMO) en ADOMD-client bibliotheken zijn beschikbaar als Installeer bare pakketten van [NuGet.org](https://www.nuget.org/). Het is raadzaam om te migreren naar NuGet-verwijzingen in plaats van Windows Installer te gebruiken. 

|Pakket  | Productversie  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.7.0.1     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.7.0.1      |

NuGet-pakket-assemblies AssemblyVersion volgen van semantische versie: primair. Secundair. Verzenden. NuGet verwijst naar de verwachte versie, zelfs als er sprake is van een andere versie in de GAC (als gevolg van MSI-installatie). De PATCH wordt voor elke release verhoogd. AMO-en ADOMD-versies worden in-sync bewaard.

## <a name="understanding-client-libraries"></a>Informatie over client bibliotheken

Analysis Services gebruik drie client Bibliotheken, ook wel bekend als gegevens providers. ADOMD.NET en Analysis Services Management Objects (AMO) worden beheerde client bibliotheken. De Analysis Services OLE DB-provider (MSOLAP-DLL) is een systeem eigen client bibliotheek. Normaal gesp roken worden alle drie tegelijk geïnstalleerd. **Azure Analysis Services vereist de nieuwste versies van alle drie de bibliotheken**. 

Micro soft-client toepassingen zoals Power BI Desktop en Excel installeren alle drie client bibliotheken en werken ze bij wanneer er nieuwe versies beschikbaar zijn. Afhankelijk van de versie of de frequentie van updates, zijn sommige client bibliotheken mogelijk niet de nieuwste versie die wordt vereist door Azure Analysis Services. Dit geldt ook voor aangepaste toepassingen of andere interfaces, zoals AsCmd, TOM en ADOMD.NET. Voor deze toepassingen moet de bibliotheken hand matig of programmatisch worden geïnstalleerd. De client bibliotheken voor hand matige installatie zijn opgenomen in SQL Server Feature Packs als distribueer bare pakketten. Deze client bibliotheken zijn echter gekoppeld aan de SQL Server-versie en zijn mogelijk niet het meest recent.  

Client bibliotheken voor client verbindingen verschillen van gegevens providers die vereist zijn om verbinding te maken vanaf een Azure Analysis Services server met een gegevens bron. Zie [gegevens bron verbindingen](analysis-services-datasource.md)voor meer informatie over gegevens bron verbindingen.

## <a name="client-library-types"></a>Typen client bibliotheken

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB-provider (MSOLAP) 

 Analysis Services OLE DB-provider (MSOLAP) is de systeem eigen client bibliotheek voor Analysis Services database verbindingen. Het wordt indirect gebruikt door zowel ADOMD.NET als AMO, waarbij verbindings aanvragen worden gedelegeerd aan de gegevens provider. U kunt de OLE DB provider ook rechtstreeks vanuit toepassings code aanroepen.  
  
 De Analysis Services OLE DB-provider wordt automatisch geïnstalleerd door de meeste hulpprogram ma's en client toepassingen die worden gebruikt voor toegang tot Analysis Services-data bases. Het moet worden geïnstalleerd op computers die worden gebruikt om toegang te krijgen tot Analysis Services gegevens.  
  
 OLE DB providers worden vaak opgegeven in verbindings reeksen. Een Analysis Services connection string een andere nomenclatuur gebruikt om te verwijzen naar de OLE DB provider: MSOLAP. \<versie>. dll.

### <a name="amo"></a>AMO  

 AMO is een beheerde client bibliotheek die wordt gebruikt voor Server beheer en gegevens definitie. Het wordt geïnstalleerd en gebruikt door hulpprogram ma's en client toepassingen. SQL Server Management Studio (SSMS) gebruikt bijvoorbeeld AMO om verbinding te maken met Analysis Services. Een verbinding met AMO is doorgaans mini maal, bestaande uit `"data source=\<servername>"`. Nadat een verbinding tot stand is gebracht, gebruikt u de API om met database verzamelingen en hoofd objecten te werken. Zowel Visual Studio als SSMS gebruiken AMO om verbinding te maken met een Analysis Services-exemplaar.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET is een beheerde gegevens-client bibliotheek die wordt gebruikt voor het opvragen van Analysis Services gegevens. Het wordt geïnstalleerd en gebruikt door hulpprogram ma's en client toepassingen. 
  
 Wanneer u verbinding maakt met een Data Base, zijn de connection string eigenschappen voor de drie bibliotheken vergelijkbaar. Bijna alle connection string die u definieert voor ADOMD.NET met behulp van [micro soft. AnalysisServices. AdomdClient. AdomdConnection. Connections Tring](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) , werkt ook voor AMO en de ANALYSIS Services OLE DB-provider (MSOLAP). Zie Eigenschappen van de [verbindings reeks &#40;Analysis Services&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services)voor meer informatie.  

  
##  <a name="how-to-determine-client-library-version"></a><a name="bkmk_LibUpdate"></a>De versie van de client bibliotheek bepalen   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Ga naar `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Als u meer dan één map hebt, kiest u het hogere nummer.
  
2.  Klik met de rechter muisknop op **Msolap. dll** > -**Eigenschappen** > **Details**. Als de bestands naam msolap140. dll is, is deze ouder dan de meest recente versie en moet een upgrade worden uitgevoerd.
    
    ![Details van de client bibliotheek](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Ga naar `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Als u meer dan één map hebt, kiest u het hogere nummer.
2. Klik met de rechter muisknop op**Eigenschappen** > **Details**van **micro soft. AnalysisServices** > .  

### <a name="adomd"></a>ADOMD

1. Ga naar `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Als u meer dan één map hebt, kiest u het hogere nummer.
2. Klik met de rechter muisknop op**Eigenschappen** > **Details**van **micro soft. AnalysisServices. AdomdClient** > .  


## <a name="next-steps"></a>Volgende stappen
[Verbinding maken met Excel](analysis-services-connect-excel.md)    
[Verbinden met Power BI](analysis-services-connect-pbi.md)
