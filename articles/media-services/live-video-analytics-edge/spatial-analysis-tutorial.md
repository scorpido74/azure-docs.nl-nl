---
title: Livevideo analyseren met Computer Vision voor ruimtelijke analyse, Azure
description: In deze zelfstudie leert u hoe u Live Video Analytics samen met de AI-functie Computer Vision ruimtelijke analyse gebruikt van Azure Cognitive Services om een livevideofeed van een (gesimuleerde) IP-camera te analyseren.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 98ee57d4916ac0a8da8b48a9cdd881468b2d75d5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929406"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Livevideo analyseren met Computer Vision voor ruimtelijke analyse (preview)

In deze zelfstudie leert u hoe u Live Video Analytics samen met de [AI-service Computer Vision ruimtelijke analyse gebruikt van Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/computer-vision/) om een livevideofeed van een (gesimuleerde) IP-camera te analyseren. U ziet hoe u met deze deductieserver de streaming video kunt analyseren voor een beter begrip van ruimtelijke relaties tussen mensen en beweging in fysieke ruimte.  Een subset van de frames in de videofeed wordt verzonden naar deze deductieserver en de resultaten worden verzonden naar IoT Edge Hub. Wanneer aan bepaalde voorwaarden wordt voldaan, worden videoclips vastgelegd en opgeslagen als Azure Media Services-assets.

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Resources instellen.
> * De code controleren.
> * De voorbeeldcode uitvoeren.
> * Gebeurtenissen bewaken.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen

Lees deze artikelen voordat u begint:

