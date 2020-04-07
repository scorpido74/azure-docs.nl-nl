---
title: Vanuit het niets een Unity-project opzetten
description: Hier wordt uitgelegd hoe u een leeg Unity-project configureert voor het gebruik met Azure Remote Rendering.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 33801316e4c0446865169560bb42f98052acba70
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679595"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Zelfstudie: Een Unity-project vanaf nul instellen

In deze zelfstudie leert u:

> [!div class="checklist"]
>
> * Een kras-Unity-project configureren voor ARR.
> * Renderingsessies maken en stoppen.
> * Bestaande sessies opnieuw gebruiken.
> * Verbinding maken en loskoppelen van sessies.
> * Modellen laden in een renderingsessie.
> * Verbindingsstatistieken weergeven.

## <a name="prerequisites"></a>Vereisten

Voor deze tutorial heb je nodig:

* Uw accountgegevens (account-ID, accountsleutel, abonnements-ID). Als je geen account hebt, [maak je een account](../../how-tos/create-an-account.md)aan .
* Windows SDK 10.0.18362.0 [(downloaden)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* De nieuwste versie van Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(download)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
  * Installeer deze modules in Unity:
    * **UWP** - Ondersteuning voor universal Windows Platform Build
    * **IL2CPP** - Windows Build Support (IL2CPP)

> [!TIP]
> De [ARR samples repository](https://github.com/Azure/azure-remote-rendering) bevat voorbereide Unity projecten voor alle tutorials. U deze projecten als referentie gebruiken.

## <a name="create-a-new-unity-project"></a>Een nieuw Unity-project maken

Maak vanuit de Unity Hub een nieuw project.
In dit voorbeeld gaan we ervan uit dat het `RemoteRendering`project wordt gemaakt in een map met de naam .

![nieuw projectvenster](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Het manifest van het project configureren

U moet het `Packages/manifest.json` bestand wijzigen dat zich in uw eenheidsprojectmap bevindt. Open het bestand in een teksteditor en sluit de onderstaande regels toe:

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

Het pakket universal render pipeline is optioneel, maar wordt aanbevolen om prestatieredenen.
Nadat u het manifest hebt gewijzigd en opgeslagen, wordt Unity automatisch vernieuwd. Controleer of de pakketten zijn geladen in het *venster Project:*

![de invoer van het pakket bevestigen](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Zorg ervoor dat u de nieuwste versie van het pakket hebt

De volgende stappen zorgen ervoor dat uw project de nieuwste versie van het remote-rendering pakket gebruikt.
1. Selecteer het pakket in het projectvenster en ![klik op het pakketpictogram: Het pakketpictogram selecteren](media/package-icons.png)
1. Klik in de inspecteur op 'Weergave ![in pakketbeheer': pakketcontrole](media/package-properties.png)
1. Kijk op de pagina package manager voor remote rendering package of de updateknop beschikbaar is. Als dit het is, dan te klikken op het ![zal het pakket bijwerken naar de laatst beschikbare versie: De ARR-pakket in de package manager](media/package-manager.png)
1. Soms kan het bijwerken van het pakket leiden tot fouten in de console. Als dit gebeurt, probeert u het project te sluiten en opnieuw te openen.

## <a name="configure-the-camera"></a>De camera configureren

Selecteer het knooppunt **Hoofdcamera.**

1. De *transformatie opnieuw instellen:*

    ![cameratransformatie opnieuw instellen](media/camera-reset-transform.png)

1. **Vlaggen wissen instellen** op Effen *kleur*

1. **Achtergrond instellen** op *Zwart*

1. Stel de **knipvlakken** in *op Dichtbij = 0,3* en *Ver = 20*. Dit betekent dat rendering geometrie knipt die dichter is dan 30 cm of verder dan 20 meter.

    ![Eigenschappen van unity-camera](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>De projectinstellingen aanpassen

1. Open *> Projectinstellingen bewerken...*
1. Selecteer Kwaliteit in de lijst links.
1. Het **standaardkwaliteitsniveau** wijzigen in *Laag*

    ![instellingen voor projectkwaliteit wijzigen](media/settings-quality.png)

1. Selecteer **Afbeeldingen** aan de linkerkant.
1. Wijzig de instelling **Scriptable Rendering Pipeline** in *HybridRenderingPipeline*. Sla deze stap over als de universele renderpijplijn niet wordt gebruikt.

    ![projectgrafische instellingen](media/settings-graphics-lwrp.png) wijzigen Soms vult de gebruikersinterface de lijst met beschikbare pijplijntypen uit de pakketten niet in, in ![welk geval het *hybridrenderingpipeline-element* handmatig naar het veld moet worden gesleept: de instellingen voor projectafbeeldingen wijzigen](media/hybrid-rendering-pipeline.png)
1. Selecteer **Player** aan de linkerkant.
1. Het tabblad **Instellingen voor universele Windows-platforms selecteren**
1. De **XR-instellingen** wijzigen om ![Windows Mixed Reality te ondersteunen: spelersinstellingen](media/xr-player-settings.png)
1. Selecteer de instellingen zoals in de screenshot hierboven:
    1. **Virtual Reality-ondersteuning** inschakelen
    1. **Diepte-indeling** instellen op *16-bits diepte*
    1. **Dieptebufferdelen inschakelen**
    1. **Stereorenderingmodus** instellen op *Single Pass Instanced*

1. In hetzelfde venster, boven *XR-instellingen,* **uitvouwen Publicatie-instellingen**
1. Scroll naar beneden naar **Mogelijkheden** en selecteer:
    * **InternetClient**
    * **InternetClientServer**
    * **Ruimtelijk Perceptie**
    * Optioneel voor ontwikkeling: **PrivateNetworkClientServer**

      Deze optie is nodig als u de Unity remote debugger op uw apparaat wilt aansluiten.

1. In **ondersteunde apparaatfamilies**schakelt u **Holografisch** en **bureaublad in**

1. Als u de Mixed Reality Toolkit wilt gebruiken, raadpleegt u de [MRTK-documentatie](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview)voor meer informatie over aanbevolen instellingen en mogelijkheden.

## <a name="validate-project-setup"></a>Projectsetup valideren

Voer de volgende stappen uit om te valideren dat de projectinstellingen correct zijn.

1. Kies het item ValidateProject in het menu RemoteRendering op de werkbalk unity editor.
1. Gebruik het venster ValidateProject om waar nodig te controleren op projectinstellingen en deze op te lossen.

    ![Validatie van unity editor-project](media/arr-unity-validation.png)

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

Dit script initialiseert Azure Remote Rendering, vertelt welk cameraobject moet worden gebruikt voor rendering en plaatst een knop **Sessie maken** in de viewport wanneer de *modus Afspelen* is geactiveerd.

> [!CAUTION]
> Het wijzigen van het script en het opslaan terwijl de afspeelmodus actief is in Unity kan resulteren in unity bevriezing en je wordt gedwongen om het af te sluiten via de taakmanager. Stop daarom altijd de afspeelmodus voordat u het *script RemoteRendering* bewerkt.

## <a name="test-azure-remote-rendering-session-creation"></a>Het maken van Azure Remote Rendering-sessies testen

Maak een nieuw GameObject in de scène en voeg de component *RemoteRendering* toe. Vul het juiste *accountdomein,* *account-id*en *accountsleutel* in voor uw externe renderingaccount:

![Eigenschappen van externe renderingcomponent](media/remote-rendering-component.png)

Start de toepassing in de editor **(druk op Afspelen** of Ctrl+P). U moet de knop **Sessie maken** in de viewport zien verschijnen. Klik erop om je eerste ARR-sessie te starten:

![Een eerste sessie maken](media/test-create.png)

Als dit niet lukt, controleert u of u uw accountgegevens correct hebt ingevoerd in de eigenschappen van de component RemoteRendering. Anders verschijnt er een bericht in het consolevenster met de sessie-id die aan u is toegewezen en waarin staat dat de sessie momenteel de *status Start* heeft:

![Sessiestartuitvoer](media/create-session-output.png)

Op dit moment is Azure het inrichten van een server voor u en het opstarten van een externe rendering virtuele machine. Dit duurt meestal **3 tot 5 minuten.** Wanneer de VM klaar is, `OnSessionStatusChanged` wordt de callback van ons Unity-script uitgevoerd en wordt de nieuwe sessiestatus afgedrukt:

![Sessieklare uitvoer](media/create-session-output-2.png)

Dit is het! Voorlopig gebeurt er niets meer. Om kosten te voorkomen, moet u altijd stoppen met sessies wanneer ze niet meer nodig zijn. In dit voorbeeld u dit doen door op de knop **Sessie stoppen** te klikken of door de Unity-simulatie te stoppen. Vanwege de eigenschap **Auto-Stop Session** op de *component ARRServiceUnity,* die standaard is ingeschakeld, wordt de sessie automatisch voor u gestopt. Als alles mislukt, als gevolg van crashes of verbindingsproblemen, kan uw sessie worden uitgevoerd zolang uw *MaxLeaseTime* voordat deze wordt afgesloten door de server.

> [!NOTE]
> Het stoppen van een sessie wordt onmiddellijk van kracht en kan niet ongedaan worden gemaakt. Eenmaal gestopt, moet je een nieuwe sessie maken, met dezelfde opstartoverhead.

## <a name="reusing-sessions"></a>Sessies opnieuw gebruiken

Het maken van een nieuwe sessie is helaas een tijdrovende operatie. Daarom moet men proberen om sessies zelden te maken, en hergebruiken ze waar mogelijk.

Voeg de volgende code in het script *RemoteRendering* in en verwijder de oude versies van de dubbele functies:

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
> Voordat u deze code uitvoert, moet u de optie **Automatisch stoppen sessie** in de component RemoteRendering deactiveren. Anders wordt elke sessie die u maakt automatisch gestopt wanneer u de simulatie stopt en mislukt een poging om deze opnieuw te gebruiken.

Wanneer u op *Afspelen*drukt, krijgt u nu drie knoppen in de viewport: **Sessie maken,** **Actieve sessies opvragen**en **Bestaande sessie gebruiken.** De eerste knop maakt altijd een nieuwe sessie. De tweede knop query's die *actieve* sessies bestaan. Als u niet handmatig een sessie-id hebt opgegeven om te proberen te gebruiken, selecteert deze actie automatisch de sessie-id voor toekomstig gebruik. De derde knop probeert verbinding te maken met een bestaande sessie. Ofwel een die u handmatig hebt opgegeven via de eigenschap *Sessie-id,* of een eigenschap die is gevonden door *Query Actieve sessies*.

De **functie AutoStartSessionAsync** wordt gebruikt om de knopdrukken buiten de editor te simuleren.

> [!TIP]
> Het is mogelijk om sessies te openen die zijn gestopt, verlopen of in een foutstatus zijn. Hoewel ze niet meer kunnen worden gebruikt voor rendering, u hun gegevens opvragen zodra u een inactieve sessie hebt geopend. De bovenstaande code controleert de `ARRService_OnSessionStarted`status van een sessie in , om automatisch te stoppen wanneer de sessie onbruikbaar is geworden.

Met deze functionaliteit u nu sessies maken en hergebruiken, die uw ontwikkelingsworkflow aanzienlijk moeten verbeteren.

Het maken van een sessie wordt meestal geactiveerd buiten de clienttoepassing vanwege de tijd die nodig is om de server op te draaien.

## <a name="connect-to-an-active-session"></a>Verbinding maken met een actieve sessie

Tot nu toe hebben we sessies gemaakt of geopend. De volgende stap is om verbinding te *maken met* een sessie. Eenmaal aangesloten, zal de rendering server beelden produceren en een videostream naar onze applicatie sturen.

Voeg de volgende code in het script *RemoteRendering* in en verwijder de oude versies van de dubbele functies:

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

Ga als volgt te werk om deze functionaliteit te testen:

1. Druk op **Afspelen** in eenheid.
1. Open een sessie:
    1. Als u al een sessie hebt, drukt u op **Actieve querysessies** en **gebruikt u bestaande sessie**.
    1. Druk anders op **Sessie maken**.
1. Druk op **Connect**.
1. Na een paar seconden moet de console-uitvoer afdrukken dat u bent verbonden.
1. Voor nu zou er niets anders moeten gebeuren.
1. Druk **op Verbreken** of stop de afspeelmodus van Unity.

>[!NOTE]
> Meerdere gebruikers kunnen een sessie *openen* om de informatie op te vragen, maar slechts één gebruiker kan tegelijk aan een sessie worden *gekoppeld.* Als een andere gebruiker al is verbonden, mislukt de verbinding met een **handshakefout.**

## <a name="load-a-model"></a>Een model laden

Voeg de volgende code in het script *RemoteRendering* in en verwijder de oude versies van de dubbele functies:

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

Wanneer u nu op afspelen drukt, een sessie opent en verbinding maakt met de knop **Model laden,** wordt de knop Model laden weergegeven. Na het klikken, zal de console output tonen de voortgang van het laden en wanneer het bereikt 100% moet je het model van een motor verschijnen:

![Model geladen in de editor](media/model-loaded-replace-me.png)

De [WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) is een belangrijk onderdeel dat wordt gebruikt voor [hologramstabiliteit.](https://docs.microsoft.com/windows/mixed-reality/hologram-stability) Het heeft echter alleen effect wanneer het wordt geïmplementeerd op een Mixed Reality-apparaat.

> [!TIP]
> Als u de [Quickstart: Converteer een model voor rendering,](../../quickstarts/convert-model.md)weet u al hoe u uw eigen modellen converteren. Het enige wat u nu hoeft te doen om het weer te geven, is de URI naar een geconverteerd model te zetten in de eigenschap *Modelnaam.*

## <a name="display-frame-statistics"></a>Framestatistieken weergeven

Azure Remote Rendering houdt verschillende informatie bij over de kwaliteit van de verbinding. Ga als volgt te werk om deze informatie snel weer te geven:

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

Maak een GameObject en noem het *FrameStats*. Bevestig het als een onderliggend knooppunt aan het *hoofdcameraobject* en stel de positie in op **x = 0, y = 0, z = 0,325**. Voeg de component **RemoteFrameStats** toe aan het object.

Voeg een **ui > canvas** onderliggend object toe aan het object *FrameStats* en stel de eigenschappen ervan als volgt in:

![canvaseigenschappen](media/framestats-canvas.png)

Voeg een **object UI > tekst** toe als onderliggend object van het canvas en stel de eigenschappen ervan als volgt in:

![teksteigenschappen](media/framestats-text.png)

Selecteer het object *FrameStats* en vul het **veld FrameStats** in door op het cirkelpictogram te klikken en het object **Tekst** te selecteren:

![tekst instellen, eigenschap](media/framestats-set-text.png)

Wanneer de tekst nu is verbonden met de externe sessie, moet de streamingstatistieken worden weergegeven:

![frame statistieken output](media/framestats-output.png)

De code schakelt de statistieken update buiten de editor als een hoofd-locked tekstvak zou storend zijn. Een meer geavanceerde implementatie is te vinden in het [Quickstart-project.](../../quickstarts/render-model.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u alle stappen geleerd die nodig zijn om een leeg Unity-project te maken en het te laten werken met Azure Remote Rendering. In de volgende zelfstudie zullen we nader bekijken hoe we met externe entiteiten kunnen werken.

> [!div class="nextstepaction"]
> [Zelfstudie: Werken met externe entiteiten in Unity](working-with-remote-entities.md)
