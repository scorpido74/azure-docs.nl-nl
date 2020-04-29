---
title: Remote Rendering voor Unity instellen
description: Externe rendering van Azure initialiseren in een Unity-project
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681140"
---
# <a name="set-up-remote-rendering-for-unity"></a>Remote Rendering voor Unity instellen

Voor het inschakelen van de externe rendering van Azure (ARR) in eenheid, bieden we speciale methoden die de specifieke aspecten van de maat eenheid verzorgen.

## <a name="startup-and-shutdown"></a>Opstarten en afsluiten

Gebruik `RemoteManagerUnity`om de externe rendering te initialiseren. Deze klasse maakt deel uit van `RemoteManager` het generieke, maar implementeert al unit-specifieke Details voor u. Unit maakt bijvoorbeeld gebruik van een specifiek coördinaten systeem. Wanneer u `RemoteManagerUnity.Initialize`aanroept, wordt de juiste Conventie ingesteld. Voor de aanroep moet u ook de eenheids camera opgeven die moet worden gebruikt voor het weer geven van de extern gerenderde inhoud.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Voor het afsluiten van externe rendering, roept `RemoteManagerStatic.ShutdownRemoteRendering()`u aan.

Nadat een `AzureSession` is gemaakt en als primaire rendering-sessie is gekozen, moet deze zijn geregistreerd bij `RemoteManagerUnity`:

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Volledige voorbeeld code

In de volgende code ziet u alle stappen die nodig zijn voor het initialiseren van Azure remote rendering in Unity:

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

## <a name="convenience-functions"></a>Gebruiks gemak functies

### <a name="session-state-events"></a>Sessie status gebeurtenissen

`RemoteManagerUnity.OnSessionUpdate`Raadpleeg de code documentatie voor meer informatie over het meebrengen van gebeurtenissen voor de sessie status.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity`is een optioneel onderdeel voor het stroom lijnen van het instellen en beheren van sessies. Het bevat opties voor het automatisch stoppen van de sessie wanneer de toepassing wordt afgesloten of de afspeel modus wordt afgesloten in de editor, en de sessie lease automatisch vernieuwen wanneer dat nodig is. De gegevens worden in de cache opgeslagen, zoals de sessie- `LastProperties` eigenschappen (Zie de variabele), en er worden gebeurtenissen weer gegeven voor sessie status wijzigingen en sessie fouten.

Er mag niet meer dan één exemplaar van `ARRServiceUnity` tegelijkertijd zijn. Het is bedoeld om sneller aan de slag te gaan met het implementeren van enkele algemene functionaliteit. Voor een grotere toepassing is het mogelijk beter om deze dingen zelf te doen, maar ook.

Zie `ARRServiceUnity` [zelf studie: een geheel nieuw eenheids project](../../tutorials/unity/project-setup.md)instellen voor een voor beeld van het instellen en gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [Het Remote Rendering-pakket voor Unity installeren](install-remote-rendering-unity-package.md)
* [Zelf studie: een geheel nieuw eenheids project instellen](../../tutorials/unity/project-setup.md)
