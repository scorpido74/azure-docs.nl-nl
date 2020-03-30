---
title: Azure Analysis Services-clientbibliotheken | Microsoft Documenten
description: Beschrijft clientbibliotheken die nodig zijn voor clienttoepassingen en hulpprogramma's om Azure Analysis Services met elkaar te verbinden
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5914c7987d5a54a6bcc779231287309517f5121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129228"
---
# <a name="client-libraries-for-connecting-to-analysis-services"></a>Clientbibliotheken voor verbinding met Analysis Services

Clientbibliotheken zijn nodig voor clienttoepassingen en hulpprogramma's om verbinding te maken met Analysis Services-servers. Microsoft-clienttoepassingen zoals Power BI Desktop, Excel, SQL Server Management Studio (SSMS) en Analysis Services-projectenextensie voor Visual Studio installeren alle drie de clientbibliotheken en werken deze samen met regelmatige toepassingsupdates bij. In sommige gevallen moet u mogelijk nieuwere versies van de clientbibliotheken installeren. Aangepaste clienttoepassingen vereisen ook clientbibliotheken worden geïnstalleerd.

## <a name="download-the-latest-client-libraries-windows-installer"></a>De nieuwste clientbibliotheken downloaden (Windows Installer)  

|Download  |Productversie  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.29.25    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.29.25       |
|[Amo](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.4.8.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.4.8.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO en ADOMD (NuGet-pakketten)

Analysis Services Management Objects (AMO) en ADOMD-clientbibliotheken zijn beschikbaar als installeerbare pakketten vanaf [NuGet.org.](https://www.nuget.org/) Het wordt aanbevolen om te migreren naar NuGet-verwijzingen in plaats van Windows Installer te gebruiken. 

|Pakket  | Productversie  | 
|---------|---------|
|[Amo](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.4.8.0     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.4.8.0      |

NuGet pakketassemblages AssemblyVersion volgen semantische versies: MAJOR. Kleine. Patch. NuGet-verwijzingen laden de verwachte versie, zelfs als er een andere versie in de GAC (als gevolg van MSI-installatie). PATCH wordt verhoogd voor elke release. AMO- en ADOMD-versies worden gesynchroniseerd.

## <a name="understanding-client-libraries"></a>Inzicht in clientbibliotheken

Analysis Services maakt gebruik van drie clientbibliotheken, ook wel bekend als dataproviders. ADOMD.NET en Analysis Services Management Objects (AMO) worden beheerde clientbibliotheken. De Analysis Services OLE DB Provider (MSOLAP DLL) is een native clientbibliotheek. Meestal zijn alle drie tegelijkertijd geïnstalleerd. **Azure Analysis Services vereist de nieuwste versies van alle drie de bibliotheken.** 

Microsoft-clienttoepassingen zoals Power BI Desktop en Excel installeren alle drie de clientbibliotheken en werken deze bij wanneer er nieuwe versies beschikbaar zijn. Afhankelijk van de versie of frequentie van updates zijn sommige clientbibliotheken mogelijk niet de nieuwste versies die vereist zijn voor Azure Analysis Services. Dit geldt ook voor aangepaste toepassingen of andere interfaces, zoals AsCmd, TOM en ADOMD.NET. Deze toepassingen vereisen handmatig of programmatisch installeren van de bibliotheken. De clientbibliotheken voor handmatige installatie zijn opgenomen in SQL Server-functiepakketten als distribueerbare pakketten. Deze clientbibliotheken zijn echter gekoppeld aan de SQL Server-versie en zijn mogelijk niet de laatste.  

Clientbibliotheken voor clientverbindingen verschillen van gegevensproviders die verbinding moeten maken van een Azure Analysis Services-server naar een gegevensbron. Zie [Gegevensbronverbindingen](analysis-services-datasource.md)voor meer informatie over gegevensbronverbindingen.

## <a name="client-library-types"></a>Clientbibliotheektypen

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB Provider (MSOLAP) 

 Analysis Services OLE DB Provider (MSOLAP) is de native clientbibliotheek voor Analysis Services databaseverbindingen. Het wordt indirect gebruikt door zowel ADOMD.NET als AMO, waarbij verbindingsaanvragen worden delegeren aan de gegevensprovider. U de OLE DB Provider ook rechtstreeks vanuit de toepassingscode bellen.  
  
 De ANALYSIS Services OLE DB Provider wordt automatisch geïnstalleerd door de meeste tools en clienttoepassingen die worden gebruikt om toegang te krijgen tot Analysis Services-databases. Het moet worden geïnstalleerd op computers die worden gebruikt om toegang te krijgen tot analysis services-gegevens.  
  
 OLE DB-providers worden vaak opgegeven in verbindingstekenreeksen. Een verbindingstekenreeks van Analysis Services gebruikt een andere nomenclatuur om te verwijzen naar de OLE DB Provider: MSOLAP. \<versie>.dll.

### <a name="amo"></a>Amo  

 AMO is een beheerde clientbibliotheek die wordt gebruikt voor serverbeheer en gegevensdefinitie. Het is geïnstalleerd en gebruikt door tools en clienttoepassingen. SQL Server Management Studio (SSMS) maakt bijvoorbeeld gebruik van AMO om verbinding te maken met Analysis Services. Een verbinding met AMO is meestal `"data source=\<servername>"`minimaal, bestaande uit . Nadat een verbinding is gemaakt, gebruikt u de API om te werken met databaseverzamelingen en belangrijke objecten. Zowel Visual Studio als SSMS gebruiken AMO om verbinding te maken met een instantie Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET is een beheerde gegevensclientbibliotheek die wordt gebruikt voor het opvragen van Analysis Services-gegevens. Het is geïnstalleerd en gebruikt door tools en clienttoepassingen. 
  
 Wanneer u verbinding maakt met een database, zijn de eigenschappen van de verbindingstekenreeks voor alle drie de bibliotheken vergelijkbaar. Bijna elke verbindingstekenreeks die u definieert voor ADOMD.NET met behulp van [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) werkt ook voor AMO en de Analysis Services OLE DB Provider (MSOLAP). Zie [Eigenschappen van verbindingstekenreeksen &#40;Analysis Services&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services)voor meer informatie.  

  
##  <a name="how-to-determine-client-library-version"></a><a name="bkmk_LibUpdate"></a>De versie van de clientbibliotheek bepalen   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Ga naar `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Als u meer dan één map hebt, kiest u het hogere getal.
  
2.  Klik met de rechtermuisknop op**de details** **van msolap.dll** > **Eigenschappen** > . Als de bestandsnaam msolap140.dll is, is deze ouder dan de nieuwste versie en moet deze worden geüpgraded.
    
    ![Details van de clientbibliotheek](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>Amo

1. Ga naar `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Als u meer dan één map hebt, kiest u het hogere getal.
2. Klik met de rechtermuisknop op**de eigenschappengegevens****van** >  **Microsoft.AnalysisServices** > .  

### <a name="adomd"></a>ADOMD

1. Ga naar `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Als u meer dan één map hebt, kiest u het hogere getal.
2. Klik met de rechtermuisknop op**de detailgegevens****van** >  **Microsoft.AnalysisServices.AdomdClient** > Eigenschappen .  


## <a name="next-steps"></a>Volgende stappen
[Verbinding maken met Excel](analysis-services-connect-excel.md)    
[Verbinden met Power BI](analysis-services-connect-pbi.md)
