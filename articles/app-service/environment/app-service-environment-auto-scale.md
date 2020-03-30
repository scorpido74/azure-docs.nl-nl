---
title: Automatisch schalen v1
description: Autoscaling en App Service Environment v1. Dit document is alleen bedoeld voor klanten die de verouderde v1 ASE gebruiken.
author: btardif
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4f071c0d09fc2fa97eeea45bd82228b7eb8434a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687282"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Autoscaling en App Service Environment v1

> [!NOTE]
> Dit artikel gaat over de App Service Environment v1.  Er is een nieuwere versie van de App Service-omgeving die gemakkelijker te gebruiken is en draait op een krachtigere infrastructuur. Voor meer informatie over de nieuwe versie start u met de [inleiding tot de app-serviceomgeving.](intro.md)
> 

Azure App Service-omgevingen ondersteunen *automatisch schalen.* U afzonderlijke werknemersgroepen automatisch schalen op basis van statistieken of planning.

![Opties voor automatisch schalen voor een werknemersgroep.][intro]

Automatisch schalen optimaliseert uw resourcegebruik door automatisch een App Service-omgeving te laten groeien en verkleinen om aan uw budget en of laadprofiel te voldoen.

## <a name="configure-worker-pool-autoscale"></a>Automatische schaal van werknemersgroep configureren
U hebt toegang tot de automatische schaalfunctionaliteit via het tabblad **Instellingen** van de werknemersgroep.

![Tabblad Instellingen van de groep werknemers.][settings-scale]

Vanaf daar moet de interface vrij vertrouwd zijn, omdat het dezelfde ervaring is die u ziet wanneer u een App Service-abonnement schaalt. 

![Handmatige schaalinstellingen.][scale-manual]

U ook een autoscale-profiel configureren.

![Instellingen voor automatisch schalen.][scale-profile]

Autoscale profielen zijn handig om limieten in te stellen op uw schaal. Op deze manier u een consistente prestatie-ervaring hebben door een lagere schaalwaarde (1) en een voorspelbare bestedingslimiet in te stellen door een bovengrens in te stellen (2).

![Schaalinstellingen in profiel.][scale-profile2]

Nadat u een profiel hebt gedefinieerd, u regels voor automatische schaal toevoegen om het aantal exemplaren in de werknemersgroep op te schalen of te beperken binnen de grenzen die door het profiel zijn gedefinieerd. Autoscale-regels zijn gebaseerd op statistieken.

![Schaalregel.][scale-rule]

 Elke werknemersgroep of front-endstatistieken kunnen worden gebruikt om regels voor automatische schaal te definiëren. Deze statistieken zijn dezelfde statistieken die u controleren in de grafieken van het resourceblad of waarschuwingen instellen.

## <a name="autoscale-example"></a>Voorbeeld van autoschaal
Autoscale van een App Service-omgeving kan het best worden geïllustreerd door door een scenario te lopen.

In dit artikel worden alle noodzakelijke overwegingen uitgelegd wanneer u automatisch schalen instelt. Het artikel leidt u door de interacties die in het spel komen wanneer u rekening houdt met autoscaling App Service-omgevingen die worden gehost in app-serviceomgevingen.

### <a name="scenario-introduction"></a>Scenario-introductie
Frank is een sysadmin voor een onderneming die een deel van de workloads die ze beheren heeft gemigreerd naar een App Service-omgeving.

De app-serviceomgeving is als volgt geconfigureerd voor handmatigschalen:

* **Vooreinden:** 3
* **Arbeiderspool 1**: 10
* **Arbeiderspool 2**: 5
* **Arbeiderspool 3**: 5

Worker pool 1 wordt gebruikt voor productieworkloads, terwijl groep 2 en werknemersgroep 3 worden gebruikt voor kwaliteitsborging (QA) en ontwikkelingsworkloads.

