---
title: Grafische binding
description: Opstelling van grafische bindingen en use cases
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681881"
---
# <a name="graphics-binding"></a>Grafische binding

Om Azure Remote Rendering in een aangepaste toepassing te kunnen gebruiken, moet deze worden geïntegreerd in de renderingpipeline van de toepassing. Deze integratie is de verantwoordelijkheid van de grafische binding.

Eenmaal ingesteld, geeft de grafische binding toegang tot verschillende functies die van invloed zijn op de gerenderde afbeelding. Deze functies kunnen worden onderverdeeld in twee categorieën: algemene functies die altijd `Microsoft.Azure.RemoteRendering.GraphicsApiType`beschikbaar zijn en specifieke functies die alleen relevant zijn voor de geselecteerde .

## <a name="graphics-binding-in-unity"></a>Grafische binding in Unity

In Unity wordt de gehele `RemoteUnityClientInit` binding behandeld door `RemoteManagerUnity.InitializeManager`de struct overgegaan in . Om de grafische modus `GraphicsApiType` in te stellen, moet het veld worden ingesteld op de gekozen binding. Het veld wordt automatisch ingevuld, afhankelijk van of er een XRDevice aanwezig is. Het gedrag kan handmatig worden overschreven met de volgende gedragingen:

* **HoloLens 2**: de [Windows Mixed Reality](#windows-mixed-reality) grafische binding wordt altijd gebruikt.
* **Platte UWP desktop app**: [Simulatie](#simulation) wordt altijd gebruikt. Als u deze modus wilt gebruiken, moet u de stappen in [Zelfstudie: Een Unity-project vanaf nul instellen.](../tutorials/unity/project-setup.md)
* **Unity editor**: [Simulatie](#simulation) wordt altijd gebruikt, tenzij een WMR VR-headset is aangesloten in welk geval ARR wordt uitgeschakeld om de niet-ARR gerelateerde delen van de toepassing te debuggen. Zie ook [holografische remoting](../how-tos/unity/holographic-remoting.md).

Het enige andere relevante deel voor Unity is toegang tot de [basisbinding,](#access)alle andere secties hieronder kunnen worden overgeslagen.

## <a name="graphics-binding-setup-in-custom-applications"></a>Grafische bindingsinstelling in aangepaste toepassingen

Als u een grafische binding wilt selecteren, neemt u de volgende twee stappen: Eerst moet de grafische binding statisch worden geinitialiseerd wanneer het programma wordt geïnitialiseerd:

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

De bovenstaande aanroep is nodig om Azure Remote Rendering te initialiseren in de holografische API's. Deze functie moet worden aangeroepen voordat een holografische API wordt aangeroepen en voordat andere API's voor externe rendering worden geopend. Op dezelfde manier moet de `RemoteManagerStatic.ShutdownRemoteRendering();` bijbehorende de-init-functie worden aangeroepen nadat er geen holografische API's meer worden aangeroepen.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Toegang tot grafische binding

Zodra een client is ingesteld, kan de basisgrafische `AzureSession.GraphicsBinding` binding worden geopend met de getter. Als voorbeeld kunnen de laatste framestatistieken als volgt worden opgehaald:

``` cs
AzureSession currentSesson = ...;
if (currentSesson.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Grafische API's

Er zijn momenteel twee grafische API's die kunnen worden geselecteerd, `WmrD3D11` en `SimD3D11`. Een derde `Headless` bestaat, maar wordt nog niet ondersteund aan de clientkant.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`is de standaardbinding die moet worden uitgevoerd op HoloLens 2. Het zal `GraphicsBindingWmrD3d11` de binding creëren. In deze modus haakt Azure Remote Rendering rechtstreeks in de holografische API's.

Om toegang te krijgen tot `GraphicsBinding` de afgeleide grafische bindingen, moet de basis worden gegoten.
Er zijn twee dingen die gedaan moeten worden om de WMR-binding te gebruiken:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Remote Rendering informeren over het gebruikte coördinatensysteem

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

Wanneer het `ptr` bovenstaande een aanwijzer `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` moet zijn naar een native object dat het coördinatensysteem voor de wereldruimte definieert waarin coördinaten in de API worden uitgedrukt.

#### <a name="render-remote-image"></a>Externe afbeelding weergeven

Aan het begin van elk frame moet het externe frame worden weergegeven in de achterbuffer. Dit wordt gedaan `BlitRemoteFrame`door aan te roepen , die zowel kleur- als diepte-informatie in vult in het momenteel gebonden renderdoel. Het is dus belangrijk dat dit gebeurt na het binden van de rugbuffer als renderdoel.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>Simulatie

`GraphicsApiType.SimD3D11`is de simulatiebinding en maakt `GraphicsBindingSimD3d11` indien geselecteerd de grafische binding. Deze interface wordt gebruikt om hoofdbeweging te simuleren, bijvoorbeeld in een desktopapplicatie en maakt een monoscopische afbeelding weer.
De setup is een beetje meer betrokken en werkt als volgt:

#### <a name="create-proxy-render-target"></a>Proxyrenderdoel maken

Externe en lokale inhoud moet worden gerenderd naar een offscreen kleur / diepte render `GraphicsBindingSimD3d11.Update` doel genaamd 'proxy' met behulp van de proxy camera gegevens die door de functie. De proxy moet overeenkomen met de resolutie van de backbuffer. Zodra een sessie `GraphicsBindingSimD3d11.InitSimulation` klaar is, moet worden aangeroepen voordat u verbinding maakt met het:

``` cs
AzureSession currentSesson = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

De init-functie moet worden voorzien van verwijzingen naar het native d3d-apparaat en naar de kleur en dieptetextuur van het proxyrenderdoel. `AzureSession.ConnectToRuntime` Eenmaal geïnitialiseerd, `DisconnectFromRuntime` en kan meerdere keren worden `GraphicsBindingSimD3d11.DeinitSimulation` opgeroepen, maar bij het `GraphicsBindingSimD3d11.InitSimulation` overschakelen naar een andere sessie, moet eerst worden opgeroepen op de oude sessie voordat kan worden opgeroepen op een andere sessie.

#### <a name="render-loop-update"></a>Update renderlus

De update renderloop bestaat uit meerdere stappen:

1. Elk frame, voordat er `GraphicsBindingSimD3d11.Update` rendering plaatsvindt, wordt aangeroepen met de huidige cameratransformatie die naar de server wordt verzonden om te worden weergegeven. Tegelijkertijd moet de geretourneerde proxy-transformatie worden toegepast op de proxycamera om te renderen in het proxy renderdoel.
Als de geretourneerde proxy-update `SimulationUpdate.frameId` nietig is, zijn er nog geen externe gegevens. In dit geval moet, in plaats van het renderen in het proxyrenderdoel, alle lokale inhoud rechtstreeks naar de backbuffer worden gerenderd met behulp van de huidige cameragegevens en worden de volgende twee stappen overgeslagen.
1. De toepassing moet nu binden de `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`proxy render doel en aanroepen . Hiermee wordt de externe kleur- en diepte-informatie in het proxyrenderdoel ingevuld. Alle lokale inhoud kan nu worden gerenderd op de proxy met behulp van de proxy camera transformeren.
1. Vervolgens moet de terugbuffer worden gebonden als `GraphicsBindingSimD3d11.ReprojectProxy` een renderdoel en op welk punt de terugbuffer kan worden gepresenteerd.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Een Unity-project vanaf nul instellen](../tutorials/unity/project-setup.md)
