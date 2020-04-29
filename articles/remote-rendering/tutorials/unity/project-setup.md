---
title: Een geheel nieuw Unity-project opstarten
description: Hierin wordt uitgelegd hoe u een blanco eenheids project configureert voor gebruik met Azure remote rendering.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 33801316e4c0446865169560bb42f98052acba70
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679595"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Zelf studie: een geheel nieuw eenheids project instellen

In deze zelfstudie leert u:

> [!div class="checklist"]
>
> * Het configureren van een Scratch unit-project voor ARR.
> * Rendering-sessies maken en stoppen.
> * Bestaande sessies opnieuw gebruiken.
> * Verbinding maken en verbreken van sessies.
> * Modellen laden in een weergave sessie.
> * Verbindings statistieken weer geven.

## <a name="prerequisites"></a>Vereisten

Voor deze zelf studie hebt u het volgende nodig:

* Uw account gegevens (account-ID, account sleutel, abonnements-ID). Als u geen account hebt, [maakt u een account](../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(down load)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* De nieuwste versie van Visual Studio 2019 [(down load)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(down load)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(down load)](https://unity3d.com/get-unity/download)
  * Deze modules installeren in Unity:
    * Ondersteuning voor **UWP** -universeel Windows-platform-build
    * **IL2CPP** -ondersteuning voor Windows-Build (IL2CPP)

> [!TIP]
> De [opslag plaats](https://github.com/Azure/azure-remote-rendering) voor de ARR-voor beelden bevat voor bereide unit-projecten voor alle zelf studies. U kunt deze projecten als referentie gebruiken.

## <a name="create-a-new-unity-project"></a>Een nieuw Unity-project maken

Maak een nieuw project op basis van de eenheids-hub.
In dit voor beeld wordt ervan uitgegaan dat het project wordt gemaakt in een map `RemoteRendering`met de naam.

![nieuw project venster](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Het manifest van het project configureren

U moet het bestand `Packages/manifest.json` wijzigen dat zich bevindt in de projectmap van uw Unity-project. Open het bestand in een tekst editor en voeg de volgende regels toe:

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

Het pakket voor de universele rendering-pijp lijn is optioneel, maar wordt aanbevolen om prestatie redenen.
Nadat u het manifest hebt gewijzigd en opgeslagen, wordt unit automatisch vernieuwd. Bevestig dat de pakketten zijn geladen in het *project* venster:

![invoer van pakketten bevestigen](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Zorg ervoor dat u de nieuwste versie van het pakket hebt

Volg de volgende stappen om ervoor te zorgen dat uw project de nieuwste versie van het pakket voor externe rendering gebruikt.
1. Selecteer het pakket in het venster Project en klik op het pictogram package: ![het pictogram package selecteren](media/package-icons.png)
1. Klik in de Inspector op ' weer geven in pakket beheer ' ![: pakket controle](media/package-properties.png)
1. Op de pagina Package Manager voor het pakket voor externe rendering raadpleegt u of de knop bijwerken beschikbaar is. Als dit het geval is, wordt het pakket met beknopte bijgewerkt naar de meest ![recente beschik bare versie: het ARR-pakket in package manager](media/package-manager.png)
1. Soms kan het bijwerken van het pakket leiden tot fouten in de console. Als dit het geval is, sluit u het project en opent u het opnieuw.

## <a name="configure-the-camera"></a>De camera configureren

Selecteer het **hoofd** knooppunt van de camera.

1. De *trans formatie*opnieuw instellen:

    ![camera transformatie opnieuw instellen](media/camera-reset-transform.png)

1. **Markeringen wissen** instellen op *effen kleur*

1. **Achtergrond** instellen op *zwart*

1. Stel de **knip plannen** in op *bijna = 0,3* en *Far = 20*. Dit betekent dat de geometrie meer dan 30 cm of meer dan 20 meters kan worden gerenderd.

    ![Eigenschappen van Unity camera](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>De project instellingen aanpassen

1. *> project instellingen bewerken...*
1. Selecteer in de lijst aan de linkerkant de optie kwaliteit.
1. Het **standaard kwaliteits niveau** wijzigen in *laag*

    ![instellingen voor project kwaliteit wijzigen](media/settings-quality.png)

1. Selecteer **afbeeldingen** aan de linkerkant.
1. Wijzig de **pipeline** -instelling voor het weer geven van een script in *HybridRenderingPipeline*. Sla deze stap over als de universele rendering-pijp lijn niet wordt gebruikt.

    ![het wijzigen van de](media/settings-graphics-lwrp.png) instellingen van een project-afbeelding in de gebruikers interface wordt de lijst met beschik bare pijplijn typen in de pakketten niet gevuld. in dat geval moet het *HybridRenderingPipeline* - ![activum hand matig naar het veld worden gesleept: instellingen van project graphics wijzigen](media/hybrid-rendering-pipeline.png)
1. Selecteer **speler** aan de linkerkant.
1. Het tabblad **universeel Windows-platform instellingen** selecteren
1. Wijzig de **XR-instellingen** ter ondersteuning van Windows Mixed ![reality: instellingen voor speler](media/xr-player-settings.png)
1. Selecteer de instellingen in de bovenstaande scherm afbeelding:
    1. **Virtual Reality ondersteund** inschakelen
    1. **Diepte notatie** instellen op *16-bits diepte*
    1. **Diepte buffer delen** inschakelen
    1. **Modus voor stereo rendering** instellen op *eenmalige instantie*

1. Vouw in hetzelfde venster, boven *XR-instellingen*, **publicatie-instellingen** uit
1. Schuif omlaag naar **mogelijkheden** en selecteer:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * Optioneel voor ontwikkeling: **PrivateNetworkClientServer**

      Deze optie is nodig als u de eenheid voor externe fout opsporing op uw apparaat wilt verbinden.

1. Schakel in **ondersteunde Apparaatsets** **Holographic** en **Desktop** in

1. Als u de Toolkit Mixed Reality wilt gebruiken, raadpleegt u de [MRTK-documentatie](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview)voor meer informatie over de aanbevolen instellingen en mogelijkheden.

## <a name="validate-project-setup"></a>Project instellingen valideren

Voer de volgende stappen uit om te controleren of de project instellingen juist zijn.

1. Kies de vermelding ValidateProject in het menu RemoteRendering van de werk balk Unity-editor.
1. Gebruik het venster ValidateProject om de project instellingen waar nodig te controleren en te herstellen.

    ![Eenheids editor project validatie](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Een script maken om de externe rendering van Azure te initialiseren

Maak een [Nieuw script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) en geef het de naam **RemoteRendering**. Open het script bestand en vervang de volledige inhoud door de onderstaande code:

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

Met dit script wordt de externe rendering van Azure geïnitialiseerd, wordt aangegeven welk camera object moet worden gebruikt voor rendering en wordt een knop **sessie maken** in de View Port geplaatst wanneer de *afspeel modus* wordt geactiveerd.

> [!CAUTION]
> Als u het script wijzigt en opslaat terwijl de afspeel modus actief is in unit-eenheid, kan dit leiden tot het bevriezen van de eenheid en kunt u deze afsluiten in taak beheer. Stop de afspeel modus daarom altijd voordat u het script *RemoteRendering* bewerkt.

## <a name="test-azure-remote-rendering-session-creation"></a>Het maken van een externe rendering-sessie door Azure testen

Maak een nieuwe GameObject in de scène en voeg de component *RemoteRendering* hieraan toe. Vul het juiste *account domein*, de *account-id*en de *account sleutel* voor uw externe rendering-account in:

![Eigenschappen van onderdeel externe Rendering](media/remote-rendering-component.png)

Start de toepassing in de editor (**Druk op Play** of CTRL + P). De knop **sessie maken** wordt weer gegeven in de View Port. Klik hierop om uw eerste ARR-sessie te starten:

![Een eerste sessie maken](media/test-create.png)

Als dit mislukt, zorg er dan voor dat u uw account gegevens correct hebt ingevoerd in de eigenschappen van de RemoteRendering-component. Anders wordt een bericht weer gegeven in het console venster met de sessie-ID die aan u is toegewezen, en wordt aangegeven dat de sessie momenteel de status *begin* heeft:

![Uitvoer van sessie starten](media/create-session-output.png)

Op dit moment wordt een server door Azure ingericht en wordt er een externe rendering van een virtuele machine gestart. Dit **duurt doorgaans 3 tot 5 minuten**. Wanneer de VM gereed is, wordt de call back `OnSessionStatusChanged` van ons unit-script uitgevoerd en wordt de nieuwe sessie status afgedrukt:

![Uitvoer sessie gereed](media/create-session-output-2.png)

Dat is alles! Er gebeurt dus niets meer. Als u kosten wilt voor komen, moet u altijd sessies stopzetten wanneer ze niet meer nodig zijn. In dit voor beeld kunt u dit doen door te klikken op de knop **sessie stoppen** of door de eenheids simulatie te stoppen. Als gevolg van de eigenschap **autostop Session** van het onderdeel *ARRServiceUnity* , dat standaard is ingeschakeld, wordt de sessie automatisch voor u gestopt. Als alles mislukt, vanwege crashes of verbindings problemen, kan uw sessie worden uitgevoerd zolang uw *MaxLeaseTime* voordat deze wordt afgesloten door de-server.

> [!NOTE]
> Het stoppen van een sessie duurt direct en kan niet ongedaan worden gemaakt. Nadat de functie is gestopt, moet u een nieuwe sessie maken, met dezelfde opstart overhead.

## <a name="reusing-sessions"></a>Sessies opnieuw gebruiken

Het maken van een nieuwe sessie is helaas een tijdrovende bewerking. Daarom moet het proberen om sessies zelden te maken en deze waar mogelijk opnieuw te gebruiken.

Voeg de volgende code toe aan het *RemoteRendering* -script en verwijder de oude versies van de dubbele functies:

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
> Voordat u deze code uitvoert, moet u ervoor zorgen dat u de optie **automatische-stop sessie** deactiveert in het onderdeel RemoteRendering. Anders wordt elke sessie die u maakt automatisch gestopt wanneer u de simulatie stopt en kan deze niet opnieuw worden gebruikt.

Wanneer u op *afspelen*klikt, krijgt u nu drie knoppen in de View Port: **sessie maken**, **actieve sessies opvragen**en **bestaande sessie gebruiken**. Met de eerste knop maakt u altijd een nieuwe sessie. Met de tweede knop wordt een query uitgevoerd waarmee *actieve* sessies bestaan. Als u niet hand matig een sessie-ID hebt opgegeven die u wilt gebruiken, dan selecteert deze actie de sessie-ID automatisch voor toekomstig gebruik. De derde knop probeert verbinding te maken met een bestaande sessie. Een van de eigenschappen die u hand matig hebt opgegeven met de eigenschap *sessie-id* onderdeel of een gevonden door *actieve sessies opvragen*.

De functie **AutoStartSessionAsync** wordt gebruikt om de knop ingedrukt te simuleren buiten de editor.

> [!TIP]
> Het is mogelijk om sessies te openen die zijn gestopt, verlopen of een fout status hebben. Hoewel ze niet meer kunnen worden gebruikt om te worden weer gegeven, kunt u een query uitvoeren op de details van de gegevens, nadat u een inactieve sessie hebt geopend. De bovenstaande code controleert de status van een sessie `ARRService_OnSessionStarted`in en wordt automatisch gestopt wanneer de sessie onbruikbaar is geworden.

Met deze functionaliteit kunt u nu sessies maken en opnieuw gebruiken, waardoor uw ontwikkelings werk stroom aanzienlijk moet worden verbeterd.

Normaal gesp roken wordt het maken van een sessie buiten de client toepassing geactiveerd vanwege de tijd die nodig is om de server op te zetten.

## <a name="connect-to-an-active-session"></a>Verbinding maken met een actieve sessie

Tot nu toe hebben we sessies gemaakt of geopend. De volgende stap is om *verbinding te maken* met een sessie. Nadat de verbinding tot stand is gebracht, produceert de rendering-server afbeeldingen en wordt een video stroom naar onze toepassing verzonden.

Voeg de volgende code toe aan het *RemoteRendering* -script en verwijder de oude versies van de dubbele functies:

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

U kunt als volgt deze functionaliteit testen:

1. Druk op **Play** in Unity.
1. Een sessie openen:
    1. Als u al een sessie hebt, drukt u op **actieve sessies opvragen** en vervolgens **bestaande sessie gebruiken**.
    1. Klik anders op **sessie maken**.
1. Druk op **verbinding maken**.
1. Na een paar seconden moet de console-uitvoer worden afgedrukt die u hebt verbonden.
1. Nu moet er niets anders gebeuren.
1. Druk op **verbinding verbreken** of op de afspeel modus van de eenheid stoppen.

>[!NOTE]
> Meerdere gebruikers kunnen een sessie *openen* om de gegevens op te vragen, maar slechts één gebruiker kan worden *verbonden* met een sessie tegelijk. Als een andere gebruiker al is verbonden, mislukt de verbinding met een **Handshake-fout**.

## <a name="load-a-model"></a>Een model laden

Voeg de volgende code toe aan het *RemoteRendering* -script en verwijder de oude versies van de dubbele functies:

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

Wanneer u nu op afspelen drukt, een sessie opent en er verbinding mee maakt, wordt de knop **Model laden** weer gegeven. Nadat u erop hebt geklikt, wordt de voortgang van het laden weer gegeven in de console-uitvoer 100 en wordt het volgende weer gegeven:

![Model geladen in de editor](media/model-loaded-replace-me.png)

De [WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) is een belang rijk onderdeel dat wordt gebruikt voor de stabiliteit van de [hologram](https://docs.microsoft.com/windows/mixed-reality/hologram-stability). Deze wordt echter alleen toegepast wanneer deze op een apparaat met een gemengde realiteit wordt geïmplementeerd.

> [!TIP]
> Als u de [Snelstartgids: een model converteren voor rendering](../../quickstarts/convert-model.md)hebt gevolgd, weet u al hoe u uw eigen modellen kunt converteren. Alles wat u nu moet doen, is door de URI naar een geconverteerd model in de eigenschap *model name* te zetten.

## <a name="display-frame-statistics"></a>Frame statistieken weer geven

De externe rendering van Azure houdt diverse informatie bij over de kwaliteit van de verbinding. Ga als volgt te werk om deze informatie snel weer te geven:

Maak een [Nieuw script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) en geef het de naam **RemoteFrameStats**. Open het script bestand en vervang de volledige inhoud door de onderstaande code:

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

Maak een GameObject en geef deze de naam *FrameStats*. Koppel deze als onderliggend knoop punt aan het *hoofd camera* object en stel de positie in op **x = 0, y = 0, z = 0,325**. Voeg het **RemoteFrameStats** -onderdeel toe aan het object.

Voeg een **gebruikers interface toe > canvas** een onderliggend object toe aan het object *FrameStats* en stel de eigenschappen als volgt in:

![eigenschappen van canvas](media/framestats-canvas.png)

Voeg een **UI > tekst** object toe als onderliggend element van het canvas en stel de eigenschappen als volgt in:

![tekst eigenschappen](media/framestats-text.png)

Selecteer het *FrameStats* -object en vul het **veld FrameStats** in door te klikken op het cirkel pictogram en het object **tekst** te selecteren:

![eigenschap Text instellen](media/framestats-set-text.png)

Wanneer u nu verbinding met de externe sessie maakt, moet de tekst de streaming-statistieken weer geven:

![uitvoer van frame statistieken](media/framestats-output.png)

Met de code wordt de statistieken-update buiten de editor uitgeschakeld als een tekstvak met een hoofd vergrendeling wordt afgeleid. Een meer geavanceerde implementatie vindt u in het project [Snelstartgids](../../quickstarts/render-model.md) .

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u alle stappen geleerd die nodig zijn om een leeg unit-project uit te voeren en het te laten werken met de externe rendering van Azure. In de volgende zelf studie gaan we kijken hoe u met externe entiteiten werkt.

> [!div class="nextstepaction"]
> [Zelf studie: werken met externe entiteiten in Unity](working-with-remote-entities.md)
