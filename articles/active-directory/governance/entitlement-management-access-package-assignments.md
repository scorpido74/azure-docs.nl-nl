---
title: Toewijzingen voor een toegangspakket in Azure AD-rechtenbeheer - Azure Active Directory weergeven, toevoegen en verwijderen
description: Meer informatie over het weergeven, toevoegen en verwijderen van toewijzingen voor een toegangspakket in Azure Active Directory-rechtenbeheer.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12e3b86f41e7188778393ab717554907ef5d44ec
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631747"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Toewijzingen voor een toegangspakket in Azure AD-rechtenbeheer weergeven, toevoegen en verwijderen

In Azure AD-rechtenbeheer u zien wie is toegewezen aan toegang tot pakketten, hun beleid en status. Als een toegangspakket een passend beleid heeft, u de gebruiker ook rechtstreeks toewijzen aan een toegangspakket. In dit artikel wordt beschreven hoe u toewijzingen voor toegangspakketten weergeven, toevoegen en verwijderen.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-rechtenbeheer wilt gebruiken en gebruikers toegang wilt geven tot pakketten, moet u over een van de volgende licenties beschikken:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-licentie

## <a name="view-who-has-an-assignment"></a>Zien wie een opdracht heeft

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Klik **op Toewijzingen** om een lijst met actieve opdrachten te bekijken.

    ![Lijst met toewijzingen aan een toegangspakket](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Klik op een specifieke opdracht om meer details te bekijken.

1. Als u een lijst met toewijzingen wilt zien die niet alle resourcerollen correct hebben ingericht, klikt u op de filterstatus en selecteert u **Leveren**.

    U aanvullende details over leveringsfouten bekijken door de bijbehorende aanvraag van de gebruiker te vinden op de pagina **Verzoeken.**

1. Als u verlopen toewijzingen wilt bekijken, klikt u op de filterstatus en selecteert u **Verlopen**.

1. Als u een CSV-bestand van de gefilterde lijst wilt downloaden, klikt u op **Downloaden**.

### <a name="viewing-assignments-programmatically"></a>Opdrachten programmatisch weergeven

U ook toewijzingen ophalen in een toegangspakket met Microsoft Graph.  Een gebruiker in een geschikte rol met `EntitlementManagement.ReadWrite.All` een toepassing met de gedelegeerde machtiging kan de API aanroepen om [accessPackageAssignments weer](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta)te geven.

## <a name="directly-assign-a-user"></a>Een gebruiker rechtstreeks toewijzen

In sommige gevallen wilt u mogelijk specifieke gebruikers rechtstreeks toewijzen aan een toegangspakket, zodat gebruikers het proces van het aanvragen van het toegangspakket niet hoeven te doorlopen. Als u gebruikers rechtstreeks wilt toewijzen, moet het toegangspakket een beleid hebben waarmee beheerders directe toewijzingen mogelijk zijn.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Klik in het linkermenu op **Toewijzingen**.

1. Klik **op Nieuwe toewijzing** om Gebruiker toevoegen aan pakket toe te voegen.

    ![Toewijzingen - Gebruiker toevoegen aan toegangspakket](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Klik **op Gebruikers toevoegen** om de gebruikers te selecteren waaraan u dit toegangspakket wilt toewijzen.

1. Selecteer **in** de lijst Beleid selecteren een beleid waarin de toekomstige aanvragen en levenscyclus van de gebruikers worden geregeld en bijgehouden. Als u wilt dat de geselecteerde gebruikers verschillende beleidsinstellingen hebben, u op **Nieuw beleid maken** klikken om een nieuw beleid toe te voegen.

1. Stel de datum en tijd in waarop de toewijzing van de geselecteerde gebruikers moet beginnen en eindigen. Als er geen einddatum is opgegeven, worden de levenscyclusinstellingen van het beleid gebruikt.

1. Optioneel een rechtvaardiging voor uw directe opdracht voor het bijhouden van gegevens.

1. Klik **op Toevoegen** om de geselecteerde gebruikers rechtstreeks aan het toegangspakket toe te wijzen.

    Klik na enkele ogenblikken op **Vernieuwen** om de gebruikers in de lijst Toewijzingen te zien.

### <a name="directly-assigning-users-programmatically"></a>Gebruikers programmatisch toewijzen

U een gebruiker ook rechtstreeks toewijzen aan een toegangspakket met Microsoft Graph.  Een gebruiker in een geschikte rol met `EntitlementManagement.ReadWrite.All` een toepassing met de gedelegeerde machtiging kan de API aanroepen om [een accessPackageAssignmentRequest](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta)te maken.

## <a name="remove-an-assignment"></a>Een toewijzing verwijderen

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Klik in het linkermenu op **Toewijzingen**.
 
1. Klik op het selectievakje naast de gebruiker wiens toewijzing u uit het toegangspakket wilt verwijderen. 

1. Klik **op** de knop Verwijderen boven aan het linkerdeelvenster. 
 
    ![Toewijzingen - Gebruiker verwijderen uit toegangspakket](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Er verschijnt een melding waarin wordt gemeld dat de toewijzing is verwijderd. 

## <a name="next-steps"></a>Volgende stappen

- [Aanvraag en instellingen voor een toegangspakket wijzigen](entitlement-management-access-package-request-policy.md)
- [Rapporten en logboeken weergeven](entitlement-management-reports.md)
