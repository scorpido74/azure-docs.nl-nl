---
title: 'Azure AD Connect: doorgeefverificatie - Huidige beperkingen | Microsoft Documenten'
description: In dit artikel worden de huidige beperkingen van Azure Active Directory (Azure AD) Pass-through Authentication beschreven
services: active-directory
keywords: Azure AD Connect Pass-Through Authentication, installeer Active Directory, vereiste onderdelen voor Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97dc67d46b08bf5765c59806b45edd82f38720cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347733"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory Pass-Through-verificatie: huidige beperkingen

>[!IMPORTANT]
>Azure Active Directory (Azure AD) Pass-through Authentication is een gratis functie en u hebt geen betaalde edities van Azure AD nodig om deze te gebruiken. Pass-through Authentication is alleen beschikbaar in het wereldwijde exemplaar van Azure AD en niet in de [Microsoft Azure Germany cloud](https://www.microsoft.de/cloud-deutschland) of de Microsoft Azure Government [cloud.](https://azure.microsoft.com/features/gov/)

## <a name="supported-scenarios"></a>Ondersteunde scenario's

De volgende scenario's worden ondersteund:

- Aanmeldingen van gebruikers bij browsergebaseerde toepassingen.
- Aanmeldingen van gebruikers bij Outlook-clients met oudere protocollen zoals Exchange ActiveSync, EAS, SMTP, POP en IMAP.
- Aanmeldingen van gebruikers voor verouderde Office-clienttoepassingen en Office-toepassingen die [moderne verificatie](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview)ondersteunen: Office 2013- en 2016-versies.
- Aanmeldingen van gebruikers voor oudere protocoltoepassingen zoals PowerShell-versie 1.0 en anderen.
- Azure AD wordt lid voor Windows 10-apparaten.
- App-wachtwoorden voor multi-factorauthenticatie.

## <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

De volgende scenario's worden _niet_ ondersteund:

- Detectie van gebruikers met [gelekte referenties.](../reports-monitoring/concept-risk-events.md#leaked-credentials)
- Azure AD Domain Services heeft wachtwoordhashsynchronisatie nodig om op de tenant te worden ingeschakeld. Daarom werken tenants die Pass-through Authentication gebruiken _alleen_ niet voor scenario's waarvoor Azure AD Domain Services nodig zijn.
- Pass-through Authentication is niet geïntegreerd met [Azure AD Connect Health](whatis-hybrid-identity-health.md).

> [!IMPORTANT]
> Als tijdelijke oplossing voor _alleen_ niet-ondersteunde scenario's (behalve Azure AD Connect-statusintegratie) schakelt u Wachtwoordhashsynchronisatie in op de pagina [Optionele functies](how-to-connect-install-custom.md#optional-features) in de wizard Azure AD Connect.
> 
> [!NOTE]
> Met wachtwoordhashsynchronisatie u verificatie via failover inschakelen als uw on-premises infrastructuur is verstoord. Deze failover van Pass-through Authentication naar Password Hash Synchronization is niet automatisch. U moet de aanmeldingsmethode handmatig schakelen met Azure AD Connect. Als de server met Azure AD Connect uitvalt, hebt u hulp van Microsoft Support nodig om Pass-through Authentication uit te schakelen.

## <a name="next-steps"></a>Volgende stappen
- [Snel aan de slag:](how-to-connect-pta-quick-start.md)aan de slag met Azure AD Pass-through Authentication.
- [Migreren van AD FS naar Pass-through Authentication](https://aka.ms/ADFSTOPTADPDownload) - Een gedetailleerde handleiding om te migreren van AD FS (of andere federatietechnologieën) naar Pass-through Authentication.
- [Smart Lockout:](../authentication/howto-password-smart-lockout.md)meer informatie over het configureren van de Smart Lockout-mogelijkheid op uw tenant om gebruikersaccounts te beschermen.
- [Technische diepe duik:](how-to-connect-pta-how-it-works.md)Begrijp hoe de Pass-through Authentication-functie werkt.
- [Veelgestelde vragen](how-to-connect-pta-faq.md): Vind antwoorden op veelgestelde vragen over de functie Pass-through Authentication.
- [Problemen oplossen:](tshoot-connect-pass-through-authentication.md)meer informatie over het oplossen van veelvoorkomende problemen met de functie Doorgeefverificatie.
- [Security deep dive](how-to-connect-pta-security-deep-dive.md): Krijg diepgaande technische informatie over de Pass-through Authentication-functie.
- [Azure AD Seamless SSO:](how-to-connect-sso.md)Meer informatie over deze aanvullende functie.
- [UserVoice:](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)gebruik het Azure Active Directory Forum om nieuwe functieaanvragen in te dienen.
