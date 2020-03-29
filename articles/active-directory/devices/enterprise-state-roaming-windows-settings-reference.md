---
title: Referentie voor roaming-instellingen van Windows 10 - Azure Active Directory
description: Instellingen die worden geroamd of een back-up gemaakt in Windows 10 met ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a7abc402f1fc2e449e7aac5effdb01b6b941100
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672632"
---
# <a name="windows-10-roaming-settings-reference"></a>Naslaginformatie over roaminginstellingen voor Windows 10

Het volgende is een lijst met de instellingen die worden geroamd of een back-up in Windows 10. 

## <a name="devices-and-endpoints"></a>Apparaten en eindpunten

Zie de volgende tabel voor een overzicht van de apparaten en accounttypen die worden ondersteund door het synchronisatie-, back-up- en herstelframework in Windows 10.

| Accounttype en -bewerking | Bureaublad | Mobiele telefoon |
| --- | --- | --- |
| Azure Active Directory: synchroniseren |Ja |Nee |
| Azure Active Directory: back-up/herstel |Nee |Nee |
| Microsoft-account: synchroniseren |Ja |Ja |
| Microsoft-account: back-up/herstel |Nee |Ja |

## <a name="what-is-backup"></a>Wat is back-up?

Windows-instellingen worden doorgaans standaard gesynchroniseerd, maar er wordt alleen een back-up gemaakt van sommige instellingen, zoals de lijst met geïnstalleerde toepassingen op een apparaat. Back-up is alleen voor mobiele apparaten en momenteel niet beschikbaar voor Enterprise State Roaming-gebruikers. Back-up maakt gebruik van een Microsoft-account en slaat de instellingen en toepassingsgegevens op in OneDrive. Als een gebruiker de synchronisatie op het apparaat uitschakelt met de app Instellingen, worden toepassingsgegevens die normaal gesproken worden gesynchroniseerd, alleen back-up. Back-upgegevens zijn alleen toegankelijk via de herstelbewerking tijdens de eerste run-ervaring van een nieuw apparaat. Back-ups kunnen worden uitgeschakeld via de apparaatinstellingen en kunnen worden beheerd en verwijderd via het OneDrive-account van de gebruiker.

## <a name="windows-settings-overview"></a>Overzicht van Windows-instellingen

De volgende instellingengroepen zijn beschikbaar voor eindgebruikers om instellingensynchronisatie op Windows 10-apparaten in te schakelen/uit te schakelen.

* Thema: bureaubladachtergrond, gebruikerstegel, taakbalkpositie, enz. 
* Internet Explorer-instellingen: browsegeschiedenis, getypte URL's, favorieten, enz. 
* Wachtwoorden: Windows-referentiebeheer, inclusief Wi-Fi-profielen 
* Taalvoorkeuren: spellingwoordenboek, systeemtaalinstellingen 
* Toegankelijkheid: verteller, schermtoetsenbord, vergrootglas 
* Andere Windows-instellingen: zie De details van Windows-instellingen
* Microsoft Edge-browserinstelling: Microsoft Edge-favorieten, leeslijst en andere instellingen

