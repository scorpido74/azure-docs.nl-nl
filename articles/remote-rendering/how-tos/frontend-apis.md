---
title: Azure Frontend API's voor verificatie
description: Legt uit hoe u de C# frontend API gebruiken voor verificatie
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681348"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>De Azure Frontend API's gebruiken voor verificatie

In deze sectie beschrijven we hoe we de C#API kunnen gebruiken voor verificatie.

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

AzureFrontendAccountInfo wordt gebruikt om de verificatiegegevens voor een ```AzureFrontend``` instantie in de SDK in te stellen.

De belangrijke velden zijn:

```cs

    public class AzureFrontendAccountInfo
    {
        // Something akin to "<region>.mixedreality.azure.com"
        public string AccountDomain;

        // Can use one of:
        // 1) ID and Key.
        // 2) AuthenticationToken.
        // 3) AccessToken.
        public string AccountId = Guid.Empty.ToString();
        public string AccountKey = string.Empty;
        public string AuthenticationToken = string.Empty;
        public string AccessToken = string.Empty;
    }

```

Gebruik voor het _regiodeel_ in het domein een [regio bij u in de buurt.](../reference/regions.md)

De accountgegevens kunnen worden verkregen bij de portal zoals beschreven in de alinea [accountgegevens ophalen.](create-an-account.md#retrieve-the-account-information)

## <a name="azure-frontend"></a>Azure Frontend

De relevante klassen ```AzureFrontend``` ```AzureSession```zijn en . ```AzureFrontend```wordt gebruikt voor accountbeheer en functionaliteit op accountniveau, waaronder: asset conversie en rendering sessie creatie. ```AzureSession```wordt gebruikt voor functionaliteit op sessieniveau en omvat: sessie-update, query's, vernieuwing en ontmanteling.

Elke geopende/gemaakte ```AzureSession``` zal een verwijzing naar de frontend die het heeft gemaakt. Om netjes af te sluiten, moeten alle sessies worden toegewezen voordat de frontend zal worden deallocated.

Als u een sessie deallocatie afwijst, wordt de VM op Azure niet gestopt, `AzureSession.StopAsync` moet expliciet worden aangeroepen.

Zodra een sessie is gemaakt en de status is gemarkeerd als gereed, `AzureSession.ConnectToRuntime`kan deze verbinding maken met de runtime voor externe rendering met .

### <a name="threading"></a>Threading

Alle AzureSession- en AzureFrontend-async-oproepen worden voltooid in een achtergrondthread, niet de hoofdtoepassingsthread.

### <a name="conversion-apis"></a>Conversie-API's

Zie [de API voor de rest van de modelconversie voor](conversion/conversion-rest-api.md)meer informatie over de conversieservice .

#### <a name="start-asset-conversion"></a>Assetconversie starten

``` cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string modelName, string modelUrl, string assetContainerUrl)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionParams(modelName, modelUrl, assetContainerUrl));
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

#### <a name="get-conversion-status"></a>Conversiestatus genereren

``` cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

### <a name="rendering-apis"></a>Rendering API's

Zie [de REST API voor sessiebeheer](session-rest-api.md) voor meer informatie over sessiebeheer.

Een renderingsessie kan dynamisch worden gemaakt op de service of een reeds bestaande sessie-id kan worden 'geopend' in een AzureSession-object.

#### <a name="create-rendering-session"></a>Renderingsessie maken

``` cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="open-an-existing-rendering-session"></a>Een bestaande renderingsessie openen

Het openen van een bestaande sessie is een synchrone oproep.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>Huidige renderingsessies bekijken

``` cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

### <a name="session-apis"></a>Session API's

#### <a name="get-rendering-session-properties"></a>Eigenschappen van renderingsessie

``` cs
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="update-rendering-session"></a>Renderingsessie bijwerken

``` cs
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="stop-rendering-session"></a>Renderingsessie stoppen

``` cs
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="connect-to-arr-inspector"></a>Verbinding maken met ARR-inspecteur

``` cs
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session, string hostname)
{
    _pendingAsync = session.ConnectToArrInspectorAsync(hostname);
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
#if WINDOWS_UWP
                UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
                {
                    var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
                    await Windows.System.Launcher.LaunchFileAsync(file);
                }, true);
#else
                InvokeOnAppThreadAsync(() =>
                {
                    System.Diagnostics.Process.Start("file:///" + htmlPath);
                });
#endif
            }
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

## <a name="next-steps"></a>Volgende stappen

* [Een account maken](create-an-account.md)
* [Voorbeeld PowerShell-scripts](../samples/powershell-example-scripts.md)
