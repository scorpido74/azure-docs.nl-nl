---
title: Upgraden van DirSync en Azure AD Sync | Microsoft Documenten
description: Beschrijft hoe u upgraden van DirSync en Azure AD Sync naar Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 803fcc0161f2a092006e60db5a98f5bf18dce1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381175"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Windows Azure Active Directory-synchronisatie en Azure Active Directory-synchronisatie upgraden
Azure AD Connect is de beste manier om verbinding te maken met uw on-premises directory met Azure AD en Office 365. Dit is een goed moment om te upgraden naar Azure AD Connect vanuit Windows Azure Active Directory Sync (DirSync) of Azure AD Sync, omdat deze hulpprogramma's nu worden afgeschaft en vanaf 13 april 2017 niet meer worden ondersteund.

De twee hulpprogramma's voor identiteitssynchronisatie die zijn afgeschaft, zijn aangeboden voor single forest-klanten (DirSync) en voor multi-forest en andere geavanceerde klanten (Azure AD Sync). Deze oudere hulpprogramma's zijn vervangen door één oplossing die beschikbaar is voor alle scenario's: Azure AD Connect. Het biedt nieuwe functionaliteit, functieverbeteringen en ondersteuning voor nieuwe scenario's. Om uw on-premises identiteitsgegevens te kunnen blijven synchroniseren met Azure AD en Office 365, raden we u ten zeerste aan om te upgraden naar Azure AD Connect. Microsoft garandeert niet dat deze oudere versies na 31 december 2017 werken.

De laatste release van DirSync werd uitgebracht in juli 2014 en de laatste release van Azure AD Sync werd uitgebracht in mei 2015.

## <a name="what-is-azure-ad-connect"></a>Wat is Azure AD Connect?
Azure AD Connect is de opvolger van DirSync en Azure AD Sync. Het combineert alle scenario's deze twee ondersteund. U er meer over lezen in [het integreren van uw on-premises identiteiten met Azure Active Directory.](whatis-hybrid-identity.md)

## <a name="deprecation-schedule"></a>Afschaffingsschema
| Date | Opmerking |
| --- | --- |
| 13 april 2016 |Windows Azure Active Directory Sync ('DirSync') en Microsoft Azure Active Directory Sync ('Azure AD Sync') worden aangekondigd als afgeschaft. |
| 13 april 2017 |Ondersteuning eindigt. Klanten kunnen geen ondersteuningsaanvraag meer openen zonder eerst te upgraden naar Azure AD Connect. |
|31 december 2017|Azure AD accepteert mogelijk geen communicatie meer van Windows Azure Active Directory Sync ('DirSync') en Microsoft Azure Active Directory Sync ('Azure AD Sync').

## <a name="how-to-transition-to-azure-ad-connect"></a>Overstappen op Azure AD Connect
Als u DirSync uitvoert, zijn er twee manieren waarop u upgraden: In-place upgrade en parallelle implementatie. Een in-place upgrade wordt aanbevolen voor de meeste klanten en als u een recent besturingssysteem en minder dan 50.000 objecten. In andere gevallen wordt aanbevolen om een parallelle implementatie uit te voeren waarbij uw DirSync-configuratie wordt verplaatst naar een nieuwe server met Azure AD Connect.

| Oplossing | Scenario |
| --- | --- |
| [Upgraden van DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Als u al een bestaande DirSync-server hebt.</li> |
| [Upgraden van Azure AD Sync](how-to-upgrade-previous-version.md) |<li>Als u overstapt van Azure AD Sync.</li> |

Als u wilt zien hoe u een in-place upgrade van DirSync naar Azure AD Connect uitvoeren, raadpleegt u deze kanaal 9-video:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>Veelgestelde vragen
**V: Ik heb een e-mailmelding ontvangen van het Azure-team en/of een bericht van het Office 365-berichtencentrum, maar ik gebruik Connect.**  
De melding is ook verzonden naar klanten die Azure AD Connect gebruiken met een buildnummer 1.0. \*.0 (met behulp van een pre-1.1 release). Microsoft raadt klanten aan om op de hoogte te blijven van Azure AD Connect-releases. De [automatische upgradefunctie](how-to-connect-install-automatic-upgrade.md) die in 1.1 is geïntroduceerd, maakt het eenvoudig om altijd een recente versie van Azure AD Connect te installeren.

**V: Werkt DirSync/Azure AD Sync niet meer op 13 april 2017?**  
DirSync/Azure AD Sync blijft werken op 13 april 2017.  Azure AD accepteert echter geen communicatie meer van DirSync/Azure AD Sync na 31 december 2017.

**V: Van welke DirSync-versies kan ik upgraden?**  
Het wordt ondersteund om te upgraden van een DirSync-release die momenteel wordt gebruikt. 

**V: Hoe zit het met de Azure AD Connector voor FIM/MIM?**  
De Azure AD Connector voor FIM/MIM is **niet** aangekondigd als afgeschaft. Het is op **functie bevriezen**; er wordt geen nieuwe functionaliteit toegevoegd en het ontvangt geen bugfixes. Microsoft raadt klanten aan deze te gebruiken om van het abonnement naar Azure AD Connect te gaan. Het wordt ten zeerste aanbevolen om geen nieuwe implementaties te starten. Deze Connector zal in de toekomst worden aangekondigd.

## <a name="additional-resources"></a>Aanvullende resources
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
