---
title: Meer informatie over Azure AD Connect 1.4. xx. x en apparaat weer geven | Microsoft Docs
description: In dit document wordt een probleem beschreven dat zich voordoet bij versie 1.4. xx. x van Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 1ba17feea880bb55c3b4a14a06b3d803dba2350a
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316952"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Meer informatie over Azure AD Connect 1.4. xx. x en het weer geven van apparaten
Met versie 1.4. xx. x van Azure AD Connect kunnen sommige klanten enkele of alle Windows-apparaten zien, verdwijnen van Azure AD. Dit is geen oorzaak van bezorgdheid, omdat deze apparaat-id's niet worden gebruikt door Azure AD tijdens de autorisatie van voorwaardelijke toegang. Met deze wijziging worden geen Windows-apparaten verwijderd die correct zijn geregistreerd bij Azure AD voor hybride Azure AD-deelname.

Als u ziet dat de verwijdering van object-objecten in azure AD de drempel waarde voor het verwijderen van het exporteren overschrijdt, wordt u geadviseerd dat de klant de verwijderingen kan door lopen. [Procedure: verwijderingen naar flow toestaan wanneer deze de drempel waarde voor verwijderen overschrijden](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Achtergrond
Windows-apparaten die zijn geregistreerd als Hybrid Azure AD join, worden weer gegeven in azure AD als objecten voor apparaten. Deze apparaatobject kunnen worden gebruikt voor voorwaardelijke toegang. Windows 10-apparaten worden gesynchroniseerd met de Cloud via Azure AD Connect, Windows-apparaten met een lagere versie worden rechtstreeks geregistreerd met behulp van AD FS of naadloze eenmalige aanmelding.

## <a name="windows-10-devices"></a>Windows 10-apparaten
Alleen Windows 10-apparaten met een specifieke userCertificate-kenmerk waarde die is geconfigureerd met hybride Azure AD-deelname, moeten worden gesynchroniseerd met de Cloud door Azure AD Connect. In eerdere versies van Azure AD Connect was deze vereiste niet strikt afgedwongen, wat leidt tot overbodige apparaatfuncties in azure AD. Dergelijke apparaten in azure AD hebben altijd de status ' in behandeling ', omdat deze apparaten niet zijn bedoeld om te worden geregistreerd bij Azure AD. 

Met deze versie van Azure AD Connect worden alleen Windows 10-apparaten gesynchroniseerd die correct zijn geconfigureerd voor hybride Azure AD. Windows 10-apparaatgroepen zonder de specifieke userCertificate van Azure AD worden verwijderd uit Azure AD.

## <a name="down-level-windows-devices"></a>Windows-apparaten op lagere niveaus
Azure AD Connect moet nooit [Windows-apparaten op lagere niveaus](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)synchroniseren. Alle apparaten in azure AD die voorheen onjuist zijn gesynchroniseerd, worden nu verwijderd uit Azure AD. Als Azure AD Connect probeert [Windows-apparaten op lagere niveaus](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)te verwijderen, is het apparaat niet de versie die is gemaakt door het [micro soft-Workplace join voor niet-Windows 10-computers MSI](https://www.microsoft.com/download/details.aspx?id=53554) en kan het niet worden gebruikt door een andere Azure AD-functie.

Sommige klanten moeten mogelijk opnieuw een bezoek [brengen over: Plan uw hybride Azure Active Directory deelname-](../devices/hybrid-azuread-join-plan.md) implementatie om hun Windows-apparaten correct te registreren en zorg ervoor dat dergelijke apparaten volledig kunnen deel nemen aan op apparaten gebaseerde voorwaardelijke toegang. 

## <a name="next-steps"></a>Volgende stappen
- [Geschiedenis van Azure AD Connect-versie](reference-connect-version-history.md)
