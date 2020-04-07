---
title: Externe rendering instellen voor eenheid
description: Azure Remote Rendering initialiseren in een Unity-project
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681140"
---
# <a name="set-up-remote-rendering-for-unity"></a>Externe rendering instellen voor eenheid

Om Azure Remote Rendering (ARR) in Unity in te schakelen, bieden we speciale methoden die zorgen voor een aantal Unity-specifieke aspecten.

## <a name="startup-and-shutdown"></a>Opstarten en afsluiten

Als u Externe rendering `RemoteManagerUnity`wilt initialiseren, gebruikt u . Deze klasse roept `RemoteManager` de generieke, maar al implementeert Unity-specifieke details voor u. Unity maakt bijvoorbeeld gebruik van een specifiek coördinatensysteem. Bij `RemoteManagerUnity.Initialize`het bellen zal de juiste conventie worden opgezet. Het gesprek vereist ook dat u de Unity-camera levert die moet worden gebruikt voor het weergeven van de op afstand weergegeven inhoud.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Voor het afsluiten van `RemoteManagerStatic.ShutdownRemoteRendering()`Externe rendering, bel .

Nadat `AzureSession` een is gemaakt en gekozen als de primaire `RemoteManagerUnity`rendering sessie, moet deze worden geregistreerd bij:

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Volledige voorbeeldcode

De onderstaande code toont alle stappen die nodig zijn om Azure Remote Rendering in Unity te initialiseren:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Gemaksfuncties

### <a name="session-state-events"></a>Sessiestatusgebeurtenissen

`RemoteManagerUnity.OnSessionUpdate`ziet u in gebeurtenissen voor wanneer de sessiestatus wordt gewijzigd, raadpleegt u de codedocumentatie voor meer informatie.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity`is een optioneel onderdeel om het installatie- en sessiebeheer te stroomlijnen. Het bevat opties om de sessie automatisch te stoppen wanneer de toepassing wordt verlaten of de play-modus wordt afgesloten in de editor, evenals automatisch verlengen van de sessie lease wanneer dat nodig is. Het caches gegevens zoals de sessie-eigenschappen (zie de variabele) `LastProperties` en stelt gebeurtenissen bloot voor sessiestatuswijzigingen en sessiefouten.

Er kunnen niet meer dan `ARRServiceUnity` één exemplaar tegelijk zijn. Het is bedoeld om u sneller op weg te helpen door een aantal algemene functionaliteitte implementeren. Voor een grotere toepassing kan het beter zijn om die dingen zelf te doen, dat wel.

Zie `ARRServiceUnity` [Zelfstudie: Een Unity-project vanaf nul instellen](../../tutorials/unity/project-setup.md)en gebruiken voor een voorbeeld hoe u het instellen en gebruiken gebruikt.

## <a name="next-steps"></a>Volgende stappen

* [Het remote rendering-pakket voor Unity installeren](install-remote-rendering-unity-package.md)
* [Zelfstudie: Een Unity-project vanaf nul instellen](../../tutorials/unity/project-setup.md)