* [Overzicht van Live Video Analytics in IoT Edge](overview.md)
* [Terminologie van Live Video Analytics in IoT Edge](terminology.md)
* [Mediagrafiekconcepten](media-graph-concept.md)
* [Video-opname op basis van gebeurtenissen](event-based-video-recording-concept.md)
* [Zelfstudie: Een IoT Edge-module ontwikkelen](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Live Video Analytics implementeren op Azure Stack Edge](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten om de module ruimtelijke analyse te verbinden met de module Live Video Analytics.

* [Visual Studio Code](https://code.visualstudio.com/) op uw ontwikkelcomputer. Zorg ervoor dat u beschikt over de [Azure IoT Tools-extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
    * Zorg ervoor dat het netwerk waarmee uw ontwikkelcomputer is verbonden Advanced Message Queueing Protocol toestaat via poort 5671. Dankzij deze instelling kan Azure IoT Tools communiceren met Azure IoT Hub.
* [Azure Stack Edge ](https://azure.microsoft.com/products/azure-stack/edge/)met GPU-versnelling.  
    U wordt aangeraden Azure Stack Edge te gebruiken met GPU-versnelling, maar de container kan worden uitgevoerd op elk ander apparaat met een [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/). 
* [Azure Cognitive Service Computer Vision-container](https://azure.microsoft.com/services/cognitive-services/computer-vision/) voor ruimtelijke analyse.  
    Om deze container te kunnen gebruiken, moet u een Computer Vision-resource hebben om de bijbehorende **API-sleutel** en **eindpunt-URI** op te halen. De API-sleutel is beschikbaar op de Azure Portal-pagina's Computer Vision Overzicht en Sleutels. De sleutel en het eindpunt zijn vereist om de container te kunnen starten.

## <a name="overview"></a>Overzicht

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="Overzicht van ruimtelijke analyse":::
 
Dit diagram laat zien hoe de signalen in deze zelfstudie stromen. Een [Edge-module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuleert een IP-camera die als host fungeert voor een RTSP-server (Real-Time Streaming Protocol). Een [RTSP-bron](media-graph-concept.md#rtsp-source)-knooppunt haalt de videofeed van deze server, en verstuurt videoframes naar het [framefilterprocessor](media-graph-concept.md#frame-rate-filter-processor)-knooppunt. Deze processor beperkt de framesnelheid van de videostream die het processorknooppunt MediaGraphCognitiveServicesVisionExtension bereikt.

De node MediaGraphCognitiveServicesVisionExtension speelt de rol van proxy. Het converteert videoframes naar het opgegeven afbeeldingstype. Vervolgens wordt de afbeelding via **gedeeld geheugen** doorgestuurd naar een andere Edge-module die AI-bewerkingen achter een gRPC-eindpunt uitvoert. In dit voorbeeld is die Edge-module de module ruimtelijke analyse. Het processorknooppunt MediaGraphCognitiveServicesVisionExtension voert twee dingen uit:

* Het verzamelt de resultaten en publiceert gebeurtenissen naar het sinkknooppunt [IoT Hub](media-graph-concept.md#iot-hub-message-sink). Het knooppunt verzendt die gebeurtenissen vervolgens naar [IoT Edge-hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub). 
* Het legt ook een videoclip van 30 seconden vast van de RTSP-bron met een [signaalpoortprocessor](media-graph-concept.md#signal-gate-processor) en slaat het op als een Media Services-asset.

## <a name="create-the-computer-vision-resource"></a>De Computer Vision-resource maken

U moet een Azure-resource maken van het type Computer Vision op de [Azure Portal](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal) of via Azure CLI. U kunt de resource maken wanneer uw aanvraag voor toegang tot de container is goedgekeurd en uw Azure-abonnements-id is geregistreerd. Ga naar https://aka.ms/csgate om uw use-case en uw Azure-abonnements-id in te dienen.  U moet de Azure-resource maken met hetzelfde Azure-abonnement dat is opgegeven in het aanvraagformulier voor toegang.

### <a name="gathering-required-parameters"></a>Vereiste parameters verzamelen

Er zijn drie primaire parameters vereist voor alle Cognitive Services-containers, met inbegrip van de container voor ruimtelijke analyse. De gebruiksrechtovereenkomst (EULA) moet aanwezig en geaccepteerd zijn. Bovendien zijn er een eindpunt-URL en API-sleutel nodig.

### <a name="endpoint-uri-endpoint_uri"></a>Eindpunt URI {ENDPOINT_URI}

De waarde van de eindpunt-URI is beschikbaar op de Azure Portal- pagina Overzicht van de Cognitive Services-resource. Navigeer naar de overzichtspagina en zoek de eindpunt-URI. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Sleutels en eindpunt":::

### <a name="keys-api_key"></a>Sleutels {API_KEY}

Deze sleutel wordt gebruikt om de container voor de ruimtelijke analyse te starten en is beschikbaar op de Azure Portal-pagina Sleutels van de bijbehorende Cognitive Service-resource. Navigeer naar de pagina Sleutels en zoek de sleutels.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/endpoint-uri.png" alt-text="Eindpunt-URI":::

## <a name="set-up-azure-stack-edge"></a>Azure Stack Edge instellen

Volg [deze stappen](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep) om de Azure Stack Edge in te stellen en vervolg met onderstaande stappen om de modules Live Video Analytics en ruimtelijke analyse te implementeren.

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

1. Kloon de opslagplaats vanuit deze locatie: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Open in Visual Studio Code de map waarin de opslagplaats is gedownload.
1. Ga in Visual Studio Code naar de map src/cloud-to-device-console-app. Maak daar een bestand en geef het de naam appsettings.json. Dit bestand bevat de instellingen die nodig zijn om het programma uit te voeren.
1. U haalt de IotHubConnectionString op uit de Azure Stack Edge door deze stappen te volgen:

    * Ga naar uw IoT Hub in Azure Portal en klik op `Shared access policies` in het linkerdeelvenster.
    * Klik op `iothubowner` om de gedeelde toegangssleutels op te halen.
    * Kopieer de `Connection String – primary key` en plak deze vervolgens in het invoervak in de VSCode.
    
        De verbindingsreeks ziet er als volgt uit: <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. Kopieer onderstaande inhoud in het bestand. Zorg ervoor dat u de variabelen vervangt.
    
    ```json
    {
        "IoThubConnectionString" : " HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>”,
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. Ga naar de map src/edge en maak een bestand met de naam .env.
1. Kopieer de inhoud van het bestand /clouddrive/lva-sample/edge-deployment/.env. De tekst moet lijken op de volgende code.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Implementatiesjabloon instellen  

Voeg de module ruimtelijke analyse toe aan /src/edge/deployment.template.json. In het sjabloon bevinden zich de modules IvaEdge, rtspsim en onze module ruimtelijke analyse.

<p>
<details>
<summary>Vouw deze weergave uit en bekijk ons voorbeeldimplementatiesjabloon.  
Kopieer de inhoud van hier en plak die in uw/src/edge/deployment.template.json.
</summary>
<pre><code>
{
  "$schema-template": "2.0.0",
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": {}
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "5671/tcp": [
                      {
                        "HostPort": "5671"
                      }
                    ],
                    "8883/tcp": [
                      {
                        "HostPort": "8883"
                      }
                    ],
                    "443/tcp": [
                      {
                        "HostPort": "443"
                      }
                    ]
                  }
                }
              }
            }
          }
        },
        "modules": {
          "lvaEdge": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/media/live-video-analytics:1",
              "createOptions": {
                "HostConfig": {
                  "LogConfig": {
                    "Type": "",
                    "Config": {
                      "max-size": "10m",
                      "max-file": "10"
                    }
                  },
                  "Binds": [
                    "$OUTPUT_VIDEO_FOLDER_ON_DEVICE:/var/media/",
                    "$APPDATA_FOLDER_ON_DEVICE:/var/lib/azuremediaservices"
                  ],
                  "IpcMode": "host",
                  "ShmSize": 1536870912
                }
              }
            },
            "env": {
              "IS_DEVELOPER_ENVIRONMENT": {
                "value": "true"
              }
            }
          },
          "rtspsim": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2",
                "createOptions": {
                  "HostConfig": {
                    "Mounts": [
                      {
                        "Target": "/live/mediaServer/media",
                        "Source": "lvaspatialanalysislocal",
                        "Type": "volume"
                      }
                    ],
                    "PortBindings": {
                      "554/tcp": [
                        {
                          "HostPort": "554"
                        }
                      ]
                    }
                  }
                }
              }
            },
          "spatialAnalysis": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azure-cognitive-services/spatial-analysis:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "50051/tcp": [
                      {
                        "HostPort": "50051"
                      }
                    ]
                  },
                  "IpcMode": "host",
                  "Binds": [
                      "/tmp/.X11-unix:/tmp/.X11-unix"
                  ],
                  "Runtime": "nvidia",
                  "ShmSize": 536870911,
                  "LogConfig": {
                      "Type": "json-file",
                      "Config": {
                          "max-size": "10m",
                          "max-file": "200"
                      }
                  }
                }
              }
            },
            "env": {
              "DISPLAY": {
                "value": ":0"
              },
              "ARCHON_SHARED_BUFFER_LIMIT": {
                "value": "377487360"
              },
              "ARCHON_PERF_MARKER": {
                "value": "false"
              },
              "QT_X11_NO_MITSHM": {
                "value": "1"
              },
              "OMP_WAIT_POLICY": {
                "value": "PASSIVE"
              },
              "EULA": {
                "value": "accept"
              },
              "BILLING_ENDPOINT": {
                "value": "<Use one key from Archon azure resource (keys page)>"
              },
              "API_KEY": {
                "value": "<Use endpoint from Archon azure resource (overview page)>"
              }
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "LVAToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "lvaEdge": {
      "properties.desired": {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/microsoft.media/mediaservices/$AMS_ACCOUNT",
        "aadTenantId": "$AAD_TENANT_ID",
        "aadServicePrincipalAppId": "$AAD_SERVICE_PRINCIPAL_ID",
        "aadServicePrincipalSecret": "$AAD_SERVICE_PRINCIPAL_SECRET",
        "aadEndpoint": "https://login.microsoftonline.com",
        "aadResourceId": "https://management.core.windows.net/",
        "armEndpoint": "https://management.azure.com/",
        "diagnosticsEventsOutputName": "AmsDiagnostics",
        "operationalEventsOutputName": "AmsOperational",        
        "logLevel": "Info",
        "logCategories": "Application,Events,MediaPipeline",
        "allowUnsecuredEndpoints": true,
        "telemetryOptOut": false
      }
    },
    "spatialAnalysis": {
      "properties.desired": {
        "globalSettings": {
          "PlatformTelemetryEnabled": true,
          "CustomerTelemetryEnabled": true
        },
        "graphs": {
            "polygonCross": {
              "version": 2,
              "enabled": true,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0,0],[0.6,0],[0.6,0.9],[0,0.9],[0,0]],\"threshold\":50,\"events\":[{\"type\":\"enter/exit\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personCount": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0.8,0],[1,0],[1,1],[0.8,1],[0.8,0]],\"threshold\":50,\"events\":[{\"type\":\"count\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personDistance": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0,\"gpu_index\": 0,\"do_calibration\": true}",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\": \"distance_zone\", \"polygon\": [[0,0],[0,1],[1,1],[1,0],[0,0]],\"threshold\": 35.00,\"events\":[{\"type\": \"people_distance\",\"config\":{\"trigger\": \"event\",\"output_frequency\":1,\"minimum_distance_threshold\":6.0,\"maximum_distance_threshold\":35.0}}]}]}"
              },
              "nodesloglevel": "info"
            }
        }
      }
    }
  }
}
</code>
</pre>
</details>
</p>

Er zijn een paar dingen waar u op moet letten:

1. Stel de poortbinding in.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. IpcMode in IvaEdge en de ruimtelijkeanalysemodule createOptions moeten hetzelfde zijn en ingesteld op host.
1. Uw implementatiesjabloonbestand moet 'implementatie' bevatten in de bestandsnaam, anders kan het niet worden herkend en een manifest maken voor implementatie.
1. Zorg ervoor dat u de Volume Bounds hebt ingesteld, zodat de RTSP-simulator kan werken. Zie [Docker Volume Mounts instellen](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts) voor meer informatie.

    1. [Maak verbinding met de SMB-share](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-add-shares#connect-to-an-smb-share) en kopieer het [videobestand](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) naar de lokale share.
    1. Controleer of de module rtspsim het volgende bevat:
        
        ```json
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
## <a name="generate-and-deploy-the-deployment-manifest"></a>Het implementatiemanifest genereren en implementeren

Het implementatiemanifest geeft aan welke modules op een edge-apparaat worden geïmplementeerd. Ook bevat het de configuratie-instellingen voor deze modules.

Volg deze stappen om het manifest te genereren op basis van het sjabloonbestand en het vervolgens te implementeren op het edge-apparaat.

1. Open Visual Studio Code.
1. Selecteer naast het deelvenster AZURE IOT HUB het pictogram Meer acties om de IoT Hub-verbindingsreeks in te stellen. U kunt de tekenreeks kopiëren vanuit het bestand src/cloud-to-device-console-app/appsettings.json.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Ruimtelijke analyse: verbindingsreeks":::
1. Klik met de rechtermuisknop op src/edge/deployment.template.json en selecteer IoT Edge-implementatiemanifest genereren.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Ruimtelijke analyse: implementatie amd64 JSON":::
    
    Met deze actie maakt u een manifestbestand met de naam deployment.amd64.json in de map src/edge/config.
1. Klik met de rechtermuisknop op src/edge/config/deployment.amd64.json, selecteer Implementatie voor één apparaat maken en selecteer vervolgens de naam van uw edge-apparaat.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Ruimtelijke analyse: implementatiesjabloon JSON":::   
1. Wanneer u wordt gevraagd om een IoT Hub-apparaat te selecteren, kiest u uw Azure Stack Edge-naam in de vervolgkeuzelijst.
1. Vernieuw Azure IoT Hub na ongeveer 30 seconden in de linkerbenedenhoek van het venster. Op het edge-apparaat worden nu de volgende geïmplementeerde modules weergegeven:
    
    * Live Video Analytics in IoT Edge (modulenaam lvaEdge).
    * RTSP-simulator (Real-Time Streaming Protocol) (modulenaam rtspsim).
    * Ruimtelijke analyse (modulenaam spatialAnalysis).
    
Als de implementatie is geslaagd, verschijnt het volgende bericht in OUTPUT:

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

Vervolgens kunt u de modules `lvaEdge`, `rtspsim`, `spatialAnalysis` en `rtspsim` vinden onder Apparaten/modules en de status moet 'actief' zijn.

## <a name="prepare-to-monitor-events"></a>Het bewaken van gebeurtenissen voorbereiden

Volg deze stappen om deze gebeurtenissen te bekijken:

1. Open het deelvenster Explorer in Visual Studio Code en zoek Azure IoT Hub in de linkerbenedenhoek.
1. Vouw het knooppunt Apparaten uit.
1. Klik met de rechtermuisknop op uw Azure Stack Edge en selecteer Bewaking van ingebouwd gebeurteniseindpunt starten.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Ruimtelijke analyse: bewaking starten":::
     
## <a name="run-the-program"></a>Het programma uitvoeren

Er is een program.cs dat de directe methodes in src/cloud-to-device-console-app/operations.json aanroept. We moeten de operations.json instellen en een topologie opgeven voor het mediagrafiekgebruik.
In operations.json:

Stel de topologie als volgt in (topologyFile voor lokale topologie, topologyUrl voor onlinetopologie):

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyFile": "../edge/spatialAnalysistopology.json"
    }
},
```

Maak een grafiekexemplaar als dit, stel hier de parameters in topologie in:

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
                },
                {
                    "name": "rtspUserName",
                    "value": "testuser"
                },
                {
                    "name": "rtspPassword",
                    "value": "testpassword"
                }
            ]
        }
    }
},
```

<p>
<details>
<summary>Vouw uit om ons voorbeeldtopologiebestand voor de module spatialAnalysis te bekijken:
</summary>
<pre><code>
{
    "@apiVersion": "1.0",
    "name": "InferencingWithCVExtension",
    "properties": {
      "description": "Analyzing live video using spatialAnalysis Extension to send images to an external inference engine",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "grpcUrl",
          "type": "String",
          "description": "inferencing Url",
          "default": "tcp://spatialAnalysis:50051"
        },
        {
          "name": "frameRate",
          "type": "String",
          "description": "Rate of the frames per second to be received from LVA.",
          "default": "2"
        },
        {
          "name": "spatialanalysisusername",
          "type": "String",
          "description": "spatialanalysis endpoint username",
          "default": "not-in-use"
        },
        {
          "name": "spatialanalysispassword",
          "type": "String",
          "description": "spatialanalysis endpoint password",
          "default": "not-in-use"  
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphFrameRateFilterProcessor",
          "name": "frameRateFilter",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ],
          "maximumFps": "${frameRate}"
        },
        {
          "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
          "name": "computerVisionExtension",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${grpcUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${spatialanalysisusername}",
              "password": "${spatialanalysispassword}"
            }
          },
          "image": {
            "scale": {
              "mode": "pad",
              "width": "1408",
              "height": "786"
            },
            "format": {
              "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
              "pixelFormat": "bgr24"
            }
          },
          "inputs": [
            {
              "nodeName": "frameRateFilter"
            }
          ]
        },
        {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "computerVisionExtension"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "PT0S",
            "minimumActivationTime": "PT30S",
            "maximumActivationTime": "PT30S"
          }
      ],
      "sinks": [
        {
            "@type": "#Microsoft.Media.MediaGraphAssetSink",
            "name": "assetSink",
            "assetNamePattern": "sampleAssetFromEVR-CV-LVAEdge-${System.DateTime}",
            "segmentLength": "PT30S",
            "LocalMediaCacheMaximumSizeMiB": "200",
            "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
            "inputs": [
                {
                    "nodeName": "signalGateProcessor"
                }
            ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "inferenceOutput",
          "inputs": [
            {
              "nodeName": "computerVisionExtension"
            }
          ]
        }
      ]
    }
  }
</code>
</pre>
</details>
</p>


Gebruik MediaGraphRealTimeComputerVisionExtension om verbinding te maken met de module ruimtelijke analyse. Stel de ${grpcUrl} in tcp://spatialAnalysis:<PORT_NUMBER> in, zoals tcp://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcUrl}",
    "credentials": {
        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
        "username": "${spatialanalysisusername}",
        "password": "${spatialanalysispassword}"
    }
    },
    "image": {
    "scale": {
        "mode": "pad",
        "width": "1408",
        "height": "786"
    },
    "format": {
        "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
        "pixelFormat": "bgr24"
    }
    },
    "inputs": [
    {
        "nodeName": "frameRateFilter"
    }
    ]
}
```

Voer een foutopsporingssessie uit en volg de TERMINAL-instructies. Hiermee worden de topologie en het grafiekexemplaar ingesteld, het grafiekexemplaar geactiveerd en ten slotte de resources verwijderd.

## <a name="interpret-results"></a>Resultaten interpreteren

Wanneer een mediagrafiek wordt gemaakt, zou u een 'MediaSessionEstablished'-gebeurtenis moeten zijn. Hier een [voorbeeld van een MediaSessionEstablished-gebeurtenis](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

De module ruimtelijke analyse verzendt ook AI Insight-gebeurtenissen naar Live Video Analytics en vervolgens naar IoTHub en wordt ook weergegeven in OUTPUT. De ENTITEIT is detecteren van objecten en GEBEURTENIS is ruimteanalysegebeurtenissen. Deze uitvoer wordt doorgegeven naar Live Video Analytics.

Voorbeelduitvoer voor personZoneEvent (van cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics-bewerking):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

Probeer verschillende bewerkingen die de module `spatialAnalysis` biedt uit, zoals **personCount** en **personDistance** door de vlag 'ingeschakeld' in en uit te schakelen in het grafiekknooppunt van uw distributiemanifestbestand.
>[!Tip]
> Gebruik een [videobestand](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) met meer dan één persoon in het frame.

> [!NOTE]
> U kunt maar één bewerking tegelijkertijd uitvoeren. Zorg er dus voor dat er maar één vlag is ingesteld op **waar** en dat de andere op **onwaar** staan.
