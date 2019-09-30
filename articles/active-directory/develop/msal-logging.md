---
title: Logboek registratie in micro soft Authentication Library (MSAL)-toepassingen | Azure
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
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3235037d2b60322ab3e5c393c0a19b1a42bdc6c
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678037"
---
# <a name="logging-in-msal-applications"></a>Logboek registratie in MSAL-toepassingen

Apps van micro soft Authentication Library (MSAL) genereren logboek berichten die kunnen helpen bij het vaststellen van problemen. Een app kan logboek registratie met een paar regels code configureren en aangepaste controle hebben over het detail niveau en bepalen of persoonlijke en organisatie gegevens worden vastgelegd. We raden u aan om een MSAL-call back te maken en een manier te bieden waarop gebruikers logboeken kunnen indienen wanneer ze verificatie problemen hebben.

## <a name="logging-levels"></a>Registratie niveaus

MSAL biedt verschillende detail niveaus voor logboek registratie:

- Fout: Geeft aan dat er iets is overgegaan en dat er een fout is gegenereerd. Gebruiken voor fout opsporing en het identificeren van problemen.
- Waarschuwing: Er is niet noodzakelijkerwijs een fout of een fout opgetreden, maar is bedoeld voor diagnostische gegevens en het lokaliseren van problemen.
- Valuta MSAL registreert gebeurtenissen die bedoeld zijn voor informatie die niet nood zakelijk is bedoeld voor fout opsporing.
- uitgebreide Standaard. MSAL registreert de volledige details van het gedrag van de tape wisselaar.

## <a name="personal-and-organizational-data"></a>Persoonlijke en organisatie gegevens

Standaard worden in de MSAL-logger geen zeer gevoelige persoonlijke of bedrijfs gegevens vastgelegd. De bibliotheek biedt de mogelijkheid om logboek registratie van persoonlijke en organisatie gegevens in te scha kelen als u dit besluit.

## <a name="logging-in-msalnet"></a>Aanmelden MSAL.NET

 > [!NOTE]
 > Zie de [MSAL.net-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) voor voor beelden van MSAL.net-logboek registratie en meer.

In MSAL 3. x wordt logboek registratie per toepassing ingesteld bij het maken van de `.WithLogging` app met behulp van de opbouw functie voor Builder. Deze methode accepteert optionele para meters:

- met `Level` kunt u bepalen welk registratie niveau u wilt. Als u deze instelt op fouten, worden er alleen fouten opgehaald
- met `PiiLoggingEnabled` kunt u persoonlijke en bedrijfs gegevens registreren indien ingesteld op waar. Deze instelling is standaard ingesteld op ONWAAR, zodat uw toepassing geen persoonlijke gegevens registreert.
- `LogCallback` wordt ingesteld op een gemachtigde die de logboek registratie doet. Als `PiiLoggingEnabled` waar is, ontvangt deze methode de berichten twee keer: wanneer de para meter `containsPii` gelijk is aan False en het bericht zonder persoons gegevens, en een tweede keer waarbij de para meter `containsPii` gelijk is aan True en het bericht persoonlijke gegevens kan bevatten. In sommige gevallen (wanneer het bericht geen persoonlijke gegevens bevat), is het bericht hetzelfde.
- `DefaultLoggingEnabled` schakelt de standaard logboek registratie in voor het platform. De standaard instelling is False. Als u deze instelt op True, wordt gebeurtenis tracering gebruikt in Desktop/UWP-toepassingen, NSLog op iOS en logcat op Android.

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

## <a name="logging-in-msal-for-android-using-java"></a>Logboek registratie in MSAL voor Android met behulp van Java

Schakel logboek registratie in bij het maken van een app door het maken van een logboek registratie. De retour aanroep heeft de volgende para meters:

