---
title: Inzicht in Azure AD Connect 1.4.xx.x en het verdwijnen van apparaten | Microsoft Documenten
description: In dit document wordt een probleem beschreven dat zich voordoet met versie 1.4.xx.x van Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176024"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Inzicht in Azure AD Connect 1.4.xx.x en het verdwijnen van apparaten
Met versie 1.4.xx.x van Azure AD Connect zien sommige klanten sommige of al hun Windows-apparaten uit Azure AD verdwijnen. Dit is geen reden tot bezorgdheid, omdat deze apparaatidentiteiten niet worden gebruikt door Azure AD tijdens de voorwaardelijke toegangsautorisatie. Met deze wijziging worden geen Windows-apparaten verwijderd die correct zijn geregistreerd bij Azure AD voor Hybride Azure AD Join.

Als u het verwijderen van apparaatobjecten in Azure AD de exportverwijderingsdrempel ziet overschrijden, wordt de klant aangeraden de verwijderingen door te laten gaan. [How To: deletes laten stromen wanneer ze de verwijderingsdrempel overschrijden](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Achtergrond
Windows-apparaten die zijn geregistreerd als Hybride Azure AD Joined, worden in Azure AD weergegeven als apparaatobjecten. Deze apparaatobjecten kunnen worden gebruikt voor voorwaardelijke toegang. Windows 10-apparaten worden gesynchroniseerd met de cloud via Azure AD Connect, Windows-apparaten op downniveau worden rechtstreeks geregistreerd via AD FS of naadloze enkele aanmelding.

## <a name="windows-10-devices"></a>Windows 10-apparaten
Alleen Windows 10-apparaten met een specifieke userCertificate-kenmerkwaarde die is geconfigureerd door Hybrid Azure AD Join, worden door Azure AD Connect met de cloud gesynchroniseerd. In eerdere versies van Azure AD Connect is deze vereiste niet rigoureus afgedwongen, wat resulteerde in onnodige apparaatobjecten in Azure AD. Dergelijke apparaten in Azure AD bleven altijd in de status 'in behandeling', omdat deze apparaten niet bedoeld waren om te worden geregistreerd bij Azure AD. 

Deze versie van Azure AD Connect synchroniseert alleen Windows 10-apparaten die correct zijn geconfigureerd om Hybride Azure AD Joined te zijn. Windows 10-apparaatobjecten zonder de Azure AD join-specifieke userCertificate worden verwijderd uit Azure AD.

## <a name="down-level-windows-devices"></a>Windows-apparaten op downniveau
Azure AD Connect mag [windows-apparaten](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)op downniveau nooit synchroniseren. Alle apparaten in Azure AD die voorheen onjuist zijn gesynchroniseerd, worden nu verwijderd uit Azure AD. Als Azure AD Connect probeert [Windows-apparaten op downniveau](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)te verwijderen, is het apparaat niet het apparaat dat is gemaakt door de [Microsoft Workplace Join voor msi-computers die niet zijn voor Windows 10-computers](https://www.microsoft.com/download/details.aspx?id=53554) en kan het niet worden verbruikt door andere Azure AD-functies.

Sommige klanten moeten mogelijk opnieuw [naar How To: Plan uw hybride Azure Active Directory-join-implementatie](../devices/hybrid-azuread-join-plan.md) om hun Windows-apparaten correct te laten registreren en ervoor te zorgen dat dergelijke apparaten volledig kunnen deelnemen aan voorwaardelijke toegang op basis van apparaten. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Hoe kan ik controleren welke apparaten met deze update worden verwijderd?

Als u wilt controleren welke apparaten worden verwijderd, u dit PowerShell-script gebruiken:https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Dit script genereert een rapport over certificaten die zijn opgeslagen in Active Directory Computer-objecten, met name certificaten die zijn uitgegeven door de functie Hybride Azure AD.This script generates a report about certificates stored in Active Directory Computer objects, specifically, certificates issued by the Hybrid Azure AD join feature.
Het controleert de certificaten die aanwezig zijn in de eigenschap UserCertificate van een computerobject in AD en valideert voor elk niet-verlopen certificaat dat aanwezig is, of het certificaat is uitgegeven voor de hybride apo-advertentie-joinfunctie (d.w.z. Onderwerpnaam komt overeen met CN={ObjectGUID}).
Voorheen synchroniseerde Azure AD Connect elke computer met Azure AD die ten minste één geldig certificaat bevat, maar vanaf Azure AD Connect-versie 1.4 kan de synchronisatie-engine hybride Azure AD-joincertificaten identificeren en wordt het cloudfilter 'cloudfilter' computerobject van synchronisatie naar Azure AD, tenzij er een geldig Hybride Azure AD-joincertificaat is.
Azure AD-apparaten die al zijn gesynchroniseerd met AD, maar geen geldig Hybride Azure AD-joincertificaat hebben, worden door de synchronisatieengine verwijderd (CloudFiltered=TRUE).

## <a name="next-steps"></a>Volgende stappen
- [Geschiedenis van azure AD Connect-versie](reference-connect-version-history.md)
