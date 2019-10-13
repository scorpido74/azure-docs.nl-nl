---
title: Verbinding maken met Azure Analysis Services servers | Microsoft Docs
description: Meer informatie over hoe u verbinding kunt maken met en gegevens kunt ophalen van een Analysis Services-server in Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a8059ac748f73ad8f9036f8e675e876e3a8716be
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72295185"
---
# <a name="connecting-to-servers"></a>Verbinding maken met servers

In dit artikel wordt beschreven hoe u verbinding maakt met een server met behulp van gegevens modellering en beheer toepassingen als SQL Server Management Studio (SSMS) of SQL Server Data Tools (SSDT). Of, met client rapportage toepassingen zoals micro soft Excel, Power BI Desktop of aangepaste toepassingen. Verbindingen met Azure Analysis Services HTTPS gebruiken.

## <a name="client-libraries"></a>Clientbibliotheken

[De nieuwste client bibliotheken ophalen](analysis-services-data-providers.md)

Alle verbindingen met een server, ongeacht het type, vereisen bijgewerkte AMO-, ADOMD.NET-en OLEDB-client bibliotheken om verbinding te maken met en een interface met een Analysis Services-server. Voor SSMS, SSDT, Excel 2016 en hoger, en Power BI, worden de nieuwste client bibliotheken geïnstalleerd of bijgewerkt met maandelijkse releases. In sommige gevallen is het echter mogelijk dat een toepassing niet de meest recente heeft. Bijvoorbeeld wanneer beleids regels updates vertragen of Office 365-updates op het uitgestelde kanaal staan.

## <a name="server-name"></a>Servernaam

Wanneer u een Analysis Services-server maakt in azure, geeft u een unieke naam en de regio op waar de server moet worden gemaakt. Wanneer u de server naam in een verbinding opgeeft, is het server naamgevings schema:

```
<protocol>://<region>/<servername>
```
 Waarbij het protocol de teken reeks **asazure**is, is de regio de URI waarin de server is gemaakt (bijvoorbeeld westus.asazure.Windows.net) en servername de naam is van uw unieke server in de regio.

### <a name="get-the-server-name"></a>De server naam ophalen

Kopieer de volledige server naam in **Azure Portal** > Server > **overzicht** > **Server naam**. Als andere gebruikers in uw organisatie ook verbinding maken met deze server, kunt u deze naam met de server delen. Wanneer u een server naam opgeeft, moet het volledige pad worden gebruikt.

![Servernaam bepalen in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Het protocol voor de regio VS Oost 2 is **aspaaseastus2**.

## <a name="connection-string"></a>Verbindingsreeks

Wanneer u verbinding maakt met Azure Analysis Services met behulp van het object model in tabel vorm, gebruikt u de volgende connection string indelingen:

###### <a name="integrated-azure-active-directory-authentication"></a>Geïntegreerde Azure Active Directory verificatie

Geïntegreerde verificatie haalt de Azure Active Directory referentie cache op, indien beschikbaar. Als dat niet het geval is, wordt het venster Azure-aanmelding weer gegeven.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Verificatie Azure Active Directory met gebruikers naam en wacht woord

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows-verificatie (geïntegreerde beveiliging)

Gebruik het Windows-account dat het huidige proces uitvoert.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Verbinding maken via een. ODC-bestand

Met oudere versies van Excel kunnen gebruikers verbinding maken met een Azure Analysis Services server met behulp van een ODC-bestand (Office Data Connection). Zie [een ODC-bestand (Office Data Connection) maken](analysis-services-odc.md)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

[Verbinding maken met Excel](analysis-services-connect-excel.md)-    
[Verbinding maken met Power BI](analysis-services-connect-pbi.md)   
[Uw server beheren](analysis-services-manage.md)   

