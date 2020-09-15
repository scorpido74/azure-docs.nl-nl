---
title: Afbeeldings binding
description: Installatie van grafische bindingen en use cases
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.custom: devx-track-csharp
ms.openlocfilehash: 3d0628777fbd6250fff4bb8347461d206d13782d
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561870"
---
# <a name="graphics-binding"></a>Afbeeldings binding

Als u de externe rendering van Azure in een aangepaste toepassing wilt gebruiken, moet u deze integreren in de rendering-pijp lijn van de toepassing. Deze integratie is de verantwoordelijkheid van de grafische binding.

Zodra de afbeeldings binding is ingesteld, hebt u toegang tot verschillende functies die van invloed zijn op de gerenderde afbeelding. Deze functies kunnen worden onderverdeeld in twee categorieën: algemene functies die altijd beschikbaar zijn en specifieke functies die alleen relevant zijn voor de geselecteerde `Microsoft.Azure.RemoteRendering.GraphicsApiType` .

## <a name="graphics-binding-in-unity"></a>Afbeeldings binding in eenheid

In eenheid wordt de volledige binding verwerkt door de struct die is `RemoteUnityClientInit` door gegeven aan `RemoteManagerUnity.InitializeManager` . Als u de grafische modus wilt instellen, moet u het `GraphicsApiType` veld instellen op de gekozen binding. Het veld wordt automatisch ingevuld, afhankelijk van of er een XRDevice aanwezig is. Het gedrag kan hand matig worden overschreven met het volgende gedrag:

