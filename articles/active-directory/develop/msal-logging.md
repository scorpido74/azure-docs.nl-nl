---
title: Logboek registratie in MSAL-toepassingen (micro soft Authentication Library)
titleSuffix: Microsoft identity platform
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
ms.date: 11/11/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97ea1c5260d1082619d59a2b8614a0ba7e9181a8
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145168"
---
# <a name="logging-in-msal-applications"></a>Logboek registratie in MSAL-toepassingen

Apps van micro soft Authentication Library (MSAL) genereren logboek berichten die kunnen helpen bij het vaststellen van problemen. Een app kan logboek registratie met een paar regels code configureren en aangepaste controle hebben over het detail niveau en bepalen of persoonlijke en organisatie gegevens worden vastgelegd. We raden u aan om een MSAL-call back te maken en een manier te bieden waarop gebruikers logboeken kunnen indienen wanneer ze verificatie problemen hebben.

## <a name="logging-levels"></a>Registratie niveaus

MSAL biedt verschillende detail niveaus voor logboek registratie:

- Fout: geeft aan dat er iets is overgegaan en dat er een fout is gegenereerd. Gebruiken voor fout opsporing en het identificeren van problemen.
- Waarschuwing: er is niet noodzakelijkerwijs een fout of een fout opgetreden, maar is bedoeld voor diagnostische gegevens en het lokaliseren van problemen.
- Info: MSAL registreert gebeurtenissen die bedoeld zijn voor informatie die niet nood zakelijk is bedoeld voor fout opsporing.
- Uitgebreid: standaard. MSAL registreert de volledige details van het gedrag van de tape wisselaar.

## <a name="personal-and-organizational-data"></a>Persoonlijke en organisatie gegevens

Standaard worden in de MSAL-logger geen zeer gevoelige persoonlijke of bedrijfs gegevens vastgelegd. De bibliotheek biedt de mogelijkheid om logboek registratie van persoonlijke en organisatie gegevens in te scha kelen als u dit besluit.

Voor meer informatie over het MSAL van logboek registratie in een bepaalde taal, kiest u het tabblad dat overeenkomt met uw taal:

## <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Aanmelden MSAL.NET

 > [!NOTE]
 > Zie de [MSAL.net-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) voor voor beelden van MSAL.net-logboek registratie en meer.

In MSAL 3. x wordt logboek registratie per toepassing ingesteld bij het maken van de app met behulp van de functie voor het wijzigen van de `.WithLogging` Builder. Deze methode accepteert optionele para meters:

- met `Level` kunt u bepalen welk registratie niveau u wilt. Als u deze instelt op fouten, worden er alleen fouten opgehaald
- met `PiiLoggingEnabled` kunt u persoonlijke en organisatie gegevens registreren indien ingesteld op waar. Deze instelling is standaard ingesteld op ONWAAR, zodat uw toepassing geen persoonlijke gegevens registreert.
- `LogCallback` is ingesteld op een gemachtigde die de logboek registratie doet. Als `PiiLoggingEnabled` waar is, ontvangt deze methode de berichten twee keer: eenmaal met de para meter `containsPii` is ingesteld op False en het bericht zonder persoons gegevens, en een tweede keer met de `containsPii` para meter is ingesteld op True en het bericht kan persoons gegevens bevatten. In sommige gevallen (wanneer het bericht geen persoonlijke gegevens bevat), is het bericht hetzelfde.
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

## <a name="androidtabandroid"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Logboek registratie in MSAL voor Android met behulp van Java

Schakel logboek registratie in bij het maken van een app door het maken van een logboek registratie. De retour aanroep heeft de volgende para meters:

- `tag` is een teken reeks die door de bibliotheek wordt door gegeven aan de call back. Het is gekoppeld aan de logboek vermelding en kan worden gebruikt om logboek registratie berichten te sorteren.
- met `logLevel` kunt u bepalen welk registratie niveau u wilt. De ondersteunde logboek niveaus zijn: `Error`, `Warning`, `Info`en `Verbose`.
- `message` is de inhoud van de logboek vermelding.
- `containsPII` geeft aan of berichten met persoonlijke gegevens of organisatie gegevens worden geregistreerd. Deze instelling is standaard ingesteld op ONWAAR, zodat uw toepassing geen persoonlijke gegevens kan registreren. Als `containsPII` is `true`, ontvangt deze methode de berichten twee keer: eenmaal met de para meter `containsPII` ingesteld op `false` en de `message` zonder persoonlijke gegevens, en een tweede keer waarbij de `containsPii` para meter is ingesteld op `true` en het bericht mogelijk persoonlijke gegevens bevat. In sommige gevallen (wanneer het bericht geen persoonlijke gegevens bevat), is het bericht hetzelfde.

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

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

 Schakel logboek registratie in MSAL. js (Java script) in door een traceer object door te geven tijdens de configuratie voor het maken van een `UserAgentApplication`-exemplaar. Dit logger object heeft de volgende eigenschappen:

- `localCallback`: een call back-instantie die kan worden geleverd door de ontwikkelaar om logboeken op een aangepaste manier te gebruiken en publiceren. Implementeer de localCallback-methode, afhankelijk van hoe u logboeken wilt omleiden.
- `level` (optioneel): het Configureer bare logboek niveau. De ondersteunde logboek niveaus zijn: `Error`, `Warning`, `Info`en `Verbose`. De standaardwaarde is `Info`.
- `piiLoggingEnabled` (optioneel): als deze optie is ingesteld op waar, worden persoonlijke en organisatie gegevens vastgelegd in een logboek. Standaard is dit false, zodat uw toepassing geen persoonlijke gegevens kan registreren. Persoonlijke gegevens logboeken worden nooit geschreven naar de standaard uitvoer, zoals console, Logcat of NSLog.
- `correlationId` (optioneel): een unieke id, die wordt gebruikt om de aanvraag toe te wijzen met het antwoord op fout opsporing. De standaard instelling is RFC4122-versie 4 GUID (128 bits).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
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

## <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL voor iOS-en macOS-logboek registratie-ObjC

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

### <a name="personal-data"></a>Persoons gegevens

Standaard worden door MSAL geen persoonlijke gegevens (PII) vastgelegd of geregistreerd. Met de bibliotheek kunnen app-ontwikkel aars dit inschakelen via een eigenschap in de MSALLogger-klasse. Door `pii.Enabled`in te scha kelen, wordt de app verantwoordelijk voor het veilig verwerken van uiterst gevoelige gegevens en de volgende regelgevings vereisten.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Registratie niveaus

Gebruik een van de volgende waarden om het logboek registratie niveau in te stellen wanneer u zich aanmeldt met behulp van MSAL voor iOS en macOS:

|Niveau  |Beschrijving |
|---------|---------|
| `MSALLogLevelNothing`| Alle logboek registratie uitschakelen |
| `MSALLogLevelError` | Standaard niveau, alleen informatie afdrukken wanneer er fouten optreden |
| `MSALLogLevelWarning` | Berichten |
| `MSALLogLevelInfo` |  Bibliotheek ingangs punten, met para meters en verschillende sleutel hanger bewerkingen |
|`MSALLogLevelVerbose`     |  API-tracering |

Bijvoorbeeld:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Indeling van logboek bericht

Het bericht gedeelte van MSAL-logboek berichten heeft de indeling van `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Bijvoorbeeld:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Het opgeven van correlatie-Id's en tijds tempels is handig voor het volgen van problemen. Informatie over tijds tempel en correlatie-ID is beschikbaar in het logboek bericht. De enige betrouw bare plaats om deze op te halen, is afkomstig uit MSAL-logboek berichten.

## <a name="swifttabswift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL voor iOS-en macOS-logboek registratie-Swift

Stel een call back in om MSAL-logboek registratie vast te leggen en deze op te nemen in de logboek registratie van uw eigen toepassing. De hand tekening (vertegenwoordigd in doel-C) voor de retour aanroep ziet er als volgt uit:

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

### <a name="personal-data"></a>Persoons gegevens

Standaard worden door MSAL geen persoonlijke gegevens (PII) vastgelegd of geregistreerd. Met de bibliotheek kunnen app-ontwikkel aars dit inschakelen via een eigenschap in de MSALLogger-klasse. Door `pii.Enabled`in te scha kelen, wordt de app verantwoordelijk voor het veilig verwerken van uiterst gevoelige gegevens en de volgende regelgevings vereisten.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Registratie niveaus

Gebruik een van de volgende waarden om het logboek registratie niveau in te stellen wanneer u zich aanmeldt met behulp van MSAL voor iOS en macOS:

|Niveau  |Beschrijving |
|---------|---------|
| `MSALLogLevelNothing`| Alle logboek registratie uitschakelen |
| `MSALLogLevelError` | Standaard niveau, alleen informatie afdrukken wanneer er fouten optreden |
| `MSALLogLevelWarning` | Berichten |
| `MSALLogLevelInfo` |  Bibliotheek ingangs punten, met para meters en verschillende sleutel hanger bewerkingen |
|`MSALLogLevelVerbose`     |  API-tracering |

Bijvoorbeeld:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Indeling van logboek bericht

Het bericht gedeelte van MSAL-logboek berichten heeft de indeling van `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Bijvoorbeeld:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Het opgeven van correlatie-Id's en tijds tempels is handig voor het volgen van problemen. Informatie over tijds tempel en correlatie-ID is beschikbaar in het logboek bericht. De enige betrouw bare plaats om deze op te halen, is afkomstig uit MSAL-logboek berichten.