De App Service-abonnementen voor QA en dev zijn geconfigureerd op handmatige schaal. Het productie-app-serviceplan is ingesteld op automatisch schalen om te gaan met variaties in belasting en verkeer.

Frank is zeer vertrouwd met de toepassing. Ze weten dat de piekuren voor belasting tussen 9:00 en 18:00 uur liggen, omdat dit een lob-toepassing (line-of-business) is die werknemers gebruiken terwijl ze op kantoor zijn. Het gebruik daalt daarna wanneer gebruikers voor die dag worden gedaan. Buiten de piekuren is er nog steeds wat belasting omdat gebruikers de app op afstand kunnen openen met behulp van hun mobiele apparaten of thuis-pc's. Het productie-app-serviceplan is al geconfigureerd om automatisch te schalen op basis van CPU-gebruik met de volgende regels:

![Specifieke instellingen voor de LOB-app.][asp-scale]

| **Autoscale profiel – Weekdagen – App Service-abonnement** | **Autoscale profiel – Weekends – App Service plan** |
| --- | --- |
| **Naam:** Weekdagprofiel |**Naam:** Weekendprofiel |
| **Schaal door:** Plannings- en prestatieregels |**Schaal door:** Plannings- en prestatieregels |
| **Profiel:** Weekdagen |**Profiel:** Weekend |
| **Type:** Herhaling |**Type:** Herhaling |
| **Doelbereik:** 5 tot 20 exemplaren |**Doelbereik:** 3 tot 10 exemplaren |
| **Dagen:** Maandag, dinsdag, woensdag, donderdag, vrijdag |**Dagen:** Zaterdag, zondag |
| **Starttijd:** 09:00 uur |**Starttijd:** 09:00 uur |
| **Tijdzone:** UTC-08 |**Tijdzone:** UTC-08 |
|  | |
| **Regel automatisch schalen (Opschalen)** |**Regel automatisch schalen (Opschalen)** |
| **Bron:** Productie (app-serviceomgeving) |**Bron:** Productie (app-serviceomgeving) |
| **Metrische:** CPU % |**Metrische:** CPU % |
| **Werking:** Meer dan 60% |**Werking:** Meer dan 80% |
| **Duur:** 5 minuten |**Duur:** 10 minuten |
| **Tijdaggregatie:** Gemiddelde |**Tijdaggregatie:** Gemiddelde |
| **Actie:** Aantal verhogen met 2 |**Actie:** Aantal verhogen met 1 |
| **Afkoelen (minuten):** 15 |**Afkoelen (minuten):** 20 |
|  | |
| **Regel automatisch schalen (Omlaag schalen)** |**Regel automatisch schalen (Omlaag schalen)** |
| **Bron:** Productie (app-serviceomgeving) |**Bron:** Productie (app-serviceomgeving) |
| **Metrische:** CPU % |**Metrische:** CPU % |
| **Werking:** Minder dan 30% |**Werking:** Minder dan 20% |
| **Duur:** 10 minuten |**Duur:** 15 minuten |
| **Tijdaggregatie:** Gemiddelde |**Tijdaggregatie:** Gemiddelde |
| **Actie:** Aantal daling en daling met 1 |**Actie:** Aantal daling en daling met 1 |
| **Afkoelen (minuten):** 20 |**Afkoelen (minuten):** 10 |

### <a name="app-service-plan-inflation-rate"></a>App Service plan inflatie
App Service-abonnementen die zijn geconfigureerd om automatisch te schalen, doen dit tegen een maximumtarief per uur. Dit tarief kan worden berekend op basis van de waarden die zijn opgegeven op de regel automatisch schalen.

Het begrijpen en berekenen van het inflatiepercentage van het *App-serviceplan* is belangrijk voor de automatische schaal van de App-service-omgeving, omdat schaalwijzigingen in een werknemersgroep niet onmiddellijk zijn.

Het inflatiepercentage van het App-serviceplan wordt als volgt berekend:

