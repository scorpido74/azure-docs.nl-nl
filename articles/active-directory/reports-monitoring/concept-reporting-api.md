---
title: Aan de slag met de Azure AD-rapportage-API | Microsoft Documenten
description: Aan de slag met de Azure Active Directory reporting API
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56f240a5191dd483f89889f3ffe13b1819ca1e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399326"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Aan de slag met de Azure Active Directory reporting API

Azure Active Directory biedt u verschillende [rapporten](overview-reports.md)met nuttige informatie voor toepassingen zoals SIEM-systemen, audit- en business intelligence-hulpprogramma's. 

Door de Microsoft Graph API voor Azure AD-rapporten te gebruiken, u programmatische toegang tot de gegevens krijgen via een set OP RUST gebaseerde API's. U kunt deze API's vanuit een groot aantal computertalen en hulpprogramma's aanroepen.

In dit artikel vindt u een overzicht van de rapportage-API, inclusief manieren om er toegang toe te krijgen.

Als u problemen ondervindt, raadpleegt u [hoe u ondersteuning krijgt voor Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

## <a name="prerequisites"></a>Vereisten

Als u toegang wilt krijgen tot de rapportage-API, met of zonder tussenkomst van de gebruiker, moet u:

1. Rollen toewijzen (beveiligingslezer, beveiligingsbeheerder, globale beheerder)
2. Een toepassing registreren
3. Machtigingen verlenen
4. Configuratie-instellingen verzamelen

Zie de vereisten [voor toegang tot de Azure Active Directory-rapportage-API](howto-configure-prerequisites-for-reporting-api.md)voor gedetailleerde instructies. 

## <a name="api-endpoints"></a>API-eindpunten 

Het eindpunt van de Microsoft `https://graph.microsoft.com/beta/auditLogs/directoryAudits` Graph API voor controlelogboeken is en `https://graph.microsoft.com/beta/auditLogs/signIns`het eindpunt van de Microsoft Graph API voor aanmeldingen is . Zie de referentie [van](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) de API voor controle en [api-verwijzing voor aanmelding](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn)voor meer informatie .

Daarnaast u de [API voor risicodetecties van identiteitsbescherming](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) gebruiken om programmatische toegang te krijgen tot beveiligingsdetecties met Microsoft Graph. Zie [Aan de slag met Azure Active Directory Identity Protection en Microsoft Graph](../identity-protection/graph-get-started.md)voor meer informatie. 
  
## <a name="apis-with-microsoft-graph-explorer"></a>API's met Microsoft Graph Explorer

U de [Microsoft Graph-verkenner](https://developer.microsoft.com/graph/graph-explorer) gebruiken om uw aanmeldings- en audit-API-gegevens te verifiëren. Meld u aan bij uw account met de knoppen voor aanmelding in de gebruikersinterface van Graph Explorer en stel **AuditLog.Read.All** en **Directory.Read.Alle** machtigingen voor uw tenant in zoals weergegeven.   

![Graph Explorer](./media/concept-reporting-api/graph-explorer.png)

![Gebruikersinterface voor machtigingen wijzigen](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Certificaten gebruiken om toegang te krijgen tot de Azure AD-rapportage-API 

Gebruik de Azure AD Reporting API met certificaten als u van plan bent rapportagegegevens op te halen zonder tussenkomst van de gebruiker.

Zie [Gegevens opvragen met de Azure AD-rapportage-API met certificaten](tutorial-access-api-with-certificates.md)voor gedetailleerde instructies.

## <a name="next-steps"></a>Volgende stappen

 * [Vereisten voor toegang tot rapportage-API](howto-configure-prerequisites-for-reporting-api.md) 
 * [Gegevens ophalen met de rapportage-API van Azure AD met certificaten](tutorial-access-api-with-certificates.md)
 * [Fouten in azure AD-rapportage-API oplossen](troubleshoot-graph-api.md)


