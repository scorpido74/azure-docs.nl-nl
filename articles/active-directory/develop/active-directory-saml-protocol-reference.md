---
title: Hoe Azure AD het SAML-protocol gebruikt | Microsoft Documenten
description: In dit artikel vindt u een overzicht van de SAML-profielen voor één aanmelding en één afmelding in Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: dc7771f29fb5d00aedfe5162a98f5f0c14544a7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161168"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Hoe Azure AD het SAML-protocol gebruikt

Azure Active Directory (Azure AD) gebruikt het SAML 2.0-protocol om toepassingen in staat te stellen hun gebruikers één aanmeldingservaring te bieden. In [de SAML-profielen](single-sign-on-saml-protocol.md) voor eenmalig aanmelden en [eenmalig aanmelden](single-sign-out-saml-protocol.md) van Azure AD wordt uitgelegd hoe SAML-beweringen, -protocollen en -bindingen worden gebruikt in de service voor identiteitsprovider.

SAML-protocol vereist dat de identiteitsprovider (Azure AD) en de serviceprovider (de toepassing) informatie over zichzelf uitwisselen.

Wanneer een toepassing is geregistreerd bij Azure AD, registreert de app-ontwikkelaar federatiegerelateerde informatie met Azure AD. Deze informatie omvat de **Redirect URI** en **Metadata URI** van de toepassing.

Azure AD gebruikt de **metagegevens URI van** de cloudservice om de ondertekeningssleutel en de afmeld-URI op te halen. De klant kan de app openen in **Azure AD -> App-registratie** en vervolgens in **Instellingen -> Eigenschappen**kunnen ze de URL van afmelden bijwerken. Op deze manier kan Azure AD het antwoord naar de juiste URL verzenden. 

Azure Active Directory legt tenantspecifieke en algemene (tenantonafhankelijke) single sign-on- en single sign-out eindpunten bloot. Deze URL's vertegenwoordigen adresseerbare locaties - ze zijn niet alleen id's - zodat u naar het eindpunt gaan om de metagegevens te lezen.

* Het tenant-specifieke eindpunt bevindt zich op `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. De * \<TenantDomainName>* tijdelijke aanduiding vertegenwoordigt een geregistreerde domeinnaam of TenantID GUID van een Azure AD-tenant. De federatiemetagegevens van de contoso.com tenant zijn bijvoorbeeld op:https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Het tenant-onafhankelijke eindpunt bevindt zich op `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. In dit eindpuntadres **wordt algemeen gesproken** weergegeven in plaats van een tenantdomeinnaam of -id.

Zie [Federatiemetagegevens](../azuread-dev/azure-ad-federation-metadata.md)voor informatie over de metagegevensdocumenten van de federatie die Azure AD publiceert.
