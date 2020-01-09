---
title: Identiteits gegevens opslag voor Europese klanten-Azure AD
description: Meer informatie over waar identiteit gerelateerde gegevens in Azure Active Directory worden opgeslagen voor de Europese klanten.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7360b11f41cc08c2beb3ffa227e1658881798502
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423004"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Identiteit gegevensopslag voor Europese klanten in Azure Active Directory
Identiteits gegevens worden opgeslagen door Azure AD op een geografische locatie op basis van het adres van uw organisatie bij het abonneren op een micro soft online service, zoals Office 365 en Azure. Voor informatie over waar uw identiteits gegevens worden opgeslagen, kunt u de sectie [waar bevinden zich uw gegevens?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) in het micro soft vertrouwens centrum gebruiken.

Voor klanten die een adres in Europa hebben geleverd, houdt Azure AD de meeste identiteits gegevens binnen de Europese data centers. Dit document bevat informatie over alle gegevens die buiten Europa door Azure AD Services zijn opgeslagen.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Multi-factor Authentication (MFA) Microsoft Azure
    
- Alle twee ledige verificatie met behulp van telefoon gesprekken of SMS afkomstig van Amerikaanse data centers en worden ook gerouteerd door wereld wijde providers.
- Push meldingen met behulp van de Microsoft Authenticator-app, afkomstig van Amerikaanse data centers. Daarnaast kunnen specifieke services van apparaten ook worden afgespeeld en deze services zijn mogelijk buiten Europa.
- OATH-codes worden altijd gevalideerd in de Verenigde Staten. 

Voor meer informatie over welke gebruikers gegevens worden verzameld door Azure Multi-Factor Authentication-server (MFA-server) en Azure MFA in de Cloud, Zie [azure multi-factor Authentication gebruikers gegevens verzamelen](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C-beleids configuratie gegevens en sleutel containers worden opgeslagen in de data centers van de VS. Deze bevatten geen persoonlijke gegevens van de gebruiker. Zie voor meer informatie over configuraties voor beleid het artikel [Azure Active Directory B2C: ingebouwde beleidsregels](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B slaat uitnodigingen met Verwissel-en omleidings-URL-informatie op in de VS-data centers. Daarnaast worden e-mail adressen van gebruikers die zich afmelden voor het ontvangen van B2B-uitnodigingen ook opgeslagen in de data centers in de VS.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure Active Directory Domain Services slaat gebruikersgegevens op dezelfde locatie op als het door de klant geselecteerde Azure Virtual Network. Dus als het netwerk zich buiten Europa bevindt, worden de gegevens gerepliceerd en opgeslagen buiten Europa.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federatie in micro soft Exchange Server 2013
    
- Toepassings-id (AppID): een uniek nummer dat wordt gegenereerd door het Azure Active Directory-verificatie systeem om Exchange-organisaties te identificeren.
- Lijst met goedgekeurde federatieve domeinen voor toepassing
- Open bare sleutel voor token ondertekening van toepassing 

Zie het artikel [Federation: Exchange 2013 Help](https://docs.microsoft.com/exchange/federation-exchange-2013-help) voor meer informatie over Federatie in micro soft Exchange Server.


## <a name="other-considerations"></a>Andere overwegingen

Services en toepassingen die met Azure AD worden geïntegreerd, hebben toegang tot identiteits gegevens. Evalueer elke service en toepassing die u gebruikt om te bepalen hoe identiteits gegevens worden verwerkt door die specifieke service en toepassing en of ze voldoen aan de vereisten voor gegevens opslag van uw bedrijf.

Zie de sectie [Waar bevinden uw gegevens zich?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) van het Microsoft Trust Center voor meer informatie over de gegevenslocatie van Microsoft-services.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over de functies en functionaliteit die hierboven worden beschreven:
- [Wat is Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure Active Directory Self-Service Password Reset](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Wat is Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Wat is Azure AD B2B-samenwerking?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
