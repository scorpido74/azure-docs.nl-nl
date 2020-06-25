---
title: 'Azure AD Connect synchronisatie: Prullenbak van AD inschakelen | Microsoft Docs'
description: In dit onderwerp wordt het gebruik van de functie voor de Prullenbak van AD met Azure AD Connect aanbevolen.
services: active-directory
keywords: Prullenbak van AD, onbedoeld verwijderen, bron anker
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed71e53a8cedc2907ac06dd75f11f9c762a78772
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85357202"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect synchronisatie: Prullenbak van AD inschakelen
Het is raadzaam om de functie AD recycle bin in te scha kelen voor uw on-premises Active Directory, die zijn gesynchroniseerd met Azure AD. 

Als u per ongeluk een on-premises AD-gebruikers object hebt verwijderd en dit herstelt met behulp van de functie, herstelt Azure AD het bijbehorende Azure AD-gebruikers object.  Raadpleeg het artikel [scenario-overzicht voor het herstellen van verwijderde Active Directory objecten](https://technet.microsoft.com/library/dd379542.aspx)voor meer informatie over de PRULLENBAK van AD.

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Voor delen van het inschakelen van de Prullenbak van AD
Deze functie helpt bij het herstellen van Azure AD-gebruikers objecten door het volgende te doen:

* Als u per ongeluk een on-premises AD-gebruikers object hebt verwijderd, wordt het bijbehorende Azure AD-gebruikers object verwijderd in de volgende synchronisatie cyclus. Azure AD houdt het verwijderde Azure AD-gebruikers object standaard gedurende 30 dagen in de modus voor zacht verwijderen.

* Als u on-premises AD-Prullenbak hebt ingeschakeld, kunt u het verwijderde on-premises AD-gebruikers object herstellen zonder de bron anker waarde te wijzigen. Wanneer het herstelde on-premises AD-gebruikers object is gesynchroniseerd met Azure AD, herstelt Azure AD het bijbehorende tijdelijke, verwijderde Azure AD-gebruikers object. Raadpleeg artikel [Azure AD Connect: ontwerp concepten](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor)voor meer informatie over het kenmerk bron anker.

* Als er geen on-premises AD-Prullenbak functie is ingeschakeld, moet u mogelijk een AD-gebruikers object maken om het verwijderde object te vervangen. Als Azure AD Connect synchronisatie service is geconfigureerd voor het gebruik van door het systeem gegenereerd AD-kenmerk (zoals ObjectGuid) voor het kenmerk bron anker, heeft het zojuist gemaakte AD-gebruikers object niet dezelfde bron anker waarde als het verwijderde AD-gebruikers object. Wanneer het zojuist gemaakte AD-gebruikers object is gesynchroniseerd met Azure AD, maakt Azure AD een nieuw Azure AD-gebruikers object in plaats van het voorlopig verwijderde Azure AD-gebruikers object te herstellen.

> [!NOTE]
> Azure AD houdt standaard een verwijderde Azure AD-gebruikers objecten in de modus voor zacht verwijderen gedurende 30 dagen voordat ze permanent worden verwijderd. Beheerders kunnen de verwijdering van dergelijke objecten echter versnellen. Zodra de objecten permanent zijn verwijderd, kunnen ze niet meer worden hersteld, zelfs als de on-premises AD-Prullenbak functie is ingeschakeld.

## <a name="next-steps"></a>Volgende stappen
**Overzichts onderwerpen**

* [Azure AD Connect synchronisatie: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)

* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
