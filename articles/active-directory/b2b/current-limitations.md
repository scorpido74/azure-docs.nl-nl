---
title: Beperkingen van B2B-samen werking-Azure Active Directory | Microsoft Docs
description: Huidige beperkingen voor Azure Active Directory B2B-samen werking
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: b45277c89193c51f70836bcef8a21636fc9c7973
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77196130"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Beperkingen van Azure AD B2B-samen werking
Azure Active Directory (Azure AD) B2B-samen werking is momenteel onderhevig aan de beperkingen die in dit artikel worden beschreven.

## <a name="possible-double-multi-factor-authentication"></a>Mogelijke dubbele multi-factor Authentication
Met Azure AD B2B kunt u multi-factor Authentication afdwingen voor de resource organisatie (de uitnodigende organisatie). De redenen voor deze benadering worden beschreven in [voorwaardelijke toegang voor B2B-samenwerkings gebruikers](conditional-access.md). Als een partner al multi-factor Authentication heeft ingesteld en afgedwongen, moeten hun gebruikers de verificatie mogelijk één keer uitvoeren in hun eigen organisatie en vervolgens opnieuw in uw bedrijf.

## <a name="instant-on"></a>Direct aan
In de B2B-samenwerkings stromen voegen we gebruikers toe aan de Directory en kunnen ze deze dynamisch bijwerken tijdens de inwisseling van de uitnodiging, de toewijzing van apps, enzovoort. De updates en schrijf bewerkingen worden gewoonlijk uitgevoerd in één directory-exemplaar en moeten worden gerepliceerd voor alle exemplaren. De replicatie is voltooid zodra alle exemplaren zijn bijgewerkt. Soms kunnen replicatie latenties optreden wanneer het object wordt geschreven of bijgewerkt in één exemplaar en de aanroep voor het ophalen van dit object naar een ander exemplaar. Als dat gebeurt, moet u vernieuwen of opnieuw proberen om te helpen. Als u een app met behulp van onze API schrijft, probeert u een goede, verdedigings praktijk om dit probleem op te lossen.

## <a name="azure-ad-directories"></a>Azure AD-directory's
Azure AD B2B is onderhevig aan Azure AD-service Directory limieten. Zie [Azure AD-service limieten en-beperkingen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)voor meer informatie over het aantal directory's dat een gebruiker kan maken en het aantal directory's waarmee een gebruiker of gast gebruiker kan behoren.

## <a name="national-clouds"></a>Nationale Clouds
[Nationale Clouds](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) zijn fysiek geïsoleerde exemplaren van Azure. B2B-samen werking wordt niet ondersteund in nationale Cloud grenzen. Als uw Azure-Tenant zich bijvoorbeeld in de open bare, globale Cloud bevindt, kunt u geen gebruiker uitnodigen waarvan het account zich in een nationale Cloud bevindt. Als u wilt samen werken met de gebruiker, vraagt u deze voor een ander e-mail adres of maakt u een gebruikers account voor de gebruiker in uw Directory.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over Azure AD B2B-samen werking:

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Uitnodigingen voor B2B-samen werking overdragen](delegate-invitations.md)

