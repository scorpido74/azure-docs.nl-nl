---
title: Azure Stream Analytics op IoT Edge
description: Maak randtaken in Azure Stream Analytics en implementeer ze op apparaten met Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 8bb1bd018866bda9270b78507f0462b6c4d4ea17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475889"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics op IoT Edge
 
Met Azure Stream Analytics (ASA) in IoT Edge kunnen ontwikkelaars in bijna realtime analytische intelligence implementeren dichter in de buurt van IoT-apparaten. Op deze manier kan er gebruik worden gemaakt van de volledige waarde van de gegevens die door het apparaat worden gegenereerd. Azure Stream Analytics biedt een lage latentie, tolerantie en efficiënt gebruik van bandbreedte en naleving. Ondernemingen kunnen nu besturingslogica implementeren die dicht bij de industriële bewerkingen ligt en een aanvulling vormen op Big Data-analyses die worden uitgevoerd in de cloud.  

Azure Stream Analytics op IoT Edge wordt uitgevoerd binnen het [Azure IoT Edge-framework.](https://azure.microsoft.com/campaigns/iot-edge/) Zodra de taak is gemaakt in ASA, u deze implementeren en beheren met IoT Hub.

## <a name="scenarios"></a>Scenario's
![Diagram op hoog niveau van IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Command and control met lage latentie**: Bijvoorbeeld, productieveiligheidssystemen moeten reageren op operationele gegevens met ultralage latentie. Met ASA op IoT Edge u sensorgegevens in bijna realtime analyseren en opdrachten uitvoeren wanneer u afwijkingen detecteert om een machine te stoppen of waarschuwingen te activeren.
*   **Beperkte connectiviteit met de cloud:** bedrijfskritieke systemen, zoals externe mijnbouwapparatuur, verbonden schepen of offshore-boringen, moeten gegevens analyseren en erop reageren, zelfs wanneer de cloudconnectiviteit met tussenpozen is. Met ASA wordt uw streaminglogica onafhankelijk van de netwerkconnectiviteit uitgevoerd en u kiezen wat u naar de cloud verzendt voor verdere verwerking of opslag.
* **Beperkte bandbreedte**: Het volume van de gegevens die worden geproduceerd door straalmotoren of verbonden auto's kan zo groot zijn dat gegevens moeten worden gefilterd of voorbewerkt voordat ze naar de cloud worden verzonden. Met ASA u de gegevens filteren of samenvoegen die naar de cloud moeten worden verzonden.
* **Naleving**: Naleving van de regelgeving vereist mogelijk dat sommige gegevens lokaal worden geanonimiseerd of samengevoegd voordat ze naar de cloud worden verzonden.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-taken in Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Wat is een "edge" baan?

ASA Edge-taken worden uitgevoerd in containers die zijn geïmplementeerd op [Azure IoT Edge-apparaten.](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works) Ze bestaan uit twee delen:
1.  Een cloudonderdeel dat verantwoordelijk is voor taakdefinitie: gebruikers definiëren invoer, uitvoer, query en andere instellingen (out of order-gebeurtenissen, enz.) in de cloud.
2.  Een module die wordt uitgevoerd op uw IoT-apparaten. Het bevat de ASA-engine en ontvangt de taakdefinitie vanuit de cloud. 

ASA gebruikt IoT Hub om edge-taken te implementeren op apparaten(en). Meer informatie over [iot edge-implementatie is hier te vinden.](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)

![Azure Stream Analytics Edge-taak](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Installatie-instructies
De stappen op hoog niveau worden beschreven in de volgende tabel. Meer details worden gegeven in de volgende secties.

|      |Stap   | Opmerkingen   |
| ---   | ---   |  ---      |
| 1   | **Een opslagcontainer maken**   | Opslagcontainers worden gebruikt om uw taakdefinitie op te slaan waar ze toegankelijk zijn voor uw IoT-apparaten. <br>  U elke bestaande opslagcontainer opnieuw gebruiken.     |
| 2   | **Een ASA-randtaak maken**   |  Maak een nieuwe taak en selecteer **Edge** als **hostingomgeving.** <br> Deze taken worden gemaakt/beheerd vanuit de cloud en worden uitgevoerd op uw eigen IoT Edge-apparaten.     |
| 3   | **Uw IoT Edge-omgeving instellen op uw apparaat(en)**   | Instructies voor [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) of [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **ASA implementeren op uw IoT Edge-apparaat(en)**   |  ASA-taakdefinitie wordt geëxporteerd naar de opslagcontainer die eerder is gemaakt.       |

U [deze stapsgewijze zelfstudie](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) volgen om uw eerste ASA-taak op IoT Edge te implementeren. De volgende video moet u inzicht geven in het proces om een Stream Analytics-taak uit te voeren op een IoT-edge-apparaat:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Een opslagcontainer maken
Er is een opslagcontainer vereist om de door ASA samengestelde query en de taakconfiguratie te exporteren. Het wordt gebruikt om de ASA Docker-afbeelding te configureren met uw specifieke query. 
1. Volg [deze instructies](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) om een opslagaccount te maken vanuit de Azure-portal. U alle standaardopties behouden om dit account te gebruiken bij ASA.
2. Maak in het nieuw gemaakte opslagaccount een blobopslagcontainer:
    1. Klik op **Blobs,** dan **+ Container**. 
    2. Voer een naam in en bewaar de container als **privé**.

#### <a name="create-an-asa-edge-job"></a>Een ASA Edge-taak maken
> [!Note]
> Deze zelfstudie richt zich op het maken van ASA-banen met Azure-portal. U ook [de Visual Studio-plug-in gebruiken om een ASA Edge-taak te maken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Maak vanuit de Azure-portal een nieuwe 'Stream Analytics-taak'. [Directe link om hier een nieuwe ASA-baan te maken.](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob)

2. Selecteer **Edge** als hostingomgeving in het scherm Maken als **hostingomgeving** (zie de volgende afbeelding)

   ![Stream Analytics-taak op Edge maken](media/stream-analytics-edge/create-asa-edge-job.png)
3. Functiedefinitie
    1. **Invoerstroom(s) definiëren**. Definieer een of meerdere invoerstromen voor uw taak.
    2. Referentiegegevens definiëren (optioneel).
    3. **Uitvoerstroom(en) definiëren**. Definieer een of meer uitvoerstromen voor uw taak. 
    4. **Query definiëren**. Definieer de ASA-query in de cloud met behulp van de inline-editor. De compiler controleert automatisch de syntaxis ingeschakeld voor ASA edge. U uw query ook testen door voorbeeldgegevens te uploaden. 

4. Stel de opslagcontainergegevens in het menu **Instellingen van IoT Edge** in.

5. Optionele instellingen instellen
    1. **Bestelling van evenementen**. U out-of-orderbeleid configureren in de portal. Documentatie is [hier](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)beschikbaar.
    2. **Locale**. Stel de internaliseringsindeling in.



> [!Note]
> Wanneer een implementatie wordt gemaakt, exporteert ASA de taakdefinitie naar een opslagcontainer. Deze taakdefinitie blijft hetzelfde tijdens de duur van een implementatie. Als u een taak die op de rand wordt uitgevoerd, wilt bijwerken, moet u de taak in ASA bewerken en vervolgens een nieuwe implementatie maken in IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Uw IoT Edge-omgeving instellen op uw apparaat(en)
Edge-taken kunnen worden geïmplementeerd op apparaten met Azure IoT Edge.
Hiervoor moet u de volgende stappen volgen:
- Maak een Iot Hub.
- Installeer Docker- en IoT Edge-runtime op uw edge-apparaten.
- Stel uw apparaten in als **IoT Edge-apparaten** in IoT Hub.

Deze stappen worden beschreven in de IoT Edge-documentatie voor [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) of [Linux.](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Implementatie ASA op uw IoT Edge-apparaat(en)
##### <a name="add-asa-to-your-deployment"></a>ASA toevoegen aan uw implementatie
- Open in de Azure-portal IoT Hub, navigeer naar **IoT Edge** en klik op het apparaat dat u voor deze implementatie wilt targeten.
- Selecteer **Modules instellen**en selecteer + **Toevoegen** en kies Azure Stream **Analytics Module**.
- Selecteer het abonnement en de ASA Edge-taak die u hebt gemaakt. Klik op Opslaan.
![ASA-module toevoegen aan uw implementatie](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Tijdens deze stap maakt ASA een map met de naam 'EdgeJobs' in de opslagcontainer (als deze nog niet bestaat). Voor elke implementatie wordt een nieuwe submap gemaakt in de map 'EdgeJobs'.
> Wanneer u uw taak implementeert op IoT Edge-apparaten, maakt ASA een sas (shared access signature) voor het taakdefinitiebestand. De SAS-sleutel wordt veilig verzonden naar de IoT Edge-apparaten met behulp van device twin. Het verstrijken van deze sleutel is drie jaar vanaf de dag van de oprichting. Wanneer u een IoT Edge-taak bijwerkt, wordt de SAS gewijzigd, maar wordt de afbeeldingsversie niet gewijzigd. Zodra u **update,** volg de implementatie workflow, en een update melding is aangemeld op het apparaat.


Zie [deze pagina](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)voor meer informatie over IoT Edge-implementaties.


##### <a name="configure-routes"></a>Routes configureren
IoT Edge biedt een manier om berichten tussen modules en tussen modules en IoT Hub te definiëren. De volledige syntaxis wordt [hier](https://docs.microsoft.com/azure/iot-edge/module-composition)beschreven .
Namen van de ingangen en uitvoer die in de ASA-taak zijn gemaakt, kunnen worden gebruikt als eindpunten voor routering.  

###### <a name="example"></a>Voorbeeld

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
In dit voorbeeld worden de routes weergegeven voor het scenario dat in de volgende afbeelding wordt beschreven. Het bevat een randtaak genaamd "**ASA**", met een ingang genaamd "**temperatuur**" en een uitvoer genaamd "**alert**".
![Diagramvoorbeeld van berichtroutering](media/stream-analytics-edge/edge-message-routing-example.png)

In dit voorbeeld worden de volgende routes gedefinieerd:
- Elk bericht van de **tempSensor** wordt verzonden naar de module met de naam **ASA** naar de ingang met de naam **temperatuur,**
- Alle uitgangen van **de ASA-module** worden verzonden naar de IoT Hub die aan dit apparaat is gekoppeld ($upstream),
- Alle uitgangen van **de ASA-module** worden naar het **controleeindpunt** van de **tempSensor**verzonden.


## <a name="technical-information"></a>Technische informatie
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Huidige beperkingen voor IoT Edge-taken in vergelijking met cloudtaken
Het doel is om pariteit te hebben tussen IoT Edge-taken en cloudtaken. De meeste SQL-querytaalfuncties worden ondersteund, waardoor dezelfde logica kan worden uitgevoerd op zowel cloud als IoT Edge.
De volgende functies worden echter nog niet ondersteund voor randtaken:
* Door de gebruiker gedefinieerde functies (UDF) in JavaScript. UDF is beschikbaar in [C# voor IoT Edge-taken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (voorbeeld).
* Door de gebruiker gedefinieerde aggregaten (UDA).
* Azure ML-functies.
* Meer dan 14 aggregaten in één stap gebruiken.
* AVRO-formaat voor invoer/uitvoer. Op dit moment worden alleen CSV en JSON ondersteund.
* De volgende SQL-operatoren:
    * PARTITIE DOOR
    * GetMetadataPropertyValue
* Beleid voor late aankomst

### <a name="runtime-and-hardware-requirements"></a>Runtime en hardwarevereisten
Als u ASA op IoT Edge wilt uitvoeren, hebt u apparaten nodig die [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/)kunnen uitvoeren. 

ASA en Azure IoT Edge gebruiken **Docker-containers** om een draagbare oplossing te bieden die wordt uitgevoerd op meerdere hostbesturingssystemen (Windows, Linux).

ASA op IoT Edge wordt beschikbaar gesteld als Windows- en Linux-afbeeldingen en draait op zowel x86-64- als ARM-architecturen (Advanced RISC Machines). 


### <a name="input-and-output"></a>Invoer en uitvoer
#### <a name="input-and-output-streams"></a>Invoer- en uitvoerstromen
ASA Edge-taken kunnen ingangen en uitvoer van andere modules uitvoeren op IoT Edge-apparaten. Als u verbinding wilt maken van en met specifieke modules, u de routeringsconfiguratie instellen op implementatietijd. Meer informatie wordt beschreven over [de iEt edge module samenstelling documentatie](https://docs.microsoft.com/azure/iot-edge/module-composition).

Voor zowel ingangen als uitgangen worden CSV- en JSON-indelingen ondersteund.

Voor elke invoer- en uitvoerstroom die u in uw ASA-taak maakt, wordt een overeenkomstig eindpunt gemaakt op uw geïmplementeerde module. Deze eindpunten kunnen worden gebruikt in de routes van uw implementatie.

Op dit moment zijn edge hub de enige ondersteunde typen streaminvoer en streamuitvoer. Referentieinvoer ondersteunt het type referentiebestand. Andere uitvoer kan worden bereikt met behulp van een cloud job stroomafwaarts. Een Stream Analytics-taak die wordt gehost in Edge, verzendt bijvoorbeeld uitvoer naar Edge Hub, waarmee de uitvoer naar IoT Hub kan worden verzonden. U een tweede azure stream analytics-taak met cloud gehoste Azure Stream Analytics-taak gebruiken met invoer van IoT Hub en uitvoer naar Power BI of een ander uitvoertype.



##### <a name="reference-data"></a>Referentiegegevens
Referentiegegevens (ook wel opzoektabel genoemd) is een eindige gegevensset die statisch of traag verandert. Het wordt gebruikt om een lookup uit te voeren of om te correleren met uw gegevensstroom. Als u referentiegegevens wilt gebruiken in uw Azure Stream Analytics-taak, gebruikt u doorgaans een [JOIN voor verwijzingsgegevens](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) in uw query. Zie [Referentiegegevens gebruiken voor opzoekingen in Stream Analytics](stream-analytics-use-reference-data.md)voor meer informatie.

Alleen lokale referentiegegevens worden ondersteund. Wanneer een taak wordt geïmplementeerd op IoT Edge-apparaat, worden referentiegegevens van het door de gebruiker gedefinieerde bestandspad geladen.

Ga als lid van dag en ontwerp met referentiegegevens op Edge:

1. Maak een nieuwe invoer voor uw taak.

2. Kies **Referentiegegevens** als **brontype**.

3. Maak een referentiegegevensbestand gereed op het apparaat. Voor een Windows-container plaatst u het referentiegegevensbestand op het lokale station en deelt u het lokale station met de Docker-container. Maak voor een Linux-container een Docker-volume en vult het gegevensbestand in op het volume.

4. Stel het bestandspad in. Gebruik voor Windows Host OS en Windows-container het absolute pad: `E:\<PathToFile>\v1.csv`. Voor een Windows Host OS en Linux container of een Linux OS `<VolumeName>/file1.txt`en Linux container, gebruik maken van het pad in het volume: .

![Nieuwe referentiegegevensinvoer voor Azure Stream Analytics-taak op IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

De referentiegegevens voor IoT Edge-update worden geactiveerd door een implementatie. Eenmaal geactiveerd, kiest de ASA-module de bijgewerkte gegevens zonder de lopende taak te stoppen.

Er zijn twee manieren om de referentiegegevens bij te werken:
* Werk het referentiegegevenspad in uw ASA-taak bij vanuit Azure-portal.
* Werk de Implementatie van IoT Edge bij.

## <a name="license-and-third-party-notices"></a>Aankondigingen van licenties en derden
* [Azure Stream Analytics op IoT Edge-licentie](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Bericht van derden voor Azure Stream Analytics op IoT Edge.](https://go.microsoft.com/fwlink/?linkid=862828)

## <a name="azure-stream-analytics-module-image-information"></a>Afbeeldingsgegevens van azure Stream Analytics-modules 

Deze versie-informatie is voor het laatst bijgewerkt op 2019-06-27:

- Afbeelding: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-amd64`
   - basisafbeelding: microsoft/dotnet:2.1.6-runtime-alpine3.7
   - Platform:
      - architectuur: amd64
      - besturingssysteem: linux
  
- Afbeelding: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-arm32v7`
   - basisafbeelding: microsoft/dotnet:2.1.6-runtime-bionic-arm32v7
   - Platform:
      - architectuur: arm
      - besturingssysteem: linux
  
- Afbeelding: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-windows-amd64`
   - basisafbeelding: microsoft/dotnet:2.1.6-runtime-nanoserver-1809
   - Platform:
      - architectuur: amd64
      - besturingssysteem: windows
      
      
## <a name="get-help"></a>Help opvragen
Probeer het Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure Iot Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA op IoT Edge-zelfstudie](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Stream Analytics Edge-taken ontwikkelen met behulp van Visual Studio-hulpprogramma's](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [CI/CD implementeren voor Stream Analytics met API's](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
