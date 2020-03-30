---
title: Logboekregistratie en diagnostische gegevens
description: Uitgebreide uitleg over het genereren en ophalen van logboekregistratie en diagnostische gegevens in Azure Spatial Anchors.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270130"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Logboekregistratie en diagnostische gegevens in Azure Spatial Anchors

Azure Spatial Anchors biedt een standaard registratiemechanisme dat handig is voor app-ontwikkeling. De diagnostische logboekmodus Ruimtelijke Ankers is handig wanneer u meer informatie nodig hebt voor foutopsporing. Diagnostische logboekregistratie slaat afbeeldingen van de omgeving op.

## <a name="standard-logging"></a>Standaardlogboekregistratie
In de API Voor ruimtelijke ankers u zich abonneren op het logboekregistratiemechanisme om nuttige logboeken te krijgen voor het ontwikkelen en debuggen van toepassingen. De standaard logging API's slaan geen afbeeldingen van de omgeving op de apparaatschijf op. De SDK biedt deze logboeken als gebeurteniscallbacks. Het is aan jou om deze logboeken te integreren in het loggingmechanisme van de toepassing.

### <a name="configuration-of-log-messages"></a>Configuratie van logboekberichten
Er zijn twee callbacks van belang voor de gebruiker. In het volgende voorbeeld ziet u hoe u de sessie configureert.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Gebeurtenissen en eigenschappen

Deze callbacks voor gebeurtenissen worden verstrekt om logboeken en fouten uit de sessie te verwerken:

- [LogLevel:](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel)hiermee geeft u het detailniveau op dat de gebeurtenissen van de runtime moeten ontvangen.
- [OnLogDebug:](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug)Biedt standaard foutopsporingslogboekgebeurtenissen.
- [Fout:](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error)geeft logboekgebeurtenissen op die door de runtime als fouten worden beschouwt.

## <a name="diagnostics-logging"></a>Logboekregistratie voor diagnostiek

Naast de standaard modus van de werking voor logging, Spatial Anchors heeft ook een diagnostische modus. De diagnostische modus legt afbeeldingen van de omgeving vast en registreert ze op de schijf. U deze modus gebruiken om bepaalde soorten problemen te debuggen, zoals het niet voorspelbaar vinden van een anker. Schakel diagnostische logboekregistratie alleen in om een specifiek probleem te reproduceren. Schakel het dan uit. Schakel geen diagnose in wanneer u uw apps normaal uitvoert.

Tijdens een ondersteuningsinteractie met Microsoft kan een Microsoft-vertegenwoordiger vragen of u bereid bent een diagnostische bundel in te dienen voor verder onderzoek. In dit geval u besluiten om diagnostische gegevens in te schakelen en het probleem te reproduceren, zodat u de diagnostische bundel indienen.

Als u een diagnoselogboek bij Microsoft indient zonder voorafgaande bevestiging van een Microsoft-vertegenwoordiger, blijft de indiening onbeantwoord.

In de volgende secties wordt uitgelegd hoe u de diagnostische modus inschakelt en hoe u diagnostische logboeken bij Microsoft indienen.

### <a name="enable-diagnostics-logging"></a>Diagnostische logboekregistratie inschakelen

Wanneer u een sessie inschakelt voor het registreren van diagnostiek, hebben alle bewerkingen in de sessie de bijbehorende diagnostische logboekregistratie in het lokale bestandssysteem. Tijdens het loggen worden afbeeldingen van de omgeving opgeslagen op de schijf.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>De diagnosebundel indienen

In het volgende codefragment ziet u hoe u een diagnostische bundel indient bij Microsoft. Deze bundel bevat afbeeldingen van de omgeving die door de sessie is vastgelegd nadat u diagnose hebt ingeschakeld.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Delen van een diagnosebundel
De diagnostische bundel bevat mogelijk de volgende informatie:

- **Hoofdframeafbeeldingen**: Afbeeldingen van de omgeving die tijdens de sessie zijn vastgelegd terwijl de diagnose is ingeschakeld.
- **Logboeken**: Logboekgebeurtenissen die zijn geregistreerd door de runtime.
- **Metagegevens van de sessie:** metagegevens die de sessie identificeren.
