---
title: Azure Communication Services-overzicht van de clientbibliotheek
titleSuffix: An Azure Communication Services concept document
description: Biedt een overzicht van de aanroepende clientbibliotheek.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 6021bb4fa5d30de423ab56bfb5fdea6af2665a4a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334477"
---
# <a name="calling-client-library-overview"></a>Overzicht van de aanroepende clientbibliotheek

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Er zijn twee afzonderlijke families van het aanroepen van clientbibliotheken, voor *clients* en *services.* Momenteel beschikbare clientbibliotheken zijn bedoeld voor ervaringen van eindgebruikers: websites en systeemeigen apps.

De service-clientbibliotheken zijn nog niet beschikbaar en bieden toegang tot de onbewerkte spraak- en videogegevensvlakken, geschikt voor integratie met bots en andere services.

## <a name="calling-client-library-capabilities"></a>Mogelijkheden voor aanroepende clientbibliotheek

De volgende lijst bevat de set van functies die momenteel beschikbaar zijn in de Azure Communication Services die clientbibliotheken aanroepen.

| Groep van functies | Mogelijkheid                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Belangrijkste mogelijkheden | Een een-op-een-oproep tussen twee gebruikers plaatsen                                                                           | ✔️   | ✔️            | ✔️  
|                   | Een oproep met meer dan twee gebruikers (Maximaal 350 gebruikers) plaatsen                                                       | ✔️   | ✔️            | ✔️ 
|                   | Een een-op-een-oproep promoten met twee gebruikers in een groepsaanroep met meer dan twee gebruikers                                 | ✔️   | ✔️            | ✔️ 
|                   | Een groepsoproep toevoegen nadat deze is gestart                                                                              | ✔️   | ✔️            | ✔️ 
|                   | Een andere VoIP-deelnemer uitnodigen om deel te nemen aan een actieve groepsoproep                                                       | ✔️   | ✔️            | ✔️ 
|                   | Microfoon dempen/dempen opheffen                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | Schakelen tussen camera's                                                                                              | ✔️   | ✔️            | ✔️           
|                   | Lokaal vasthouden/opheffen                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | Actieve spreker                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Een spreker kiezen voor aanroepen                                                                                            | ✔️   | ✔️            | ✔️           
|                   | Microfoon kiezen voor aanroepen                                                                                         | ✔️   | ✔️            | ✔️           
|                   | Status van een deelnemer weergeven<br/>*Niet-actief, vroege media, verbinding maken, verbonden, in de wacht, in lobby, verbroken*         | ✔️   | ✔️            | ✔️           
|                   | Status van een aanroep weergeven<br/>*Vroege media, binnenkomend, verbinden, rinkelen, verbonden, in de wacht, verbinding verbreken, verbroken* | ✔️   | ✔️            | ✔️           
|                   | Weergeven als een deelnemer is gedempt                                                                                      | ✔️   | ✔️            | ✔️           
|                   | De reden weergeven waarom een deelnemer een gesprek heeft verlaten                                                                       | ✔️   | ✔️            | ✔️     
| Scherm delen    | Het volledige scherm delen vanuit de toepassing                                                                 | ✔️   | ❌            | ❌           
|                   | Een specifieke toepassing delen (in de lijst met actieve toepassingen)                                                | ✔️   | ❌            | ❌           
|                   | Een tabblad van webbrowser delen vanuit de lijst met geopende tabbladen                                                                  | ✔️   | ❌            | ❌           
|                   | Deelnemer kan externe schermdeling weergeven                                                                            | ✔️   | ✔️            | ✔️         
| Rooster            | Deelnemers weergeven                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | Een deelnemer verwijderen                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | Een een-op-een-aanroep met een PSTN-deelnemer plaatsen                                                                     | ✔️   | ✔️            | ✔️   
|                   | Een groepsaanroep met PSTN-deelnemers plaatsen                                                                           | ✔️   | ✔️            | ✔️
|                   | Een een-op-een-aanroep promoten met een PSTN-deelnemer in een groepsaanroep                                                 | ✔️   | ✔️            | ✔️
|                   | Inbellen vanuit een groepsaanroep als een PSTN-deelnemer                                                                    | ✔️   | ✔️            | ✔️   
| Algemeen           | Uw microfoon, spreker en camera testen met een service voor audio testen (beschikbaar door het aanroepen van 8:echo123)                   |  ✔️  | ✔️            | ✔️   

## <a name="calling-client-library-browser-support"></a>Browser ondersteuning voor clientbibliotheek aanroepen

De volgende tabel bevat de set van ondersteunde browsers en versies die momenteel beschikbaar zijn.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **De aanroepende clientbibliotheek gebruiken** | Chrome *, nieuwe Edge | Chrome *, Safari** | Chrome*  | Chrome* | Chrome* | Safari** |


\* Let wel dat naast de vorige twee releases ook de meest recente versie van Chrome wordt ondersteund.<br/>

\* * Let wel dat Safari-versies 13.1 + worden ondersteund. Uitgaande video voor Safari macOS wordt nog niet ondersteund, maar wordt wel ondersteund op iOS. Het delen van een uitgaand scherm wordt alleen ondersteund op Desktop iOS.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met oproepen](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Raadpleeg voor meer informatie de volgende artikelen:
- Stel u op de hoogte van algemene [aanroepstromen](../call-flows.md)
- Meer informatie over [aanroeptypen](../voice-video-calling/about-call-types.md)
- [Uw PSTN-oplossing plannen](../telephony-sms/plan-solution.md)
