---
title: Namen van Azure Analysis Services-aliasserver | Microsoft Documenten
description: Meer informatie over het maken van azure Analysis Services-servernaamaliassen. Gebruikers kunnen vervolgens verbinding maken met uw server met een kortere aliasnaam in plaats van de servernaam.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e7017fad90e32cb8c4b952987fe248e463e4d03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572282"
---
# <a name="alias-server-names"></a>Namen van aliasservers

Door een naamalias van de server te gebruiken, kunnen gebruikers verbinding maken met uw Azure Analysis Services-server met een kortere *alias* in plaats van de servernaam. Wanneer u verbinding maakt vanuit een clienttoepassing, wordt de alias opgegeven als eindpunt met behulp van de **link://** protocolnotatie. Het eindpunt retourneert vervolgens de echte servernaam om verbinding te maken.

Alias-servernamen zijn goed voor:

- Het migreren van modellen tussen servers zonder gebruikers. 
- Vriendelijke servernamen zijn gemakkelijker te onthouden voor gebruikers. 
- Direct gebruikers naar verschillende servers op verschillende tijdstippen van de dag. 
- Richt gebruikers in verschillende regio's naar instanties die geografisch dichter bij elkaar liggen, zoals bij het gebruik van Azure Traffic Manager. 

Elk HTTPS-eindpunt dat een geldige Azure Analysis Services-servernaam retourneert, kan als alias dienen. Het eindpunt moet HTTPS over poort 443 ondersteunen en de poort mag niet worden opgegeven in de URI.

![Alias met koppelingsindeling](media/analysis-services-alias/aas-alias-browser.png)

Wanneer u verbinding maakt vanuit een client, wordt de naam van de aliasserver ingevoerd met **behulp van link://** protocol-indeling. Bijvoorbeeld in Power BI Desktop:

![Power BI-bureaubladverbinding](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Een alias maken

Als u een aliaseindpunt wilt maken, u elke methode gebruiken die een geldige Azure Analysis Services-servernaam retourneert. Bijvoorbeeld een verwijzing naar een bestand in Azure Blob Storage met de echte servernaam of het maken en publiceren van een ASP.NET Web Forms-toepassing.

In dit voorbeeld wordt een ASP.NET Web Formulieren-toepassing gemaakt in Visual Studio. De stramienpaginaverwijzing en gebruikersbesturingselement worden verwijderd van de pagina Default.aspx. De inhoud van Default.aspx is gewoon de volgende paginarichtlijn:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

De Page_Load gebeurtenis in Default.aspx.cs gebruikt de methode Response.Write() om de naam van de Azure Analysis Services-server te retourneren.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Zie ook

[Clientbibliotheken](analysis-services-data-providers.md)   
[Verbinding maken vanuit Power BI Desktop](analysis-services-connect-pbi.md)
