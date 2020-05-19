---
title: Aanmelden via selfservice voor externe identiteiten-Azure AD
description: Meer informatie over het toestaan van externe gebruikers om zich aan te melden voor uw toepassingen zelf door aanmelding via selfservice in te scha kelen. Maak een persoonlijke aanmeldings ervaring door de self-service voor het registreren van gebruikers aan te passen.
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
ms.openlocfilehash: 0e0325b43b6726f04d5994b60404f218ac58122d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597532"
---
# <a name="self-service-sign-up-preview"></a>Aanmelden via self-service (preview-versie)
|     |
| --- |
| Aanmelden via self-service is een open bare preview-functie van Azure Active Directory. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over Previews.|
|     |

Wanneer u toepassingen deelt met externe gebruikers, weet u mogelijk niet altijd vooraf wie toegang tot een toepassing nodig heeft. Als alternatief voor het rechtstreeks verzenden van uitnodigingen naar personen, kunt u externe gebruikers toestaan zich aan te melden voor specifieke toepassingen zelf door het aanmelden via een self-service in te scha kelen. U kunt een persoonlijke aanmeldings ervaring maken door de self-service voor het registreren van gebruikers aan te passen. U kunt bijvoorbeeld opties bieden voor Azure AD-of Social id-providers en informatie verzamelen over de gebruiker.

## <a name="user-flow-for-self-service-sign-up"></a>Gebruikers stroom voor Self-service registratie

Met een self-service voor het aanmelden van gebruikers wordt een aanmeldings ervaring gemaakt voor uw externe gebruikers via de toepassing die u wilt delen. De gebruikers stroom kan worden gekoppeld aan een of meer van uw toepassingen. Eerst schakelt u de Self-Service-aanmelding voor uw Tenant in en gaat u met alle id-providers die u wilt toestaan dat externe gebruikers gebruik kunnen maken van de aanmelding. Vervolgens maakt u de gebruikers stroom voor registratie en past u deze aan en wijst u uw toepassingen hieraan toe.
U kunt instellingen voor de gebruikers stroom configureren om te bepalen hoe de gebruiker zich aanmeldt voor de toepassing:

- Account typen die worden gebruikt voor aanmelding, zoals sociale accounts zoals Facebook of Azure AD-accounts
- Kenmerken die moeten worden verzameld van de gebruiker die zich aanmeldt, zoals de voor naam, de post code of het land van locatie

Wanneer een gebruiker zich wil aanmelden bij uw toepassing, of het nu gaat om een web-, mobiel-, desktop-of single-page-toepassing (SPA), initieert de toepassing een autorisatie aanvraag naar het eind punt van de gebruikers stroom. De gebruikers stroom definieert en beheert de gebruikers ervaring. Wanneer een registratie gebruikers stroom is voltooid, genereert Azure AD een token en leidt de gebruiker vervolgens terug naar uw toepassing. Meerdere toepassingen kunnen dezelfde gebruikers stroom gebruiken.

## <a name="example-of-self-service-sign-up"></a>Voor beeld van een self-service registratie

In het volgende voor beeld ziet u hoe u met de self-service registratie mogelijkheden voor gast gebruikers uw sociale id-providers naar Azure AD brengt.  
Een partner van Woodgrove opent de Woodgrove-app. Ze bepalen dat ze zich willen registreren voor een leveranciers account, zodat ze het account van uw leverancier aanvragen selecteren, waarmee de self-service-registratie stroom wordt gestart.

![Voor beeld van start pagina voor aanmelden via self-service](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

Ze gebruiken het e-mail adres van hun keuze om zich aan te melden.

![Voor beeld van het weer geven van een selectie van Facebook voor aanmelden](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Azure AD maakt een relatie met Woodgrove met behulp van het Facebook-account van de partner en maakt een nieuw account.

Woodgrove wil meer weten over de gebruiker, zoals de naam, de bedrijfs naam, de code van de zakelijke registratie, het telefoon nummer.

![Voor beeld van aanmeldings kenmerken van gebruikers](media/self-service-sign-up-overview/example-enter-user-attributes.png)

De gebruiker voert de informatie in, zet de registratie stroom voort en krijgt toegang tot de resources die ze nodig hebben.

![Voor beeld van de gebruiker die is aangemeld](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>Volgende stappen

 Zie [Self-Service-aanmelding toevoegen aan een app](self-service-sign-up-user-flow.md)voor meer informatie.