![App Service plan inflatie berekening.][ASP-Inflation]

Op basis van de regel Autoscale – Scale Up voor het Weekday-profiel van het productie-app-serviceplan:

![App Service plan inflatie voor weekdagen op basis van Autoscale - Scale Up regel.][Equation1]

In het geval van de regel Autoscale – Scale Up voor het weekendprofiel van het productie-appserviceplan, zou de formule besluiten om:

![App Service plan inflatie voor het weekend op basis van Autoscale - Scale Up regel.][Equation2]

Deze waarde kan ook worden berekend voor scale-down bewerkingen.

Op basis van de regel Autoscale – Scale Down voor het Weekday-profiel van het productie-app-serviceplan ziet dit er als volgt uit:

![App Service plan inflatie voor weekdagen op basis van Autoscale - Scale Down regel.][Equation3]

In het geval van de regel Autoscale – Scale Down voor het weekendprofiel van het productie-app-serviceplan, zou de formule het oplossen om:  

![App Service plan inflatie voor het weekend op basis van Autoscale - Scale Down regel.][Equation4]

Het productie-app-serviceplan kan gedurende de week maximaal acht exemplaren/uur en vier exemplaren/uur in het weekend groeien. Het kan instanties vrijgeven met een maximumsnelheid van vier exemplaren/uur gedurende de week en zes exemplaren/uur in het weekend.

Als er meerdere App Service-abonnementen worden gehost in een werknemersgroep, moet u het *totale inflatiepercentage* berekenen als de som van het inflatiepercentage voor alle App-serviceplannen die worden gehost in die werknemersgroep.

