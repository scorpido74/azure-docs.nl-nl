---
title: Gehoste test drive | Azure Marketplace
description: Een gehoste test drive voor een Marketplace instellen
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d054064e236e121e02bf58a0eb73b5a62f24a09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278260"
---
# <a name="hosted-test-drive"></a>Gehoste Test Drive

Een gehoste test drive verwijdert de complexiteit van Setup door micro soft die als host fungeert en onderhoud van de service die de gebruikers inrichting en het ongedaan maken van de inrichting van het test station uitvoert. Dit artikel is voor uitgevers die hun aanbieding op AppSource hebben of een nieuw abonnement bouwen en een gehoste test drive willen aanbieden, die verbinding maakt met een Dynamics 365 voor klant betrokkenheid, Dynamics 365 voor Financiën en bewerkingen of een Dynamics 365 Business Central-exemplaar.

## <a name="how-to-publish-a-test-drive"></a>Een test station publiceren

Ga naar de bestaande aanbieding of maak een nieuwe aanbieding.

Selecteer de optie test drive in het menu aan de zijkant.

Selecteer \'Ja\' \'als u een test station\' wilt gebruiken.

Geef de volgende velden op in \'de\' sectie Details.

- **Beschrijving**: Geef een overzicht van uw test station op. Deze tekst wordt weer gegeven aan de gebruiker terwijl het test station wordt ingericht. Dit veld ondersteunt HTML als u opgemaakte inhoud wilt leveren.
- **Gebruikers handleiding**: Upload een gedetailleerde gebruikers handleiding (bestand van het type. PDF), waarmee u kunt testen of gebruikers uw app kunnen gebruiken.
- **Demo video testen**: Upload eventueel een video met een demonstratie van uw app.

Verleen AppSource toestemming voor het inrichten en verwijderen van test drive-gebruikers in uw Tenant met behulp van de [hier](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)bevindene instructies.

In deze stap \'genereert u de Azure AD-App-Id\' en \'Azure AD-App sleutel\' waarden die hieronder worden genoemd.

Geef de volgende velden op in \'de sectie\' technische configuratie:

- **Type test station**: Kies \'de optie micro soft gehost (voor beeld Dynamics 365 voor klant betrokkenheid). Dit geeft aan dat micro soft de service zal hosten en onderhouden die de inrichting van de gebruiker van het test station en het ongedaan maken van de inrichting uitvoert.
- **Max. aantal gelijktijdige test stations**: Stel dit veld in op de hoeveelheid gelijktijdige gebruikers die op elk gewenst moment een actief test station kunnen hebben. Elke gebruiker gebruikt een Dynamics-licentie terwijl hun test station actief is. u moet er dus voor zorgen dat u ten minste beschikt over de vele Dynamics-licenties die beschikbaar zijn voor gebruikers van het test station. Aanbevolen waarde van 3-5.
- **Duur van test station (uren)**: Stel dit veld in op het aantal uren dat de gebruikers testen station actief zullen zijn. Na dit aantal uur wordt de inrichting van de gebruiker ongedaan gemaakt van uw Tenant. Aanbevolen waarde van 2-24 uur, afhankelijk van de complexiteit van uw app. De gebruiker kan altijd een andere test schijf aanvragen als deze niet meer actief zijn en het test station opnieuw willen openen.
- **URL van exemplaar**: Geef een URL op waarmee de gebruiker van het test station voor het eerst wordt genavigeerd wanneer het test station wordt gestart. Dit is doorgaans de URL van uw Dynamics 365-exemplaar waarop uw app en voorbeeld gegevens zijn geïnstalleerd. Voorbeeld waarde: https:\//Testdrive.CRM.Dynamics.com
- **Azure AD-Tenant-id**: Geef de id op van de Azure-Tenant voor uw Dynamics 365-exemplaar. Als u deze waarde wilt ophalen, meldt u zich \'aan\'  - \> bij Azure Portal en navigeert u\> naar Azure Active Directory eigenschappen uit menu Blade selecteren: Kopieer de map-id. Voorbeeld waarde: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure AD-App ID**: id van de Azure AD-app die u hebt gemaakt in stap 7. \ voorbeeld waarde: 53852862-a2ae-4e43-9461-faa49650a096
- **Azure AD-App sleutel**: geheim voor de Azure AD-app die u hebt gemaakt in stap 7. \ voorbeeld waarde: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk =
- **Azure AD-Tenant naam**: Geef de naam op van de Azure-Tenant voor uw Dynamics 365-exemplaar. Gebruik de indeling \<tenantnaam. \>onmicrosoft.com. Voorbeeld waarde: testdrive.onmicrosoft.com
- **URL van Web API-exemplaar**: Geef de Web-API-URL op voor uw Dynamics 365-exemplaar. U kunt deze waarde ophalen door u aan te melden bij uw micro soft Dynamics 365-exemplaar en\> te navigeren\> naar instelling-\> aanpassing-ontwikkelaars resources-instance Web API (deze URL kopiëren). Voorbeeld waarde: https:\//Testdrive.CRM.Dynamics.com/API/data/v9.0
- **Rolnaam**: Geef de naam op van de aangepaste Dynamics 365-beveiligingsrol die u hebt gemaakt voor het test station. Dit is de rol die wordt toegewezen aan gebruikers tijdens hun test station. Voorbeeld waarde: testdriverole

## <a name="next-steps"></a>Volgende stappen

Wanneer u klaar bent met het **publiceren** van uw aanbieding, hebt u een **voor beeld** van uw aanbieding als uw app de certificering heeft door gegeven. Start een test station in de gebruikers interface en controleer of de test stations correct worden uitgevoerd. Zodra u vertrouwd bent met uw preview-aanbieding, is het tijd om **Live te gaan!**
