---
title: 'Synchronisatie van Azure AD Connect: PRULLENBAK VAN AD inschakelen | Microsoft Documenten'
description: In dit onderwerp wordt aanbevolen de functie AD Prullenbak te gebruiken met Azure AD Connect.
services: active-directory
keywords: AD-prullenbak, per ongeluk verwijderen, bronanker
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe7d3ea7d4f6d648438efc1a484d5909ade2f23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60382892"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Synchronisatie van Azure AD Connect: PRULLENBAK AD inschakelen
Het wordt aanbevolen om de functie AD-prullenbak in te schakelen voor uw on-premises actieve mappen, die zijn gesynchroniseerd met Azure AD. 

Als u per ongeluk een on-premises AD-gebruikersobject hebt verwijderd en deze met de functie hebt hersteld, herstelt Azure AD het bijbehorende Azure AD-gebruikersobject.  Raadpleeg artikel [Scenariooverzicht voor het herstellen van verwijderde Active Directory-objecten voor](https://technet.microsoft.com/library/dd379542.aspx)informatie over de functie AD-prullenbak .

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Voordelen van het inschakelen van de PRULLENBAK van het AD
Deze functie helpt bij het herstellen van Azure AD-gebruikersobjecten door het volgende te doen:

* Als u per ongeluk een on-premises AD-gebruikersobject hebt verwijderd, wordt het bijbehorende Azure AD-gebruikersobject in de volgende synchronisatiecyclus verwijderd. Azure AD houdt het verwijderde Azure AD-gebruikersobject standaard gedurende 30 dagen in zachte status.

* Als u de functie AD-prullenbak op locatie hebt ingeschakeld, u het verwijderde on-premises AD-gebruikersobject herstellen zonder de waarde bronanker te wijzigen. Wanneer het herstelde on-premises AD-gebruikersobject wordt gesynchroniseerd met Azure AD, herstelt Azure AD het bijbehorende, verwijderde Azure AD-gebruikersobject. Raadpleeg artikel [Azure AD Connect: Ontwerpconcepten](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor)voor informatie over het kenmerk Bronanker .

* Als u de functie AD-prullenbak niet hebt ingeschakeld, moet u mogelijk een AD-gebruikersobject maken om het verwijderde object te vervangen. Als Azure AD Connect Synchronization Service is geconfigureerd om door het systeem gegenereerde AD-kenmerk (zoals ObjectGuid) te gebruiken voor het kenmerk Bronanker, heeft het nieuw gemaakte AD-gebruikersobject niet dezelfde waarde voor bronanker als het verwijderde AD-gebruikersobject. Wanneer het nieuw gemaakte AD-gebruikersobject is gesynchroniseerd met Azure AD, maakt Azure AD een nieuw Azure AD-gebruikersobject in plaats van het door zachte verwijderde Azure AD-gebruikersobject te herstellen.

> [!NOTE]
> Azure AD bewaart standaard verwijderde Azure AD-gebruikersobjecten gedurende 30 dagen in zachte verwijderde status voordat ze permanent worden verwijderd. Beheerders kunnen het verwijderen van dergelijke objecten echter versnellen. Zodra de objecten permanent zijn verwijderd, kunnen ze niet meer worden hersteld, zelfs als de functie AD-prullenbak op locatie is ingeschakeld.

## <a name="next-steps"></a>Volgende stappen
**Overzichtsonderwerpen**

* [Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)

* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
