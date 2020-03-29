---
title: Multitenant-app toevoegen aan Azure AD-toepassingsgalerie | Microsoft Documenten
description: Hier wordt uitgelegd hoe u uw op maat gemaakte multitenant-toepassing aanbieden in de azure AD-toepassingsgalerie.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.openlocfilehash: be660ad42c1336d479f1793b20d2994682db1225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702756"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Een multitenant-toepassing toevoegen aan de Azure AD-toepassingsgalerie

## <a name="what-is-the-azure-ad-application-gallery"></a>Wat is de Azure AD-toepassingsgalerie?

Azure Active Directory (Azure AD) is een cloudgebaseerde identiteitsservice. De [azure AD-toepassingsgalerie](https://azure.microsoft.com/marketplace/active-directory/all/) bevindt zich in de Azure Marketplace-appstore, waar alle toepassingsconnectoren worden gepubliceerd voor eenmalige aanmelding en gebruikersinrichting. Klanten die Azure AD gebruiken als identiteitsprovider, vinden de verschillende SaaS-toepassingsconnectors die hier worden gepubliceerd. IT-beheerders voegen connectors toe vanuit de app-galerie en configureren en gebruiken de connectors voor éénaanmelding en inrichten. Azure AD ondersteunt alle belangrijke federatieprotocollen, waaronder SAML 2.0, OpenID Connect, OAuth en WS-Fed voor eenmalige aanmelding. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Als uw toepassing SAML of OpenIDConnect ondersteunt
Als u een multitenant-toepassing hebt die u wilt aanbieden in de Azure AD-toepassingsgalerie, moet u er eerst voor zorgen dat uw toepassing een van de volgende toepassingstechnologieën ondersteunt:

- **OpenID Connect**: Als u uw app wilt laten aanbieden, maakt u de multitenant-toepassing in Azure AD en implementeert u het [Azure AD-toestemmingskader](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) voor uw toepassing. Stuur het aanmeldingsverzoek naar een gemeenschappelijk eindpunt, zodat elke klant toestemming kan geven voor de toepassing. U de toegang van een gebruiker beheren op basis van de tenant-id en de UPN van de gebruiker die in het token is ontvangen. Verzend de toepassing met behulp van het proces dat is beschreven in [Het aanbieden van uw toepassing in de Azure Active Directory-toepassingsgalerie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML:** Als uw toepassing SAML 2.0 ondersteunt, kan de app worden weergegeven in de galerie. Volg de instructies in [Het aanbieden van uw toepassing in de Azure Active Directory-toepassingsgalerie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Als uw toepassing geen SAML of OpenIDConnect ondersteunt
Toepassingen die SAML of OpenIDConnect niet ondersteunen, kunnen nog steeds worden geïntegreerd in de app-galerij via een wachtwoordvoormeldingstechnologie.

Met eenmalige aanmelding voor wachtwoorden, ook wel wachtwoordvaulting genoemd, u gebruikerstoegang en wachtwoorden beheren voor webtoepassingen die identiteitsfederatie niet ondersteunen. Het is ook handig voor scenario's waarin meerdere gebruikers één account moeten delen, zoals de sociale media-app-accounts van uw organisatie. 

Als u uw toepassing met deze technologie wilt aanbieden:
1. Maak een webtoepassing met een HTML-aanmeldingspagina om [een enkele aanmelding voor wachtwoorden](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)te configureren. 
2. Dien de aanvraag in zoals beschreven in [Aanbieding van uw toepassing in de Azure Active Directory-toepassingsgalerie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Escalaties

Voor eventuele escalaties stuurt u e-mail naar [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) en nemen we zo snel mogelijk een back-to-mail met u terug.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [aanbieden van uw toepassing in de Azure Active Directory-toepassingsgalerie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
