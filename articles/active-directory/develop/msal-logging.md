---
title: Inloggen in MSAL-apps | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het inloggen in MSAL-toepassingen (Microsoft Authentication Library).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 58697cc535357710c6889f05060b5e04e129ae7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084890"
---
# <a name="logging-in-msal-applications"></a>Inloggen op MSAL-toepassingen

MSAL-apps (Microsoft Authentication Library) genereren logboekberichten die problemen kunnen helpen bij het diagnosticeren van problemen. Een app kan logboekregistratie configureren met een paar regels code en aangepaste controle hebben over het detailniveau en of persoonlijke en organisatorische gegevens al dan niet worden geregistreerd. We raden u aan een MSAL-registratiecallback te maken en gebruikers een manier te bieden om logboeken in te dienen wanneer ze verificatieproblemen hebben.

## <a name="logging-levels"></a>Logboekniveaus

MSAL biedt verschillende niveaus van logging detail:

- Fout: geeft aan dat er iets is misgegaan en er een fout is gegenereerd. Gebruiken voor het debuggen en identificeren van problemen.
- Waarschuwing: Er is niet noodzakelijkerwijs een fout of fout, maar zijn bedoeld voor diagnostiek en het lokaliseren van problemen.
- Info: MSAL logt gebeurtenissen die bedoeld zijn voor informatieve doeleinden die niet noodzakelijkerwijs bedoeld zijn voor het opsporen van debuggen.
- Verbose: Standaard. MSAL registreert de volledige details van het gedrag van de bibliotheek.

## <a name="personal-and-organizational-data"></a>Persoonlijke en organisatorische gegevens

Standaard legt de MSAL-logger geen zeer gevoelige persoonlijke of organisatorische gegevens vast. De bibliotheek biedt de mogelijkheid om het registreren van persoonlijke en organisatorische gegevens mogelijk te maken als u besluit dit te doen.

Kies het tabblad dat overeenkomt met uw taal voor meer informatie over MSAL-logboekregistratie in een bepaalde taal:

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Inloggen MSAL.NET

 > [!NOTE]
 > Zie de [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) voor voorbeelden van MSAL.NET logging en meer.

In MSAL 3.x wordt logboekregistratie per toepassing `.WithLogging` ingesteld bij het maken van apps met behulp van de builder-modifier. Deze methode heeft optionele parameters:

- `Level`stelt u in staat om te beslissen welk niveau van logging u wilt. Als u deze instelt op Fouten, worden er alleen fouten
- `PiiLoggingEnabled`stelt u in staat om persoonlijke en organisatorische gegevens te registreren als deze op true zijn ingesteld. Standaard is dit ingesteld op false, zodat uw toepassing geen persoonlijke gegevens registreert.
- `LogCallback`is ingesteld op een gemachtigde die de logboekregistratie doet. Als `PiiLoggingEnabled` dit waar is, ontvangt deze methode de `containsPii` berichten twee keer: één keer met de `containsPii` parameter staat gelijk aan false en het bericht zonder persoonlijke gegevens, en een tweede keer met de parameter is gelijk aan true en het bericht kan persoonlijke gegevens bevatten. In sommige gevallen (wanneer het bericht geen persoonlijke gegevens bevat), zal het bericht hetzelfde zijn.
- `DefaultLoggingEnabled`hiermee wordt de standaardlogboekregistratie voor het platform ingeschakeld. Standaard is het vals. Als u het instelt op true maakt het gebruik van Event Tracing in Desktop/ UWP-toepassingen, NSLog op iOS en logcat op Android.

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

## <a name="android"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Inloggen MSAL voor Android met Java

Schakel logboekregistratie in bij het maken van apps door een terugmelding voor logboekregistratie te maken. De callback neemt de volgende parameters:

- `tag`is een tekenreeks die door de bibliotheek wordt doorgegeven aan de terugroep. Het is gekoppeld aan de logvermelding en kan worden gebruikt om logboekberichten te sorteren.
- `logLevel`stelt u in staat om te beslissen welk niveau van logging u wilt. De ondersteunde logniveaus `Error`zijn: `Info`, `Verbose` `Warning`, en .
- `message`is de inhoud van de logvermelding.
- `containsPII`hiermee wordt aangegeven of berichten met persoonlijke gegevens of organisatorische gegevens worden geregistreerd. Standaard is dit ingesteld op false, zodat uw toepassing geen persoonlijke gegevens registreert. Als `containsPII` `true`dat zo is, ontvangt deze methode `containsPII` de berichten `false` twee `message` keer: eenmaal met de `containsPii` parameter ingesteld `true` op en het zonder persoonlijke gegevens, en een tweede keer met de parameter ingesteld op en het bericht kan persoonlijke gegevens bevatten. In sommige gevallen (wanneer het bericht geen persoonlijke gegevens bevat), zal het bericht hetzelfde zijn.

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

