---
title: Id-providers voor externe identiteiten-Azure AD
description: Azure Active Directory B2B-samen werking ondersteunt multi-factor Authentication (MFA) voor selectieve toegang tot uw bedrijfs toepassingen
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395473e70137ead2b7185d54d165f82e9b3f1f04
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598148"
---
# <a name="identity-providers-for-external-identities"></a>Id-providers voor externe identiteiten

Een *id-provider* maakt, onderhoudt en beheert id-gegevens en biedt tegelijkertijd verificatieservices voor toepassingen. Wanneer u uw apps en resources deelt met externe gebruikers, is Azure AD de standaard id-provider voor delen. Dit betekent dat wanneer u externe gebruikers uitnodigt die al over een Azure AD of Microsoft-account beschikken, ze automatisch kunnen aanmelden zonder verdere configuratie van uw onderdeel.

U kunt gebruikers echter in staat stellen zich aan te melden met verschillende id-providers. U kunt bijvoorbeeld Federatie instellen met sociale id-providers die worden ondersteund door Azure AD, waaronder Google en Facebook. U kunt ook communiceren met een externe ID-provider die ondersteuning biedt voor de SAML-of WS-voeder-protocollen. Met externe ID-provider Federatie kunt u externe gebruikers de mogelijkheid bieden om zich aan te melden bij uw apps met hun bestaande sociale of zakelijke accounts.

## <a name="how-it-works"></a>Hoe het werkt

Externe Azure AD-identiteiten is vooraf geconfigureerd voor Federatie met Google en Facebook. Als u deze id-providers in uw Azure AD-Tenant wilt instellen, maakt u een toepassing bij elke id-provider en configureert u de referenties. U ontvangt een client-of App-ID en een client-of app-geheim, dat u vervolgens kunt toevoegen aan uw Azure AD-Tenant.

Zodra u een id-provider aan uw Azure AD-Tenant hebt toegevoegd:

- Wanneer u een externe gebruiker uitnodigt voor apps of resources in uw organisatie, kan de externe gebruiker zich aanmelden met een eigen account bij die id-provider.
- Wanneer u [Aanmelden via selfservice](self-service-sign-up-overview.md) voor uw apps inschakelt, kunnen externe gebruikers zich aanmelden voor uw apps met hun eigen accounts met de id-providers die u hebt toegevoegd. 

Wanneer u uw uitnodiging ingewisselt of zich aanmeldt voor uw app, heeft de externe gebruiker de mogelijkheid zich aan te melden en te verifiëren met de ID-provider voor sociale netwerken:

![Scherm afbeelding van het aanmeldings scherm met Google-en Facebook-opties](media/identity-providers/sign-in-with-social-identity.png)

Voor een optimale aanmeldings ervaring kunt u waar mogelijk met id-providers communiceren, zodat u uw uitgenodigde gasten een naadloze aanmeldings ervaring krijgt wanneer ze toegang hebben tot uw apps.  

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het toevoegen van id-providers voor aanmelding bij uw toepassingen:

- [Google toevoegen](google-federation.md) aan uw lijst met sociale id-providers
- [Facebook toevoegen](facebook-federation.md) aan uw lijst met sociale id-providers
- [Stel directe Federatie](direct-federation.md) in met een wille keurige organisatie waarvan de ID-provider ondersteuning biedt voor het SAML 2,0-of WS-voeder protocol. Houd er rekening mee dat directe Federatie geen optie is voor Self-service registratie gebruikers stromen.
