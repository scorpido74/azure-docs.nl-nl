---
title: Ontwerpontwerppatronen
titleSuffix: Azure Cognitive Services
description: Ontwerppatronen en best practices voor openbaarmaking.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: 3e7d8ee2b156a30b11cda79798a8af8a8ecf4f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776619"
---
# <a name="disclosure-design-patterns"></a>Ontwerppatronen van openbaarmakingen
Nu jullie het juiste [niveau van openbaarmaking](concepts-disclosure-guidelines.md#disclosure-assessment) voor jullie synthetische stemervaring&#39;hebben bepaald, is het&#39;een goed moment om potentiële ontwerppatronen te verkennen.
## <a name="overview"></a>Overzicht
Er is een spectrum van disclosure design patronen die u toepassen op uw synthetische stem ervaring. Als de uitkomst van jullie onthullingsbeoordeling 'High Disclosure' was, raden we [**expliciete openbaarmaking**](#explicit-disclosure)aan , wat betekent dat we de oorsprong van de synthetische stem ronduit moeten communiceren. [**Impliciete openbaarmaking**](#implicit-disclosure) omvat signalen en interactiepatronen die stemervaringen ten goede komen, ongeacht of de vereiste openbaarmakingsniveaus hoog of laag zijn.
![Spectrum van openbaarmakingspatronen](media/responsible-ai/disclosure-patterns/affordances.png)






| Expliciete openbaarmakingspatronen                                                                                                                                                                                    | Impliciete openbaarmakingspatronen                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Transparante introductie](#transparent-introduction)<br> [Mondelinge transparante inleiding](#verbal-transparent-introduction)<br>  [Expliciet regellijn](#explicit-byline)<br>  [Aanpassing en kalibratie](#customization-and-calibration)<br> [Openbaarmaking van ouders](#parental-disclosure)<br> [Het bieden van mogelijkheden om meer te weten te komen over hoe de stem tot stand is gehaald](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Openbaarmaking van de mogelijkheid](#capability-disclosure)<br>[Impliciete aanwijzingen en feedback](#implicit-cues--feedback)<br> [Gesprekstransparantie](#conversational-transparency) |



Gebruik de volgende grafiek om rechtstreeks te verwijzen naar de patronen die van toepassing zijn op uw synthetische stem. Sommige van de andere voorwaarden in deze grafiek kunnen ook van toepassing zijn op uw scenario:<br/>



| Als uw synthetische stem ervaring ... | Aanbevelingen | Ontwerppatronen |
| --- | --- | --- |
| Vereist hoge openbaarmaking  | Gebruik ten minste één expliciet patroon en impliciete aanwijzingen vooraf om gebruikers te helpen associaties op te bouwen. |[Expliciete openbaarmaking](#explicit-disclosure)<br>[Impliciete openbaarmaking](#implicit-disclosure)  |
| Vereist lage openbaarmaking | Openbaarmaking kan minimaal of onnodig zijn, maar kan profiteren van een aantal impliciete patronen. | [Openbaarmaking van de mogelijkheid](#capability-disclosure)<br>[Gesprekstransparantie](#conversational-transparency)  |
| Heeft een hoge mate van betrokkenheid | Bouw voor de lange termijn en bieden meerdere toegangspunten tot openbaarmaking langs de gebruikersreis. Het is ten zeerste aan te raden om een onboarding-ervaring te hebben. | [Transparante introductie](#transparent-introduction)<br>[Aanpassing en kalibratie](#customization-and-calibration)<br>[Openbaarmaking van de mogelijkheid](#capability-disclosure) |
| Omvat kinderen als het primaire beoogde publiek | Richt ouders als de primaire openbaarmaking publiek en ervoor te zorgen dat ze effectief kunnen communiceren openbaarmaking aan kinderen.  | [Openbaarmaking van ouders](#parental-disclosure)<br>[Mondelinge transparante inleiding](#verbal-transparent-introduction)<br> [Impliciete openbaarmaking](#implicit-disclosure)<br> [Gesprekstransparantie](#conversational-transparency)  |
| Omvat blinde gebruikers of mensen met slechtziendheid als primaire beoogde doelgroep  | Zorg voor alle gebruikers en zorg ervoor dat elke vorm van visuele openbaarmaking alternatieve tekst of geluidseffecten met elkaar heeft verbonden. Houd u aan toegankelijkheidsnormen voor contrastverhouding en weergavegrootte. Gebruik auditieve signalen om openbaarmaking te communiceren.  | [Mondelinge transparante inleiding](#verbal-transparent-introduction) <br>[Auditieve signalen](#implicit-cues--feedback)<br>[Haptische aanwijzingen](#implicit-cues--feedback)<br>[Gesprekstransparantie](#conversational-transparency)<br>[Toegankelijkheidsnormen](https://www.microsoft.com/accessibility) |
| Is schermloos, apparaatloos of gebruikt stem als primaire of enige interactie-modus | Gebruik auditieve signalen om openbaarmaking te communiceren. | [Mondelinge transparante inleiding](#verbal-transparent-introduction) <br> [Auditieve signalen](#implicit-cues--feedback)  |
| Omvat mogelijk meerdere gebruikers/luisteraars (bijv. persoonlijke assistent in meerdere huishoudens)  | Houd rekening met verschillende gebruikerscontexten en niveaus van begrip en bieden meerdere mogelijkheden voor openbaarmaking in de gebruikersreis.  | [Transparante inleiding (gebruiker retourneren)](#transparent-introduction)<br> [Het bieden van mogelijkheden om meer te weten te komen over hoe de stem tot stand is gehaald](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Gesprekstransparantie](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Expliciete openbaarmaking
Als uw synthetische spraakervaring een hoge openbaarmaking vereist, is het het beste om ten minste één van de volgende expliciete patronen te gebruiken om de synthetische aard duidelijk te vermelden.
### <a name="transparent-introduction"></a>Transparante introductie

Voordat de spraakervaring begint, introduceert u de digitale assistent door volledig transparant te zijn over de oorsprong van zijn stem en zijn mogelijkheden. Het optimale moment om dit patroon te gebruiken is bij het instappen van een nieuwe gebruiker of bij het introduceren van nieuwe functies voor een terugkerende gebruiker. Het implementeren van impliciete signalen tijdens een introductie helpt gebruikers een mentaal model te vormen over het synthetische karakter van de digitale agent.

#### <a name="first-time-user-experience"></a>Eerste gebruikerservaring

![Transparante introductie tijdens first run experience](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*De synthetische stem wordt geïntroduceerd tijdens het onboarding en een nieuwe gebruiker.*

Aanbevelingen
- Beschrijf dat de stem kunstmatig &quot;is&quot;(bijv. digitaal)
- Beschrijf wat de agent kan doen
- Vermeld expliciet de stem&#39;oorsprong
- Bied een toegangspunt om meer te weten te komen over de synthetische stem

#### <a name="returning-user-experience"></a>Gebruikerservaring retourneren

Als een gebruiker de onboarding-ervaring overslaat, blijft u toegangspunten bieden voor de transparent introduction-ervaring totdat de gebruiker de stem voor de eerste keer activeert.
<br/>

![Transparante introductie tijdens de gebruikerservaring retourneren](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Zorg voor een consistent toegangspunt tot de synthetische spraakervaring. Laat de gebruiker terugkeren naar de onboarding-ervaring wanneer deze de stem voor het eerst activeert op elk moment in de gebruikersreis.*


### <a name="verbal-transparent-introduction"></a>Mondelinge transparante inleiding

Een gesproken prompt waarin de oorsprong van de digitale assistent&#39;stem is expliciet genoeg op zijn eigen om openbaarmaking te bereiken. Dit patroon is het beste voor hoge openbaarmaking scenario's waar spraak is de enige wijze van interactie beschikbaar.
<br/>

![Mondeling gesproken transparante inleiding](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Gebruik een transparante introductie wanneer er momenten in de gebruikerservaring zijn waarop u een persoon al introduceren of toeschrijven&#39;zijn stem.*


![Mondeling gesproken transparante inleiding in eerste persoon](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Voor extra transparantie kan de stemacteur de oorsprong van de synthetische stem in de eerste persoon onthullen.*

### <a name="explicit-byline"></a>Expliciet regellijn

Gebruik dit patroon als de gebruiker interactie heeft met een audiospeler of interactieve component om de stem te activeren.


![Expliciete omlijn in een nieuwsmediascenario](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Een expliciete naamregel is de toeschrijving van waar de stem vandaan kwam.*

Aanbevelingen

- Aanbiedingspunt voor meer informatie over de gesynthetiseerde stem

### <a name="customization-and-calibration"></a>Aanpassing en kalibratie

Geef gebruikers controle over hoe de digitale assistent op hen reageert (d.w.z. hoe de stem klinkt).  Wanneer een gebruiker interageert met een systeem op hun eigen voorwaarden en met specifieke doelen in het achterhoofd, dan per definitie, ze hebben al begrepen dat het&#39;niet een echte persoon.

#### <a name="user-control"></a>Gebruikersbeheer

Bied keuzes die een betekenisvolle en merkbare impact hebben op de synthetische spraakervaring.

![Gebruikersvoorkeuren](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Met gebruikersvoorkeuren kunnen gebruikers hun ervaring aanpassen en verbeteren.*

Aanbevelingen

- Gebruikers toestaan de stem aan te passen (bijvoorbeeld het selecteren van taal- en spraaktype)
- Geef gebruikers een manier om het systeem te leren reageren op zijn/haar unieke stem (bijvoorbeeld spraakkalibratie, aangepaste commando's)
- Optimaliseren voor door gebruikers gegenereerde of contextuele interacties (bijv. herinneringen)

#### <a name="persona-customization"></a>Persona-aanpassing

Bied manieren om de digitale assistent aan te passen&#39;de stem. Als de stem is gebaseerd op een beroemdheid of een zeer herkenbaar persoon, overweeg dan het gebruik van zowel visuele als gesproken introducties wanneer gebruikers een voorbeeld van de stem bekijken.

![Aanpassing van de stem](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Het aanbieden van de mogelijkheid om te kiezen uit een set van stemmen helpt overbrengen van de kunstmatige natuur.*

Aanbevelingen
- Gebruikers toestaan een voorbeeld te bekijken van het geluid van elke stem
- Gebruik een authentieke introductie voor elke stem
- Geef toegangspunten om meer te weten te komen over de gesynthetiseerde stem

### <a name="parental-disclosure"></a>Openbaarmaking van ouders

Naast het voldoen aan coppa-regels, informatie verstrekken aan ouders als uw primaire beoogde publiek jonge kinderen is en uw blootstellingsniveau hoog is. Voor gevoelig gebruik, overwegen gating de ervaring totdat een volwassene heeft erkend het gebruik van de synthetische stem. Moedig ouders aan om de boodschap aan hun kinderen over te brengen.

![Openbaarmaking voor ouders](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Een transparante introductie geoptimaliseerd voor ouders zorgt ervoor dat een volwassene bewust werd gemaakt van het synthetische karakter van de stem voordat een kind ermee omgaat.*

Aanbevelingen

- Betarget ouders als het primaire publiek voor openbaarmaking
- Moedig ouders aan om openbaarmaking aan hun kinderen mee te delen
- Geef toegangspunten om meer te weten te komen over de gesynthetiseerde stem
- Gate de ervaring door &quot;het&quot; vragen van ouders een eenvoudige bescherming vraag om te laten zien dat ze de openbaarmaking hebben gelezen

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Het bieden van mogelijkheden om meer te weten te komen over hoe de stem tot stand is gehaald

Bied contextgevoelige toegangspunten tot een pagina, pop-up of externe site die meer informatie geeft over de synthetische spraaktechnologie. U bijvoorbeeld een koppeling weergeven om meer te weten te komen tijdens het instappen of wanneer de gebruiker om meer informatie vraagt tijdens het gesprek.

![Toegangspunt voor meer informatie](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Voorbeeld van een toegangspunt om de mogelijkheid te bieden om meer te weten te komen over de gesynthetiseerde stem.*

Zodra een gebruiker vraagt om meer informatie over de synthetische stem, het primaire doel is om hen te informeren over de oorsprong van de synthetische stem en om transparant te zijn over de technologie.

![Geef gebruikers meer informatie over synthetische spraak](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Meer informatie kan worden aangeboden in een externe site help site.*

Aanbevelingen

- Vereenvoudig complexe concepten en vermijd het gebruik van legale en technische jargon
- Begraaf deze inhoud niet in privacy- en gebruiksvoorwaarden
- Houd de inhoud beknopt en gebruik afbeeldingen wanneer deze beschikbaar zijn

## <a name="implicit-disclosure"></a>Impliciete openbaarmaking

Consistentie is de sleutel tot het bereiken van openbaarmaking impliciet gedurende de hele user journey. Consistent gebruik van visuele en auditieve signalen op verschillende apparaten en interactiewijzen kan helpen bij het opbouwen van verbanden tussen impliciete patronen en expliciete openbaarmaking.

![Consistentie van impliciete signalen](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Impliciete aanwijzingen & feedback

Antropomorfisme kan zich op verschillende manieren manifesteren, van de werkelijke visuele weergave van de agent, tot de stem, geluiden, lichtpatronen, stuiterende vormen of zelfs de trilling van een apparaat. Bij het definiëren van uw persona, leverage impliciete signalen en feedback patronen in plaats van streven naar een zeer mens-achtige avatar. Dit is een manier om de noodzaak van meer expliciete openbaarmaking te minimaliseren.

![Visuele aanwijzingen en feedback](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Deze signalen helpen de agent te antropomorfiseren zonder al te menselijk te zijn. Ze kunnen ook effectieve openbaarmakingmechanismen worden wanneer ze in de loop van de tijd consequent worden gebruikt.*

Houd rekening met de verschillende manieren van interactie van uw ervaring bij het opnemen van de volgende typen signalen:

| Visuele aanwijzingen                                                                                                                                                               | Auditieve signalen                                                      | Haptische aanwijzingen |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Responsieve real-time cues (bijvoorbeeld animaties)<br> Niet-schermsignalen (bijvoorbeeld verlichting en patronen op een apparaat)<br>  | Sonicon (bijvoorbeeld een kort onderscheidend geluid, serie van muzikale noten) | Trillingen   |

### <a name="capability-disclosure"></a>Openbaarmaking van de mogelijkheid

Openbaarmaking kan impliciet worden bereikt door nauwkeurige verwachtingen te stellen voor waartoe de digitale assistent in staat is. Geef voorbeeldopdrachten zodat gebruikers kunnen leren hoe ze met de digitale assistent kunnen communiceren en contextuele hulp kunnen bieden om meer te weten te komen over de synthetische stem in de vroege stadia van de ervaring.

![Visuele aanwijzingen en feedback](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Gesprekstransparantie

Wanneer gesprekken in onverwachte paden vallen, u overwegen standaardantwoorden te maken die kunnen helpen bij het resetten van verwachtingen, het versterken van de transparantie en gebruikers naar succesvolle paden sturen. Er zijn mogelijkheden om expliciete openbaarmaking ook in gesprek te gebruiken.

![Onverwachte paden verwerken](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Off-task &quot;of&quot; persoonlijke vragen gericht aan de agent zijn een goed moment om gebruikers te herinneren aan de synthetische aard van de agent en sturen ze om te gaan met het op de juiste manier of om ze te verwijzen naar een echte persoon.

![Vragen over het afhandelen van taken](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Wanneer bekend te maken

Er zijn veel mogelijkheden voor openbaarmaking gedurende de hele gebruikersreis. Ontwerp voor het eerste gebruik, tweede gebruik, nth &quot;gebruik..., maar omarm ook momenten van het niet&quot; benadrukken van transparantie, zoals wanneer het systeem een fout maakt of wanneer de gebruiker een beperking van de agent ontdekt&#39;s mogelijkheden.

![Openbaarmakingsmogelijkheden tijdens een gebruikerstraject](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Voorbeeld van een standaard gebruikersreis van de digitale assistent die verschillende openbaarmakingsmogelijkheden belicht.

### <a name="up-front"></a>Up-front

Het optimale moment voor openbaarmaking is de eerste keer dat een persoon interageert met de synthetische stem.In een persoonlijk spraakassistentscenario zou dit tijdens het instappen zijn, of de eerste keer dat de gebruiker de ervaring vrijwel losmaakt. In andere scenario's kan het de eerste keer zijn dat een synthetische stem inhoud op een website leest of de eerste keer dat een gebruiker interactie heeft met een virtueel teken.

- [Transparante introductie](#transparent-introduction)
- [Openbaarmaking van de mogelijkheid](#capability-disclosure)
- [Aanpassing en kalibratie](#customization-and-calibration)
- [Impliciete aanwijzingen](#implicit-cues--feedback)

### <a name="upon-request"></a>Op verzoek

Gebruikers moeten gemakkelijk toegang hebben tot aanvullende informatie, voorkeuren kunnen beheren en op elk moment tijdens de gebruikersreis transparante communicatie kunnen ontvangen wanneer daarom wordt gevraagd.

- [Het bieden van mogelijkheden om meer te weten te komen over hoe de stem tot stand is gehaald](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Aanpassing en kalibratie](#customization-and-calibration)
- [Gesprekstransparantie](#conversational-transparency)

### <a name="continuously"></a>Voortdurend

Gebruik de impliciete ontwerppatronen die de gebruikerservaring continu verbeteren.

- [Openbaarmaking van de mogelijkheid](#capability-disclosure)
- [Impliciete aanwijzingen](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Wanneer het systeem uitvalt

Gebruik openbaarmaking als een kans om gracieus te falen.

- [Gesprekstransparantie](#conversational-transparency)
- [Het bieden van mogelijkheden om meer te weten te komen over hoe de stem tot stand is gehaald](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Overdracht aan de mens](#conversational-transparency)



## <a name="additional-resources"></a>Aanvullende bronnen
- [Richtlijnen voor Microsoft Bot](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Richtlijnen voor Cortana-ontwerp](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Richtlijnen voor microsoft Windows UWP-spraakontwerp](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Richtlijnen voor spraakopdrachten van Microsoft Windows Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Referentiedocumenten

* [Openbaarmaking voor Voice Talent](https://aka.ms/disclosure-voice-talent)
* [Richtlijnen voor verantwoorde implementatie van synthetische spraaktechnologie](concepts-guidelines-responsible-deployment-synthetic.md)
* [Overzicht van Gating](concepts-gating-overview.md)
* [Hoe openbaar te maken](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>Volgende stappen

* [Openbaarmaking voor Voice Talent](https://aka.ms/disclosure-voice-talent)