Standaard zal de MSAL-logger geen persoonlijke identificeerbare informatie of organisatorischidentificeerbare informatie vastleggen.
De registratie van persoonlijke identificeerbare informatie of identificeerbare informatie van de organisatie mogelijk maken:

```java
Logger.getInstance().setEnablePII(true);
```

Ga als u logboekregistratie van persoonsgegevens en organisatiegegevens uit:

```java
Logger.getInstance().setEnablePII(false);
```

Standaard is logboekregistratie bij logcat uitgeschakeld. Inschakelen:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

 Schakel logboekregistratie IN MSAL.js (JavaScript) in door een `UserAgentApplication` loggerobject door te geven tijdens de configuratie voor het maken van een instantie. Dit loggerobject heeft de volgende eigenschappen:

- `localCallback`: een Callback-instantie die door de ontwikkelaar kan worden geleverd om logboeken op een aangepaste manier te gebruiken en te publiceren. Implementeer de localCallback-methode, afhankelijk van hoe u logboeken wilt omleiden.
- `level`(optioneel): het configureerbare logboekniveau. De ondersteunde logniveaus `Error`zijn: `Info`, `Verbose` `Warning`, en . De standaardwaarde is `Info`.
- `piiLoggingEnabled`(optioneel): als deze is ingesteld op true, worden persoonlijke en organisatorische gegevens logboeken. Standaard is dit vals, zodat uw toepassing geen persoonlijke gegevens registreert. Logboeken van persoonlijke gegevens worden nooit geschreven naar standaarduitvoerzoals Console, Logcat of NSLog.
- `correlationId`(optioneel): een unieke id die wordt gebruikt om het verzoek in kaart te brengen met het antwoord voor foutopsporingsdoeleinden. Standaard waarden voor RFC4122 versie 4 guid (128 bits).

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

## <a name="objective-c"></a>[Doelstelling-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL voor iOS en macOS logging-ObjC

Stel een callback in om MSAL-logboekregistratie vast te leggen en deze op te nemen in de logboekregistratie van uw eigen toepassing. De handtekening voor de callback ziet er als volgt uit:

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

### <a name="personal-data"></a>Persoonsgegevens

Standaard legt MSAL geen persoonlijke gegevens (PII) vast of registreert deze niet. De bibliotheek stelt app-ontwikkelaars in staat om dit in te schakelen via een eigenschap in de MSALLogger-klasse. Door het `pii.Enabled`inschakelen neemt de app de verantwoordelijkheid voor het veilig omgaan met zeer gevoelige gegevens en het volgen van wettelijke vereisten.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Logboekniveaus

Als u het logboekniveau wilt instellen wanneer u zich aanmeldt met MSAL voor iOS en macOS, gebruikt u een van de volgende waarden:

|Niveau  |Beschrijving |
|---------|---------|
| `MSALLogLevelNothing`| Alle logboekregistratie uitschakelen |
| `MSALLogLevelError` | Standaardniveau, drukt informatie alleen uit wanneer er fouten optreden |
| `MSALLogLevelWarning` | Waarschuwingen |
| `MSALLogLevelInfo` |  Bibliotheektoegangspunten, met parameters en diverse sleutelhangerbewerkingen |
|`MSALLogLevelVerbose`     |  API-tracering |

Bijvoorbeeld:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Notatie van logboekberichten

