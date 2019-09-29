---
title: Vooraf gebouwde domein verwijzing-LUIS
titleSuffix: Azure Cognitive Services
description: Referentie voor de vooraf gemaakte domeinen vooraf gemaakte verzamelingen van intenties en entiteiten van Language Understanding Intelligent Services (LUIS zijn).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 7e2b5c5c5d2ca4c0d6ab820866341c5f30082c5f
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672775"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Vooraf ontwikkelde domein referentie voor uw LUIS-app
Deze referentie bevat informatie over de [vooraf gemaakte domeinen](luis-how-to-use-prebuilt-domains.md), die zijn vooraf gemaakte verzamelingen van intenties en entiteiten die LUIS biedt.

[Aangepaste domeinen](luis-how-to-start-new-app.md), daarentegen, beginnen met geen intents en modellen. U kunt een vooraf gedefinieerde domein intenties en entiteiten toevoegen aan een aangepast model.

## <a name="supported-domains-across-cultures"></a>Ondersteunde domeinen in cult uren

De volgende tabel bevat een overzicht van de momenteel ondersteunde domeinen. Ondersteuning voor Engels is meestal meer dan andere. 

| Entiteittype       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| [Agenda](#calendar)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Verbindings](#communication)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [E-mail](#email)           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [HomeAutomation](#homeautomation)           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Opmerkingen](#notes)      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Gelegenheden](#places)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [RestaurantReservation](#restaurantreservation)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Taken](#todo)     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Ma's](#utilities)          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Meteorologische](#weather)        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Web](#web)    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Vooraf ontwikkelde domeinen worden **niet ondersteund** in:

* Frans-Canadees
* Hindi
* Spaans (Mexico)

## <a name="description-for-luis-prebuilt-domains"></a>Beschrijving van vooraf gemaakte LUIS-domeinen
## <a name="calendar"></a>**Agenda**
Agenda is alles over persoonlijke vergaderingen en afspraken, geen open bare gebeurtenis (bijvoorbeeld planningen voor de WK, agenda in Seattle) of een algemene agenda (bijvoorbeeld op welke dag het vandaag is, wat is het begin, wanneer de dag van de arbeid is).
### <a name="intents"></a>**Intents**
Doel naam | Description | Voorbeelden
---------|----------|---------------
 AcceptEventEntry | Een (n) afspraak/vergadering/evenement/gebeurtenis accepteren in de agenda. | Een afspraak accepteren. <br> De gebeurtenis accepteren <br> Accepteer de huidige vergadering.
 Annuleren | Annuleer de lopende actie per virtuele assistent, zoals het annuleren van het proces voor het maken van een vergadering. <br> _**Kennisgeving**: Deze intentie omvat hoofd zakelijk de actie annuleren in het scenario van de agenda. Als u de algemene expressie op Annuleren nodig hebt, kunt u ' annuleren ' gebruiken in het domein **Hulpprogram ma's** ._ | Het is OK, maar u hoeft alleen maar de gebeurtenis te annuleren. <br> Nee, ik annuleer alleen de afspraak.
 ChangeCalendarEntry | De agenda-item wijzigen of opnieuw plannen. | Mijn 6 uur opnieuw plannen afspraak morgen tot 17.00 uur <br> Afspraak van dokter voor 5 uur opnieuw plannen <br> De lunch opnieuw plannen met Wilma Olson tot en met vrijdag. <br> De tijd van de gebeurtenis wijzigen.
 CheckAvailability | Beschikbaarheid voor een afspraak of vergadering agenda van de gebruiker of een andere persoon kalender niet vinden. | Wanneer is Jim beschikbaar om te voldoen? <br> Weer geven wanneer Carola morgen beschikbaar is. <br> Chris vrij is op zaterdag?
 Bevestigen | Controleer of een bewerking of actie moet worden uitgevoerd op basis van eerdere opzet. <br> _**Kennisgeving**: Deze intentie omvat hoofd zakelijk de actie bevestigen voor het agenda scenario. Als u meer algemene expressies op bevestigen wilt, kunt u ' Bevestig ' bedoelen in het domein **Hulpprogram ma's** ._| Dat klopt, de vergadering maken <br> Ja, bedankt, verbinding maken met de vergadering.
 ConnectToMeeting | Verbinding maken met een vergadering. | Verbinding maken met 11:00-telefonische vergaderingen met Andy. <br> Accepteer de oproep voor de budget vergadering.
 ContactMeetingAttendees | Neem contact op met de genodigden van de vergadering. | Vertel de vergadering die ik op de hoogte ben van 3:00-vergaderingen. <br> Waarschuw collega's voor 8 am-vergaderingen die moeten beginnen om 8:30.
 CreateCalendarEntry | Een nieuwe eenmalige item toevoegen aan de agenda. | Maak een vergadering over het bespreken van problemen. <br> een vergadering maken metabc@microsoft.com
 DeleteCalendarEntry | Aanvraag voor het verwijderen van een agenda-item.| Mijn afspraak met Carola annuleren. <br> Mijn negen uur verwijderen vergadertijdstip. <br> Mijn gebeurtenis verwijderen.
  FindCalendarEntry | Open de agenda toepassing of zoek in het agenda-item. | Zoek de tandarts afspraak bekijken. <br> Mijn agenda weer geven. <br> Wat zit er in mijn agenda op donderdag?
 FindCalendarWhen | Controleer het tijdstip waarop de planning plaatsvindt. | Wanneer kan ik aan de bruin en Susan voldoen? <br> Wanneer heb ik een brunch gepland? 
 FindCalendarWhere | Controleer de locatie waar de planning plaatsvindt. | Waar bevinden mijn afspraken zich morgen? <br>Waar ben ik met Stacy en Michael morgen voor het diner?
  FindCalendarWho | Controleer de deel nemer (s) die deel nemen aan het doel schema. | Ik wil de bevestigde attendants op de vergadering van morgen om 2. <br> Is de eerstvolgende verpleegt de vergadering?
 FindCalendarDetail | Controleer de Details voor de planning en geef deze weer. | Ik heb u de details nodig van de vergadering die ik heb gepland met mijn collega-Paul.
 FindDuration | Controleer de duur. | Hoe lang duurt het om boodschappen uit te kiezen? <br> Hoe lang heb ik voor de lunch?
 FindMeetingRoom | Zoek de beschik bare Vergader zalen. | Wat voldoet aan kamers? <br> Zoek een nieuwe locatie van de vergadering.
 GoBack | Ga terug naar de laatste stap of het vorige item.  <br> _**Kennisgeving**: Raadpleeg het domein **Utilities** voor meer GoBack General uitingen._ | Vorige versie <br> Terug naar laatste e-mail.
 Afwijzen | De gebruiker weigert de voorgestelde virtuele assistent. <br> _**Kennisgeving**: Raadpleeg het domein toC- **Hulpprogram ma's** voor meer informatie over het afwijzen van de algemene uitingen._ | Het is niet nodig om de gebeurtenis in te stellen. <br> Ik kan op dat moment andere dingen doen.
ShowNext | Controleer de volgende gebeurtenis. <br> _**Kennisgeving**: Raadpleeg het domein **Hulpprogram ma's** voor meer ShowNext algemene uitingen._ | Mijn volgende gebeurtenis geven. <br> Wat staat er nu in de agenda?
 ShowPrevious | Controleer de vorige gebeurtenis. <br> _**Kennisgeving**: Raadpleeg het domein **Hulpprogram ma's** voor meer ShowPrevious algemene uitingen._ | Wat is de planning voor dit?
 TimeRemaining | Controleer de resterende tijd tot de volgende gebeurtenis. | Weer geven hoeveel tijd ik voor mijn vergaderingen heb. <br> De hoeveelheid tijd weer geven die ik heb voordat mijn volgende vergadering begint.
 
### <a name="entities"></a>**Rijg**
Naam van entiteit | Entiteittype | Description | Voorbeelden | Sleuven
-------|-----------------------|-------------|---------|--------
Naam contactpersoon | personName | De naam van een deel nemer aan een contact persoon of vergadering. | Ontmoet u met **Betsy**. <br>  Ontmoet met **Aubrey** op 3 juli om 17.00 uur | Betsy <br> Aubrey <br> Karen 
DestinationCalendar | Simple | De naam van de doel kalender. | lunch met mom dinsdag 12 **persoonlijk** <br> Mijn **Google** Calendar gebruiken als mijn standaard agenda. <br> Yoga-klasse bijwerken naar ma-wo om 15:00 uur in **persoonlijke** agenda weer geven. | Google <br> Personal <br> bedrijf <br> hoofd
Duration | datetime | De duur van een vergadering, afspraak of resterende tijd. | Voeg aan de agenda van werk kalender met Dick toe om Scholarship Details morgen om de **20 minuten**te bespreken. <br> Toevoegen aan de agenda een gebeurtenis op Subway op vrijdag ik bevalt met Thomas gedurende **een uur** . | een uur <br> 2 dagen <br> 20 minuten 
EndDate | datetime | De eind datum voor een vergadering of afspraak. | Agenda Voeg concert toe op bas-zaal Mary 3e naar **Mary 5e** | Mary 5e  
EndTime | datetime | Eind tijd voor een vergadering of afspraak. | kunt u deze 2 30 tot **drie** | drie 
Location | Simple | Locatie van agenda-item, afspraak of vergadering.  Adressen, plaatsen en regio's zijn goede voorbeelden van locaties. | plaats een vergadering in **Fremont** om de Tablet in Birma te plaatsen <br> Pro bonoe Meeting in **Edina** | 209 Nashville sportschool <br> 897 pancake house <br> Garage 
MeetingRoom | Simple | Ruimte voor een vergadering of afspraak. | Toevoegen aan werk Agenda-vergadering met Sjaak op 2 uur in zijn **kantoor** deze vrijdag | zijn kantoor <br> Vergader Zaal <br> Kamer 2
MoveEarlierTimeSpan | datetime | De tijd gebruiker wil een vergadering of afspraak eerder verplaatsen. | Verplaats mijn lunch datum met **30 minuten**vooruit. | 30 minuten <br> twee uur 
MoveLaterTimeSpan |  datetime | De tijd gebruiker wil een vergadering of afspraak later verplaatsen. | mijn vergadering pushen met orchidee Box **4 uur**terug. | 4 uur <br> 15 minuten 
OrderReference | Simple | De rangtelwoord of relatieve positie in een lijst, het identificeren van een item om op te halen. | Wat is mijn volgende afspraak voor morgen? | volgende
OriginalEndDate | datetime | De oorspronkelijke eind datum van een vergadering of afspraak. | Mijn vakantie wijzigen van eindigt op **vrijdag** in maandag | Vrijdag 
OriginalEndTime | datetime | De oorspronkelijke eind tijd van een vergadering of afspraak. | Hiermee wordt het abonnement dat eindigt op **3** ga tot 4 | 3
OriginalStartDate | datetime | De oorspronkelijke begin datum van een vergadering of afspraak. | Wijzig de afspraak van **morgen**van 10 uur tot en met woensdag om 9:00 uur  | morgen 
OriginalStartTime | datetime | De oorspronkelijke begin tijd van een vergadering of afspraak. | Wijzig de afspraak van morgen van **10 uur** tot en met woensdag om 9:00 uur | 10 uur
PositionReference | ordinal | De absolute positie in een lijst, waarmee een item wordt geïdentificeerd dat moet worden opgehaald. | De **tweede** | seconde <br> Nee. 3 <br> nummer 5
RelationshipName | list | De relatie naam van een contact persoon. | lunch toevoegen om 1:00 uur met mijn **vrouw** | vrouw <br> veehouderij <br> zus 
SlotAttribute | Simple | De kenmerk gebruiker wil een query of bewerking uitvoeren. | gebeurtenis **locatie** wijzigen <br> Wijzig de **tijd** in zeven uur | location <br> time 
Begin | datetime | De begin datum van een vergadering of afspraak. | Een vergadering maken op **woensdag** om 16:00 uur | Woensdag 
StartTime | datetime | Begin tijd van een vergadering of afspraak. | een vergadering maken op woensdag om **16:00 uur** | 4 uur
Subject | eenvoudig, patroon. Iedere | Onderwerp, zoals de titel van een vergadering of afspraak. | Welke tijd is de **partij voorbereidings** vergadering? | Tand arts <br> Lunch met Julia 
Message | eenvoudig, patroon. Iedere | Het bericht dat naar de deel nemers moet worden verzonden. | Deel nemers van het diner om **te gaan**. | Ik ben te laat

## <a name="communication"></a>**Verbindings**
Aanvragen voor het maken van aanroepen, het verzenden van teksten/IMs, het zoeken/toevoegen van contact personen en diverse andere communicatie aanvragen (doorgaans uitgaand). De naam van de _contact persoon_ uitingen niet bij het communicatie domein.

### <a name="intents"></a>**Intents**
Doel naam | Description | Voorbeelden
---------|----------|---------
AddContact | Een nieuwe contactpersoon toevoegen aan lijst met contactpersonen van de gebruiker. | Nieuwe contact persoon toevoegen.  <br>   Sla dit getal op en plaats de naam als Jeroen.
AddFlag | Markering toevoegen aan een e-mail bericht | Dit e-mail bericht markeren <br> Voeg een markering toe aan dit e-mail bericht.
AddMore | Voeg meer toe aan een e-mailadres of de tekst, als onderdeel van een stap voor stap e-mail of SMS-samenstelling. | Voeg meer toe aan tekst.  <br>   Voeg meer toe aan de hoofd tekst van de e-mail.
Antwoord | Een inkomend telefoongesprek te beantwoorden. | De oproep beantwoorden.  <br>   Selecteer deze.
AssignContactNickname | Een bijnaam toewijzen aan een contactpersoon. | Wijzig Isaac in Dad.  <br>   De bijnaam van Jim bewerken. <br>   Bijnaam toevoegen aan Patti Owens.
Terugpostactie | Een telefoon gesprek retour neren. | Terugbellen.
CallVoiceMail | Verbinding maken met de voicemail van de gebruiker. | Verbinding maken met mijn voicemail bericht venster. <br>Voice mail. <br>   Voice mail bellen.
Annuleren | Een bewerking annuleren. | Annuleren. <br>   Beëindig het.
CheckIMStatus | De status van een contact persoon in expres berichten controleren. | Is de onlinestatus van Jim ingesteld op opgeslagen? 
CheckMessages | Controleer op de nieuwe berichten of e-mails. | Mijn postvak in controleren <br>  Heb ik nieuwe e-mail?
Bevestigen | Controleer of een actie. | Ja, verzenden. <br> Rechts, ik bevestig dat ik deze e-mail wil verzenden.
EndCall | Een telefoon gesprek beëindigen. | Leg de oproep vast. <br> Uiteindelijk.
FindContact | De contactgegevens op naam vinden. | Vind het nummer van mum. <br>   Het nummer van de Carola weer geven.
FinishTask | De huidige taak volt ooien. | Ik ben klaar <br> Dat is alles.
TurnForwardingOff | Oproep doorschakelen uitschakelen. | Het door sturen van mijn oproepen stoppen. <br> Schakel het door sturen van oproepen uit.
TurnForwardingOn | Het door sturen van oproepen inschakelen. | Mijn oproepen naar 3333 doorsturen <br>  Scha kelen bij oproep door sturen naar 3333.
GetForwardingsStatus | De huidige status van de oproep doorschakelen ophalen. | Wordt mijn doorschakelen ingeschakeld? <br>   Vertel me of mijn oproep status is in-of uitgeschakeld.
GetNotifications | Ontvang de meldingen. | Mijn meldingen openen <br>   kunt u mijn telefoon Facebook-meldingen controleren
GoBack | Ga terug naar de vorige stap. | Ga terug naar twitter <br>   Een stap terug <br>   Terug
Negeren | Een binnenkomende oproep negeren. | Geen antwoord <br>   Aanroep negeren
IgnoreWithMessage | Een binnenkomende oproep negeren en reageren met de tekst in plaats daarvan. | Geen deze oproep wordt beantwoord, maar in plaats daarvan een bericht verzenden. <br>   Negeren en terug tekstbericht te verzenden.
Inbelverbinding | Een telefoongesprek. | Jim aanroepen <br>   Bel 311.
FindSpeedDial | Zoek de speedial nummer een telefoonnummer in dat is ingesteld op en vice versa. | Wat is mijn telefoonnummer 5? <br>   Heb ik snelheid set kiezen? <br>   Wat is het telefoonnummer van 941-5555-333?
Vooruit | Een e-mail door sturen | Stuur deze e-mail door naar Greg.
ReadAloud | Lees een bericht of een e-mailbericht naar de gebruiker. | Lees de tekst. <br>   Wat ze zeggen in het bericht?
PressKey | Druk op een knop of een getal op het toetsenbord. | Ster met kiezen. <br>   Druk op 1-2-3.
QueryLastText | De laatste tekst of het vorige bericht opvragen. | Wie heb ik tekst? <br>   Wie heb ik onlangs aangemeld?
Kiezen | Kiezen of het opnieuw aanroepen van een getal. | Kies opnieuw. <br>   Mijn laatste aanroep opnieuw.
Afwijzen | Een binnenkomende oproep afwijzen. | Oproep afwijzen <br>   Nu kan geen antwoord <br>   Niet beschikbaar is op dit moment en wordt voor de terugbelfunctie later opnieuw.
Beantwoorden | Een bericht beantwoorden. | reageren op Lore Hound <br>   antwoord door ik op mijn manier te typen
SearchMessages | Zoek de berichten op enkele voor waarden. | Kunt u zoeken naar mijn e-mails die zijn verzonden door Jeroen?
EmailVerzenden | Een e-mail verzenden. Dit doel is van toepassing op e-mailadres, maar geen SMS-berichten. | E-mail naar Mike wateren: Mike, de afgelopen week was Splendid. <br>   Stuur een e-mail naar Bob.
SendMessage | Een SMS-bericht of een expresbericht verzenden. | Tekst naar Chris en Carola verzenden <br>   Facebook-bericht Greg <br>   
SetSpeedDial | Stel een snelkoppeling naar de snelheid kiezen voor het telefoonnummer van een contactpersoon. | Stel snelkiezen een voor Carola. <br>   Snelheid kiezen voor mom instellen.
ShowCurrentNotification | Huidige meldingen weer geven. | Zijn er nieuwe meldingen? <br> Een melding weer geven.
ShowNext | Zie het volgende item, bijvoorbeeld in een lijst van de SMS-berichten of e-mailberichten. | Het volgende object weergeven. <br>   Ga naar de volgende pagina.
ShowPrevious | Zie het vorige item, bijvoorbeeld in een lijst van de SMS-berichten of e-mailberichten. | Het vorige voorbeeld weergegeven. <br>   Bestaande. <br>   Ga naar vorige.
TurnSpeakerOff | De telefoon spreker uitschakelen. | Breng me uit spreker. <br>   Handsfree uitschakelen.
TurnSpeakerOn | Schakel de sprekerherkenning-telefoon. | Handsfree-modus. <br>   Plaats Handsfree op.

### <a name="entities"></a>**Rijg**
Naam van entiteit | Entiteittype | Description | Voorbeelden | Sleuven
------|-------|----------|--------------|---------------
Attachment | Simple | De bijlage die de gebruiker wil verzenden per tekst of e-mail bericht. | Een **bestand** vanuit OneNote e-mailen. <br> Verzend mijn housekeeping- **document** naar Katie. | file <br> documenten
AudioDeviceType | Simple | Type audio apparaat (spreker, headset, microfoon, enzovoort). | Antwoord met behulp van **hands free**. <br> Kies opnieuw op de telefoon met de **spreker**. | woont <br> Hands Free <br> Apparaatklasse
Category | Simple | De categorie van een bericht of e-mail, de categorie moet een duidelijke definitie hebben in het e-mail systeem, zoals ' ongelezen ', ' vlag '. De definitie van de beschrijving met een lees-o, bijvoorbeeld ' nieuw ' en ' recent ' zijn geen categorieën. | Alle e-mail markeren als **gelezen**  <br> Nieuwe e-mail met **hoge prioriteit** voor Paul | belangrijk <br> hoge prioriteit <br> lezen
ContactAttribute | Simple | Een kenmerk van de contact persoon voor wie de gebruikers vragen zijn.| **Verjaar dagen** volgende maand waarover ik moet weten? | verjaar dagen <br> address <br> Telefoon nummer
Naam contactpersoon | personName  | De naam van de ontvanger van een contactpersoon of een bericht. | Verzend het e-mail bericht naar **Stevens** | Stevens
Date/Time | datetime | Datum/tijd van een ontvangen e-mail. | De e-mail van **vandaag**lezen <br> Wie heb ik **vandaag**per e-mail gestuurd? <br> Wie heeft ik op **7 uur**gebeld? | vandaag <br> morgen
DestinationPhone | Simple | De doel gebruiker wil een tekst aanroepen of verzenden naar. | een **gesprek tot stand** brengen <br> een tekst bericht naar **Home** verzenden | Boomhut <br> start
EmailAddress | email | Het e-mail adres dat de gebruiker wil verzenden of opvragen. | e-mail verzenden naarMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | eenvoudig, patroon. Iedere | De tekst die wordt gebruikt als de onderwerpregel voor een e-mailbericht. | E-mail bericht naar David met **onderwerp opstellen**  | RE: interessant verhaal
Sleutel | Simple | De belangrijkste gebruiker wil op drukken. | Druk op de **spatie** toets. <br> Druk op **9** | pond ( <br> sterren <br> 8
Regel | Simple | De lijn gebruiker wil een e-mail of tekst verzenden vanuit. | Mijn laatste **Hotmail** -e-mail lezen. <br> Bel via **mobiel**. <br> Bel PA met mijn **werk** lijn.| Hotmail <br> Skype <br> Britse cel
SenderName | personName | De naam van de afzender. | Het e-mail bericht van **David** lezen <br> E-mail berichten van Chanda | David <br> Chanda
FromRelationshipName | Simple | De relatie naam van de afzender. | Bericht lezen van **pa**. <br> Lees ik alle SMS-berichten van **mom**? | Je vader <br> Mama 
Message | eenvoudig, patroon. Iedere |  Het bericht te verzenden als een e-mailadres of tekst.  | E-mail verzenden met de melding**Ik ben bezet**. | Ik ben bezet
OrderReference | Simple | De rangtelwoord of relatieve positie in een lijst, het identificeren van een item om op te halen. | Wat is het **laatste** bericht dat ik heb verzonden? <br> Lees de **laatste** e-mail van Nokia. <br> Lees **nieuwe** tekst berichten. | duren <br> latest <br> Afgelopen <br> nieuwste
PositionReference | eenvoudig, rang telwoord | De rangtelwoord of relatieve positie in een lijst, het identificeren van een item om op te halen.| Wat is het **eerste** bericht dat ik heb verzonden? <br> De **derde** .| Eerste <br> externe
phoneNumber | phoneNumber | De telefoon nummer waarmee de gebruiker een tekst wil aanroepen of verzenden. | tekst verzenden naar **4 1 5 6 8 4 5 2 8 4** | 3525214446
RelationshipName | Simple | De relatie naam van een contact persoon of geadresseerde van het bericht. | E-mail verzenden naar mijn **vrouw** | vrouw
SearchTexts | eenvoudig, patroon. alle | De teksten die worden gebruikt voor het filteren van e-mails of berichten | Alle e-mail berichten doorzoeken die '**Surface Pro**' bevatten | Surface Pro
Vernieuwen | Simple | Het snelkeuzenummer. | Bel **3 4 5**. <br> Stel Snelkeuze **één**in. | 345 <br> 5

## <a name="email"></a>**E-mail**
E-mail is een subdomein van het *communicatie* domein. Het hoofd zakelijk bevat aanvragen voor het verzenden en ontvangen van berichten via e-mail.
### <a name="intents"></a>**Intents**
Doel naam | Description | Voorbeelden
---------|----------|---------
AddMore | Voeg meer toe aan een e-mailadres of de tekst, als onderdeel van een stap voor stap e-mail of SMS-samenstelling. | Voeg meer toe aan de hoofd tekst van de e-mail.
Annuleren | Een bewerking annuleren. <br> ***Kennisgeving**: Raadpleeg het domein **Hulpprogram ma's** voor meer informatie over de algemene uitingen. * | Annuleren. Verzend het niet. <br> Beëindig het.
CheckMessages | Controleer op de nieuwe berichten/e-mails zonder voorwaardelijke informatie. Als er een voor waarde is, behoort de uitingen bij *SearchMessage* intentie. | Controleer mijn postvak in. <br> Heb ik nieuwe e-mail berichten? 
Bevestigen | Bevestig een lopende actie met betrekking tot het verwerken van e-mail berichten. <br> ***Kennisgeving**: Raadpleeg het domein **Hulpprogram ma's** voor meer informatie over de algemene uitingen. * | Ja, verzenden. <br> Ik bevestig dat ik wil dit e-mailbericht verzenden.
Verwijderen | Een e-mail of de e-mail berichten verwijderen die door bepaalde voor waarden worden gefilterd. | Het e-mail bericht in de Prullenbak plaatsen <br> Mijn postvak in leegmaken. <br> Verwijder de e-mail van Mary.
ReadAloud | Lees een bericht of een e-mailbericht naar de gebruiker. <br> ***Kennisgeving**: Raadpleeg het domein **Hulpprogram ma's** voor meer ReadAloud algemene uitingen. *  | Lees het e-mail bericht dat is verzonden door Mary.
Beantwoorden | Een bericht op de huidige e-mail beantwoorden. | Een antwoord op het e-mail bericht maken. <br> Antwoord door ' hartelijk dank dat u het beste bij te houden hebt '.
SearchMessages | Zoek de berichten op enkele voor waarden, zoals de naam van de afzender, de tijd en het onderwerp. | De berichten van Nisheen weer geven. <br> Kunt u mijn e-mail berichten met de titel "ABC" doorzoeken?
EmailVerzenden | Een e-mail verzenden. | E-mail aan Mark: Mike, de afgelopen week was Splendid. <br> Stuur een e-mail naar Bob.
ShowNext | De volgende items in een lijst met tekst berichten of e-mails weer geven. <br> ***Kennisgeving**: Raadpleeg het domein **Hulpprogram ma's** voor meer ShowNext algemene uitingen. * | Het volgende object weergeven. <br> Ga naar de volgende pagina.
ShowPrevious | De vorige items in een lijst met tekst berichten of e-mails weer geven. <br> ***Kennisgeving**: Raadpleeg het domein **Hulpprogram ma's** voor meer ShowPrevious algemene uitingen. * | Het vorige voorbeeld weergegeven. <br> Bestaande. <br> Ga naar vorige.
Vooruit | Een e-mail door sturen. | Stuur deze e-mail door naar Greg.
AddFlag | Voeg een markering toe aan een e-mail bericht. | Dit e-mail bericht markeren <br> Voeg een markering toe aan dit e-mail bericht.
QueryLastText | Vraag de laatste e-mail op. | Wie heeft e-mail naar mij gestuurd? <br> Wie heb ik onlangs per e-mail gestuurd?


### <a name="entities"></a>**Rijg**
Naam van entiteit | Entiteittype | Description | Voorbeelden | Sleuven
------|-------|----------|--------------|---------------
Attachment | Simple | De bijlage die de gebruiker wil verzenden per tekst of e-mail bericht. | Een **bestand** vanuit OneNote e-mailen. <br> Verzend mijn housekeeping- **document** naar Katie. | file <br> documenten
Naam contactpersoon | personName  | De naam van de ontvanger van een contactpersoon of een bericht. | Verzend het e-mail bericht naar **Stevens** | Stevens
Date | datetime | De datum waarop een e-mail is ontvangen. | De e-mail van **vandaag**lezen <br> Wie heb ik **vandaag**per e-mail gestuurd? | vandaag
EmailAddress | email | Het e-mail adres dat de gebruiker wil verzenden of opvragen. | e-mail verzenden naarMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | eenvoudig, patroon. Iedere | De tekst die wordt gebruikt als de onderwerpregel voor een e-mailbericht. | E-mail bericht naar David met **onderwerp opstellen**  | RE: interessant verhaal
SenderName | personName | De naam van de afzender. | Het e-mail bericht van **David** lezen <br> E-mail berichten van Chanda | David <br> Chanda
FromRelationshipName | Simple | De relatie naam van de afzender. | Bericht lezen van **pa**. <br> Lees ik alle SMS-berichten van **mom**? | Je vader <br> Mama 
Message | eenvoudig, patroon. Iedere |  Het bericht te verzenden als een e-mailadres of tekst.  | E-mail verzenden met de melding**Ik ben bezet**. | Ik ben bezet
Category | Simple | De categorie van een bericht of e-mail, de categorie moet een duidelijke definitie hebben in het e-mail systeem, zoals ' ongelezen ', ' vlag '. De definitie van de beschrijving met een lees-o, bijvoorbeeld ' nieuw ' en ' recent ' zijn geen categorieën. | Alle e-mail markeren als **gelezen**  <br> Nieuwe e-mail met **hoge prioriteit** voor Paul | belangrijk <br> hoge prioriteit <br> lezen
OrderReference | Simple | De rangtelwoord of relatieve positie in een lijst, het identificeren van een item om op te halen. | Wat is het **laatste** bericht dat ik heb verzonden? <br> Lees de **laatste** e-mail van Nokia. <br> Lees **nieuwe** tekst berichten. | duren <br> latest <br> Afgelopen <br> nieuwste
PositionReference | eenvoudig, rang telwoord | De rangtelwoord of relatieve positie in een lijst, het identificeren van een item om op te halen.| Wat is het **eerste** bericht dat ik heb verzonden? <br> De **derde** .| Eerste <br> externe
RelationshipName | Simple | De relatie naam van een contact persoon of geadresseerde van het bericht. | E-mail verzenden naar mijn **vrouw** | vrouw
Time | datetime | Time | e- **Tonight**verzenden. | tonight
SearchTexts | eenvoudig, patroon. alle | De teksten die worden gebruikt voor het filteren van e-mails of berichten | Alle e-mail berichten doorzoeken die '**Surface Pro**' bevatten | Surface Pro 
Regel | Simple | De lijn gebruiker wil een e-mail van verzenden via. | Mijn laatste **Hotmail** -e-mail lezen. <br> Een e-mail verzenden vanuit mijn **werk account**.| Hotmail <br> werk account 

## <a name="homeautomation"></a>**HomeAutomation**
Het domein HomeAutomation biedt intenties en entiteiten die betrekking hebben op het beheren van Smart-Home-apparaten. Hoofd zakelijk ondersteunt de controle opdracht met betrekking tot verlichting en lucht voorwaarde. Maar heeft een aantal generalisatie mogelijkheden op andere elektrische apparaten.
### <a name="supported-devices-and-properties"></a>**Ondersteunde apparaten en eigenschappen**
Apparaat | properties
-------|---------
Temperatuur sensor | Temperatuur
Licht, licht | Aan/uit, helderheid, kleur
TV, radio | Aan-uit, volume
Airconditioning (A/C), thermo staat | Aan/uit, Tempe ratuur, kracht
Ventilator | Aan/uit
Afzet mogelijkheden, DVD-speler, ice-maker, enzovoort. | Aan-uit

### <a name="intents"></a>**Intents**
Doel naam | Description | Voorbeelden
---------|----------|---------
 Uitschakelen | Gebruiker wil apparaat of instellingen uitschakelen.  | Schakel de lampjes uit. <br> Schakel iets uit. <br> Iets uit.
 Inschakelen | Schakel een apparaat in of schakel het apparaat in op een bepaalde instelling of modus. | Schakel het lampje in op 50%. <br> Mijn koffie maker inschakelen <br> Kunt u mijn koffie maker inschakelen?
 SetDevice | Gebruiker wil het apparaat instellen op een bepaalde instelling of type.  | Stel de Air-voor waarde in op 26 graden. <br> Zet de lampjes blauw. <br> Roep dit lampje aan als Nightlight.
 QueryState | Gebruiker vraagt om de status van een apparaat of instelling.  | Waar wordt de Thermo staat ingesteld? <br> Is de A/C aan? <br> Wat is de Tempe ratuur van de slaap kamer?
 TurnUp | Instellingen of helderheid van apparaten in te scha kelen. | De lichten met 75 procent opfleuren. <br> Hier de helderheid van 10 procent.  <br> Maak het warmer in de woon kamer.
 TurnDown | Het wordt uitgeschakeld, maar niet uitschakelen van een apparaat, hetzij via dimmen, de Tempe ratuur of de helderheid van de lichten. | Zet de tape wisselaar met 44% omlaag. <br> De lichten lichter. <br> Maak de room-koeling.
### <a name="entities"></a>**Rijg**
Naam van entiteit | Entiteittype | Description | Voorbeelden
-------|----------|--------------|---------------
Apparaatnaam | List | Door de gebruiker gedefinieerde tekst voor hun apparaten. | Blauw<br> Kerst <br> Eigen
DeviceType | List | Ondersteunde apparaten. | Verlichting <br> Air-voor waarde <br> nightlight
Location | Simple | Locatie of kamer waar het apparaat zich bevindt. | Keuken<br> Downstairs <br> Kamer
NumericalChange | Simple | Het bedrag waarmee een instelling wordt verg root of verkleind. <br> <br> _De sleuf wordt alleen weer gegeven met turn_up-en turn_down-intentie._ | 3<br> 50%<br>
OrderReference | ordinal | Het doel van deze sleuf is het vastleggen van de selectie van de items. Hiermee wordt de positie van het item in een lijst aangegeven. | Eerste<br> Tweede
Kwantiteits meter | List | De kwantor geeft aan hoeveel exemplaren van een bepaalde entiteit er naar wordt verwezen. Bijvoorbeeld ' all ', ' elk ', enzovoort. | Alle<br> Elke<br> Alles
Instelling | Simpel | De instelling waarmee de gebruiker hun apparaat wil instellen, zoals scène, niveau, intensiteit, kleur, modus, Tempe ratuur, status van het apparaat. | Blauw<br> 72 <br> Temperatuur 
Settingtype hebben | List | De instelling van het apparaat waarmee de gebruiker is geïnteresseerd. | Temperatuur<br> 
Tijd/datum |  datetime | Tijd en duur voor het besturings systeem van een apparaat| 5 minuten <br> 15:00 uur
Eenheid | List | Voor het labelen van woorden als ' graden ', ' percentage ', ' Fahrenheit ', ' Celsius ' moet elke eenheids term afzonderlijk worden gelabeld. | Vrijheid<br> Percent


## <a name="notes"></a>**Opmerkingen**
Opmerking domein vereenvoudigt het maken van notities en het schrijven van items voor gebruikers.
### <a name="intents"></a>**Intents**
Doel naam | Description | Voorbeelden
---------|----------|---------
AddToNote | Informatie toevoegen aan een geopende notitie. | Toevoegen aan mijn plannings notitie om contact op te nemen met mijn chef over het project.
Wissen | Alle items uit een bestaande notitie wissen. | Alle items uit mijn vakantie notitie verwijderen. <br>Alles wissen van mijn Lees notitie.
Bevestigen | Controleer of een actie met betrekking tot een opmerking. <br> ***Kennisgeving**: Deze intentie omvat voornamelijk de actie bevestigen voor het notitie scenario. Als u meer algemene expressies wilt gebruiken, moet u ' Bevestig ' bedoelen in het domein **Hulpprogram ma's** . * | Het is een goed overzicht van deze opmerking. <br>Ik bevestig dat alle items in lijsten worden bewaard.
Maken | Maak een nieuwe notitie. | Maak een notitie. <br>Houd er rekening mee dat Jason de eerste week van mei kan hebben. 
Verwijderen | Een volledige notitie verwijderen. | Mijn vakantie notitie verwijderen. <br>Mijn boodschappen notitie verwijderen.
ReadAloud | Lees een hardop bericht. | Lees me de eerste opmerking. <br>Lees de details.
Sluiten | De huidige notitie sluiten. | Sluit de notitie. <br>De huidige notitie sluiten.
Open | Open een reeds bestaande notitie. | Open de notitie van de aanroep. <br>Open de notitie ' planning '.
RemoveSentence | Een zin voor een notitie verwijderen. | De laatste zin verwijderen. <br>Verwijder chips uit mijn boodschappen notitie. <br>Pennen verwijderen uit de winkel notitie van mijn school
ChangeTitle | Wijzig de titel van de notitie. | Deze notitie krijgt de naam "planning".

### <a name="entities"></a>**Rijg**
Naam van entiteit | Entiteittype | Description | Voorbeelden 
------- | ------- | ------- | -------
Text | eenvoudig, patroon. Iedere | De tekst van een opmerking of een herinnering. | voordat u walking uitbreiden <br> lange termijn morgen
Titel | eenvoudig, patroon. Iedere | De titel van een opmerking. | boodschappen <br> mensen om aan te roepen <br> taak
CreationDate | datetimeV2 | Deze sleuf geldt wanneer de gebruiker wordt gevraagd om notities te maken binnen een bepaald datum-en tijd venster. | 
Kwantiteits meter | List | Wanneer een gebruiker wordt gevraagd een actie uit te voeren op ' alle ', ' elke ' of ' alle ' items of alle tekst in een notitie. | all <br> Iedere <br> elke
OrderReference | ordinal | De gebruiker wil acties uitvoeren met ' First ', ' last ', ' Next ', enzovoort. | first <br> duren


## <a name="places"></a>**Gelegenheden**
Locaties zijn onder andere bedrijven, instituten, restaurants, open bare ruimten en adressen. Het domein biedt ondersteuning voor het zoeken en opvragen van de informatie, zoals locatie, exploitatie-uren en afstand. 
### <a name="intents"></a>**Intents**
Doel naam | Description | Voorbeelden
---------|----------|---------
MakeCall | Bellen naar een locatie. | Applebees op 1000/200 Rojas St en bel.
FindPlace | Zoeken naar een locatie (business, instelling, restaurant, openbare ruimte, adres). Maar niet: <li> Alleen bedrijfs naam: Starbucks <li> Alleen locatie naam: Seattle/Noor wegen <li> Alleen voor cuisine, voeding of product: Pizza/guacamole/Italiaans | Waar is de dichtstbijzijnde bibliotheek? <br> Starbucks in Seattle. <br> Waar is de dichtstbijzijnde bibliotheek? <br> 
GetAddress | Vraag voor het adres van een centrale locatie. | Het adres van guu op Robson straat weer geven. <br> Wat is het adres van de dichtstbijzijnde ergens?
GetDistance | Vraag over de afstand van de huidige locatie naar een specifieke plaats. | Hoe ver weg Inn vakantie is?<br> Hoe ver gaat het hier om Bellevue kwadraat? <br> Wat is de afstand tot Tahoe?
GetPhoneNumber | Vragen om het telefoonnummer van een centrale locatie. | Wat is het telefoonnummer van de dichtstbijzijnde ergens?<br> Geef het nummer op voor Home Depot. <br> Een telefoon nummer voor Disneyland.
GetPriceRange | Vraag naar het bereik van per inwoner verbruik voor een locatie. | Gemiddelde prijs van J. Sushi in Seattle. <br> Is de halve prijs aangeboden Cineplex op woensdag? <br> Wat kost een hele lobster diner op Sizzler?
GetStarRating | Vragen om de beoordeling van een centrale locatie. | Hoe wordt Zucca beoordeeld?<br> Hoeveel sterren is er in het Frans wasgoed?<br> Het aquarium in Monterrey goed is?
GetHours | Vragen over de operationele uur voor een centrale locatie. | Op welk tijdstip wordt Safeway gesloten?<br> Wat zijn de uren voor Start Depot?<br> Is er ergens nog steeds openen?
GetReviews | Beoordelingen van een plaats om vragen. | Recensies weer geven voor Cheesecake Factory. <br> Lees Cineplex-Beoordelingen. <br> Zijn er recente Beoordelingen voor Humperdinks?
GetMenu | Vragen om het menu-items voor een restaurant. | Zucca biedt fungeren iets Veganistische? <br> Wat is er in het menu op Sizzler? <br> Het menu van Applebee weer geven.
RatePlace | Beoordeel een plaats. | 4 sterren-classificatie voor Maxi maal pizza in Kimberly. <br> Geef 4-Star op voor Bonefish op TripAdvisor. <br> Maak een beoordeling van 4 sterren voor La Hacienda.
AddFavoritePlace | De gebruiker wil een locatie toevoegen aan hun favorieten of MVP-lijst. | Deze locatie opslaan in mijn favorieten. <br> Beste aankoop toevoegen aan mijn favorieten.

### <a name="entities"></a>**Rijg**
LUIS-entiteiten | Entiteittype | Description | Voorbeelden | Utterance-voor beelden
--------------|-------------|-------------|----------|-------------------
AbsoluteLocation | Simple | De locatie of het adres van een centrale locatie. | Palo Alto <br> 300 112th Ave SE <br> Seattle | **1020 Middlefield extern bureau blad.** in **Palo Alto** <br> vogel Seed-winkels in **Seattle** <br> Haal de afstand van hier naar **300 112Th Laan se**.
Recreatie | Simple | De objectieve kenmerken en voor delen van een open bare plaats. | afgebakend <br> gratis vervangende domeinpagina | Kirkland **afgebakende** -restaurants. <br> Is er een **gratis parkeren** bij de buurt?
Cuisine | Simple | Een type eten, cuisine of cuisine nationaliteit. | Chinees <br> Italiaans <br> Sushi <br> Noodle <br> | Help me een **Chinees** restaurant te vinden. <br> Wat zijn de openings uren van het **Sushi** -restaurant? <br> Waar bevindt zich het dichtstbijzijnde **steake** huis?
Date | datetime | DateTime of duration voor de datum van de doel locatie. | morgen <br> vandaag <br> 6 p.m. | Hoe lang sluit aquarium **morgen**af? <br> de dichtstbijzijnde fiets winkel die na **6 uur** is geopend
afstand | dimensie | De afstand tot een open bare locatie van de currenct-positie van de gebruiker. | 15 mijl <br> 10 mijl | een kleding archief binnen **15 mijl** <br> Een kinder restaurant met slechts **10 kilo meters**
MealType | List | Het type maaltijd zoals ontbijt of lunch. | Ontbijt <br> Diner | Zoeken naar **ontbijt** Greenwood Seattle <br> Vind me een plek om mijn **lunch**te ontvangen.
Nabijgelegen | List | Beschrijf een plaats in de buurt zonder absolute locatie of adres. | afgerond <br> in dit gebied <br> rondom | Zoek naar het **dichtstbijzijnde** Indiase restaurant. <br> Waar bevindt zich mijn **lokale** Wetherspoon? <br> Kunt u een goede restaurants **rond me**doen?
OpenStatus | List | Geeft aan of een plaats open of gesloten is. | open <br> Gesloten | Op welke tijd is het Yogurt-land vandaag **gesloten** ? <br> Wat zijn de **openings** tijden voor Costco?
PlaceName | Simple | De naam van een doel dat is een bedrijf, restaurant, openbare voordelen of instelling. De naam van de locatie kan een placetype bevatten als deze wordt gebruikt. | Centrale Park <br> Safeway <br> Walmart| Hoe lang wordt de **SafeWay** -apotheek geopend? <br> Is **Walmart** open?
PlaceType | Simple | Het type van een doel dat is een lokale bedrijven, restaurant, openbare voordelen of instelling. | Restaurant <br> Opera <br> bioscoopvertoningen | **bioscoops** in Cambridge <br> Is er een **restaurant** dichtbij?
PriceRange | Simple | Het prijs bereik van de producten of service van de locatie. | goedkope <br> rendabel <br> dure | **Betaal bare** apparaten herstellen <br> Wat is een **goedkope** pizza-locatie die nu wordt geopend?
Product | Simple | Het product die worden aangeboden door een centrale locatie. | centrifuge <br> televisies | Waar vind ik de beste plaats voor het verkrijgen van **levens middelen**? <br> Ga naar Oost-Kilbride die op zoek zijn naar **televisies**.
Classificatie | Simple | De classificatie van een centrale locatie. | 5 sterren <br> Boven <br> groot | Zijn er **goede** plaatsen waar ik naartoe kan gaan en morgen eten <br> **beste** Amsterdam Restaurants <br> Lijst met de **eerste** drie Pizza-winkels.


## <a name="restaurantreservation"></a>**RestaurantReservation**
Het gereserveerde domein voor restaurant ondersteunt intenties voor de verwerking van de reserve ring voor restaurants.
### <a name="intents"></a>**Intents**
Doel naam | Description | Voorbeelden
---------|----------|---------
Reserve | Vraag een reservering voor een restaurant. | Reserveer op Zucca voor twee voor Tonight. <br> Een tabel voor morgen boeken. <br> Tabel voor 3 in Palo Alto op 7. <br> Maak een reserve ring voor 3 bij een rode Lobster.
DeleteReservation | Een reserve ring voor een restaurant annuleren of verwijderen. | Annuleer de reserve ring op Zucca morgen avond. <br> Meer informatie over mijn reserve ring voor rode Lobster om 7:00 uur volgende vrijdag. <br> Ik heb de reserve ring niet nodig voor Zucca. Annuleer de bewerking.
ChangeReservation | Wijzig de tijd, plaats of het aantal mensen voor een bestaande restaurant reservering. | Stel mijn reserve ring in op 9.00 uur <br> Wijzig mijn reserve ring van Zucca naar rood Lobster. <br> 7 personen in plaats van 6 voor de reserve ring op Zucca.
Bevestigen | Bevestig een actie met betrekking tot de reserve ring. <br> ***Kennisgeving**: Deze intentie omvat voornamelijk de actie bevestigen voor het reserverings scenario van het restaurant. Als u meer algemene expressies wilt gebruiken, moet u ' Bevestig ' bedoelen in het domein **Hulpprogram ma's** . * | Ja, ik heb de reserve ringen voor Tonight op 8 uur in de deeg Maker gemaakt. <br> Ja, alleen in de lijst opnemen. <br> Bevestig de reserve ring op de sushi-balk.
FindReservationDetail | Gedetailleerde informatie over de bestaande reserve ring weer geven. | Mijn reserve ring zoeken op Renaissance San Diego <br> De reserve ring morgen weer geven. <br> Details van mijn reserve ring weer geven voor Zucca.
FindReservationWhere | Controleer de absolute locatie van de reserve ring. | Waar bevindt zich de locatie van Zucca in mijn reserve ring? <br> De land instellingen van mijn reserve ring weer geven morgen.
FindReservationWhen | De exacte tijd van de reserve ring controleren | Wanneer is de reserve ring van Zucca voor morgen? <br> De tijd voor mijn reserve ring op een rode Lobster.
Afwijzen | De gebruiker weigert welke virtuele assistent er is voorgesteld voor het beheren van een reserve ring. <br> ***Let**op:P lease Raadpleeg het domein **hulpprogram ma's** voor meer informatie over de algemene uitingen. * | Het is niet nodig om de gebeurtenis in te stellen. | Nee, ik wil de reserve ring niet wijzigen. <br> Nee, ik wil niet boeken, ik heb een fout gemaakt.

### <a name="entities"></a>**Rijg**
LUIS entiteit | Entiteittype | Description | Voorbeelden
-------|------|---------|-------------------
Adres | Simple | Een locatie van de gebeurtenis of het adres voor een reservering. | Palo Alto<br>300 112th Ave SE<br>Seattle
Lucht | List | Een beschrijving van de atmosfeer van een restaurant. | Romantisch<br>illegaal<br>goed voor groepen<br>gedaan
Cuisine | Simple | Een type eten, cuisine of cuisine nationaliteit. | Chinees<br>Italiaans<br>Mexicaanse<br>Sushi<br>Noodle<br>steak
MealType | List | Een maaltijd-type dat is gekoppeld aan een reservering. | Ontbijt<br>Diner<br>Lunchpauze<br>Supper
PlaceName | Simple | De naam van een restaurant | Zucca<br>Cheesecake Factory<br>rode Lobster
Classificatie | Simple | De classificatie van een plaats of een restaurant. | 5 sterren<br>3 sterren<br>4-ster
NumberPeople | Simple | Aantal personen voor reserve ring | 3<br>zes
Time | datetime| De tijd voor het reserveren van restaurants | morgen<br>tonight<br>7:00 uur<br>Kerst avond


## <a name="todo"></a>**Taken**
_TODO_ -domein bevat typen taak lijsten waarmee gebruikers hun taken kunnen toevoegen, markeren en verwijderen.
### <a name="intents"></a>**Intents**
Doel naam | Description | Voorbeelden
---------|----------|---------
AddToDo | De gebruiker wil taak items toevoegen voor een van de lijst typen. |  Herinner me aan het kopen van melk. <br> Een item met de naam ' melk kopen ' toevoegen aan mijn taken lijst
Bevestigen | De gebruiker wil een voortdurende actie bevestigen. De utterance bevat een expliciet signaal als ' ja ' om een bewerking te bevestigen. <br> <br> ***Kennisgeving**: Dit is met name de actie bevestigen voor een ToDo-scenario. Als u meer algemene expressies wilt gebruiken, moet u ' Bevestig ' bedoelen in het domein **Hulpprogram ma's** . * | Verwijder de taak. <br> Ik weet dat ik deze taak wil toevoegen. <br> Ja, ik wil dat doen.
Annuleren | De gebruiker wil de lopende actie annuleren.  <br> <br> ***Kennisgeving**: Deze intentie omvat voornamelijk de actie bevestigen voor het reserverings scenario van het restaurant. Als u meer algemene expressies wilt gebruiken, moet u ' Bevestig ' bedoelen in het domein **Hulpprogram ma's** . * | Nee, vergeet het niet. <br> u hoeft alleen de taak te annuleren. <br> Nee, voeg het niet toe.
DeleteToDo | Een item in de taken lijst verwijderen door te verwijzen naar de bijbehorende volg orde of alle taak items te verwijderen. | Alle taken verwijderen. <br> Help me bij het verwijderen van de tweede.
MarkToDo | Een taak als voltooid markeren of volt ooien door te verwijzen naar de volg orde of de inhoud van de taak. | De taak "melk kopen" markeren als voltooid. <br> Volledige taak lezen. <br> Alles volt ooien.
ShowNextPage | Een lijst wordt in verschillende pagina's gesplitst om weer te geven. Lijst items weer geven op de volgende pagina voor de gebruiker. | Kunt u de volgende pagina in de boodschappen lijst weer geven? <br> Wat zit er nu? <br> Volgende stappen
ShowPreviousPage | Lijst items weer geven op de vorige pagina voor de gebruiker. | vorige weer geven. <br> Ik moet eerdere taken controleren.
ShowToDo | Alle items in de taken lijst weer geven. | Mijn boodschappen lijst weer geven. <br> Mijn boodschappen lijst weer geven.

### <a name="entities"></a>**Rijg**
LUIS entiteit | Entiteittype | Description | Voorbeelden
-------|------|---------|-------------------
ContainsAll | Simple | Alle items in de taken lijst weer geven | kunt u helpen om **alle** taken te verwijderen. <br> **Alles**volt ooien.
ordinal | ordinal | Een rang telwoord of numerieke verwijzing naar een item. | De **derde** markeren als voltooid. <br> De **eerste** taak verwijderen.
List | Simple | Type taak lijst.  | Schoenen toevoegen aan mijn **winkel** lijst.
FoodOfGrocery | List | Een lijst met voedings gegevens detecteren | Herinner me aan het kopen van **melk**. <br> Voeg **Beef** toe aan mijn boodschappen lijst.
TaskContent | eenvoudig, patroon. alle | De inhoud van een taak detecteren. | Herinner mij eraan om **mijn moeder te bellen** . <br> **Verjaardag van** Jeroen toevoegen aan mijn taken lijst


## <a name="utilities"></a>**Ma's**
Het domein _Hulpprogram ma's_ is een algemeen domein tussen alle _Luis_ -modellen met algemene intenties en uitingen in verschillen scenario's.
### <a name="intents"></a>**Intents**
Doel naam | Description | Voorbeelden
---------|----------|---------
 Annuleren | Een actie/aanvraag/taak/service annuleren. | Deze annuleren. <br> Denk eraan dat u dit niet vergeet.
 Bevestigen | Controleer of een actie. | Zeker <br> Ja, een ogen blik geduld <br> Werkelijk.
 Afwijzen | De gebruiker weigert de voorgestelde virtuele assistent. | Nee
 FinishTask | Een taak die de gebruiker begon voltooien. | Ik ben klaar. <br> Dat is alles. <br> Volt.
 GoBack | Ga naar de vorige stap, of Ga terug naar een vorige stap. | Ga terug naar een stap. <br> Terug.
 Help | Aanvraag voor hulp. | Hulp. <br> Help openen.
 Herhaal deze procedure | Een actie herhalen. | Zeg het opnieuw.
 ShowNext | De volgende items weer geven of laten zien. | Het volgende object weergeven.
 ShowPrevious | Het vorige item weergeven in een serie. | Vorige weer geven.
 Opnieuw | De app opnieuw starten of een nieuwe sessie starten. | Opnieuw.
 Stoppen | Vertel de virtuele assistent om te stoppen met praten.  | Laat dit niet zeggen.
 SelectAny | Gebruiker vraagt om een item of resultaten te selecteren die op het scherm worden weer gegeven. | Een van deze. <br> Selecteer een wille keurig item.
 SelectNone | Gebruiker selecteert geen van de bestaande items en vraagt om meer opties. | Geef me andere suggesties. <br> Geen van deze.
  SelectItem | Gebruiker vraagt om een item of resultaten te selecteren die op het scherm worden weer gegeven. | Selecteer de derde. <br> Selecteer de bovenste rechter bovenhoek.
 Escaleren | Vraag een klanten service om de problemen op te lossen. | Kan ik contact opnemen met een persoon?
 ReadAloud | Iets hardop voor de gebruiker lezen. | Lees deze pagina. <br> Lees dit hardop.

### <a name="entities"></a>**Rijg**
LUIS entiteit | Entiteittype | Description | Voorbeelden
------------|-------------|-------------|---------
ordinal | ordinal | Een rang telwoord of numerieke verwijzing naar een item. | De **tweede** . <br> **Volgende** .
nummer | nummer | Aantal items dat door de gebruiker wil | De volgende **3** items
DirectionalReference | Simple | Een referentie punt voor waar op het scherm een item zich bevindt. | De juiste <br> Hoofd

## <a name="weather"></a>**Meteorologische**
Weer domein is gericht op het controleren van weers omstandigheden en adviseurs met de locatie en tijd of de controle tijd op basis van weer situaties.
### <a name="intents"></a>**Intents**
Doel naam | Description | Voorbeelden
---------|----------|---------
 CheckWeatherValue | Vraag om een weer bericht of een weers relatie voor een locatie in de prognose of eerdere informatie. <br> Voor beelden van weers-gerelateerde factoren zijn: <li> temperatuur </li> <li> regen/sneeuw/precipitatie </li> <li> droge/natte/vochtigheid </li> <li> fogcomputing </li> <li> UV-index </li> <li> inchen regen/winter </li> | Wat is de index van de lucht kwaliteit in Beijing? <br> Hoeveel regen wordt er in Seattle in maart verwacht? <br> De hoogste Tempe ratuur van Hawaï vastleggen.
 CheckWeatherTime | Vraag naar het tijdstip van prognoses of historische weers factoren voor een locatie. | Wanneer is de verwachting op regen? <br> Een goede tijd om naar Canada te gaan <br> Wanneer is de nieuwste maand in Minnesota?
 QueryWeather | Vraag over het weer geven van de voor waarde of de weers factoren voor een specifieke locatie waarvoor een ' Ja, Nee of misschien ' antwoord wordt verwacht of vraag om een activiteit te adviseren die afhankelijk is van de weers omstandigheden. | Wordt de regen morgen? <br> Is de huidige zon? <br> Is mogelijk te vroeg om naar Alaska te gaan?
 ChangeTemperatureUnit | Wijzig de eenheid van het weer, met inbegrip van Celsius, Kelvin en Fahrenheit. | Converteren in Celsius. <br> Kan dit in Kelvin worden weer geven?
 GetWeatherAdvisory | Ontvang weers-en waarschuwings adviezen van een specifieke locatie. | Zijn er weer adviezen in Memphis? <br> Zijn er weer waarschuwingen voor mijn gebied?

### <a name="entities"></a>**Rijg**
LUIS entiteit | Entiteittype | Description | Voorbeelden
------------|-------------|-------------|---------
Location | Geografie | De absolute of impliciete locatie voor een weer aanvraag. | Palo Alto<br>Sjanghai<br>Seattle<br>Delvina<br>
Date/Time   | datetime | Datum/tijd voor het opvragen van het weer. | November<br>per uur<br>ochtend<br>Dit weekend<br>10 dagen<br>
AdditionalWeatherCondition | list | Extra beschrijving woord voor het weer, zoals de snelheid of richting van wind. | direction<br>Snel<br>Waarmee
Historie | Simple | Beschrijving van de historische weers omstandigheden, waaronder gemiddelde 、-grens waarden in de afgelopen periode. | bewaren<br>historisch/geschiedenis<br>lidstaat<br>beste tijd<br>ooit opgenomen
PrecipitationUnit | dimensie | De precipitatie voor sneeuw of regen. | 5 inch<br>6 cm
SuitableFor | Simple | De beschrijving van een menselijke activiteit onder een weers omstandigheden, wat gebruikelijk is wanneer gebruikers een query uitvoeren op activiteiten die afhankelijk zijn van de weers omstandigheden. | jassen<br>paraplu<br>Zwembad
TemperatureUnit |temperatuur | temperatuur | 18 Celsius<br>7 Kelvin-graden
WeatherRange | Simple | De specifieke conditie van de Tempe ratuur, wind en andere weers omstandigheden binnen een bepaalde periode | maximum<br>Hogesnelheidsnet<br>laag<br>gemiddeld hoog<br>Hoogst
WeatherCondition | Simple | Beschrijving van de weers voorwaarde | zon<br>vloeiend<br>regen achtig<br>temperatuur<br>richting<br>hot
WindDirectionUnit | list | De richtings woorden van wind | Noord<br>South<br>East<br>West<br>zijde


## <a name="web"></a>**Web**
Het webdomein biedt de intentie en entiteiten voor het zoeken naar een website.
### <a name="intents"></a>**Intents**
Doel naam | Description | Voorbeelden
---------|----------|---------
 Webzoekopdracht | Een aanvraag om naar een opgegeven website te navigeren of te zoeken in een zoek machine. | Zoek vlak in google.com. <br> Een prettige verjaardag nummer op het web zoeken <br> Ga naar www.twitter.com.

### <a name="entities"></a>**Rijg**
LUIS entiteit | Entiteittype | Description | Voorbeelden
------------|-------------|-------------|---------
Search Engine | List | De gebruiker van de zoek machine wil gebruiken. | Bing <br> Google
Tekst | eenvoudig, patroon. Iedere | De tekst gebruiker wil zoeken. <br> _"Vrienden in Facebook" als brons markeren als de website na "in" geen zoek machine is. De URL moet ook worden gelabeld als brons._ | Film <br> Deep learning <br> Piet
Koppelen | url | De website koppeling. | www.twitter.com