![Totale inflatieberekening voor meerdere App Service-abonnementen die worden gehost in een werknemersgroep.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Het inflatiepercentage van het App-service-abonnement gebruiken om regels voor automatische schaal van werknemersgroepen te definiëren
Worker-groepen die App Service-abonnementen hosten die zijn geconfigureerd voor automatisch schalen, moeten een capaciteitsbuffer toegewezen krijgen. De buffer zorgt ervoor dat de automatische schaalbewerkingen het App Service-abonnement zo nodig kunnen laten groeien en verkleinen. De minimale buffer zou het berekende inflatiepercentage van het Totale App-serviceplan zijn.

Omdat het beperken van de app-service-omgevingsgrootte enige tijd in beslag neemt, moet elke wijziging rekening houden met verdere vraagwijzigingen die kunnen plaatsvinden terwijl een schaalbewerking aan de gang is. Om aan deze latentie tegemoet te komen, raden we u aan het berekende inflatiepercentage van het Total App Service Plan te gebruiken als het minimumaantal exemplaren dat wordt toegevoegd voor elke automatische schaalbewerking.

Met deze informatie kan Frank het volgende autoscale profiel en regels definiëren:

![Autoscale profielregels voor LOB bijvoorbeeld.][Worker-Pool-Scale]

| **Autoscale profiel – Weekdagen** | **Autoscale profiel – Weekends** |
| --- | --- |
| **Naam:** Weekdagprofiel |**Naam:** Weekendprofiel |
| **Schaal door:** Plannings- en prestatieregels |**Schaal door:** Plannings- en prestatieregels |
| **Profiel:** Weekdagen |**Profiel:** Weekend |
| **Type:** Herhaling |**Type:** Herhaling |
| **Doelbereik:** 13 tot 25 exemplaren |**Doelbereik:** 6 tot 15 exemplaren |
| **Dagen:** Maandag, dinsdag, woensdag, donderdag, vrijdag |**Dagen:** Zaterdag, zondag |
| **Starttijd:** 07:00 uur |**Starttijd:** 09:00 uur |
| **Tijdzone:** UTC-08 |**Tijdzone:** UTC-08 |
|  | |
| **Regel automatisch schalen (Opschalen)** |**Regel automatisch schalen (Opschalen)** |
| **Bron:** Arbeiderspool 1 |**Bron:** Arbeiderspool 1 |
| **Metrische:** WerknemersBeschikbaar |**Metrische:** WerknemersBeschikbaar |
| **Werking:** Minder dan 8 |**Werking:** Minder dan 3 |
| **Duur:** 20 minuten |**Duur:** 30 minuten |
| **Tijdaggregatie:** Gemiddelde |**Tijdaggregatie:** Gemiddelde |
| **Actie:** Aantal verhogen met 8 |**Actie:** Aantal verhogen met 3 |
| **Afkoelen (minuten):** 180 |**Afkoelen (minuten):** 180 |
|  | |
| **Regel automatisch schalen (Omlaag schalen)** |**Regel automatisch schalen (Omlaag schalen)** |
| **Bron:** Arbeiderspool 1 |**Bron:** Arbeiderspool 1 |
| **Metrische:** WerknemersBeschikbaar |**Metrische:** WerknemersBeschikbaar |
| **Werking:** Groter dan 8 |**Werking:** Groter dan 3 |
| **Duur:** 20 minuten |**Duur:** 15 minuten |
| **Tijdaggregatie:** Gemiddelde |**Tijdaggregatie:** Gemiddelde |
| **Actie:** Aantal daling en daling met 2 |**Actie:** Aantal dalingen met 3 |
| **Afkoelen (minuten):** 120 |**Afkoelen (minuten):** 120 |

Het doelbereik dat in het profiel is gedefinieerd, wordt berekend door de minimuminstanties die zijn gedefinieerd in het profiel voor het App Service-plan + buffer.

Het maximumbereik is de som van alle maximale bereiken voor alle App Service-abonnementen die in de werknemersgroep worden gehost.

De telling voor de opschaingsregels moet worden ingesteld op ten minste 1x het inflatiepercentage van het app-serviceplan voor opschalen.

Daling aantal kan worden aangepast aan iets tussen 1/2X of 1X de App Service Plan Inflatie voor schaal naar beneden.

### <a name="autoscale-for-front-end-pool"></a>Automatisch schalen voor front-end pool
Regels voor front-end autoscale zijn eenvoudiger dan voor werknemerspools. In de eerste plaats moet je  
zorg ervoor dat de duur van de meting en de cooldown-timers van mening zijn dat schaalbewerkingen op een App Service-abonnement niet onmiddellijk zijn.

Voor dit scenario weet Frank dat het foutenpercentage toeneemt nadat frontends het cpu-gebruik van 80% hebben bereikt en stelt hij de regel voor automatisch schalen in om de exemplaren als volgt te verhogen:

![Instellingen voor automatisch schalen voor front-end pool.][Front-End-Scale]

| **Automatisch schaalprofiel – Vooreinden** |
| --- |
| **Naam:** Autoscale – Vooreinden |
| **Schaal door:** Plannings- en prestatieregels |
| **Profiel:** Dagelijks |
| **Type:** Herhaling |
| **Doelbereik:** 3 tot 10 exemplaren |
| **Dagen:** Dagelijks |
| **Starttijd:** 09:00 uur |
| **Tijdzone:** UTC-08 |
|  |
| **Regel automatisch schalen (Opschalen)** |
| **Bron:** Front-end pool |
| **Metrische:** CPU % |
| **Werking:** Meer dan 60% |
| **Duur:** 20 minuten |
| **Tijdaggregatie:** Gemiddelde |
| **Actie:** Aantal verhogen met 3 |
| **Afkoelen (minuten):** 120 |
|  |
| **Regel automatisch schalen (Omlaag schalen)** |
| **Bron:** Arbeiderspool 1 |
| **Metrische:** CPU % |
| **Werking:** Minder dan 30% |
| **Duur:** 20 minuten |
| **Tijdaggregatie:** Gemiddelde |
| **Actie:** Aantal dalingen met 3 |
| **Afkoelen (minuten):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
