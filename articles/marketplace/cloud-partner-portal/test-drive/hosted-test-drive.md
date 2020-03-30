---
title: Gehoste testrit | Azure Marketplace
description: Een marketplace-teststation instellen
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d054064e236e121e02bf58a0eb73b5a62f24a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278260"
---
# <a name="hosted-test-drive"></a>Gehoste Test Drive

Een gehoste testdrive verwijdert de complexiteit van de installatie door Microsoft-hosting en onderhoudt de service die de installatie en deprovisioning voor gebruikers van de Test Drive uitvoert. Dit artikel is voor uitgevers die hun aanbod op AppSource hebben of een nieuwe willen bouwen en een Hosted Test Drive willen aanbieden, die verbinding maakt met een Dynamics 365 voor Customer Engagement, Dynamics 365 voor Financiën en Operations of Dynamics 365 Business Central Exemplaar.

## <a name="how-to-publish-a-test-drive"></a>Een teststation publiceren

Navigeer naar bestaande aanbiedingen of maak een nieuwe aanbieding.

Selecteer de optie Teststation in het zijmenu.

Selecteer \'\' Ja \'voor de\' optie Een teststation inschakelen.

Geef de volgende \'velden\' op in de sectie Details.

- **Beschrijving**: Geef een overzicht van uw proefrit. Deze tekst wordt aan de gebruiker getoond terwijl de testdrive wordt ingericht. Dit veld ondersteunt HTML als u opgemaakte inhoud wilt bieden.
- **Gebruikershandleiding**: Upload een gedetailleerde gebruikershandleiding (bestand van type .pdf) waarmee gebruikers van Test Drive begrijpen hoe ze uw app moeten gebruiken.
- **Demovideo teststation:** upload optioneel een video waarin uw app wordt geüpload.

Geef AppSource-toestemming voor het inrichten en deprovisionen van Test Drive-gebruikers in uw tenant met behulp van de instructies die hier worden [gevonden.](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)

In deze stap genereert \'u de\' hieronder \'vermelde azure\' AD-app-id en Azure AD-app-sleutelwaarden.

Geef de volgende \'velden\' op in de sectie Technische configuratie:

- **Type teststation**: \'kies microsoft gehost (bijvoorbeeld Dynamics 365 voor klantbetrokkenheid)' optie. Dit geeft aan dat Microsoft de service host en onderhoudt die de testdrive-gebruikersinrichting en -deprovisioning uitvoert.
- **Max Concurrent Test Drives:** Stel dit veld in op het aantal gelijktijdige gebruikers dat op elk gewenst moment een actieve testrit kan hebben. Elke gebruiker gebruikt een Dynamics-licentie terwijl zijn teststation actief is, dus u moet ervoor zorgen dat u ten minste zoveel Dynamics-licenties beschikbaar hebt voor Gebruikers van Test Drive. Aanbevolen waarde van 3-5.
- **Testritduur (uren):** Stel dit veld in op het aantal uren waarvoor de testdrive van de gebruikers actief is. Na deze vele uren wordt de gebruiker van uw tenant verwijderd. Aanbevolen waarde van 2-24 uur, afhankelijk van de complexiteit van uw app. De gebruiker kan altijd een andere testdrive aanvragen als de tijd op raakt en de testdrive opnieuw wil openen.
- **Instantie-URL**: geef een URL op waarnaar de testdrive-gebruiker in eerste instantie wordt genavigeerd wanneer hij de testdrive start. Dit is meestal de URL van uw Dynamics 365-exemplaar waarop uw app- en voorbeeldgegevens zijn geïnstalleerd. Voorbeeldwaarde: https:\//testdrive.crm.dynamics.com
- **Azure AD-tenant-id:** geef de id van de Azure-tenant op voor uw Dynamics 365-exemplaar. Als u deze waarde wilt ophalen, \'logt\'  - \> u in bij\> Azure Portal en navigeert u naar Azure Active Directory Select-eigenschappen vanuit het menublad - Kopieer de directory-id. Voorbeeldwaarde: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure AD App ID**: ID van de Azure AD App die u hebt gemaakt in stap 7.\ Voorbeeldwaarde: 53852862-a2ae-4e43-9461-faa49650a096
- **Azure AD App Key**: Geheim voor de Azure AD App gemaakt in stap 7.\ Voorbeeldwaarde: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Azure AD-tenantnaam:** geef de naam van de Azure-tenant op voor uw Dynamics 365-exemplaar. Gebruik de \<indeling van de tenantnaam. \>onmicrosoft.com. Voorbeeldwaarde: testdrive.onmicrosoft.com
- **Url van instantieweb-API:** geef de URL van de web-API op voor uw Dynamics 365-exemplaar. U deze waarde ophalen door u aan te melden bij\> uw\> Microsoft\> Dynamics 365-exemplaar en door te navigeren naar Instelling - Aanpassen - Ontwikkelaarsbronnen - InstantieWeb-API (Kopieer deze URL). Voorbeeldwaarde: https:\//testdrive.crm.dynamics.com/api/data/v9.0
- **Rolnaam:** Geef de naam op van de aangepaste Dynamics 365-beveiligingsrol die u hebt gemaakt voor teststation. Dit is de rol die tijdens hun testrit aan gebruikers wordt toegewezen. Voorbeeldwaarde: testdriverol

## <a name="next-steps"></a>Volgende stappen

Wanneer u uw aanbieding wilt **publiceren,** heeft u een **voorbeeld** van uw aanbieding nadat uw app is gecertificeerd. Start een testrit in de gebruikersinterface en controleer of uw teststations correct worden uitgevoerd. Zodra u zich comfortabel voelt met uw preview-aanbod, is het nu tijd om live te **gaan!**
