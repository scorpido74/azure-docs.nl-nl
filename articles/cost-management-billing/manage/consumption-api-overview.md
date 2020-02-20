---
title: Overzicht van API voor Azure-gebruiksgegevens
description: Lees hoe u met de API's voor Azure-gebruiksgegevens programmatisch toegang kunt krijgen tot de kosten en gebruiksgegevens van uw Azure-resources.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.topic: reference
ms.workload: billing
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 68d838bf6d2df72299df2796f3cc47c463ba95be
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200894"
---
# <a name="azure-consumption-api-overview"></a>Overzicht van API voor Azure-gebruiksgegevens

De API's voor Azure-gebruiksgegevens bieden u programmatische toegang tot de kosten en gebruiksgegevens van uw Azure-resources. Deze API's ondersteunen momenteel alleen Enterprise-enrollments en Web Direct-abonnementen (op enkele uitzonderingen na). De API's worden voortdurend bijgewerkt ter ondersteuning van andere typen Azure-abonnementen.

API's voor Azure-gebruiksgegevens bieden toegang tot:
- Enterprise- en Web Direct-klanten
    - Gebruiksgegevens
    - Marketplace-kosten
    - Aanbevelingen voor reserveringen
    - Reserveringsdetails
    - Reserveringssamenvattingen
- Alleen voor Enterprise-klanten
    - Prijzenoverzicht
    - Budgetten
    - Tegoeden

## <a name="usage-details-api"></a>API voor gedetailleerde gebruiksgegevens

Gebruik de API voor gedetailleerde gebruiksgegevens om kosten- en gebruiksgegevens voor alle resources van de eerste partij van Azure op te vragen. U ontvangt de informatie in de vorm van records met gedetailleerde gebruiksgegevens, die momenteel één keer per meter per resource per dag worden verzonden. De informatie kan worden gebruikt om de totale kosten voor alle resources te berekenen of om de kosten of het gebruik van specifieke resources nader te onderzoeken.

De API omvat:

