---
title: Een geheel nieuw Unity-project opstarten
description: Hier wordt uitgelegd hoe u een blanco Unity-project configureert voor gebruik met Azure Remote Rendering.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 7901f12763cb97fa76c0908e76755247ae934a20
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300586"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Zelfstudie: Een geheel nieuw Unity-project opstarten

In deze zelfstudie leert u:

> [!div class="checklist"]
>
> * Het configureren van een geheel nieuw Unity-project voor ARR.
> * Rendering-sessies maken en stoppen.
> * Bestaande sessies opnieuw gebruiken.
> * Verbinden en verbreken van sessies.
> * Modellen laden in een rendering-sessie.
> * Verbindingsstatistieken weergeven.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Uw accountinformatie (account-id, accountsleutel, abonnements-id). Als u nog geen account hebt, [maakt u er een](../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* De nieuwste versie van Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/). 
* [Visual Studio Tools voor Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). Met name de volgende *workload*-installaties zijn verplicht:
  * **Desktopontwikkeling met C++**
  * **Universal Windows Platform (UWP)-ontwikkeling**
* Git [(downloaden)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
  * Installeer de volgende modules in Unity:
    * **UWP**: ondersteuning voor UWP-builds (Universeel Windows-platform)
    * **IL2CPP**: ondersteuning voor Windows-builds (IL2CPP)

> [!TIP]
> De [ARR-voorbeeldrepository](https://github.com/Azure/azure-remote-rendering) bevat voorbereide Unity-projecten voor alle zelfstudies. U kunt deze projecten als referentie gebruiken.

## <a name="create-a-new-unity-project"></a>Een nieuw Unity-project maken

Maak vanuit de Unity-hub een nieuw project.
In dit voorbeeld wordt ervan uitgegaan dat het project wordt gemaakt in een map met de naam `RemoteRendering`.

![venster Nieuw project](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Het manifest van het project configureren

U moet het bestand wijzigen `Packages/manifest.json` dat zich in de Unity-projectmap bevindt. Open het bestand in een teksteditor en voeg de volgende regels toe:

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

Het pijplijnpakket voor universele rendering is optioneel, maar wordt om prestatieredenen aanbevolen.
Nadat u het manifest hebt gewijzigd en opgeslagen, wordt Unity automatisch vernieuwd. Controleer of de pakketten zijn geladen in het venster *Project*:

![invoer van pakketten bevestigen](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Zorg ervoor dat u de nieuwste versie van het pakket hebt

Volg de volgende stappen om ervoor te zorgen dat uw project de nieuwste versie van het pakket voor remote rendering gebruikt.
1. Selecteer het pakket in het venster Project en klik op het pictogram :::no-loc text="package":::: ![Het pakket-pictogram selecteren](media/package-icons.png)
1. Klik in de Inspector op Weergeven in pakketbeheer: ![pakketcontrole](media/package-properties.png)
1. Kijk op de pakketbeheerpagina voor het pakket voor remote rendering of de knop Bijwerken beschikbaar is. Als dit het geval is, kunt u het pakket naar de meest recente beschikbare versie bijwerken door erop te klikken: ![Het ARR-pakket in Pakketbeheer](media/package-manager.png)
1. Soms kan het bijwerken van het pakket leiden tot fouten in de console. Als dit het geval is, sluit u het project en opent u het opnieuw.

## <a name="configure-the-camera"></a>De camera configureren

Selecteer het **hoofdcamera**-knooppunt.

1. Stel de *Transformatie* opnieuw in:

    ![cameratransformatie opnieuw instellen](media/camera-reset-transform.png)

1. Stel **:::no-loc text="Clear flags":::** in op *:::no-loc text="Solid Color":::*

1. Stel **:::no-loc text="Background":::** in op *:::no-loc text="Black":::*

1. Stel de **:::no-loc text="Clipping Planes":::** in op *Near = 0,3* en *Far = 20*. Dit betekent dat rendering geometrie zal afknippen/bijsnijden van dichter dan 30 cm of verder dan 20 meter.

    ![Eigenschappen van Unity-camera](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>De projectinstellingen aanpassen

1. Open *Bewerken > Projectinstellingen...*
1. Selecteer in de lijst aan de linkerkant de optie Kwaliteit.
1. Wijzig het **Standaardkwaliteitsniveau** in *Laag*

    ![instellingen voor projectkwaliteit wijzigen](media/settings-quality.png)

1. Selecteer **Graphics** aan de linkerkant.
1. Wijzig de instelling **Scriptable Rendering Pipeline** in *HybridRenderingPipeline*. Sla deze stap over als de universele rendering-pijplijn niet wordt gebruikt.

    ![instellingen van projectgraphics wijzigen](media/settings-graphics-lwrp.png) Soms wordt de lijst met beschikbare pijplijntypen van de pakketten niet door de gebruikersinterface ingevuld. In dat geval moet de *HybridRenderingPipeline*-asset handmatig naar het veld worden gesleept: ![instellingen van projectgraphics wijzigen](media/hybrid-rendering-pipeline.png)
1. Selecteer **Speler** aan de linkerkant.
1. Selecteer het tabblad **Instellingen Universeel Windows-platform**.
1. Wijzig de **XR-instellingen** om Windows Mixed Reality te ondersteunen: ![Player-instellingen](media/xr-player-settings.png)
1. Selecteer de instellingen zoals in de bovenstaande schermopname:
    1. Schakel **Virtual Reality ondersteund** in
    1. **Diepte-indeling instellen** op *16-bits diepte*
    1. Schakel **diepte buffer delen** in
    1. Stel **Stereo renderingsmodus** in op *Single Pass Instanced*

1. Vouw in hetzelfde venster boven *XR-instellingen***Publicatie-instellingen uit.**
1. Schuif omlaag naar **Mogelijkheden** en selecteer:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * Optioneel voor ontwikkeling: **PrivateNetworkClientServer**

      Deze optie is nodig als u de Unity-foutopsporing met uw apparaat wilt verbinden.

1. Schakel in **Ondersteunde apparaten**, **Holographic** en **Desktop** in

1. Als u de Mixed Reality-toolkit wilt gebruiken, raadpleegt u de [MRTK-documentatie](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview) voor meer informatie over aanbevolen instellingen en mogelijkheden.

## <a name="validate-project-setup"></a>Projectinstellingen valideren

Voer de volgende stappen uit om te controleren of de projectinstellingen juist zijn.

1. Kies de vermelding ValidateProject in het menu RemoteRendering in de werkbalk van de Unity-editor.
1. Gebruik het venster ValidateProject om de projectinstellingen waar nodig te controleren en te herstellen.

    ![Projectvalidatie in Unity-editor](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Een script maken om Azure Remote Rendering te initialiseren

Maak een [nieuw script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) en geef het de naam **RemoteRendering**. Open het scriptbestand en vervang de volledige inhoud door de onderstaande code:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

Met dit script wordt Azure Remote Rendering geïnitialiseerd, wordt aangegeven welk cameraobject moet worden gebruikt voor rendering en wordt de knop **Sessie maken** in de viewport geplaatst, wanneer de *afspeelmodus* wordt geactiveerd.

> [!CAUTION]
> Als u het script wijzigt en opslaat terwijl de afspeelmodus actief is in Unity, kan dit ertoe leiden dat Unity vastloopt en u deze moet afsluiten in Taakbeheer. Stop de afspeelmodus daarom altijd voordat u het *RemoteRendering*-cript bewerkt.

## <a name="test-azure-remote-rendering-session-creation"></a>Het maken van een sessie met Azure Remote Rendering testen

Maak een nieuwe GameObject in de scène en voeg de*RemoteRendering*-component toe. Vul het juiste *Accountdomein*, *Account-ID* en *Accountsleutel* in voor uw Remote Rendering-account:

![Eigenschappen van de Remote Rendering-component](media/remote-rendering-component.png)

Start de toepassing in de editor (**druk op Play** of CTRL + P). De knop **Sessie maken** wordt weer gegeven in de viewport. Klik hierop om uw eerste ARR-sessie te starten:

![Een eerste sessie maken](media/test-create.png)

Als dit mislukt, controleer dan of u uw accountgegevens correct hebt ingevoerd in de eigenschappen van de RemoteRendering-component. Anders wordt er een bericht weergegeven in het consolevenster met de sessie-ID die aan u is toegewezen, en wordt aangegeven dat de sessie zich momenteel in de *begin*fase bevindt:

![Uitvoer van sessie starten](media/create-session-output.png)

Op dit moment wordt een server ingericht door Azure en wordt er een virtuele machine voor remote rendering gestart. Dit duurt **doorgaans 3 tot 5 minuten**. Wanneer de virtuele machine klaar is, wordt de `OnSessionStatusChanged` callback van het Unity-script uitgevoerd en wordt de nieuwe sessiestatus weergegeven:

![Uitvoer sessie gereed](media/create-session-output-2.png)

Dat is alles! Voorlopig gebeurt er dus niets meer. Als u kosten wilt voorkomen, moet u sessies altijd stopzetten wanneer ze niet meer nodig zijn. In dit voorbeeld kunt u dit doen door te klikken op de knop **Sessie stoppen** of door de Unity-simulatie te stoppen. Als gevolg van de eigenschap **Sessie automatisch stoppen** van de component *ARRServiceUnity*, die standaard is ingeschakeld, wordt de sessie automatisch voor u gestopt. Als dit niet lukt, vanwege crashes of verbindingsproblemen, wordt uw sessie door de server afgebroken wanneer deze de *MaxLeaseTime* heeft bereikt.

> [!NOTE]
> Het stoppen van een sessie heeft direct effect en kan niet ongedaan worden gemaakt. Nadat de functie is gestopt, moet u een nieuwe sessie maken, met dezelfde opstart-overhead.

## <a name="reusing-sessions"></a>Sessies opnieuw gebruiken

Het maken van een nieuwe sessie is helaas een tijdrovende bewerking. Daarom moet u proberen zo min mogelijk sessies te maken en deze waar mogelijk opnieuw te gebruiken.

Voeg de volgende code toe aan het *RemoteRendering*-script en verwijder de oude versies van de dubbele functies:

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> Voordat u deze code uitvoert, moet u ervoor zorgen dat u de optie **Sessie automatisch stoppen** in de component RemoteRendering heeft uitgeschakeld. Anders wordt elke sessie die u maakt automatisch gestopt wanneer u de simulatie stopt en kan deze niet opnieuw worden gebruikt.

Wanneer u op *Afspelen* klikt, krijgt u nu drie knoppen in de viewport. **Sessie maken**, **Opvragen van actieve sessies** en **Bestaande sessie gebruiken**. Met de eerste knop maakt u altijd een nieuwe sessie. Met de tweede knop kunt u *actieve* sessies opvragen. Als u niet handmatig een sessie-ID hebt opgegeven die u wilt gebruiken, dan selecteert deze actie de sessie-ID automatisch voor toekomstig gebruik. Met de derde knop kunt u verbinding maken met een bestaande sessie. Ofwel een die u handmatig heeft opgegeven via de componenteigenschap *Sessie-ID*, of een die is gevonden door *Actieve sessies opvragen*.

De functie **AutoStartSessionAsync** wordt gebruikt om het indrukken van de knop buiten de editor te simuleren.

> [!TIP]
> Het is mogelijk om sessies te openen die zijn gestopt, verlopen of een foutstatus hebben. Hoewel ze niet meer kunnen worden gebruikt voor rendering, kunt u de gegevens, opvragen nadat u een inactieve sessie hebt geopend. De bovenstaande code controleert de status van een sessie in `ARRService_OnSessionStarted` om automatisch te stoppen wanneer de sessie onbruikbaar wordt.

Met deze functionaliteit kunt u nu sessies maken en hergebruiken, wat uw ontwikkelingswerkstroom aanzienlijk zal verbeteren.

Normaal gesproken wordt het maken van een sessie buiten de clienttoepassing geactiveerd vanwege de tijd die nodig is om de server op te zetten.

## <a name="connect-to-an-active-session"></a>Verbinding maken met een actieve sessie

Tot nu toe hebben we sessies gemaakt of geopend. De volgende stap is het *verbinden* met een sessie. Nadat de verbinding tot stand is gebracht, produceert de rendering-server afbeeldingen en wordt een videostream naar onze toepassing verzonden.

Voeg de volgende code toe aan het *RemoteRendering*-script en verwijder de oude versies van de dubbele functies:

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

U kunt deze functionaliteit als volgt testen:

1. Druk op **Afspelen** in Unity.
1. Een sessie openen:
    1. Als u al een sessie hebt, drukt u op **Actieve sessies opvragen** en vervolgens op **Bestaande sessie gebruiken**.
    1. Als dat niet het geval is, drukt u op **Sessie maken**.
1. Druk op **Verbinden**.
1. Na een paar seconden moet de console-uitvoer weergeven dat u bent verbonden.
1. Voorlopig hoeft u niet anders te doen.
1. Druk op **Verbinding verbreken** of stop de afspeelmodus van Unity.

>[!NOTE]
> Meerdere gebruikers kunnen een sessie *openen* om de gegevens op te vragen, maar er kan slechts één gebruiker tegelijk met een sessie *verbonden* zijn. Als een andere gebruiker al is verbonden, mislukt de verbinding met een **handshake-fout**.

## <a name="load-a-model"></a>Een model laden

Voeg de volgende code toe aan het *RemoteRendering*-script en verwijder de oude versies van de dubbele functies:

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

Wanneer u nu op afspelen klikt, een sessie opent en er verbinding mee maakt, wordt de knop **Model laden** weergegeven. Nadat u erop hebt geklikt, wordt de voortgang van het laden weergegeven in de console-uitvoer en wanneer het 100% bereikt, ziet u het model van een engine:

![Model geladen in de editor](media/model-loaded-replace-me.png)

De [WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) is een belangrijk component dat wordt gebruikt voor [hologram-stabiliteit](https://docs.microsoft.com/windows/mixed-reality/hologram-stability). Deze wordt echter alleen toegepast wanneer deze op een Mixed Reality-apparaat wordt geïmplementeerd.

> [!TIP]
> Als u de [Quickstart hebt gevolgd: Een model converteren voor rendering](../../quickstarts/convert-model.md), weet u al hoe u uw eigen modellen kunt converteren. Het enige wat u nu hoeft te doen, is de URL van een geconverteerd model in de eigenschap *Modelnaam* plaatsen.

## <a name="display-frame-statistics"></a>Framestatistieken weergeven

Azure Remote Rendering houdt diverse informatie bij over de kwaliteit van de verbinding. Ga als volgt te werk om deze informatie snel weer te geven:

Maak een [nieuw script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) en geef het de naam **RemoteFrameStats**. Open het scriptbestand en vervang de volledige inhoud door de onderstaande code:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

Maak een GameObject en geef deze de naam *FrameStats*. Koppel deze als onderliggend knooppunt aan het object *Hoofdcamera* en stel de positie in op **x = 0, y = 0, z = 0,325**. Voeg het component **RemoteFrameStats** toe aan het object.

Voeg een onderliggend object **UI > Canvas** toe aan het object *FrameStats* en stel de eigenschappen als volgt in:

![canvaseigenschappen](media/framestats-canvas.png)

Voeg een object **UI > Tekst** toe als onderliggend element van het canvas en stel de eigenschappen als volgt in:

![teksteigenschappen](media/framestats-text.png)

Selecteer het object *FrameStats* en vul het veld **FrameStats** in door op het cirkelpictogram te klikken en het object **Tekst** te selecteren:

![Teksteigenschap instellen](media/framestats-set-text.png)

Wanneer u nu verbinding maakt met de externe sessie, zou de tekst de streaming-statistieken moeten weergeven:

![uitvoer van framestatistieken](media/framestats-output.png)

De code schakelt de statistiekenupdate buiten de editor uit, omdat een vergrendeld tekstvak storend zou zijn. In het [Quickstart](../../quickstarts/render-model.md)-project vindt u een meer geavanceerdere implementatie.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u alle stappen geleerd die nodig zijn om een leeg Unity-project uit te voeren en te laten werken met Azure Remote Rendering. In de volgende zelfstudie gaan we kijken hoe u met externe entiteiten werkt.

> [!div class="nextstepaction"]
> [Zelfstudie: Werken met externe entiteiten in Unity](working-with-remote-entities.md)
