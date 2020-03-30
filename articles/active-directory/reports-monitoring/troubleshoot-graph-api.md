---
title: Fouten in Azure Active Directory-rapportage-API oplossen | Microsoft Documenten
description: Biedt u een oplossing voor fouten tijdens het aanroepen van Azure Active Directory Reporting API's.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d1fb4f49e4f9ad41f971d869873200e6180b5cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399284"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Fouten in Azure Active Directory-rapportage-API oplossen

In dit artikel worden de veelvoorkomende foutberichten weergegeven die u tegenkomen terwijl u toegang krijgt tot activiteitsrapporten met behulp van de Microsoft Graph-API en stappen voor de oplossing ervan.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP-fout in interne server tijdens toegang tot Microsoft Graph V2-eindpunt

We ondersteunen momenteel het Microsoft Graph v2-eindpunt niet - zorg ervoor dat u toegang krijgt tot de activiteitslogboeken met behulp van het Microsoft Graph v1-eindpunt.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Fout: Geen van beide huurder is B2C of huurder heeft geen premium licentie

Voor het openen van aanmeldingsrapporten is een Azure Active Directory premium 1 -licentie (P1) vereist. Als u dit foutbericht ziet terwijl u aanmeldingen hebt geopend, controleert u of uw tenant een licentie heeft met een Azure AD P1-licentie.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Fout: Gebruiker zit niet in de toegestane rollen 

Als u dit foutbericht ziet terwijl u controlelogboeken of aanmeldingen met de API probeert te openen, controleert u of uw account deel uitmaakt van de rol **Beveiligingslezer** of **Rapportlezer** in uw Azure Active Directory-tenant. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fout: Toepassing ontbreekt AAD 'Lees directory gegevens' toestemming 

Volg de stappen in de [vereisten om toegang te krijgen tot de Azure Active Directory-rapportage-API](howto-configure-prerequisites-for-reporting-api.md) om ervoor te zorgen dat uw toepassing wordt uitgevoerd met de juiste set machtigingen. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Fout: Toepassing ontbreekt Microsoft Graph API 'Lees alle audit log gegevens' toestemming

Volg de stappen in de [vereisten om toegang te krijgen tot de Azure Active Directory-rapportage-API](howto-configure-prerequisites-for-reporting-api.md) om ervoor te zorgen dat uw toepassing wordt uitgevoerd met de juiste set machtigingen. 

## <a name="next-steps"></a>Volgende stappen

[De api-verwijzing voor controle gebruiken](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[Gebruik de API-verwijzing voor aanmeldingsactiviteitsrapport](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
