---
title: Hybride identiteitsontwerp - beheertaken Azure | Microsoft Documenten
description: Met Conditional Access-beheer controleert Azure Active Directory de specifieke voorwaarden die u kiest bij het verifiëren van de gebruiker en voordat u toegang tot de toepassing toestaat. Zodra aan deze voorwaarden is voldaan, wordt de gebruiker geverifieerd en toegang tot de toepassing toegestaan.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a829d39ff21a1abeafd3b4362747894d196d9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109389"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Plan voor de levenscyclus van hybride identiteit
Identiteit is een van de fundamenten van uw strategie voor bedrijfsmobiliteit en applicatietoegang. Of u zich nu aanmeldt bij uw mobiele apparaat of SaaS-app, uw identiteit is de sleutel tot het verkrijgen van toegang tot alles. Op het hoogste niveau omvat een oplossing voor identiteitsbeheer het verenigen en synchroniseren tussen uw identiteitsrepositories, waaronder het automatiseren en centraliseren van het proces van het inrichten van resources. De identiteitsoplossing moet een gecentraliseerde identiteit zijn in on-premises en cloud en ook een of andere vorm van identiteitsfederatie gebruiken om gecentraliseerde verificatie te onderhouden en veilig te delen en samen te werken met externe gebruikers en bedrijven. Resources variëren van besturingssystemen en toepassingen tot mensen in of aangesloten bij een organisatie. De organisatiestructuur kan worden gewijzigd om tegemoet te komen aan het inrichtingsbeleid en de procedures.

Het is ook belangrijk om een identiteitsoplossing te hebben die is afgestemd op het versterken van uw gebruikers door hen zelfbedieningservaringen te bieden om ze productief te houden. Uw identiteitsoplossing is robuuster als gebruikers met één melding kunnen worden ingeschakeld voor alle bronnen die ze nodig hebben. Beheerders op alle niveaus kunnen gestandaardiseerde procedures gebruiken voor het beheren van gebruikersreferenties. Sommige administratieniveaus kunnen worden verlaagd of geëlimineerd, afhankelijk van de breedte van de inrichtingsbeheeroplossing. Bovendien u beheermogelijkheden, handmatig of automatisch, veilig verdelen over verschillende organisaties. Een domeinbeheerder kan bijvoorbeeld alleen de personen en bronnen in dat domein weergeven. Deze gebruiker kan administratieve en insteltaken uitvoeren, maar is niet bevoegd om configuratietaken uit te voeren, zoals het maken van werkstromen.

## <a name="determine-hybrid-identity-management-tasks"></a>Hybride taken voor identiteitsbeheer bepalen
Het distribueren van administratieve taken in uw organisatie verbetert de nauwkeurigheid en effectiviteit van het beheer en verbetert de balans van de werklast van een organisatie. Hieronder volgen de draaipunten die een robuust identiteitsbeheersysteem definiëren.

 ![overwegingen op het niveau van identiteitsbeheer](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Als u hybride identiteitsbeheertaken wilt definiëren, moet u enkele essentiële kenmerken van de organisatie begrijpen die een hybride identiteit zal aannemen. Het is belangrijk om te begrijpen welke huidige repositories wordt gebruikt voor identiteitsbronnen. Door deze kernelementen te kennen, hebt u de fundamentele vereisten en op basis daarvan moet u meer gedetailleerde vragen stellen die u zullen leiden tot een betere ontwerpbeslissing voor uw identiteitsoplossing.  

Zorg er bij het definiëren van deze vereisten voor dat ten minste de volgende vragen worden beantwoord

* Inrichtingsopties: 
  
  * Ondersteunt de hybride identiteitsoplossing een robuust systeem voor accounttoegangsbeheer en -inrichting?
  * Hoe worden gebruikers, groepen en wachtwoorden beheerd?
  * Reageert het beheer van de identiteitslevenscyclus? 
    * Hoe lang duurt het instellen van wachtwoordupdates voor het account?
* Licentiebeheer: 
  
  * Behandelt de hybride identiteitsoplossing licentiebeheer?
    * Zo ja, welke mogelijkheden zijn beschikbaar?
  * Heeft de oplossing te maken met groepsgebaseerd licentiebeheer? 
  
    * Zo ja, is het mogelijk om er een beveiligingsgroep aan toe te wijzen? 
    * Zo ja, wijst de cloudmap automatisch licenties toe aan alle leden van de groep? 
    * Wat gebeurt er als een gebruiker vervolgens wordt toegevoegd aan of uit de groep wordt verwijderd, wordt een licentie automatisch toegewezen of verwijderd? 
* Integratie met andere externe identiteitsproviders:
  * Kan deze hybride oplossing worden geïntegreerd met externe identiteitsproviders om single sign-on te implementeren?
  * Is het mogelijk om alle verschillende identiteitsaanbieders te verenigen in een samenhangend identiteitssysteem?
  * Zo ja, hoe en welke zijn ze en welke mogelijkheden zijn beschikbaar?

## <a name="synchronization-management"></a>Synchronisatiebeheer
Een van de doelen van een identity manager, om alle identiteitsproviders te kunnen meenemen en gesynchroniseerd te houden. U houdt de gegevens gesynchroniseerd op basis van een gezaghebbende hoofdidentiteitsprovider. In een hybride identiteitsscenario, met een gesynchroniseerd beheermodel, beheert u alle gebruikers- en apparaatidentiteiten in een on-premises server en synchroniseert u de accounts en, optioneel, wachtwoorden naar de cloud. De gebruiker voert hetzelfde wachtwoord on-premises in als in de cloud en bij aanmelding wordt het wachtwoord geverifieerd door de identiteitsoplossing. Dit model maakt gebruik van een directorysynchronisatietool.

![directory](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) synchronisatie Om de synchronisatie van uw hybride identiteitsoplossing goed te ontwerpen, zorgt u ervoor dat de volgende vragen worden beantwoord:
*    Wat zijn de synchronisatieoplossingen die beschikbaar zijn voor de hybride identiteitsoplossing?
*    Wat zijn de beschikbare mogelijkheden voor één teken op mogelijkheden?
*    Wat zijn de opties voor identiteitsfederatie tussen B2B en B2C?

## <a name="next-steps"></a>Volgende stappen
[Bepalen hybride identity management adoptie strategie](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Zie ook
[Overzicht ontwerpoverwegingen](plan-hybrid-identity-design-considerations-overview.md)

