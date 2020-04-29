---
title: Azure front-end-Api's voor verificatie
description: Uitleg over het gebruik van de C#-frontend-API voor verificatie
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681348"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>De Azure-frontend-API's gebruiken voor verificatie

In deze sectie wordt beschreven hoe u de C#-API voor verificatie gebruikt.

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

AzureFrontendAccountInfo wordt gebruikt voor het instellen van de verificatie gegevens voor ```AzureFrontend``` een instantie in de SDK.

De belangrijkste velden zijn:

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

Voor het _regio_ deel in het domein gebruikt u een [regio bij u](../reference/regions.md)in de buurt.

De account gegevens kunnen worden opgehaald uit de portal, zoals wordt beschreven in de alinea [account gegevens ophalen](create-an-account.md#retrieve-the-account-information) .

## <a name="azure-frontend"></a>Azure-front-end

De relevante klassen zijn ```AzureFrontend``` en ```AzureSession```. ```AzureFrontend```wordt gebruikt voor account beheer en functionaliteit op account niveau, waaronder: activa conversie en rendering-sessie maken. ```AzureSession```wordt gebruikt voor de functionaliteit op sessie niveau en bevat: sessie-update, query's, vernieuwen en buiten gebruik stellen.

Elk geopend/gemaakt ```AzureSession``` bevat een verwijzing naar het front-end dat het heeft gemaakt. Als u wilt opschonen, moeten alle sessies worden verwijderd voordat de toewijzing van de front-end wordt opgeheven.

Bij het ongedaan maken van de toewijzing van een sessie wordt `AzureSession.StopAsync` de VM in azure niet gestopt. deze moet expliciet worden aangeroepen.

Zodra een sessie is gemaakt en de status ervan is gemarkeerd als gereed, kan deze verbinding maken met de externe rendering runtime met `AzureSession.ConnectToRuntime`.

### <a name="threading"></a>Threading

Alle asynchrone aanroepen voor AzureSession en AzureFrontend worden uitgevoerd in een achtergrond thread, niet op de hoofd toepassings thread.

### <a name="conversion-apis"></a>Conversie-Api's

Zie voor meer informatie over de conversie service [de model conversie-rest API](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Activum conversie starten

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

#### <a name="get-conversion-status"></a>Conversie status ophalen

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

### <a name="rendering-apis"></a>Rendering-Api's

Zie [de rest API voor sessie beheer](session-rest-api.md) voor meer informatie over sessie beheer.

Een rendering-sessie kan dynamisch worden gemaakt voor de service of een al bestaande sessie-ID kan worden geopend in een AzureSession-object.

#### <a name="create-rendering-session"></a>Rendering-sessie maken

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

#### <a name="open-an-existing-rendering-session"></a>Een bestaande rendering-sessie openen

Het openen van een bestaande sessie is een synchrone aanroep.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>Huidige rendering-sessies ophalen

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

### <a name="session-apis"></a>Sessie-Api's

#### <a name="get-rendering-session-properties"></a>Eigenschappen van rendering-sessie ophalen

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

#### <a name="update-rendering-session"></a>Rendering-sessie bijwerken

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

#### <a name="stop-rendering-session"></a>Rendering-sessie stoppen

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

#### <a name="connect-to-arr-inspector"></a>Verbinding maken met ARR Inspector

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
* [PowerShell-voorbeeldscripts](../samples/powershell-example-scripts.md)
