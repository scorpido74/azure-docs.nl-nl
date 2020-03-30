---
title: Gebruikers met directe licenties toevoegen aan groepslicenties - Azure AD | Microsoft Documenten
description: Migreren van afzonderlijke gebruikerslicenties naar licenties op basis van groepen met Azure Active Directory
services: active-directory
keywords: Azure AD-licenties
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c06d81f2f3f6cee781889d05ae08a1fd125df52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025684"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Gebruikers met afzonderlijke licenties migreren naar groepen voor licenties

Mogelijk worden bestaande licenties geïmplementeerd bij gebruikers in de organisaties via directe toewijzing. dat wil zeggen, met behulp van PowerShell-scripts of andere hulpprogramma's om individuele gebruikerslicenties toe te wijzen. Voordat u groepslicenties gaat gebruiken om licenties in uw organisatie te beheren, u dit migratieplan gebruiken om bestaande oplossingen naadloos te vervangen door groepslicenties.

Het belangrijkste om in gedachten te houden is dat u een situatie moet vermijden waarin migreren naar groepslicenties ertoe zal leiden dat gebruikers hun momenteel toegewezen licenties tijdelijk verliezen. Elk proces dat kan leiden tot verwijdering van licenties moet worden vermeden om het risico te verwijderen dat gebruikers de toegang tot services en hun gegevens verliezen.

## <a name="recommended-migration-process"></a>Aanbevolen migratieproces

1. U beschikt over bestaande automatisering (bijvoorbeeld PowerShell) die licentietoewijzing en -verwijdering voor gebruikers beheert. Laat het lopen zoals het is.

1. Maak een nieuwe licentiegroep (of bepaal welke bestaande groepen ze willen gebruiken) en zorg ervoor dat alle vereiste gebruikers als lid worden toegevoegd.

1. De vereiste licenties toewijzen aan deze groepen; uw doel moet zijn om dezelfde licentiestatus weer te geven die uw bestaande automatisering (bijvoorbeeld PowerShell) toepast op die gebruikers.

1. Controleer of licenties zijn toegepast op alle gebruikers in die groepen. Deze toepassing kan worden gedaan door de verwerkingsstatus van elke groep te controleren en door controlelogboeken te controleren.

   - U individuele gebruikers controleren door te kijken naar hun licentiegegevens. U zult zien dat zij dezelfde licenties hebben toegewezen "direct" en "overgenomen" van groepen.

   - U een PowerShell-script uitvoeren om te [controleren hoe licenties aan gebruikers worden toegewezen.](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses)

   - Wanneer dezelfde productlicentie zowel rechtstreeks als via een groep aan de gebruiker wordt toegewezen, wordt slechts één licentie door de gebruiker verbruikt. Er zijn dus geen extra licenties nodig om migratie uit te voeren.

1. Controleer of er geen licentietoewijzingen zijn mislukt door elke groep te controleren op gebruikers die in de foutstatus staan. Zie [Licentieproblemen voor een groep identificeren en oplossen voor](licensing-groups-resolve-problems.md)meer informatie.

Overweeg de oorspronkelijke directe toewijzingen te verwijderen. We raden u aan het geleidelijk te doen en eerst de uitkomst van een subset van gebruikers te controleren. Als u de oorspronkelijke directe toewijzingen aan gebruikers achterlaten, maar wanneer de gebruikers hun gelicentieerde groepen verlaten, behouden ze de direct toegewezen licenties, wat misschien niet is wat u wilt.

## <a name="an-example"></a>Een voorbeeld

Een organisatie heeft 1.000 gebruikers. Alle gebruikers hebben Office 365 Enterprise E3-licenties nodig. Momenteel heeft de organisatie een PowerShell-script dat op locatie wordt uitgevoerd en licenties van gebruikers toevoegt en verwijdert terwijl ze komen en gaan. De organisatie wil het script echter vervangen door groepslicenties, zodat licenties automatisch kunnen worden beheerd door Azure AD.

Zo zou het migratieproces eruit kunnen zien:

1. Wijs de Office 365 E3-licentie met behulp van de Azure-portal toe aan de groep **Alle gebruikers** in Azure AD.

1. Controleer of de licentietoewijzing voor alle gebruikers is voltooid. Ga naar de overzichtspagina voor de groep, selecteer **Licenties**en controleer de verwerkingsstatus boven aan het **zwaard Licenties.**

   - Zoek naar "Laatste licentiewijzigingen zijn toegepast op alle gebruikers" om te bevestigen dat de verwerking is voltooid.

   - Zoek naar een melding boven over gebruikers voor wie licenties mogelijk niet zijn toegewezen. Hebben we geen licenties meer voor sommige gebruikers? Hebben sommige gebruikers conflicterende licentieplannen die voorkomen dat ze groepslicenties overnemen?

1. Spot controleren sommige gebruikers om te controleren of ze zowel de directe en groepslicenties toegepast. Ga naar de profielpagina voor een gebruiker, selecteer **Licenties**en bekijk de status van licenties.

   - Dit is de verwachte gebruikersstatus tijdens de migratie:

      ![de verwachte gebruikersstatus tijdens de migratie](./media/licensing-groups-migrate-users/expected-user-state.png)

     Dit bevestigt dat de gebruiker zowel directe als overgenomen licenties heeft. We zien dat Office 365 E3 is toegewezen.

   - Selecteer elke licentie om te zien welke services zijn ingeschakeld. Als u wilt controleren of de directe en groepslicenties exact dezelfde services voor de gebruiker inschakelen, selecteert u **Toewijzingen**.

1. Nadat u hebt bevestigd dat zowel directe als groepslicenties gelijkwaardig zijn, u beginnen met het verwijderen van directe licenties van gebruikers. U dit testen door ze te verwijderen voor individuele gebruikers in de portal en vervolgens automatiseringsscripts uit te voeren om ze in bulk te laten verwijderen. Hier is een voorbeeld van dezelfde gebruiker met de directe licenties verwijderd via de portal. Merk op dat de licentiestatus ongewijzigd blijft, maar we zien geen directe opdrachten meer.

   ![bevestigen dat directe licenties worden verwijderd](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere scenario's voor groepslicentiebeheer:

- [Wat is groepsgebaseerde licenties in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Licenties toewijzen aan een groep in Azure Active Directory](licensing-groups-assign.md)
- [Licentieproblemen voor een groep vaststellen en oplossen in Azure Active Directory](licensing-groups-resolve-problems.md)
- [Gebruikers migreren tussen productlicenties met groepslicenties in Azure Active Directory](licensing-groups-change-licenses.md)
- [Aanvullende scenario’s voor Azure Active Directory-licenties op basis van groepen](licensing-group-advanced.md)
- [PowerShell-voorbeelden voor groepslicenties in Azure Active Directory](licensing-ps-examples.md)