* **HoloLens 2**: de binding van de [Windows Mixed Reality](#windows-mixed-reality) -afbeelding wordt altijd gebruikt.
* **Platte UWP bureau blad-app**: [simulatie](#simulation) wordt altijd gebruikt.
* **Unity editor**: [simulatie](#simulation) wordt altijd gebruikt tenzij er een WMR VR-headset is verbonden in welk geval ARR wordt uitgeschakeld, zodat er fouten in de niet-ARR gerelateerde onderdelen van de toepassing kunnen worden opgespoord. Zie ook [Holographic Remoting](../how-tos/unity/holographic-remoting.md).

Het enige andere relevante deel voor de eenheid heeft toegang tot de [basis binding](#access). alle andere secties hieronder kunnen worden overgeslagen.

## <a name="graphics-binding-setup-in-custom-applications"></a>Installatie van afbeeldings binding in aangepaste toepassingen

Als u een afbeeldings binding wilt selecteren, voert u de volgende twee stappen uit: eerst moet de binding van de afbeelding statisch worden geïnitialiseerd wanneer het programma wordt geïnitialiseerd:

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.graphicsApi = GraphicsApiType::WmrD3D11;
managerInit.connectionType = ConnectionType::General;
managerInit.right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering
```

De bovenstaande aanroep is vereist voor het initialiseren van de externe rendering van Azure in de Holographic-Api's. Deze functie moet worden aangeroepen voordat een Holographic-API wordt aangeroepen en voordat andere Api's voor het weer geven van externe rendering worden gebruikt. Op dezelfde manier moet de bijbehorende functie deinit `RemoteManagerStatic.ShutdownRemoteRendering();` worden aangeroepen nadat er geen Holographic-api's meer worden aangeroepen.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Grafische binding openen

Zodra een client is ingesteld, is de basis koppeling voor afbeeldingen toegankelijk met de `AzureSession.GraphicsBinding` getter. Als voor beeld kunnen de laatste frame statistieken als volgt worden opgehaald:

```cs
AzureSession currentSession = ...;
if (currentSession.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (*binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Grafische Api's

Er zijn momenteel twee grafische Api's die kunnen worden geselecteerd, `WmrD3D11` en `SimD3D11` . Er bestaat al een derde account `Headless` , maar dit wordt nog niet ondersteund aan de client zijde.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11` is de standaard binding die op HoloLens 2 moet worden uitgevoerd. De binding wordt gemaakt `GraphicsBindingWmrD3d11` . In deze modus wordt Azure remote rendering rechtstreeks in de Holographic-Api's weer gegeven.

Voor toegang tot de afgeleide grafische bindingen moet de basis `GraphicsBinding` cast zijn.
Er zijn twee dingen die moeten worden uitgevoerd voor het gebruik van de WMR-binding:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Op de hoogte brengen van een externe rendering van het gebruikte coördinaten systeem

```cs
AzureSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (*wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```

Waar de bovenstaande `ptr` moet een verwijzing zijn naar een systeem eigen `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` object dat het wereld wijde coördinaten systeem definieert waarin de coördinaten in de API worden uitgedrukt.

#### <a name="render-remote-image"></a>Externe installatie kopie weer geven

Aan het begin van elk frame moet het externe frame worden weer gegeven in de back-buffer. Dit wordt gedaan door `BlitRemoteFrame` aan te roepen, waardoor zowel kleur-als diepte gegevens worden gevuld in het momenteel gekoppelde weergave doel. Het is dus belang rijk dat u dit doet nadat u de back-upbuffer hebt gebonden als een weergave doel.

```cs
AzureSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>Simulatie

`GraphicsApiType.SimD3D11` is de simulatie binding en als deze is geselecteerd, wordt de `GraphicsBindingSimD3d11` grafische binding gemaakt. Deze interface wordt gebruikt voor het simuleren van de hoofd verplaatsing, bijvoorbeeld in een bureaublad toepassing en het renderen van een monoscopic-installatie kopie.

Als u de simulatie binding wilt implementeren, is het belang rijk dat u begrijpt wat het verschil is tussen de lokale camera en het externe frame, zoals wordt beschreven op de pagina [camera](../overview/features/camera.md) .

Er zijn twee camera's nodig:

* **Lokale camera**: deze camera vertegenwoordigt de huidige camera positie die wordt aangedreven door de toepassings logica.
* **Proxy camera**: deze camera komt overeen met het huidige *externe frame* dat is verzonden door de server. Wanneer er een vertraging optreedt tussen de client die een frame aanvraagt en de aankomst, is het *externe frame* altijd een bit achter de verplaatsing van de lokale camera.

De basis benadering is dat zowel de externe installatie kopie als de lokale inhoud worden weer gegeven in een niet-scherm doel met de proxy camera. De proxy installatie kopie wordt vervolgens opnieuw geprojecteerd naar de lokale camera ruimte, die verder wordt uitgelegd in een [vertraagde herprojectie](../overview/features/late-stage-reprojection.md)van het stadium.

De instelling is een beetje meer betrokken en werkt als volgt:

#### <a name="create-proxy-render-target"></a>Proxy weergave doel maken

Externe en lokale inhoud moeten worden gerenderd naar een weergave doel met een achtergrond kleur/diepte met de proxy camera gegevens die door de functie worden geleverd `GraphicsBindingSimD3d11.Update` .

De proxy moet overeenkomen met de resolutie van de back-upbuffer en moet een geheel getal zijn *DXGI_FORMAT_R8G8B8A8_UNORM* -of *DXGI_FORMAT_B8G8R8A8_UNORM* -indeling. Wanneer een sessie gereed is, `GraphicsBindingSimD3d11.InitSimulation` moet worden aangeroepen voordat er verbinding mee kan worden gemaakt:

```cs
AzureSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

De init-functie moet worden geleverd met pointers naar het systeem eigen D3D-apparaat en naar het kleur-en diepte patroon van het doel van de proxy weergave. Eenmaal is geïnitialiseerd `AzureSession.ConnectToRuntime` en `DisconnectFromRuntime` kan meerdere keren worden aangeroepen, maar wanneer u overschakelt naar een andere sessie, `GraphicsBindingSimD3d11.DeinitSimulation` moet eerst worden aangeroepen voor de oude sessie voordat deze `GraphicsBindingSimD3d11.InitSimulation` kan worden aangeroepen in een andere sessie.

#### <a name="render-loop-update"></a>Lus-update weer geven

De update voor de render-lus bestaat uit meerdere stappen:

1. Elk frame, voordat een rendering plaatsvindt, `GraphicsBindingSimD3d11.Update` wordt aangeroepen met de huidige camera transformatie die wordt verzonden naar de server die moet worden gerenderd. Op hetzelfde moment moet de geretourneerde proxy transformatie worden toegepast op de proxy camera om weer te geven in het doel van de proxy weergave.
Als de geretourneerde proxy update `SimulationUpdate.frameId` Null is, zijn er nog geen externe gegevens. In dit geval moet u in plaats van het renderen van het doel van de proxy weer geven, alle lokale inhoud rechtstreeks met de huidige camera gegevens naar de back-buffer worden gerenderd en de volgende twee stappen worden overgeslagen.
1. De toepassing moet nu het doel en de oproep van de proxy-rendering binden `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy` . Hiermee worden de externe kleur en diepte gegevens in het doel van de proxy weergave opgevuld. Lokale inhoud kan nu worden weer gegeven op de proxy met behulp van de trans formatie van de proxy camera.
1. Vervolgens moet de back-buffer zijn gebonden als een weergave doel en worden `GraphicsBindingSimD3d11.ReprojectProxy` aangeroepen waarop de back buffer kan worden weer gegeven.

```cs
AzureSession currentSession = ...;
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

```cpp
ApiHandle<AzureSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdate update;
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate;
simBinding->Update(update, &proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="api-documentation"></a>API-documentatie

* [C# RemoteManagerStatic. StartupRemoteRendering ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [C# GraphicsBinding-klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [C# GraphicsBindingWmrD3d11-klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [C# GraphicsBindingSimD3d11-klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [Structuur van C++ RemoteRenderingInitialization](https://docs.microsoft.com/cpp/api/remote-rendering/remoterenderinginitialization)
* [C++ GraphicsBinding-klasse](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbinding)
* [C++ GraphicsBindingWmrD3d11-klasse](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [C++ GraphicsBindingSimD3d11-klasse](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>Volgende stappen

* [Camera](../overview/features/camera.md)
* [Vertraagde fase van het project](../overview/features/late-stage-reprojection.md)
* [Zelfstudie: Extern gegenereerde modellen bekijken](../tutorials/unity/view-remote-models/view-remote-models.md)