![Uw instellingen synchroniseren](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> Dit artikel is van toepassing op de Microsoft Edge Legacy HTML-gebaseerde browser die in juli 2015 met Windows 10 is gelanceerd. Het artikel is niet van toepassing op de nieuwe Microsoft Edge Chromium-gebaseerde browser uitgebracht op 15 januari 2020. Zie het artikel Microsoft Edge Sync voor meer informatie over het gedrag Synchroniseren voor de nieuwe Microsoft [Edge.](/deployedge/microsoft-edge-enterprise-sync)

Synchronisatie van de Microsoft Edge-browserinstelling (favorieten, leeslijst) kan door eindgebruikers worden ingeschakeld of uitgeschakeld via de menuoptie Microsoft Edge-browserinstellingen.

![Account](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

Voor Windows 10 versie 1803 of hoger kan de synchronisatie van de instellingsgroep van Internet Explorer (favorieten, getypte URL's) door eindgebruikers worden ingeschakeld of uitgeschakeld via de menuoptie Instellingen van Internet Explorer. 

![Instellingen](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Details windows-instellingen

In de volgende tabel verwijzen andere vermeldingen in de kolom Instellingengroep naar instellingen die kunnen worden uitgeschakeld door naar Instellingen > Accounts te gaan > Uw instellingen > Andere Windows-instellingen synchroniseren. 

Interne vermeldingen in de kolom Instellingengroep verwijzen naar instellingen en apps die alleen kunnen worden uitgeschakeld voor synchronisatie in de app zelf of door synchronisatie voor het hele apparaat uit te schakelen met behulp van MDM - of Groepsbeleidsinstellingen.
Instellingen die niet zwerven of synchroniseren, behoren niet tot een groep.

| Instellingen | Bureaublad | Mobiele telefoon | Groep |
| --- | --- | --- | --- |
| **Accounts**: accountafbeelding |synchroniseren |X |Thema |
| **Accounts**: andere accountinstellingen |X |X | |
| **Geavanceerd mobiel breedband**: naam van het delen van internetverbinding (maakt automatisch ontdekken van mobiele Wi-Fi-hotspots via Bluetooth mogelijk) |X |X |Wachtwoorden |
| **App-gegevens:** afzonderlijke apps kunnen gegevens synchroniseren |back-up synchroniseren |back-up synchroniseren |Interne |
| **Lijst met**apps : lijst met geïnstalleerde apps |X |Back-up |Overige |
| **Bluetooth**: alle Bluetooth-instellingen |X |X | |
| **Opdrachtprompt**: Opdrachtprompt 'Standaardinstellingen' |synchroniseren |X |Interne |
| **Referenties:** Credential Locker |synchroniseren |synchroniseren |wachtwoord |
| **Datum, tijd en regio:** automatische tijd (synchronisatie van internettijd) |synchroniseren |synchroniseren |language |
| **Datum, tijd en regio**: 24-uursklok |synchroniseren |X |language |
| **Datum, tijd en regio**: datum en tijd |synchroniseren |X |language |
| **Datum, tijd en regio**: tijdzone | |X |language |
| **Datum, tijd en regio**: zomertijd |synchroniseren |X |language |
| **Datum, tijd en regio**: land/regio |synchroniseren |X |language |
| **Datum, tijd en regio**: eerste dag van de week |synchroniseren |X |language |
| **Datum, tijd en regio**: regionotatie (landomgeving) |synchroniseren |X |language |
| **Datum, tijd en regio**: korte datum |synchroniseren |X |language |
| **Datum, tijd en regio**: lange datum |synchroniseren |X |language |
| **Datum, tijd en regio**: korte tijd |synchroniseren |X |language |
| **Datum, tijd en regio**: lange tijd |synchroniseren |X |language |
| **Desktoppersonalisatie**: bureaubladthema (achtergrond, systeemkleur, standaardsysteemgeluiden, schermbeveiliging) |synchroniseren |X |Thema |
| **Bureaubladpersonalisatie:** achtergrond voor diavoorstelling |synchroniseren |X |Thema |
| **Desktoppersonalisatie**: taakbalkinstellingen (positie, auto-hide, enz.) |synchroniseren |X |Thema |
| **Bureaubladpersonalisatie:** indeling van het startscherm |X |Back-up | |
| **Apparaten**: gedeelde printers met wie u verbinding hebt gemaakt |X |X |andere |
| **Microsoft Edge-browser**: leeslijst |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge-browser:** favorieten |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge-browser**: topsites <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge-browser**: getypte URL's <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge-browser**: instellingen voor favorietenbalk <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge-browser:** de startknop weergeven <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge-browser**: pop-ups blokkeren <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge browser:** vraag me wat te doen met elke download <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge-browser:** aanbieden om wachtwoorden op te slaan <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge-browser:** verzenden volg geen aanvragen <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge-browser**: formuliervermeldingen opslaan <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge-browser:** zoek- en sitesuggesties weergeven terwijl ik typ <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge-browser**: voorkeur voor cookies <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge-browser:** sites beveiligde medialicenties op mijn apparaat laten opslaan <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |Interne |
| **Microsoft Edge-browser**: instelling voor schermlezers <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |Interne |
| **Hoog contrast**: Aan of uit |synchroniseren |X |toegankelijkheid |
| **Hoog contrast**: Thema-instellingen |synchroniseren |X |toegankelijkheid |
| **Internet Explorer:** tabbladen openen (URL en titel) |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer**: leeslijst |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer:** getypte URL's |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer:** browsegeschiedenis |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer:** favorieten |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer:** uitgesloten URL's |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer:** startpagina's |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer:** domeinsuggesties |synchroniseren |synchroniseren |Internet Explorer |
| **Toetsenbord:** gebruikers kunnen het schermtoetsenbord in- en uitschakelen |synchroniseren |X |toegankelijkheid |
| **Toetsenbord**: schakel sticky yes in (standaard uit) |synchroniseren |X |toegankelijkheid |
| **Toetsenbord:** filtertoetsen inschakelen (standaard uitgeschakeld) |synchroniseren |X |toegankelijkheid |
| **Toetsenbord:** schakeltoetsen in (standaard uit) |synchroniseren |X |toegankelijkheid |
| **Internet Explorer**: domeintaal: Chinees (CHS) QWERTY - maak zelflerend mogelijk |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY - inschakelen dynamische kandidaat ranking |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY - char-set Vereenvoudigd Chinees |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY - char-set traditionele Chinees |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY - fuzzy pinyin |synchroniseren |Back-up |Taal |
| **Taal**: CHS QWERTY - fuzzy paren |synchroniseren |Back-up |Taal |
| **Taal**: CHS QWERTY - volledige pinyin |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY - dubbele pinyin |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY - het lezen van automatische correctie |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY - C/E schakeltoets, shift |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY - C/E-schakeltoets, Ctrl |synchroniseren |X |Taal |
| **Taal**: CHS WUBI - single character input mode |synchroniseren |X |Taal |
| **Taal**: CHS WUBI - toon de resterende codering van de kandidaat |synchroniseren |X |Taal |
| **Taal**: CHS WUBI - pieptoon wanneer 4-codering ongeldig is |synchroniseren |X |Taal |
| **Taal**: CHT Bopomofo - inclusief CJK Ext-A |synchroniseren |X |Taal |
| **Taal**: Japans IME - voorspellend typen en aangepaste woorden |synchroniseren |synchroniseren |Taal |
| **Taal**: Koreaans (KOR) IME |X |X |Taal |
| **Taal**: handschriftherkenning |X |X |Taal |
| **Taal**: taalprofiel |synchroniseren |Back-up |Taal |
| **Taal**: spellingcontrole - autocorrectie en markeer spelfouten |synchroniseren |Back-up |Taal |
| **Taal**: lijst met toetsenborden |synchroniseren |Back-up |Taal |
| **Vergrendelingsscherm:** alle instellingen voor het vergrendelingsscherm |X |X | |
| **Vergrootglas**: aan of uit (master toggle) |X |X |Betere toegankelijkheid |
| **Vergrootglas**: inversiekleur in- of uitschakelen (standaard uit) |synchroniseren |X |Betere toegankelijkheid |
| **Vergrootglas**: tracking - volg de toetsenbordfocus |synchroniseren |X |Betere toegankelijkheid |
| **Vergrootglas**: volgen - volg de muiscursor |synchroniseren |X |Betere toegankelijkheid |
| **Vergrootglas**: starten wanneer gebruikers zich standaard aanmelden (standaard uit) |synchroniseren |X |Betere toegankelijkheid |
| **Muis:** de grootte van de muiscursor wijzigen |synchroniseren |X |andere |
| **Muis:** de kleur van de muiscursor wijzigen |synchroniseren |X |andere |
| **Muis:** alle andere instellingen |X |X | |
| **Verteller**: snel starten |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: gebruikers kunnen de spreektoonhoogte van Narrator wijzigen |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: gebruikers kunnen leestips voor narrator voor algemene items in- of uitschakelen (standaard ingeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: gebruikers kunnen in- of uitschakelen of ze getypte tekens kunnen horen (standaard ingeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: gebruikers kunnen in- of uitschakelen of ze getypte woorden kunnen horen (standaard ingeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: cursor invoegen na Narrator (standaard ingeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Verteller:** visuele markering van narratorcursor inschakelen (standaard ingeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: audiosignalen afspelen (standaard ingeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Verteller:** activeer toetsen op het aanraaktoetsenbord wanneer u uw vinger optilt (standaard uit) |synchroniseren |X |Betere toegankelijkheid |
| **Toegankelijkheid:** stel de dikte van de knipperende cursor in |synchroniseren |X |Betere toegankelijkheid |
| **Toegankelijkheid**: achtergrondafbeeldingen verwijderen (standaard uit) |synchroniseren |X |Betere toegankelijkheid |
| **Kracht en slaapstand:** alle instellingen |X |X | |
| **Beginscherm personalisatie**: accentkleur (alleen telefoon) |X |synchroniseren |Thema |
| **Typen**: spellingwoordenboek |synchroniseren |Back-up |Taal |
| **Typen**: verkeerd gespeld woord automatisch corrigeren |synchroniseren |Back-up |Taal |
| **Typen:** verkeerd gespelde woorden markeren |synchroniseren |Back-up |Taal |
| **Typen:** tekstsuggesties weergeven terwijl ik typ |synchroniseren |Back-up |Taal |
| **Typen:** een spatie toevoegen nadat ik een tekstsuggestie heb gekozen |synchroniseren |Back-up |Taal |
| **Typen:** voeg een punt toe nadat ik op de spatiebalk heb dubbeltikken |synchroniseren |Back-up |Taal |
| **Typen**: kapitaliseer de eerste letter van elke zin |synchroniseren |Back-up |Taal |
| **Typen**: gebruik alle hoofdletters wanneer ik dubbeltik op shift-toets |synchroniseren |Back-up |Taal |
| **Typen:** toetsgeluiden afspelen terwijl ik typ |synchroniseren |Back-up |Taal |
| **Typen**: personalisatiegegevens voor aanraaktoetsenbord |synchroniseren |Back-up |Taal |
| **Wi-Fi**: Wi-Fi-profielen (alleen WPA) |synchroniseren |synchroniseren |Wachtwoorden |

###### <a name="footnote-1"></a>Voetnoot 1

Minimaal ondersteunde OS-versie van Windows Creators Update (Build 15063). 

## <a name="next-steps"></a>Volgende stappen

Zie het [roamingoverzicht enterprise state voor](enterprise-state-roaming-overview.md)een overzicht van de bedrijfsstatus .
