---
title: Aan de slag - Microsoft Threat Modeling Tool - Azure | Microsoft Documenten
description: Dit is een dieper overzicht van de Threat Modeling Tool in actie.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 1454826095bcced9b20935405c0befd5a1ed1ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728222"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Aan de slag met de Threat Modeling Tool

De Microsoft Threat Modeling Tool 2018 werd uitgebracht als GA in september 2018 als een gratis **[click-to-download.](https://aka.ms/threatmodelingtool)** De verandering in het leveringsmechanisme stelt ons in staat om de nieuwste verbeteringen en bugfixes naar klanten te pushen elke keer dat ze de tool openen, waardoor het gemakkelijker te onderhouden en te gebruiken is.
Dit artikel neemt u mee door het proces van aan de slag met de Microsoft SDL threat modeling aanpak en laat u zien hoe u de tool gebruiken om grote bedreigingsmodellen te ontwikkelen als een ruggengraat van uw beveiligingsproces.

Dit artikel bouwt voort op bestaande kennis van de SDL threat modeling aanpak. Voor een snelle beoordeling, verwijzen naar **[Threat Modeling Web Applications](https://msdn.microsoft.com/library/ms978516.aspx)** en een gearchiveerde versie van Uncover Security Flaws Using the STRIDE **[Approach](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** MSDN artikel gepubliceerd in 2006.

Om snel samen te vatten, de aanpak omvat het maken van een diagram, het identificeren van bedreigingen, het beperken van hen en valideren van elke mitigatie. Hier volgt een diagram dat dit proces belicht:

![SDL-proces](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Het proces voor het modelleren van bedreigingen starten

Wanneer u de Threat Modeling Tool start, zult u een paar dingen opmerken, zoals te zien op de foto:

![Lege startpagina](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Sectie Bedreigingsmodel

| Onderdeel                                   | Details                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Knop Feedback, suggesties en problemen** | Neemt u de **[MSDN Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** voor alle dingen SDL. Het geeft u de mogelijkheid om te lezen door wat andere gebruikers doen, samen met oplossingen en aanbevelingen. Als u nog steeds niet vinden wat tmtextsupport@microsoft.com u zoekt, u een e-mail sturen naar ons ondersteuningsteam om u te helpen                                                                                                                            |
| **Een model maken**                          | Hiermee opent u een leeg canvas om uw diagram te tekenen. Zorg ervoor dat u selecteert welke sjabloon u voor uw model wilt gebruiken                                                                                                                                                                                                                                                                                                                                                                       |
| **Sjabloon voor nieuwe modellen**                 | U moet selecteren welke sjabloon u wilt gebruiken voordat u een model maakt. Onze belangrijkste sjabloon is de Azure Threat Model Template, die Azure-specifieke stencils, bedreigingen en mitigaties bevat. Selecteer voor generieke modellen de SDL TM Knowledge Base in het vervolgkeuzemenu. Wilt u uw eigen sjabloon maken of een nieuwe sjabloon indienen voor alle gebruikers? Bekijk onze **[Template Repository](https://github.com/Microsoft/threat-modeling-templates)** GitHub-pagina voor meer informatie                              |
| **Een model openen**                            | <p>Hiermee opent u eerder opgeslagen bedreigingsmodellen. De functie Onlangs geopende modellen is geweldig als u uw meest recente bestanden moet openen. Wanneer u de boventoon over de selectie houdt, vindt u twee manieren om modellen te openen:</p><p><ul><li>Open vanaf deze computer – klassieke manier om een bestand te openen met behulp van lokale opslag</li><li>Open vanuit OneDrive – teams kunnen mappen in OneDrive gebruiken om al hun bedreigingsmodellen op één locatie op te slaan en te delen om de productiviteit en samenwerking te verhogen</li></ul></p> |
| **Handleiding aan de slag**                   | Hiermee opent u de hoofdpagina **[van microsoft threat modeling tool](threat-modeling-tool.md)**                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Sjabloonsectie

| Onderdeel               | Details                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Nieuwe sjabloon maken** | Hiermee opent u een lege sjabloon waarop u bouwen. Tenzij u uitgebreide kennis hebt in het bouwen van sjablonen vanaf nul, raden wij u aan om te bouwen van bestaande |
| **Sjabloon openen**       | Hiermee opent u bestaande sjablonen om wijzigingen aan te brengen in                                                                                                              |

Het Threat Modeling Tool-team werkt voortdurend aan het verbeteren van de functionaliteit en ervaring van de tool. In de loop van het jaar kunnen enkele kleine wijzigingen plaatsvinden, maar alle belangrijke wijzigingen vereisen herschrijft in de gids. Raadpleeg het vaak om ervoor te zorgen dat u de laatste aankondigingen krijgt.

## <a name="building-a-model"></a>Een model bouwen

In deze sectie volgen we:

- Cristina (een ontwikkelaar)
- Ricardo (programmamanager) en
- Ashish (een tester)

Ze gaan door het proces van de ontwikkeling van hun eerste bedreiging model.

> Ricardo: Hallo Cristina, ik werkte aan de bedreiging model diagram en wilde ervoor zorgen dat we de details goed. Kun je me helpen het na te kijken?
> Cristina: Absoluut. Laten we dit eens bekijken.
> Ricardo opent de tool en deelt zijn scherm met Cristina.

![Basisbedreigingsmodel](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: Ok, ziet er eenvoudig uit, maar kun je me er doorheen helpen?
> Ricardo: Zeker! Hier is de verdeling:
> - Onze menselijke gebruiker wordt getekend als een externe entiteit - een vierkant
> - Ze sturen opdrachten naar onze webserver: de cirkel
> - De webserver raadpleegt een database (twee parallelle lijnen)

Wat Ricardo net toonde Cristina is een DFD, kort voor **[Data Flow Diagram](https://en.wikipedia.org/wiki/Data_flow_diagram)**. Met de Threat Modeling Tool kunnen gebruikers vertrouwensgrenzen opgeven, aangegeven door de rode stippellijnen, om aan te geven waar verschillende entiteiten de controle hebben. IT-beheerders hebben bijvoorbeeld een Active Directory-systeem nodig voor verificatiedoeleinden, zodat de Active Directory buiten hun controle valt.

> Cristina: Ziet er recht uit voor mij. Hoe zit het met de bedreigingen?
> Ricardo: Laat me het je laten zien.

## <a name="analyzing-threats"></a>Bedreigingen analyseren

Zodra hij klikt op de analyse weergave van het pictogram menu selectie (bestand met vergrootglas), wordt hij meegenomen naar een lijst van gegenereerde bedreigingen de Threat Modeling Tool gevonden op basis van de standaard sjabloon, die gebruik maakt van de SDL aanpak genaamd **[STRIDE (Spoofing, Tampering, Info Disclosure, Repudiation, Denial of Service en Elevation of Privilege)](https://en.wikipedia.org/wiki/STRIDE_(security))**. Het idee is dat software valt onder een voorspelbare set van bedreigingen, die kan worden gevonden met behulp van deze 6 categorieën.

Deze aanpak is als het beveiligen van uw huis door ervoor te zorgen dat elke deur en raam heeft een vergrendeling mechanisme op zijn plaats voor het toevoegen van een alarmsysteem of jagen na de dief.

![Basisbedreigingen](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo begint met het selecteren van het eerste item op de lijst. Dit is wat er gebeurt:

Ten eerste wordt de interactie tussen de twee stencils verbeterd

![Interactie](./media/threat-modeling-tool-getting-started/interaction.png)

Ten tweede wordt aanvullende informatie over de bedreiging weergegeven in het venster Bedreigingseigenschappen

![Interactiegegevens](./media/threat-modeling-tool-getting-started/interactioninfo.png)

De gegenereerde dreiging helpt hem te begrijpen potentiële ontwerpfouten. De STRIDE-categorisatie geeft hem een idee over mogelijke aanvalsvectoren, terwijl de aanvullende beschrijving hem precies vertelt wat er mis is, samen met mogelijke manieren om het te beperken. Hij kan bewerkbare velden gebruiken om notities te schrijven in de motiveringsdetails of prioriteitsbeoordelingen te wijzigen, afhankelijk van de bugbalk van zijn organisatie.

Azure-sjablonen bevatten aanvullende details om gebruikers te helpen niet alleen te begrijpen wat er mis is, maar ook hoe ze dit kunnen oplossen door beschrijvingen, voorbeelden en hyperlinks toe te voegen aan Azure-specifieke documentatie.

De beschrijving deed hem beseffen het belang van het toevoegen van een authenticatie mechanisme om te voorkomen dat gebruikers worden vervalst, het onthullen van de eerste bedreiging te werken. Een paar minuten in de discussie met Cristina, begrepen ze het belang van de uitvoering van toegangscontrole en rollen. Ricardo vulde een aantal snelle notities in om ervoor te zorgen dat deze werden geïmplementeerd.

Toen Ricardo inging op de bedreigingen onder Information Disclosure, realiseerde hij zich dat het toegangscontroleplan een aantal alleen-lezen accounts vereiste voor audit en het genereren van rapporten. Hij vroeg zich af of dit een nieuwe bedreiging zou moeten zijn, maar de beperkingen waren hetzelfde, dus merkte hij de dreiging dienovereenkomstig op.
Hij dacht ook over informatie openbaarmaking een beetje meer en besefte dat de back-up tapes zouden gaan encryptie nodig, een baan voor de operations team.

Bedreigingen die niet van toepassing zijn op het ontwerp vanwege bestaande beperkingen of beveiligingsgaranties, kunnen worden gewijzigd in 'Niet van toepassing' vanuit de vervolgkeuzelijst Status. Er zijn drie andere keuzes: Niet gestart - standaard selectie, Needs Investigation - gebruikt om op te volgen op items en verzacht - zodra het volledig is gewerkt.

## <a name="reports--sharing"></a>Rapporten & delen

Zodra Ricardo gaat door de lijst met Cristina en voegt belangrijke notities, mitigaties / rechtvaardigingen, prioriteit en status veranderingen, selecteert hij Rapporten -> Maak full report-> Save Report, die print een mooi rapport voor hem om door te gaan met collega's om ervoor te zorgen dat de juiste beveiligingswerk wordt uitgevoerd.

![Interactiegegevens](./media/threat-modeling-tool-feature-overview/report.png)

Als Ricardo het bestand in plaats daarvan wil delen, kan hij dit eenvoudig doen door op te slaan in het OneDrive-account van zijn organisatie. Zodra hij dat doet, kan hij de documentlink kopiëren en delen met zijn collega's. 

## <a name="threat-modeling-meetings"></a>Vergaderingen voor het modelleren van bedreigingen

Toen Ricardo zijn bedreigingsmodel naar zijn collega stuurde via OneDrive, werd Ashish, de tester, onderwhelmed. Leek ricardo en Cristina miste een flink aantal belangrijke hoek gevallen, die gemakkelijk kunnen worden aangetast. Zijn scepsis is een aanvulling op bedreigingsmodellen.

In dit scenario, nadat Ashish het bedreigingsmodel overnam, riep hij op tot twee vergadering voor bedreigingsmodellering: één vergadering om het proces te synchroniseren en door de diagrammen te lopen en vervolgens een tweede vergadering voor bedreigingsbeoordeling en afmelding.

In de eerste vergadering, Ashish bracht 10 minuten lopen iedereen door de SDL bedreiging modellering proces. Hij trok toen het diagram van het bedreigingsmodel omhoog en begon het in detail uit te leggen. Binnen vijf minuten was een belangrijk ontbrekend onderdeel geïdentificeerd.

Een paar minuten later, Ashish en Ricardo kreeg in een uitgebreide discussie over hoe de webserver werd gebouwd. Het was niet de ideale manier voor een vergadering om verder te gaan, maar iedereen uiteindelijk het erover eens dat het ontdekken van de discrepantie vroeg zou gaan om hen tijd te besparen in de toekomst.

In de tweede vergadering, het team liep door de bedreigingen, besprak een aantal manieren om ze aan te pakken, en ondertekende af op de dreiging model. Ze controleerden het document in broncontrole en gingen verder met de ontwikkeling.

## <a name="thinking-about-assets"></a>Nadenken over activa

Sommige lezers die bedreiging gemodelleerd kunnen merken dat we niet hebben gesproken over activa op alle. We hebben ontdekt dat veel software-engineers hun software beter begrijpen dan dat ze het concept van assets begrijpen en in welke activa een aanvaller mogelijk geïnteresseerd is.

Als je gaat bedreigen model een huis, zou je kunnen beginnen met na te denken over uw familie, onvervangbare foto's of waardevolle kunstwerken. Misschien u beginnen met na te denken over wie zou kunnen inbreken en het huidige beveiligingssysteem. Of je zou kunnen beginnen met het overwegen van de fysieke kenmerken, zoals het zwembad of de veranda. Deze zijn analoog aan het denken over activa, aanvallers, of software ontwerp. Elk van deze drie benaderingen werken.

De benadering van threat modeling die we hier hebben gepresenteerd is aanzienlijk eenvoudiger dan wat Microsoft in het verleden heeft gedaan. We vonden dat de software design aanpak werkt goed voor veel teams. We hopen dat dat ook de jouwe.

## <a name="next-steps"></a>Volgende stappen

Stuur uw vragen, opmerkingen tmtextsupport@microsoft.comen zorgen naar . **[Download](https://aka.ms/threatmodelingtool)** de Threat Modeling Tool om aan de slag te gaan.
