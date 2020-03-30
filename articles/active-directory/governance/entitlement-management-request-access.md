---
title: Een toegangspakket aanvragen - Azure AD-rechtenbeheer
description: Meer informatie over het gebruik van de My Access-portal om toegang te vragen tot een toegangspakket in Azure Active Directory-rechtenbeheer.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b1ccde2f1f92237978ab4e68acaa26393bbb9d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261747"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Toegang aanvragen tot een toegangspakket in Azure AD-rechtenbeheer

Met Azure AD-rechtenbeheer maakt een toegangspakket een eenmalige installatie van resources en beleidsregels mogelijk die automatisch toegang beheren voor de levensduur van het toegangspakket. 

Een toegangspakketbeheerder kan beleid configureren om goedkeuring te vereisen voor gebruikers om toegang te hebben tot pakketten. Een gebruiker die toegang nodig heeft tot een toegangspakket kan een verzoek indienen om toegang te krijgen. In dit artikel wordt beschreven hoe u een toegangsverzoek indient.

## <a name="sign-in-to-the-my-access-portal"></a>Aanmelden bij de My Access-portal

De eerste stap is om u aan te melden bij de My Access-portal, waar u toegang vragen tot een toegangspakket.

**Vereiste rol:** Aanvrager

1. Zoek naar een e-mail of een bericht van de project- of bedrijfsmanager waarmee u werkt. De e-mail moet een link bevatten naar het toegangspakket waartoe u toegang nodig hebt. De koppeling `myaccess`begint met , bevat een directory hint, en eindigt met een toegangspakket-ID.  (Voor de Amerikaanse regering `https://myaccess.microsoft.us` kan het domein in plaats daarvan zijn.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Open de koppeling.

1. Meld u aan bij de My Access-portal.

    Zorg ervoor dat u uw organisatieaccount (werk of school) gebruikt. Als u het niet zeker weet, neem dan contact op met uw project of bedrijfsmanager.

## <a name="request-an-access-package"></a>Een toegangspakket aanvragen

Zodra u het toegangspakket in de My Access-portal hebt gevonden, u een aanvraag indienen.

**Vereiste rol:** Aanvrager

1. Zoek het toegangspakket in de lijst.  Indien nodig u zoeken door een zoektekenreeks te typen en vervolgens het filter **Naam**, **catalogus**of **Resources te** selecteren.

    ![Mijn Access-portal - Zoeken naar resources](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Klik op het vinkje om het toegangspakket te selecteren.

1. Klik **op Toegang aanvragen** om het deelvenster Toegangs aanvragen te openen.

    ![Mijn Access-portal - Toegangspakketten](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Als het vak **Zakelijke rechtvaardiging** wordt weergegeven, typt u een motivering voor het nodig hebben van toegang.

1. Als **Aanvraag voor een bepaalde periode** is ingeschakeld, selecteert u **Ja** of **Nee**.

1. Geef indien nodig de begin- en einddatum op.

    ![Mijn Access-portal - Toegang aanvragen](./media/entitlement-management-shared/my-access-request-access.png)

1. Klik op **Verzenden** om uw aanvraag in te dienen als u klaar bent.

1. Klik **op Geschiedenis aanvragen** om een lijst met uw aanvragen en de status te bekijken.

    Als het toegangspakket goedkeuring vereist, bevindt de aanvraag zich nu in een goedkeuringsstatus in behandeling.

### <a name="select-a-policy"></a>Beleid selecteren

Als u toegang vraagt tot een toegangspakket met meerdere beleidsregels die van toepassing zijn, wordt u mogelijk gevraagd een beleid te selecteren. Een access package manager kan bijvoorbeeld een toegangspakket configureren met twee beleidsregels voor twee groepen interne werknemers. Het eerste beleid kan 60 dagen toegang bieden en goedkeuring vereisen. Het tweede beleid kan toegang toestaan voor 2 dagen en geen goedkeuring vereisen. Als u dit scenario tegenkomt, moet u het beleid selecteren dat u wilt gebruiken.

![Mijn Access-portal - Toegang aanvragen - meerdere beleidsregels](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>Een verzoek opnieuw indienen

Wanneer u toegang aanvraagt tot een toegangspakket, kan uw verzoek worden geweigerd of verloopt uw verzoek mogelijk als fiatteurs niet op tijd reageren. Als je toegang nodig hebt, kun je het opnieuw proberen en je verzoek opnieuw indienen. In de volgende procedure wordt uitgelegd hoe u een toegangsverzoek opnieuw indienen:

**Vereiste rol:** Aanvrager

1. Meld u aan bij de **My Access-portal.**

1. Klik **op Geschiedenis aanvragen** in het navigatiemenu aan de linkerkant.

1. Zoek het toegangspakket waarvoor u opnieuw een aanvraag indient.

1. Klik op het vinkje om het toegangspakket te selecteren.

1. Klik op de blauwe **koppeling Weergeven** rechts van het geselecteerde toegangspakket.
    
    ![Koppeling toegangspakket en weergave selecteren](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Er wordt rechts geopend met de aanvraaggeschiedenis voor het toegangspakket.
    
    ![Knop Opnieuw verzenden selecteren](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Klik op de knop **Opnieuw indienen** onder aan het deelvenster.

## <a name="cancel-a-request"></a>Een aanvraag annuleren

Als u een toegangsaanvraag indient en de aanvraag nog steeds in de **goedkeuringsstatus in behandeling** is, u de aanvraag annuleren.

**Vereiste rol:** Aanvrager

1. Klik in de Mijn Access-portal aan de linkerkant op **Geschiedenis aanvragen** om een lijst met uw aanvragen en de status weer te geven.

1. Klik **op** de koppeling Weergave voor het verzoek dat u wilt annuleren.

1. Als de aanvraag nog in de **goedkeuringsstatus in behandeling** is, u op **Verzoek annuleren** klikken om de aanvraag te annuleren.

    ![Mijn Access-portal - Aanvraag annuleren](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Klik **op Geschiedenis aanvragen** om te bevestigen dat het verzoek is geannuleerd.

## <a name="next-steps"></a>Volgende stappen

- [Toegangsaanvragen goedkeuren of weigeren](entitlement-management-request-approve.md)
- [Proces aanvragen en e-mailmeldingen](entitlement-management-process.md)
