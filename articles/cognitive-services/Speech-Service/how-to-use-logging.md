---
title: SpraakSDK-logboekregistratie - Spraakservice
titleSuffix: Azure Cognitive Services
description: Meer informatie over het inschakelen van logboekregistratie in de Speech SDK (C++, C#, Python, Objective-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 707a0f801a739a7a91cee19635e609305cd8f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805787"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Logboekregistratie inschakelen in de Spraak-SDK

Logboekregistratie bij bestand is een optionele functie voor de Spraak-SDK. Tijdens de ontwikkeling logging biedt aanvullende informatie en diagnostiek van de Speech SDK's kerncomponenten. Dit kan worden ingeschakeld door `Speech_LogFilename` de eigenschap op een spraakconfiguratieobject in te stellen op de locatie en de naam van het logboekbestand. Logboekregistratie wordt wereldwijd geactiveerd zodra een herkenningsfunctie is gemaakt vanuit die configuratie en kan daarna niet meer worden uitgeschakeld. U de naam van een logboekbestand niet wijzigen tijdens een lopende logboeksessie.

> [!NOTE]
> Logging is beschikbaar sinds Speech SDK versie 1.4.0 in alle ondersteunde Speech SDK programmeertalen, met uitzondering van JavaScript.

## <a name="sample"></a>Voorbeeld

De naam van het logboekbestand is opgegeven op een configuratieobject. Neem `SpeechConfig` het voorbeeld en ervan uitgaande dat `config`u een instantie hebt gemaakt met de naam:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

U een herkenningsobject maken op basis van het config-object. Hierdoor kunnen alle recognizers worden gelogd.

> [!NOTE]
> Als u `SpeechSynthesizer` een van het config-object maakt, wordt logboekregistratie niet ingeschakeld. Als logboekregistratie is ingeschakeld, ontvangt u `SpeechSynthesizer`ook diagnoses van de .

## <a name="create-a-log-file-on-different-platforms"></a>Een logboekbestand maken op verschillende platforms

Voor Windows of Linux kan het logboekbestand zich op elk pad bevinden waarvoor de gebruiker schrijftoestemming heeft. Schrijfmachtigingen voor bestandssysteemlocaties in andere besturingssystemen kunnen standaard worden beperkt of beperkt.

### <a name="universal-windows-platform-uwp"></a>Universal Windows Platform (UWP)

UWP-toepassingen moeten logbestanden in een van de toepassingsgegevenslocaties plaatsen (lokaal, roaming of tijdelijk). Er kan een logboekbestand worden gemaakt in de lokale toepassingsmap:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Meer informatie over bestandstoegangstoestemming voor UWP-toepassingen is [hier](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)beschikbaar.

### <a name="android"></a>Android

U een logboekbestand opslaan in interne opslag, externe opslag of de cachemap. Bestanden die zijn gemaakt in de interne opslag of de cachemap, zijn privé voor de toepassing. Het verdient de voorkeur om een logbestand te maken in externe opslag.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

De bovenstaande code slaat een logboekbestand op in de externe opslag in de hoofdmap van een toepassingsspecifieke map. Een gebruiker heeft toegang tot het bestand `Android/data/ApplicationName/logfile.txt`met de bestandsbeheerder (meestal in). Het bestand wordt verwijderd wanneer de toepassing is verwijderd.

U moet ook `WRITE_EXTERNAL_STORAGE` toestemming aanvragen in het manifestbestand:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Meer informatie over gegevens en bestandsopslag voor Android-toepassingen is [hier](https://developer.android.com/guide/topics/data/data-storage.html)beschikbaar.

#### <a name="ios"></a>iOS

Alleen mappen in de toepassingssandbox zijn toegankelijk. Bestanden kunnen worden gemaakt in de documenten, bibliotheek en tijdelijke mappen. Bestanden in de documentenmap kunnen beschikbaar worden gesteld aan een gebruiker. In het volgende codefragment wordt het maken van een logboekbestand in de map met toepassingsdocumenten weergegeven:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Als u een gemaakt bestand wilt `Info.plist` openen, voegt u de onderstaande eigenschappen toe aan de eigenschappenlijst van de toepassing:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Meer informatie over iOS File System is [hier](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)beschikbaar.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Onze C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
