---
title: Toestemmingservaringen voor Azure AD-apps
titleSuffix: Microsoft identity platform
description: Meer informatie over de azure AD-toestemmingservaringen om te zien hoe u deze gebruiken bij het beheren en ontwikkelen van toepassingen op Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.openlocfilehash: c9b449b65a8f8def9dc28a668cd9ee3671124cb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484499"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Inzicht in ervaringen met Azure AD-toepassingtoestemming

Meer informatie over de gebruikerservaring van azure active directory (Azure AD) voor toestemming van de toepassing. Zo u op intelligente wijze applicaties voor uw organisatie beheren en/of applicaties ontwikkelen met een meer naadloze toestemmingservaring.

## <a name="consent-and-permissions"></a>Toestemming en machtigingen

Toestemming is het proces van een gebruiker die namens hen toestemming verleent voor een toepassing om toegang te krijgen tot beveiligde bronnen. Een beheerder of gebruiker kan om toestemming worden gevraagd om toegang te verlenen tot zijn/haar organisatie/individuele gegevens.

De werkelijke gebruikerservaring van het verlenen van toestemming zal verschillen afhankelijk van het beleid dat is ingesteld op de tenant van de gebruiker, het [bevoegdheidsbereik](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent) (of de rol) van de gebruiker en het type machtigingen dat door de clienttoepassing wordt aangevraagd. Dit betekent dat toepassingsontwikkelaars en tenantbeheerders enige controle hebben over de toestemmingservaring. Beheerders hebben de flexibiliteit om beleid in te stellen en uit te schakelen op een tenant of app om de toestemmingservaring in hun tenant te beheren. Toepassingsontwikkelaars kunnen dicteren welke soorten machtigingen worden aangevraagd en of ze gebruikers willen begeleiden via de gebruikerstoestemmingsstroom of de beheerderstoestemmingsstroom.

- **Gebruikerstoestemmingsstroom** is wanneer een toepassingsontwikkelaar gebruikers naar het autorisatieeindpunt leidt met de bedoeling om alleen toestemming voor de huidige gebruiker vast te leggen.
- **Beheerderstoestemmingsstroom** is wanneer een toepassingsontwikkelaar gebruikers naar het eindpunt voor beheerderstoestemming leidt met de bedoeling om toestemming voor de gehele tenant vast te leggen. Om ervoor te zorgen dat de beheerdersmachtigingsstroom goed `RequiredResourceAccess` werkt, moeten toepassingsontwikkelaars alle machtigingen in de eigenschap in het toepassingsmanifest vermelden. Zie [Toepassingsmanifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)voor meer informatie .

## <a name="building-blocks-of-the-consent-prompt"></a>Bouwstenen van de toestemmingsprompt

De toestemmingsprompt is bedoeld om ervoor te zorgen dat gebruikers voldoende informatie hebben om te bepalen of ze erop vertrouwen dat de clienttoepassing namens hen toegang heeft tot beveiligde bronnen. Inzicht in de bouwstenen zal gebruikers helpen toestemming te verlenen om beter geïnformeerde beslissingen te nemen en het zal ontwikkelaars helpen betere gebruikerservaringen op te bouwen.

In het volgende diagram en de volgende tabel vindt u informatie over de bouwstenen van de toestemmingsprompt.

![Bouwstenen van de toestemmingsprompt](./media/application-consent-experience/consent_prompt.png)