## <a name="javatabjava"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL voor Java-logboek registratie

Met MSAL voor Java (MSAL4J) kunt u de bibliotheek voor logboek registratie gebruiken die u al gebruikt voor uw app, zolang deze compatibel is met SLF4J. MSAL4j maakt gebruik [van de eenvoudige logboek registratie facade voor Java](http://www.slf4j.org/) (SLF4J) als eenvoudige gevel of abstractie voor verschillende logging-frameworks, zoals [Java. util. logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [logback](http://logback.qos.ch/) en [Log4j](https://logging.apache.org/log4j/2.x/). Met SLF4J kan de gebruiker de gewenste logboek registratie raamwerk tijdens de implementatie laten aansluiten.

Als u bijvoorbeeld logback als het Framework voor logboek registratie in uw toepassing wilt gebruiken, voegt u de afhankelijkheid logback toe aan het maven pom-bestand voor uw toepassing:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Voeg vervolgens het logback-configuratie bestand toe:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J maakt tijdens de implementatie automatisch verbinding met logback. MSAL-logboeken worden naar de-console geschreven.

Zie de [SLF4J-hand leiding](http://www.slf4j.org/manual.html)voor instructies over het maken van een binding met andere frameworks voor logboek registratie.

### <a name="personal-and-organization-information"></a>Persoonlijke gegevens en organisatie-informatie

MSAL-logboek registratie legt geen persoonlijke of bedrijfs gegevens vast. In het volgende voor beeld is logboek registratie persoonlijk of organisatie gegevens standaard uitgeschakeld:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Schakel persoonlijke en organisatorische gegevens registratie in door `logPii()` in te stellen op de opbouw functie voor client toepassingen. Als u persoonlijke of bedrijfs gegevens logboek registratie inschakelt, moet uw app verantwoordelijk zijn voor het veilig verwerken van uiterst gevoelige gegevens en het voldoen aan wettelijke vereisten.

In het volgende voor beeld wordt logboek registratie van persoonlijk of organisatie gegevens ingeschakeld:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="pythontabpython"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL voor python-logboek registratie

Logboek registratie in MSAL python maakt gebruik van het standaard logboek registratie mechanisme van python, bijvoorbeeld `logging.info("msg")` u kunt de MSAL-logboek registratie als volgt configureren (en in de [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Logboek registratie voor fout opsporing inschakelen voor alle modules

De logboek registratie in een python-script is standaard uitgeschakeld. Als u logboek registratie voor fout opsporing wilt inschakelen voor alle modules in het hele python-script, gebruikt u:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Alleen stilte MSAL-logboek registratie

Als u alleen logboek registratie van de MSAL-bibliotheek wilt uitvoeren en logboek registratie voor fout opsporing in alle andere modules in uw python-script wilt inschakelen, schakelt u de logboeken uit die door MSAL python worden gebruikt:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Persoonlijke en organisatie gegevens in python

MSAL voor python registreert geen persoonlijke gegevens of bedrijfs gegevens. Er is geen eigenschap om logboek registratie van persoonlijke of organisatie gegevens in of uit te scha kelen.

U kunt de standaard logboek registratie van python gebruiken om logboeken te maken wat u wilt, maar u bent zelf verantwoordelijk voor het veilig afhandelen van gevoelige gegevens en de volgende regelgevings vereisten.

Raadpleeg voor meer informatie over logboek registratie in python de [logboek registratie](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)van python.

---