-   **Verbruiksgegevens op meterniveau**: bekijk gegevens, waaronder de gebruikskosten, de meter waardoor de kosten gegenereerd zijn, en de Azure-resource waarop de kosten betrekking hebben. Alle gebruiksdetailrecords worden toegewezen aan een dagelijkse bucket.
-   **Op rollen gebaseerd toegangsbeheer van Azure**: configureer toegangsbeleid via de [Azure-portal](https://portal.azure.com), de [Azure-opdrachtregelinterface](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) of [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) om op te geven welke gebruikers of toepassingen toegang kunnen krijgen tot de gebruiksgegevens van het abonnement. Aanroepers moeten standaard Azure Active Directory-tokens gebruiken voor verificatie. Voeg de aanroeper toe aan de rol Lezer van facturering, Lezer, Eigenaar of Inzender om toegang te krijgen tot de gebruiksgegevens voor een specifiek Azure-abonnement.
-   **Filteren**: verfijn de API-resultatenset tot een kleinere set van gebruiksdetailrecords met behulp van de volgende filters:
    - Einde/begin van gebruik
    - Resourcegroep
    - Resourcenaam
-   **Gegevensaggregatie**: gebruik OData om expressies toe te passen om gebruiksgegevens te aggregeren op tags of filtereigenschappen
-   **Gebruiksgegevens voor verschillende typen aanbiedingen**: de informatie over gebruiksgegevens is momenteel beschikbaar voor Enterprise- en Web direct-klanten.

Zie de technische specificatie van de [API voor gedetailleerde gebruiksgegevens](https://docs.microsoft.com/rest/api/consumption/usagedetails) voor meer informatie.

## <a name="marketplace-charges-api"></a>API voor Marketplace-kosten

Met de API voor Marketplace-kosten kunt u kosten- en gebruiksgegevens voor alle Marketplace-resources opvragen (Azure-aanbiedingen van derden). Deze gegevens kunnen worden gebruikt om de totale kosten voor alle Marketplace-resources te berekenen of om de kosten of het gebruik van specifieke resources nader te onderzoeken.

De API omvat:

-   **Verbruiksgegevens op meterniveau**: bekijk gegevens, waaronder de Marketplace-gebruikskosten, de meter waardoor de kosten gegenereerd zijn, en de resource waarop de kosten betrekking hebben. Alle gebruiksdetailrecords worden toegewezen aan een dagelijkse bucket.
-   **Op rollen gebaseerd toegangsbeheer van Azure**: configureer toegangsbeleid via de [Azure-portal](https://portal.azure.com), de [Azure-opdrachtregelinterface](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) of [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) om op te geven welke gebruikers of toepassingen toegang kunnen krijgen tot de gebruiksgegevens van het abonnement. Aanroepers moeten standaard Azure Active Directory-tokens gebruiken voor verificatie. Voeg de aanroeper toe aan de rol Lezer van facturering, Lezer, Eigenaar of Inzender om toegang te krijgen tot de gebruiksgegevens voor een specifiek Azure-abonnement.
-   **Filteren**: verfijn de API-resultatenset tot een kleinere set van Marketplace-records met behulp van de volgende filters:
    - Begin/einde van gebruik
    - Resourcegroep
    - Resourcenaam
-   **Gebruiksgegevens voor verschillende typen aanbiedingen**: de Marketplace-informatie is momenteel beschikbaar voor Enterprise- en Web direct-klanten.

Zie de technische specificatie van de [API voor Marketplace-kosten](https://docs.microsoft.com/rest/api/consumption/marketplaces) voor meer informatie.

## <a name="balances-api"></a>API voor saldi

Enterprise-klanten kunnen deze API gebruiken om een maandelijks overzicht te krijgen van informatie over saldi, nieuwe aankopen, Azure Marketplace-servicekosten, correcties en overschrijdingskosten. U kunt deze informatie ophalen voor de huidige factureringsperiode of een periode in het verleden. Ondernemingen kunnen deze gegevens gebruiken om handmatig berekende kosten te vergelijken met de kosten in het overzicht. Deze API biedt geen specifieke informatie over resources en ook geen geaggregeerde weergave van kosten.

De API omvat:

-   **Op rollen gebaseerd toegangsbeheer van Azure**: configureer toegangsbeleid via de [Azure-portal](https://portal.azure.com), de [Azure-opdrachtregelinterface](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) of [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) om op te geven welke gebruikers of toepassingen toegang kunnen krijgen tot de gebruiksgegevens van het abonnement. Aanroepers moeten standaard Azure Active Directory-tokens gebruiken voor verificatie. Voeg de aanroeper toe aan de rol Lezer van facturering, Lezer, Eigenaar of Inzender om toegang te krijgen tot de gebruiksgegevens voor een specifiek Azure-abonnement.
-   **Alleen voor Enterprise-klanten**: deze API is alleen beschikbaar voor EA-klanten.
    - Klanten moeten beschikken over Enterprise-beheerdersmachtigingen om deze API te kunnen aanroepen

Zie de technische specificatie van de [API voor saldi](https://docs.microsoft.com/rest/api/consumption/balances) voor meer informatie.

## <a name="budgets-api"></a>API voor budgetten

Enterprise-klanten kunnen deze API gebruiken om kosten- of gebruiksbudgetten te maken voor resources, resourcegroepen of factureringsmeters. Zodra deze informatie is vastgesteld, kunnen waarschuwingen worden geconfigureerd om te worden gewaarschuwd wanneer door de gebruiker gedefinieerde budgetdrempels worden overschreden.

De API omvat:

-   **Op rollen gebaseerd toegangsbeheer van Azure**: configureer toegangsbeleid via de [Azure-portal](https://portal.azure.com), de [Azure-opdrachtregelinterface](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) of [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) om op te geven welke gebruikers of toepassingen toegang kunnen krijgen tot de gebruiksgegevens van het abonnement. Aanroepers moeten standaard Azure Active Directory-tokens gebruiken voor verificatie. Voeg de aanroeper toe aan de rol Lezer van facturering, Lezer, Eigenaar of Inzender om toegang te krijgen tot de gebruiksgegevens voor een specifiek Azure-abonnement.
-   **Alleen voor Enterprise-klanten**: deze API is alleen beschikbaar voor EA-klanten.
-   **Configureerbare meldingen**: geef een of meer gebruikers op die moeten worden gewaarschuwd als het budget wordt overschreden.
-   **Op gebruik of kosten gebaseerde budgetten**: maak uw budget op basis van het verbruik of de kosten, al naargelang de behoeften voor uw scenario.
-   **Filteren**: filter uw budget tot een kleinere subset van resources met behulp van de volgende configureerbare filters
    - Resourcegroep
    - Resourcenaam
    - Meter
-   **Configureerbare tijdsperioden voor budgetten**: geef op hoe vaak het budget opnieuw moet worden ingesteld en hoe lang het budget geldig is.

Zie de technische specificatie van de [API voor budgetten](https://docs.microsoft.com/rest/api/consumption/budgets) voor meer informatie.

## <a name="reservation-recommendations-api"></a>API voor aanbevelingen voor reserveringen

Gebruik deze API om aanbevelingen te krijgen voor het kopen van gereserveerde instanties van virtuele machines. Aanbevelingen zijn bedoeld om klanten de verwachte kostenbesparingen en aankoopbedragen te laten analyseren.

De API omvat:

-   **Op rollen gebaseerd toegangsbeheer van Azure**: configureer toegangsbeleid via de [Azure-portal](https://portal.azure.com), de [Azure-opdrachtregelinterface](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) of [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) om op te geven welke gebruikers of toepassingen toegang kunnen krijgen tot de gebruiksgegevens van het abonnement. Aanroepers moeten standaard Azure Active Directory-tokens gebruiken voor verificatie. Voeg de aanroeper toe aan de rol Lezer van facturering, Lezer, Eigenaar of Inzender om toegang te krijgen tot de gebruiksgegevens voor een specifiek Azure-abonnement.
-   **Filteren**: verfijn uw aanbevelingsresultaten met behulp van de volgende filters:
    - Bereik
    - Bewaarperiode
-   **Reserveringsgegevens voor verschillende typen aanbiedingen**: de reserveringsinformatie is momenteel beschikbaar voor Enterprise- en Web direct-klanten.

Zie de technische specificatie van de [API voor aanbevelingen voor reserveringen](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) voor meer informatie.

## <a name="reservation-details-api"></a>API voor reserveringsdetails

Gebruik deze API om informatie over eerder aangeschafte VM-reserveringen te bekijken, bijvoorbeeld om het gereserveerde verbruik te vergelijken met het werkelijke verbruik. U kunt detailgegevens per VM-niveau weergeven.

De API omvat:

-   **Op rollen gebaseerd toegangsbeheer van Azure**: configureer toegangsbeleid via de [Azure-portal](https://portal.azure.com), de [Azure-opdrachtregelinterface](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) of [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) om op te geven welke gebruikers of toepassingen toegang kunnen krijgen tot de gebruiksgegevens van het abonnement. Aanroepers moeten standaard Azure Active Directory-tokens gebruiken voor verificatie. Voeg de aanroeper toe aan de rol Lezer van facturering, Lezer, Eigenaar of Inzender om toegang te krijgen tot de gebruiksgegevens voor een specifiek Azure-abonnement.
-   **Filteren**: verfijn de API-resultatenset tot een kleinere set van reserveringen met behulp van het volgende filter:
    - Datumbereik
-   **Reserveringsgegevens voor verschillende typen aanbiedingen**: de reserveringsinformatie is momenteel beschikbaar voor Enterprise- en Web direct-klanten.

Zie de technische specificatie van de [API voor reserveringsdetails](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) voor meer informatie.

## <a name="reservation-summaries-api"></a>API voor reserveringssamenvattingen

Gebruik deze API om samengevatte informatie over eerder aangeschafte VM-reserveringen te bekijken, bijvoorbeeld om het gereserveerde verbruik te vergelijken met het werkelijke verbruik in de aggregatie.

De API omvat:

-   **Op rollen gebaseerd toegangsbeheer van Azure**: configureer toegangsbeleid via de [Azure-portal](https://portal.azure.com), de [Azure-opdrachtregelinterface](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) of [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) om op te geven welke gebruikers of toepassingen toegang kunnen krijgen tot de gebruiksgegevens van het abonnement. Aanroepers moeten standaard Azure Active Directory-tokens gebruiken voor verificatie. Voeg de aanroeper toe aan de rol Lezer van facturering, Lezer, Eigenaar of Inzender om toegang te krijgen tot de gebruiksgegevens voor een specifiek Azure-abonnement.
-   **Filteren**: verfijn uw resultaten voor het dagelijkse overzicht met het volgende filter:
    - Gebruiksdatum
-   **Reserveringsgegevens voor verschillende typen aanbiedingen**: de reserveringsinformatie is momenteel beschikbaar voor Enterprise- en Web direct-klanten.
-   **Dagelijkse of maandelijkse aggregaties**: aanroepers kunnen opgeven of de reserveringssamenvattingsgegevens worden opgenomen in het dagelijkse of maandelijkse overzicht.

Zie de technische specificatie van de [API voor reserveringssamenvattingen](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) voor meer informatie.

## <a name="price-sheet-api"></a>API voor prijzenoverzichten
Enterprise-klanten kunnen deze API gebruiken om de aangepaste prijzen voor alle meters op te halen. Ondernemingen kunnen deze informatie gebruiken in combinatie met de gebruiksdetails en Marketplace-gebruiksgegevens om kostenberekeningen uit te voeren op basis van gebruiks- en Marketplace-gegevens.

De API omvat:

-   **Op rollen gebaseerd toegangsbeheer van Azure**: configureer toegangsbeleid via de [Azure-portal](https://portal.azure.com), de [Azure-opdrachtregelinterface](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) of [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) om op te geven welke gebruikers of toepassingen toegang kunnen krijgen tot de gebruiksgegevens van het abonnement. Aanroepers moeten standaard Azure Active Directory-tokens gebruiken voor verificatie. Voeg de aanroeper toe aan de rol Lezer van facturering, Lezer, Eigenaar of Inzender om toegang te krijgen tot de gebruiksgegevens voor een specifiek Azure-abonnement.
-   **Alleen voor Enterprise-klanten**: deze API is alleen beschikbaar voor EA-klanten. Web Direct-klanten dienen de RateCard-API te gebruiken voor het ophalen van prijzen.

Zie de technische specificatie van de [API voor prijzenoverzichten](https://docs.microsoft.com/rest/api/consumption/pricesheet) voor meer informatie.

## <a name="scenarios"></a>Scenario's

Hier volgen enkele scenario's die mogelijk zijn dankzij de API's voor gebruiksgegevens:

-   **Factuurafstemming**: heeft Microsoft het juiste bedrag in rekening gebracht?  Wat is mijn factuur en kan ik deze zelf berekenen?
-   **Doorbelasting van kosten**: ik weet nu hoeveel kosten er in rekening worden gebracht, maar wie in mijn organisatie moet die betalen?
-   **Optimalisatie van kosten**: ik weet hoeveel er in rekening is gebracht… Hoe kan ik meer halen uit mijn investering in Azure?
-   **Tracering van kosten**: ik wil het verloop van mijn Azure-gebruik en de bijbehorende kosten bekijken. Wat zijn de trends? Hoe kan het beter?
-   **Azure-uitgaven per maand**: welk bedrag heb ik deze maand al verbruikt? Moet ik mijn uitgaven en/of gebruik van Azure aanpassen? Wanneer in de maand is mijn Azure-verbruik het hoogst?
-   **Waarschuwingen instellen**: ik wil waarschuwingen instellen op basis van het resourceverbruik, of financiële waarschuwingen op basis van een budget.

## <a name="next-steps"></a>Volgende stappen

- Zie het [overzicht van Azure Billing API's](usage-rate-card-overview.md) voor informatie over het gebruik van Azure Billing-API's om programmatisch inzicht te krijgen in uw Azure-gebruik.