Het berichtgedeelte van MSAL-logboekberichten is in de vorm van`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Bijvoorbeeld:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Het verstrekken van correlatie-id's en tijdstempels zijn handig voor het opsporen van problemen. Tijdstempel- en correlatie-ID-informatie is beschikbaar in het logboekbericht. De enige betrouwbare plek om ze op te halen is van MSAL logging berichten.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL voor iOS en macOS logging-Swift

Stel een callback in om MSAL-logboekregistratie vast te leggen en deze op te nemen in de logboekregistratie van uw eigen toepassing. De handtekening (weergegeven in Doelstelling-C) voor de callback ziet er als volgt uit:

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

### <a name="personal-data"></a>Persoonsgegevens

Standaard legt MSAL geen persoonlijke gegevens (PII) vast of registreert deze niet. De bibliotheek stelt app-ontwikkelaars in staat om dit in te schakelen via een eigenschap in de MSALLogger-klasse. Door het `pii.Enabled`inschakelen neemt de app de verantwoordelijkheid voor het veilig omgaan met zeer gevoelige gegevens en het volgen van wettelijke vereisten.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Logboekniveaus

Als u het logboekniveau wilt instellen wanneer u zich aanmeldt met MSAL voor iOS en macOS, gebruikt u een van de volgende waarden:

|Niveau  |Beschrijving |
|---------|---------|
| `MSALLogLevelNothing`| Alle logboekregistratie uitschakelen |
| `MSALLogLevelError` | Standaardniveau, drukt informatie alleen uit wanneer er fouten optreden |
| `MSALLogLevelWarning` | Waarschuwingen |
| `MSALLogLevelInfo` |  Bibliotheektoegangspunten, met parameters en diverse sleutelhangerbewerkingen |
|`MSALLogLevelVerbose`     |  API-tracering |

Bijvoorbeeld:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Notatie van logboekberichten

Het berichtgedeelte van MSAL-logboekberichten is in de vorm van`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Bijvoorbeeld:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Het verstrekken van correlatie-id's en tijdstempels zijn handig voor het opsporen van problemen. Tijdstempel- en correlatie-ID-informatie is beschikbaar in het logboekbericht. De enige betrouwbare plek om ze op te halen is van MSAL logging berichten.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL voor Java-logboekregistratie

Met MSAL voor Java u de logboekbibliotheek gebruiken die u al met uw app gebruikt, zolang deze compatibel is met SLF4J. MSAL voor Java gebruikt de [Simple Logging Facade for Java](http://www.slf4j.org/) (SLF4J) als een eenvoudige gevel of abstractie voor verschillende logging frameworks, zoals [java.util.logging,](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html) [Logback](http://logback.qos.ch/) en [Log4j](https://logging.apache.org/log4j/2.x/). Met SLF4J kan de gebruiker het gewenste logging framework aansluiten bij implementatietijd.

Als u bijvoorbeeld Logboekteruging wilt gebruiken als het logboekregistratiekader in uw toepassing, voegt u de afhankelijkheid van Logboeken toe aan het Pom-bestand Maven voor uw toepassing:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Voeg vervolgens het configuratiebestand Logback toe:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J bindt zich automatisch aan Logboekterug ingedag tijdens de implementatie. MSAL logs worden naar de console geschreven.

Zie de [SLF4J-handleiding](http://www.slf4j.org/manual.html)voor instructies over het binden aan andere loggingframeworks.

### <a name="personal-and-organization-information"></a>Persoonlijke en organisatie-informatie

Msal logging legt standaard geen persoonlijke of organisatorische gegevens vast of registreert deze niet. In het volgende voorbeeld is het loggen van persoonlijke of organisatorische gegevens standaard uitgeschakeld:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Schakel het logboekregistratie van `logPii()` persoonlijke en organisatorische gegevens in door de clienttoepassingsbouwer in te stellen. Als u het loggen van persoonlijke of organisatorische gegevens inschakelt, moet uw app de verantwoordelijkheid nemen voor het veilig verwerken van zeer gevoelige gegevens en het voldoen aan eventuele wettelijke vereisten.

In het volgende voorbeeld is het registreren van persoonlijke of organisatorische gegevens ingeschakeld:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL voor Python-logboekregistratie

Inloggen in MSAL Python maakt bijvoorbeeld gebruik `logging.info("msg")` van het standaard Python-registratiemechanisme, u msal-logboekregistratie als volgt configureren (en deze in actie zien in de [username_password_sample):](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)

### <a name="enable-debug-logging-for-all-modules"></a>Foutopsporingslogboekregistratie inschakelen voor alle modules

Standaard is de logboekregistratie in een Python-script uitgeschakeld. Als u foutopsporingslogboekregistratie wilt inschakelen voor alle modules in uw hele Python-script, gebruikt u het als:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Stilte alleen MSAL logging

Als u alleen MSAL-bibliotheeklogboekregistratie wilt dempen, terwijl foutopsporing in alle andere modules in uw Python-script wordt ingeschakeld, schakelt u de logger uit die wordt gebruikt door MSAL Python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Persoonlijke en organisatorische gegevens in Python

MSAL for Python registreert geen persoonlijke gegevens of organisatorische gegevens. Er is geen eigenschap om persoonlijke of organisatiegegevens in te schakelen die in- of uitschakelen.

U standaard Python-logboekregistratie gebruiken om te loggen wat u wilt, maar u bent verantwoordelijk voor het veilig verwerken van gevoelige gegevens en het volgen van wettelijke vereisten.

Voor meer informatie over het inloggen in Python verwijzen wij u naar Python's [Logging HOWTO](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

---