- `tag` is een teken reeks die door de bibliotheek wordt door gegeven aan de retour aanroep. Het is gekoppeld aan de logboek vermelding en kan worden gebruikt om logboek registratie berichten te sorteren.
- met `logLevel` kunt u bepalen welk registratie niveau u wilt. De ondersteunde logboek niveaus zijn: `Error`, `Warning`, `Info` en `Verbose`.
- `message` is de inhoud van de logboek vermelding.
- `containsPII` geeft aan of berichten met persoonlijke gegevens of organisatie gegevens worden geregistreerd. Deze instelling is standaard ingesteld op ONWAAR, zodat uw toepassing geen persoonlijke gegevens kan registreren. Als `containsPII` `true` is, ontvangt deze methode de berichten twee keer: eenmaal met de para meter `containsPII` ingesteld op `false` en de `message` zonder persoonlijke gegevens, en een tweede keer waarbij de para meter `containsPii` is ingesteld op `true` en het bericht mogelijk persoonlijke gegevens bevat. In sommige gevallen (wanneer het bericht geen persoonlijke gegevens bevat), is het bericht hetzelfde.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Standaard worden door de MSAL-logger geen persoonlijke Identificeer bare gegevens of informatie over de organisatie vastgelegd.
De logboek registratie van persoons gegevens of door de organisatie geïdentificeerde informatie inschakelen:

```java
Logger.getInstance().setEnablePII(true);
```

Logboek registratie van persoonlijke gegevens en organisatie gegevens uitschakelen:

```java
Logger.getInstance().setEnablePII(false);
```

Logboek registratie in logcat is standaard uitgeschakeld. In te scha kelen: 
```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="logging-in-msaljs"></a>Logboek registratie in MSAL. js

 Schakel logboek registratie in MSAL. js in door een traceer object door te geven tijdens de configuratie voor het maken van een `UserAgentApplication`-exemplaar. Dit logger object heeft de volgende eigenschappen:

- `localCallback`: een call back-instantie die kan worden geleverd door de ontwikkelaar om logboeken op een aangepaste manier te gebruiken en publiceren. Implementeer de localCallback-methode, afhankelijk van hoe u logboeken wilt omleiden.
- `level`(optioneel): het Configureer bare logboek niveau. De ondersteunde logboek niveaus zijn: `Error`, `Warning`, `Info` en `Verbose`. De standaardwaarde is `Info`.
- `piiLoggingEnabled` (optioneel): als deze optie is ingesteld op waar, worden persoonlijke en organisatie gegevens vastgelegd in een logboek. Standaard is dit false, zodat uw toepassing geen persoonlijke gegevens kan registreren. Persoonlijke gegevens logboeken worden nooit geschreven naar de standaard uitvoer, zoals console, Logcat of NSLog.
- `correlationId`(optioneel): een unieke id, die wordt gebruikt om de aanvraag toe te wijzen met het antwoord op fout opsporing. De standaard instelling is RFC4122-versie 4 GUID (128 bits).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “<Enter your client id>”,
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

## <a name="logging-in-msal-for-ios-and-macos"></a>Logboek registratie in MSAL voor iOS en macOS

Stel een call back in om MSAL-logboek registratie vast te leggen en deze op te nemen in de logboek registratie van uw eigen toepassing. De hand tekening voor de retour aanroep ziet er als volgt uit:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Bijvoorbeeld:

Objective-C
```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

SWIFT
```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-identifiable-information-pii"></a>Persoonlijke Identificeer bare informatie (PII)

Standaard worden door MSAL geen PII vastgelegd of geregistreerd. Met de bibliotheek kunnen app-ontwikkel aars dit inschakelen via een eigenschap in de MSALLogger-klasse. Door PII in te scha kelen, wordt de app verantwoordelijk voor het veilig verwerken van uiterst gevoelige gegevens en de volgende regelgevings vereisten.

Objective-C
```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

SWIFT
```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Registratie niveaus

Gebruik een van de volgende waarden om het logboek registratie niveau in te stellen wanneer u zich aanmeldt met behulp van MSAL voor iOS en macOS:

|Niveau  |Description |
|---------|---------|
| `MSALLogLevelNothing`| Alle logboek registratie uitschakelen |
| `MSALLogLevelError` | Standaard niveau, alleen informatie afdrukken wanneer er fouten optreden |
| `MSALLogLevelWarning` | Waarschuwingen |
| `MSALLogLevelInfo` |  Bibliotheek ingangs punten, met para meters en verschillende sleutel hanger bewerkingen |
|`MSALLogLevelVerbose`     |  API-tracering       |

Bijvoorbeeld:

Objective-C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 SWIFT
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Indeling van logboek bericht

Het bericht gedeelte van MSAL-logboek berichten heeft de indeling`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Bijvoorbeeld:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Het opgeven van correlatie-Id's en tijds tempels is handig voor het volgen van problemen. Informatie over tijds tempel en correlatie-ID is beschikbaar in het logboek bericht. De enige betrouw bare plaats om deze op te halen, is afkomstig uit MSAL-logboek berichten.
