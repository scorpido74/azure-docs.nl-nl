---
title: Verbinding maken met Azure Analysis Services-servers| Microsoft Documenten
description: Meer informatie over het maken van verbinding met en het ophalen van gegevens van een Analysis Services-server in Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cc671629934d80b3e727ca69f9026f534d05e160
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676652"
---
# <a name="connecting-to-servers"></a>Verbinding maken met servers

In dit artikel wordt beschreven dat u verbinding maakt met een server met behulp van gegevensmodellerings- en beheertoepassingen zoals SQL Server Management Studio (SSMS) of Visual Studio met Analysis Services-projecten, of met clientrapportagetoepassingen zoals Microsoft Excel, Power BI Desktop of aangepaste toepassingen. Verbindingen met Azure Analysis Services gebruiken HTTPS.

## <a name="client-libraries"></a>Clientbibliotheken

[De nieuwste clientbibliotheken opzoeken](analysis-services-data-providers.md)

Alle verbindingen met een server, ongeacht het type, vereisen bijgewerkte AMO-, ADOMD.NET- en OLEDB-clientbibliotheken om verbinding te maken met en te communiceren met een Analysis Services-server. Voor SSMS, Visual Studio, Excel 2016 en hoger en Power BI worden de nieuwste clientbibliotheken geïnstalleerd of bijgewerkt met maandelijkse releases. In sommige gevallen is het echter mogelijk dat een toepassing niet de laatste heeft. Wanneer het beleid updates vertraagt of Office 365-updates zich bijvoorbeeld op het uitgestelde kanaal bevinden.

> [!NOTE]
> De clientbibliotheken kunnen geen verbinding maken met Azure Analysis Services via proxyservers waarvoor een gebruikersnaam en wachtwoord vereist zijn. 

## <a name="server-name"></a>Servernaam

Wanneer u een Analysis Services-server maakt in Azure, geeft u een unieke naam op en het gebied waar de server moet worden gemaakt. Wanneer u de servernaam in een verbinding opgeeft, is het servernaamgevingsschema:

```
<protocol>://<region>/<servername>
```
 Waar protocol string **asazure**is , is het gebied de Uri waar de server is gemaakt (bijvoorbeeld westus.asazure.windows.net) en is servernaam de naam van uw unieke server binnen de regio.

### <a name="get-the-server-name"></a>De servernaam oppakken

Kopieer in **Azure-portal** > server > de naam **van de Overzichtsserver** > **Server name**de volledige servernaam. Als andere gebruikers in uw organisatie ook verbinding maken met deze server, u deze servernaam met hen delen. Bij het opgeven van een servernaam moet het hele pad worden gebruikt.

![Servernaam bepalen in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Het protocol voor Oost-VS 2 regio is **aspaaseastus2**.

## <a name="connection-string"></a>Verbindingsreeks

Wanneer u verbinding maakt met Azure Analysis Services met het tabelmodel voor object, gebruikt u de volgende verbindingstekenreeksindelingen:

###### <a name="integrated-azure-active-directory-authentication"></a>Geïntegreerde Azure Active Directory-verificatie

Geïntegreerde verificatie pikt de Azure Active Directory-referentiecache op indien beschikbaar. Als dit niet het niet lukt, wordt het Azure-aanmeldingsvenster weergegeven.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Azure Active Directory-verificatie met gebruikersnaam en wachtwoord

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows-verificatie (geïntegreerde beveiliging)

Gebruik het Windows-account waarop het huidige proces wordt uitgevoerd.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Verbinding maken met een .odc-bestand

Bij oudere versies van Excel kunnen gebruikers verbinding maken met een Azure Analysis Services-server met behulp van een Office Data Connection-bestand (.odc). Zie [Een Bestand voor Office-gegevensverbinding (.odc) maken](analysis-services-odc.md)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

[Verbinding maken met Excel](analysis-services-connect-excel.md)    
[Verbinding maken met Power BI](analysis-services-connect-pbi.md)   
[Uw server beheren](analysis-services-manage.md)   

