---
title: Gehoste test drive | Azure Marketplace
description: Een gehoste test drive voor een Marketplace instellen
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 67d8421b2e545c951dcbc3280a306514e4b14897
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030050"
---
# <a name="hosted-test-drive"></a>Gehoste Test Drive

Een gehoste test drive verwijdert de complexiteit van Setup door micro soft die als host fungeert en onderhoud van de service die de gebruikers inrichting en het ongedaan maken van de inrichting van het test station uitvoert. Dit artikel is voor uitgevers die hun aanbieding op AppSource hebben of een nieuw abonnement bouwen en een gehoste test drive willen aanbieden, die verbinding maakt met een Dynamics 365 voor klant betrokkenheid, Dynamics 365 voor Financiën en bewerkingen, of Dynamics 365 Business Central exemplaar.

## <a name="how-to-publish-a-test-drive"></a>Het publiceren van een Test Drive

Ga naar de bestaande aanbieding of maak een nieuwe aanbieding.

Selecteer de optie test drive in het menu aan de zijkant.

Selecteer \'Yes @ no__t-1 voor \'Enable een test station @ no__t-3 optie.

Geef de volgende velden op in de sectie \'Details @ no__t-1.

- **Beschrijving**: Geef een overzicht van uw test station op. Deze tekst wordt weer gegeven aan de gebruiker terwijl het test station wordt ingericht. Dit veld ondersteunt HTML als u opgemaakte inhoud wilt leveren.
- **Gebruikers handleiding**: Upload een gedetailleerde gebruikers handleiding (bestand van het type. PDF) waarmee u kunt testen of gebruikers uw app kunnen gebruiken.
- **Demo video testen**: Upload eventueel een video met een demonstratie van uw app.

Verleen AppSource toestemming voor het inrichten en verwijderen van test drive-gebruikers in uw Tenant met behulp van de [hier](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)bevindene instructies.

In deze stap genereert u de \'Azure AD-App-ID @ no__t-1 en \'Azure AD-App-sleutel @ no__t-3-waarden die hieronder worden genoemd.

Geef de volgende velden op in de sectie \'Technical-configuratie @ no__t-1:

- **Type van het test station**: Kies \'Microsoft die wordt gehost (voor beeld Dynamics 365 voor Customer engagement). Dit geeft aan dat micro soft de service zal hosten en onderhouden die de inrichting van de gebruiker van het test station en het ongedaan maken van de inrichting uitvoert.
- **Maximum aantal gelijktijdige test stations**: Stel dit veld in op het aantal gelijktijdige gebruikers dat op een bepaald moment een actief test station kan hebben. Elke gebruiker gebruikt een Dynamics-licentie terwijl hun test station actief is. u moet er dus voor zorgen dat u ten minste beschikt over de vele Dynamics-licenties die beschikbaar zijn voor gebruikers van het test station. Aanbevolen waarde van 3-5.
- **Duur van test station (uren)** : Stel dit veld in op het aantal uren dat de gebruikers testen station actief moeten zijn. Na dit aantal uur wordt de inrichting van de gebruiker ongedaan gemaakt van uw Tenant. Aanbevolen waarde van 2-24 uur, afhankelijk van de complexiteit van uw app. De gebruiker kan altijd een andere test schijf aanvragen als deze niet meer actief zijn en het test station opnieuw willen openen.
- **URL van instantie**: Geef een URL op waarmee de gebruiker van het test station voor het eerst wordt genavigeerd wanneer het test station wordt gestart. Dit is doorgaans de URL van uw Dynamics 365-exemplaar waarop uw app en voorbeeld gegevens zijn geïnstalleerd. Voorbeeld waarde: https: \//Testdrive. CRM. Dynamics. com
- **Tenant-id voor Azure AD**: Geef de ID op van de Azure-Tenant voor uw Dynamics 365-exemplaar. Als u deze waarde wilt ophalen, meldt u zich aan bij Azure Portal en navigeert u naar \'Azure Active Directory @ no__t-1 @ no__t-2 @ no__t-3. Selecteer Eigenschappen in menu Blade-\> de map-ID kopiëren. Voorbeeld waarde: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure AD-App-ID**: ID van de Azure AD-app die u hebt gemaakt in stap 7. \ voorbeeld waarde: 53852862-a2ae-4e43-9461-faa49650a096
- **Azure AD-App sleutel**: Geheim voor de Azure AD-app die u hebt gemaakt in stap 7. \ voorbeeld waarde: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Azure AD-Tenant naam**: Geef de naam op van de Azure-Tenant voor uw Dynamics 365-exemplaar. Gebruik de indeling van \<tenantname. \>onmicrosoft.com. Voorbeeld waarde: testdrive.onmicrosoft.com
- **URL**van de Web-API voor het exemplaar: Geef de URL van de Web-API voor uw Dynamics 365-exemplaar op. U kunt deze waarde ophalen door u aan te melden bij uw micro soft Dynamics 365-exemplaar en te navigeren naar setting-\> Customization-\> Developer Resources-\>-exemplaar Web-API (kopieer deze URL). Voorbeeld waarde: https: \//Testdrive. CRM. Dynamics. com/API/data/v 9.0 
- **Rolnaam**: Geef de naam op van de aangepaste Dynamics 365-beveiligingsrol die u hebt gemaakt voor het test station. Dit is de rol die wordt toegewezen aan gebruikers tijdens hun test station. Voorbeeld waarde: testdriverole

## <a name="next-steps"></a>Volgende stappen

Wanneer u klaar bent met het **publiceren** van uw aanbieding, hebt u een **voor beeld** van uw aanbieding als uw app de certificering heeft door gegeven. Start een test station in de gebruikers interface en controleer of de test stations correct worden uitgevoerd. Zodra u vertrouwd bent met uw preview-aanbieding, is het tijd om **Live te gaan!**
