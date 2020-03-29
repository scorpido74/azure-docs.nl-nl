---
title: Azure AD Connect Synchronization Service Manager Operations | Microsoft Documenten
description: Het tabblad Bewerkingen in het synchronisatieservicebeheer voor Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474000d1d4d7e1358682d1421125d482e3782049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381392"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Het tabblad Bewerkingen van Synchronisatieservicebeheer gebruiken

![Onderhoudsbeheer synchroniseren](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

Op het tabblad Bewerkingen worden de resultaten van de meest recente bewerkingen weergegeven. Dit tabblad is de sleutel om problemen te begrijpen en op te lossen.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>De informatie zichtbaar maken op het tabblad Bewerkingen
De bovenste helft toont alle runs in chronologische volgorde. Standaard houdt het logboek van bewerkingen informatie over de afgelopen zeven dagen bij, maar deze instelling kan worden gewijzigd met de [planner](how-to-connect-sync-feature-scheduler.md). U wilt op zoek naar een run die niet een succes status te tonen. U de sortering wijzigen door op de kopteksten te klikken.

De kolom **Status** is de belangrijkste informatie en toont het ernstigste probleem voor een run. Hier is een korte samenvatting van de meest voorkomende statussen in volgorde van prioriteit te onderzoeken (waar * geven verschillende mogelijke fout strings).

| Status | Opmerking |
| --- | --- |
| gestopt-\* |De run kon niet voltooien. Bijvoorbeeld als het externe systeem uitvalt en niet kan worden gecontacteerd. |
| stop-fout-limiet |Er zijn meer dan 5.000 fouten. De run werd automatisch gestopt vanwege het grote aantal fouten. |
| voltooide-\*-fouten |De run voltooid, maar er zijn fouten (minder dan 5.000) die moeten worden onderzocht. |
| voltooid-\*-waarschuwingen |De run is voltooid, maar sommige gegevens zijn niet in de verwachte status. Als u fouten hebt, dan is dit bericht is meestal slechts een symptoom. Totdat u fouten hebt verholpen, moet u waarschuwingen niet onderzoeken. |
| voltooid |Geen problemen. |

Wanneer u een rij selecteert, wordt de onderste bijgewerkt om de details van die run weer te geven. Helemaal links onderin heb je misschien een lijst met de tekst **Stap #**. Deze lijst wordt alleen weergegeven als u meerdere domeinen in uw forest hebt waar elk domein door een stap wordt vertegenwoordigd. De domeinnaam is te vinden onder het kopje **Partitie**. Onder **Synchronisatiestatistieken**vindt u meer informatie over het aantal wijzigingen dat is verwerkt. U op de koppelingen klikken om een lijst met de gewijzigde objecten te krijgen. Als u objecten met fouten hebt, worden deze fouten weergegeven onder **Synchronisatiefouten**.

Zie [problemen oplossen van een object dat niet synchroniseert](tshoot-connect-object-not-syncing.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [synchronisatieconfiguratie van Azure AD Connect.](how-to-connect-sync-whatis.md)

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
