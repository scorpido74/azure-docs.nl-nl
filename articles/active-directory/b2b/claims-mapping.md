---
title: B2B-samenwerking gebruikersclaimtoewijzing - Azure Active Directory
description: Pas de gebruikersclaims aan die zijn uitgegeven in het SAML-token voor Azure Active Directory (Azure AD) B2B-gebruikers.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508865d9f89501cf70e09087c6a609beb6662b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273188"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Toewijzing van gebruikersclaims voor B2B-samenwerking in Azure Active Directory

Azure Active Directory (Azure AD) ondersteunt het aanpassen van de claims die zijn uitgegeven in het SAML-token voor B2B-samenwerkingsgebruikers. Wanneer een gebruiker zich verifieert naar de toepassing, geeft Azure AD een SAML-token uit aan de app die informatie (of claims) bevat over de gebruiker die deze op unieke wijze identificeert. Dit omvat standaard de gebruikersnaam, het e-mailadres, de voornaam en de achternaam van de gebruiker.

In de [Azure-portal](https://portal.azure.com)u de claims bekijken of bewerken die in het SAML-token naar de toepassing worden verzonden. Als u toegang wilt krijgen tot de instellingen, selecteert u **Azure Active Directory** > **Enterprise-toepassingen** > de toepassing die is geconfigureerd voor éénmalige aanmelding > **Enkele aanmelding**. Zie de SAML-tokeninstellingen in de sectie **Gebruikerskenmerken.**

![Toont de SAML-tokenkenmerken in de gebruikersinterface](media/claims-mapping/view-claims-in-saml-token.png)

Er zijn twee mogelijke redenen waarom u de claims die zijn uitgegeven in het SAML-token moet bewerken:

1. De toepassing vereist een andere set claim-URI's of claimwaarden.

2. De toepassing vereist dat de Claim NameIdentifier iets anders is dan de user principal name (UPN) die is opgeslagen in Azure AD.

Zie Claims aanpassen die zijn uitgegeven [in het SAML-token voor bedrijfstoepassingen in Azure Active Directory voor](../develop/active-directory-saml-claims-customization.md)informatie over het toevoegen en bewerken van claims.

Voor Gebruikers van B2B-samenwerking worden het in kaart brengen van NameID en UPN-cross-tenant om veiligheidsredenen voorkomen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Eigenschappen van een Azure Active Directory B2B-samenwerkingsgebruiker](user-properties.md)voor informatie over gebruikerseigenschappen van B2B-samenwerking.
- Zie [Gebruikerstokens begrijpen in Azure AD B2B-samenwerking](user-token.md)voor informatie over gebruikerstokens voor B2B-samenwerkingsverbanden .

