---
title: Hoe micro soft Identity platform het SAML-protocol gebruikt
description: Dit artikel bevat een overzicht van de SAML-profielen eenmalige aanmelding en eenmalige afmelding in Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 06f80f94be25e42c9e8f0270e6cb15aca086ae18
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552795"
---
# <a name="how-microsoft-identity-platform-uses-the-saml-protocol"></a>Hoe micro soft Identity platform het SAML-protocol gebruikt

Micro soft Identity platform gebruikt het SAML 2,0-protocol om toepassingen in staat te stellen een eenmalige aanmelding voor hun gebruikers te bieden. Met de SAML-profielen eenmalige [aanmelding](single-sign-on-saml-protocol.md) en [eenmalige afmelding](single-sign-out-saml-protocol.md) van Azure AD wordt uitgelegd hoe SAML-beweringen,-protocollen en-bindingen worden gebruikt in de id-Provider service.

Voor het SAML-protocol zijn de ID-provider (micro soft Identity platform) en de service provider (de toepassing) vereist voor het uitwisselen van informatie over zichzelf.

Wanneer een toepassing is geregistreerd bij Azure AD, registreert de app-ontwikkelaar aan de Federatie gerelateerde informatie met Azure AD. Deze informatie omvat de **omleidings-URI** en de **meta gegevens-URI** van de toepassing.

Micro soft Identity platform gebruikt de **meta gegevens-URI** van de Cloud service om de handtekening sleutel en de afmeldings-URI op te halen. De klant kan de app openen in **Azure AD-> app-registratie** en vervolgens in **instellingen-> eigenschappen**, kunnen ze de afmeldings-URL bijwerken. Op deze manier kan micro soft Identity platform het antwoord naar de juiste URL verzenden. 

Azure Active Directory stelt Tenant-specifieke en algemene (Tenant onafhankelijke) eenmalige aanmelding en eind punten voor eenmalige afmeldingen in. Deze Url's vertegenwoordigen adresseer bare locaties: ze zijn niet alleen id's, dus u kunt naar het eind punt gaan om de meta gegevens te lezen.

* Het Tenant-specifieke eind punt bevindt zich op `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` . De *\<TenantDomainName>* tijdelijke aanduiding vertegenwoordigt een geregistreerde domein naam of TenantID-GUID van een Azure AD-Tenant. De federatieve meta gegevens van de contoso.com-Tenant bevindt zich bijvoorbeeld op:https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Het Tenant-onafhankelijke eind punt bevindt zich op `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml` . In dit adres van het eind punt wordt **common** weer gegeven in plaats van de domein naam of-id van een Tenant.

Zie [federatieve meta gegevens](../azuread-dev/azure-ad-federation-metadata.md)voor meer informatie over de federatieve meta gegevens documenten die door Azure AD worden gepubliceerd.
