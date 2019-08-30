---
title: Logboek registratie in MSAL-toepassingen | Azure
description: Meer informatie over logboek registratie in micro soft Authentication Library (MSAL)-toepassingen.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02c093375ba2dc5c851a2deb35bdea28338ee982
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135772"
---
# <a name="logging"></a>Logboekregistratie
Micro soft Authentication Library (MSAL)-apps voor het genereren van logboek berichten die u kunnen helpen bij het vaststellen van problemen en informatie te geven. Een app kan logboek registratie met een paar regels code configureren en aangepaste controle hebben over het detail niveau en bepalen of persoonlijke en organisatie gegevens worden vastgelegd. U kunt het beste een call back van MSAL-logboek registratie instellen en gebruikers een manier bieden om logboeken te verzenden wanneer ze verificatie problemen ondervinden.

## <a name="logging-levels"></a>Registratie niveaus

Met de logboek registratie van MSAL kunnen meerdere detail niveaus worden vastgelegd:

- Fout: Geeft aan dat er iets is overgegaan en dat er een fout is gegenereerd. Gebruiken voor fout opsporing en het identificeren van problemen.
- Waarschuwing: Voor gebeurtenissen met een vraag en de app is meer informatie nodig. Er is niet noodzakelijkerwijs een fout of een fout opgetreden, maar dit is bedoeld voor diagnose-en zoek problemen.
- Valuta MSAL registreert gebeurtenissen die bedoeld zijn voor informatie die niet nood zakelijk is bedoeld voor fout opsporing.
- Uitgebreide Standaard. Met MSAL wordt een grote hoeveelheid informatie geregistreerd en worden alle gegevens in het bibliotheek gedrag weer geven.

## <a name="personal-and-organizational-data"></a>Persoonlijke en organisatie gegevens
Standaard worden in de MSAL-logger geen zeer gevoelige persoonlijke of bedrijfs gegevens vastgelegd. De bibliotheek biedt u de mogelijkheid om logboek registratie van persoonlijke en organisatie gegevens in te scha kelen als u dit besluit.

## <a name="logging-in-msalnet"></a>Aanmelden MSAL.NET

 > [!NOTE]
 > Bekijk de [MSAL.net-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)voor meer informatie over MSAL.net. Bekijk voor beelden van MSAL.NET-logboek registratie en meer. 
 
In MSAL 3. x wordt logboek registratie per toepassing ingesteld bij het maken van de `.WithLogging` app met behulp van de opbouw functie voor Builder. Deze methode accepteert optionele para meters:

- *Niveau* kunt u bepalen welk registratie niveau u wilt. Als u deze instelt op fouten, worden er alleen fouten opgehaald
- Met *PiiLoggingEnabled* kunt u persoonlijke en organisatie gegevens registreren indien ingesteld op waar. Deze instelling is standaard ingesteld op ONWAAR, zodat uw toepassing geen persoonlijke gegevens registreert.
- *LogCallback* wordt ingesteld op een gemachtigde die de logboek registratie doet. Als *PiiLoggingEnabled* is ingesteld op True, ontvangt deze methode de berichten twee keer: wanneer de para meter *containsPii* gelijk is aan False en het bericht zonder persoons gegevens, en een tweede keer waarbij de para meter *containsPii* gelijk is aan True en de het bericht kan persoons gegevens bevatten. In sommige gevallen (wanneer het bericht geen persoonlijke gegevens bevat), is het bericht hetzelfde.
- *DefaultLoggingEnabled* schakelt de standaard logboek registratie in voor het platform. De standaard instelling is False. Als u deze instelt op True, wordt gebeurtenis tracering gebruikt in Desktop/UWP-toepassingen, NSLog op iOS en logcat op Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Logboek registratie in MSAL. js

 U kunt logboek registratie inschakelen in MSAL. js door een traceer object door te geven tijdens de configuratie `UserAgentApplication` voor het maken van een exemplaar. Dit logger object heeft de volgende eigenschappen:

- *localCallback*: een call back-instantie die kan worden geleverd door de ontwikkelaar om logboeken op een aangepaste manier te gebruiken en publiceren. Implementeer de localCallback-methode, afhankelijk van hoe u logboeken wilt omleiden.

- *niveau* (optioneel): het Configureer bare logboek niveau. De ondersteunde logboek niveaus zijn: Fout, waarschuwing, info, uitgebreid. De standaard waarde is info.

- *piiLoggingEnabled* (optioneel): Hiermee kunt u persoonlijke en bedrijfs gegevens registreren indien ingesteld op waar. Deze instelling is standaard ingesteld op ONWAAR, zodat uw toepassing geen persoonlijke gegevens registreert. Persoonlijke gegevens logboeken worden nooit geschreven naar de standaard uitvoer, zoals console, Logcat of NSLog. De standaard waarde is ingesteld op ONWAAR.

- *correlationId* (optioneel): een unieke id, die wordt gebruikt om de aanvraag toe te wijzen met het antwoord op fout opsporing. De standaard instelling is RFC4122-versie 4 GUID (128 bits).

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```
