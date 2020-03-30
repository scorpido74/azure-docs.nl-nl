---
title: Opslag van identiteitsgegevens voor Europese klanten - Azure AD
description: Meer informatie over waar Azure Active Directory identiteitsgerelateerde gegevens opslaat voor zijn Europese klanten.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75423004"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Opslag van identiteitsgegevens voor Europese klanten in Azure Active Directory
Identiteitsgegevens worden door Azure AD opgeslagen op een geografische locatie op basis van het adres dat uw organisatie biedt wanneer u zich abonneert op een Microsoft Online-service zoals Office 365 en Azure. Voor informatie over waar uw identiteitsgegevens worden opgeslagen, u het gedeelte [Waar bevinden?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

Voor klanten die een adres in Europa hebben opgegeven, bewaart Azure AD de meeste identiteitsgegevens in Europese datacenters. Dit document bevat informatie over alle gegevens die buiten Europa worden opgeslagen door Azure AD-services.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure multi-factor authenticatie (MFA)
    
- Alle twee-factor authenticatie met behulp van telefoongesprekken of SMS zijn afkomstig uit Amerikaanse datacenters en worden ook gerouteerd door wereldwijde providers.
- Pushmeldingen met de Microsoft Authenticator-app zijn afkomstig uit Amerikaanse datacenters. Bovendien kunnen specifieke services van apparaatleveranciers ook een rol spelen en deze services misschien buiten Europa.
- OATH-codes worden altijd gevalideerd in de Verenigde Staten. 

Zie [Azure Multi-Factor Authentication user](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection)data collection voor meer informatie over welke gebruikersgegevens worden verzameld door Azure Multi-Factor Authentication Server (MFA Server) en in de cloud gebaseerde Azure MFA.

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C-beleidsconfiguratiegegevens en Sleutelcontainers worden opgeslagen in Amerikaanse datacenters. Deze bevatten geen persoonlijke gegevens van gebruikers. Zie voor meer informatie over configuraties voor beleid het artikel [Azure Active Directory B2C: ingebouwde beleidsregels](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B slaat uitnodigingen op met inwisselende koppeling en verwijst URL-informatie om in Amerikaanse datacenters. Daarnaast wordt het e-mailadres van gebruikers die zich afmelden voor het ontvangen van B2B-uitnodigingen ook opgeslagen in Amerikaanse datacenters.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure Active Directory Domain Services slaat gebruikersgegevens op dezelfde locatie op als het door de klant geselecteerde Azure Virtual Network. Dus als het netwerk zich buiten Europa bevindt, worden de gegevens gerepliceerd en opgeslagen buiten Europa.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federatie in Microsoft Exchange Server 2013
    
- Toepassings-id (AppID) - Een uniek nummer dat wordt gegenereerd door het Azure Active Directory-verificatiesysteem om Exchange-organisaties te identificeren.
- Lijst met goedgekeurde federatieve domeinen voor toepassing
- Openbare sleutel voor het ondertekenen van token van toepassing 

Zie het [Help-artikel Federation: Exchange 2013](https://docs.microsoft.com/exchange/federation-exchange-2013-help) voor meer informatie over federatie in Microsoft Exchange-server.


## <a name="other-considerations"></a>Andere overwegingen

Services en toepassingen die integreren met Azure AD hebben toegang tot identiteitsgegevens. Evalueer elke service en toepassing die u gebruikt om te bepalen hoe identiteitsgegevens worden verwerkt door die specifieke service en toepassing en of deze voldoen aan de vereisten voor gegevensopslag van uw bedrijf.

Zie de sectie [Waar bevinden uw gegevens zich?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) van het Microsoft Trust Center voor meer informatie over de gegevenslocatie van Microsoft-services.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over een van de hierboven beschreven functies en functionaliteit:
- [Wat is Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD selfservice wachtwoord opnieuw instellen](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Wat is Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Wat is Azure AD B2B-samenwerking?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