| # | Onderdeel | Doel |
| ----- | ----- | ----- |
| 1 | Gebruikers-id | Deze id vertegenwoordigt de gebruiker die de clienttoepassing vraagt om toegang te krijgen tot beveiligde bronnen namens. |
| 2 | Titel | De titel wordt gewijzigd op basis van de vraag of de gebruikers door de toestemmingsstroom van de gebruiker of beheerder gaan. In de gebruikerstoestemmingsstroom wordt de titel 'Gevraagde machtigingen' weergegeven, terwijl in de beheerdersmachtigingsstroom de titel een extra regel 'Accepteren voor uw organisatie' heeft. |
| 3 | App-logo | Deze afbeelding moet gebruikers helpen een visuele aanwijzing te hebben van de vraag of deze app de app is die ze wilden openen. Deze afbeelding wordt geleverd door toepassingsontwikkelaars en het eigendom van deze afbeelding is niet gevalideerd. |
| 4 | Naam van app | Deze waarde moet gebruikers informeren welke toepassing om toegang tot hun gegevens vraagt. Let op: deze naam wordt geleverd door de ontwikkelaars en het eigendom van deze app-naam is niet gevalideerd. |
| 5 | Uitgeversdomein | Deze waarde moet gebruikers een domein bieden dat ze mogelijk kunnen evalueren op betrouwbaarheid. Dit domein wordt geleverd door de ontwikkelaars en het eigendom van dit uitgeversdomein wordt gevalideerd. |
| 6 | Machtigingen | Deze lijst bevat de machtigingen die worden aangevraagd door de clienttoepassing. Gebruikers moeten altijd evalueren welke soorten machtigingen worden gevraagd om te begrijpen welke gegevens de clienttoepassing namens hen kan openen als ze dit accepteren. Als toepassingsontwikkelaar is het het beste om toegang te vragen, tot de machtigingen met de minste bevoegdheden. |
| 7 | Beschrijving van machtigingen | Deze waarde wordt geleverd door de service die de machtigingen blootstelt. Als u de machtigingsbeschrijvingen wilt bekijken, moet u de chevron naast de machtiging schakelen. |
| 8 | App-voorwaarden | Deze voorwaarden bevatten links naar de servicevoorwaarden en de privacyverklaring van de toepassing. De uitgever is verantwoordelijk voor het uiteenzetten van hun regels in hun servicevoorwaarden. Daarnaast is de uitgever verantwoordelijk voor het bekendmaken van de manier waarop ze gebruikersgegevens gebruiken en delen in hun privacyverklaring. Als de uitgever geen koppelingen naar deze waarden voor toepassingen met meerdere tenant's verstrekt, wordt er een waarschuwing voor de toestemmingsprompt weergegeven. |
| 9 | https://myapps.microsoft.com | Dit is de koppeling waar gebruikers alle niet-Microsoft-toepassingen die momenteel toegang hebben tot hun gegevens kunnen controleren en verwijderen. |

## <a name="common-consent-scenarios"></a>Scenario's voor algemene toestemming

Dit zijn de toestemmingservaringen die een gebruiker kan zien in de gemeenschappelijke toestemmingsscenario's:

1. Personen die toegang hebben tot een app die hen doorverwijst naar de toestemmingsstroom van de gebruiker, terwijl ze een machtigingsset vereisen die binnen hun bevoegdheidvalt.
    
    1. Beheerders zien een extra controle op de traditionele toestemmingsprompt waarmee ze toestemming kunnen geven namens de hele tenant. Het besturingselement wordt standaard uitgeschakeld, dus alleen wanneer beheerders het vakje expliciet inschakelen, wordt toestemming verleend namens de gehele tenant. Vanaf vandaag wordt dit selectievakje alleen weergegeven voor de rol Globale beheerder, zodat Cloud Admin en App Admin dit selectievakje niet meer zien.

        ![Toestemmingsprompt voor scenario 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Gebruikers zien de traditionele toestemmingsprompt.

        ![Toestemmingsprompt voor scenario 1b](./media/application-consent-experience/consent_prompt_1b.png)

2. Personen die toegang hebben tot een app waarvoor ten minste één machtiging nodig is die buiten hun bevoegdheidvalt.
    1. Beheerders zien dezelfde prompt als 1.i hierboven weergegeven.
    2. Gebruikers worden geblokkeerd voor het verlenen van toestemming voor de toepassing, en ze zullen worden verteld om hun admin te vragen om toegang tot de app. 
                
        ![Toestemmingsprompt voor scenario 1b](./media/application-consent-experience/consent_prompt_2b.png)

3. Personen die navigeren of worden doorverwezen naar de beheerderstoestemmingsstroom.
    1. Beheerders zien de toestemmingsprompt voor beheerders. De titel en de machtigingsbeschrijvingen zijn op deze prompt gewijzigd, de wijzigingen benadrukken het feit dat het accepteren van deze prompt de app toegang geeft tot de gevraagde gegevens namens de gehele tenant.
        
        ![Toestemmingsprompt voor scenario 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Niet-beheerders krijgen hetzelfde scherm te zien als hierboven 2.ii.

## <a name="next-steps"></a>Volgende stappen
- Ontvang stap voor stap een overzicht van [hoe het Azure AD-toestemmingskader toestemming implementeert.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- Voor meer diepte leest u [hoe een multi-tenanttoepassing het toestemmingskader kan gebruiken](active-directory-devhowto-multi-tenant-overview.md) om toestemming voor 'gebruiker' en 'beheerder' te implementeren, ter ondersteuning van geavanceerdere toepassingspatronen met meerdere lagen.
- Meer informatie over [het configureren van het uitgeversdomein van de app](howto-configure-publisher-domain.md).
