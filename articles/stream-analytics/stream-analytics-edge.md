---
title: Azure Stream Analytics op IoT Edge
description: Edge-taken maken in Azure Stream Analytics en deze implementeren op apparaten met Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 02/14/2020
ms.custom: seodec18
ms.openlocfilehash: 7e4a1cf43931b56cfdb3c31ffe870a07dbaa75af
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201752"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics op IoT Edge
 
Met Azure Stream Analytics (ASA) in IoT Edge kunnen ontwikkelaars in bijna realtime analytische intelligence implementeren dichter in de buurt van IoT-apparaten. Op deze manier kan er gebruik worden gemaakt van de volledige waarde van de gegevens die door het apparaat worden gegenereerd. Azure Stream Analytics biedt een lage latentie, tolerantie en efficiënt gebruik van bandbreedte en naleving. Ondernemingen kunnen nu besturingslogica implementeren die dicht bij de industriële bewerkingen ligt en een aanvulling vormen op Big Data-analyses die worden uitgevoerd in de cloud.  

Azure Stream Analytics op IoT Edge worden uitgevoerd binnen het [Azure IOT Edge](https://azure.microsoft.com/campaigns/iot-edge/) Framework. Nadat de taak is gemaakt in ASA, kunt u deze kunt implementeren en beheren met behulp van IoT-Hub.

## <a name="scenarios"></a>Scenario 's
![Diagram op hoog niveau van IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Opdracht en controle met een lage latentie**: productie veiligheids systemen moeten bijvoorbeeld reageren op operationele gegevens met een extreem lage latentie. Met ASA op IoT Edge, kunt u analyseren sensor gegevens in bijna realtime en opdrachten geven bij het detecteren van afwijkingen op een virtuele machine stoppen of alarmen te activeren.
*   **Beperkte connectiviteit met de Cloud**: essentiële systemen, zoals apparaten voor externe analyse, verbonden vaten of offshore-boren, moeten gegevens analyseren en reageren, zelfs wanneer de verbinding met de Cloud regel matig wordt uitgevoerd. Met ASA, uw streaming logica wordt uitgevoerd onafhankelijk van de verbinding met het netwerk en u kunt kiezen wat u verzendt naar de cloud voor verdere verwerking of opslag.
* **Beperkte band breedte**: de hoeveelheid gegevens die wordt geproduceerd door Jet-engines of verbonden auto's kan zo groot zijn dat gegevens moeten worden gefilterd of vooraf moeten worden verwerkt voordat deze naar de cloud worden verzonden. Met ASA, kunt u filteren en samenvoegen van de gegevens die moet worden verzonden naar de cloud.
* **Naleving**: naleving van regelgeving kan vereisen dat sommige gegevens lokaal worden geanonimiseerd of worden geaggregeerd voordat ze naar de cloud worden verzonden.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-taken in Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Wat is een 'edge'-taak?

ASA Edge-taken worden uitgevoerd in containers die zijn geïmplementeerd op [Azure IOT edge apparaten](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Deze zijn samengesteld uit twee delen:
1.  Een cloud-onderdeel dat verantwoordelijk is voor de taakdefinitie: gebruikers definiëren invoer, uitvoer en andere instellingen (niet-geordende gebeurtenissen, enzovoort) in de cloud.
2.  Een module die wordt uitgevoerd op uw IoT-apparaten. Het bevat de ASA-engine en ontvangt de taakdefinitie vanuit de cloud. 

ASA maakt gebruik van IoT-Hub aan het edge-taken implementeren naar apparaten. Meer informatie over [IOT Edge implementatie](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)vindt u hier.

![Azure Stream Analytics Edge-taak](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Installatie-instructies
De stappen op hoog niveau worden in de volgende tabel beschreven. Meer informatie krijgen in de volgende secties.

|      |Stap   | Opmerkingen   |
| ---   | ---   |  ---      |
| 1   | **Een opslag container maken**   | Storage-containers worden gebruikt voor het opslaan van de taakdefinitie van de waar ze kunnen worden geopend door uw IoT-apparaten. <br>  U kunt een bestaande storage-container opnieuw gebruiken.     |
| 2   | **Een ASA Edge-taak maken**   |  Maak een nieuwe taak, selecteer **Edge** als **hosting omgeving**. <br> Deze taken zijn gemaakt of worden beheerd vanuit de cloud en uitvoeren op uw eigen IoT Edge-apparaten.     |
| 3   | **Uw IoT Edge omgeving instellen op uw apparaat (s)**   | Instructies voor [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) of [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **ASA implementeren op uw IoT Edge apparaat (en)**   |  De definitie van de ASA-taak wordt geëxporteerd naar de opslagcontainer eerder hebt gemaakt.       |

U kunt [deze stapsgewijze zelf studie](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) volgen om uw eerste ASA-taak op IOT Edge te implementeren. De volgende video kunt u inzicht in het proces voor het uitvoeren van een Stream Analytics-taak op een IoT edge-apparaat:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Maak een opslagcontainer
Een storage-container is vereist om te exporteren van de query ASA gecompileerd en de taakconfiguratie van de. Het wordt gebruikt voor het configureren van de ASA-Docker-installatiekopie met uw specifieke query. 
1. Volg [deze instructies](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) voor het maken van een opslag account vanuit het Azure Portal. U kunt alle standaardopties aan dit account gebruiken met ASA houden.
2. Maak in de zojuist gemaakte opslagaccount, een blob storage-container:
    1. Klik op **blobs**en vervolgens op **+ container**. 
    2. Voer een naam in en behoud de container als **privé**.

#### <a name="create-an-asa-edge-job"></a>Een Edge ASA-taak maken
> [!Note]
> Deze zelfstudie richt zich op de ASA-taak maken met behulp van Azure portal. U kunt ook [de Visual Studio-invoeg toepassing gebruiken om een ASA Edge-taak te maken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Maken van de Azure-portal een nieuwe 'Stream Analytics-taak'. [Direct link om hier een nieuwe ASA-taak te maken](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Selecteer in het scherm maken de optie **Edge** as **Hosting Environment** (Zie de volgende afbeelding)

   ![Stream Analytics-taak voor edge-apparaten maken](media/stream-analytics-edge/create-asa-edge-job.png)
3. Taakdefinitie
    1. **Geef een of meer invoer gegevens**op. Definieer een of meer invoerstromen voor uw taak.
    2. Definieer referentiegegevens (optioneel).
    3. **Geef een of meer uitvoer stroom**op. Definieer een of meerdere uitvoer stromen voor uw taak. 
    4. **Query definiëren**. Definieer de ASA-query in de cloud met behulp van de inline-editor. De compiler wordt automatisch gecontroleerd of de syntaxis voor ASA edge is ingeschakeld. U kunt ook uw query testen door het uploaden van voorbeeldgegevens. 

4. Stel de gegevens van de opslag container in het menu **IOT Edge instellingen** in.

5. Optionele instellingen
    1. **Gebeurtenis volgorde**. U kunt de volgorde out-beleid configureren in de portal. Documentatie is [hier](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)beschikbaar.
    2. **Land instelling**. Stel de indeling van de deur.



> [!Note]
> Wanneer een implementatie wordt gemaakt, wordt de taakdefinitie in ASA geëxporteerd naar een opslagcontainer. Deze taakdefinitie blijven hetzelfde tijdens de duur van een implementatie. Als gevolg hiervan, als u wilt een taak die wordt uitgevoerd op de rand van het bij te werken, moet u de ASA-taak bewerken en maak vervolgens een nieuwe implementatie van IoT-Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Instellen van uw IoT Edge-omgeving op uw apparaat/apparaten
Edge-taken kunnen worden geïmplementeerd op apparaten met Azure IoT Edge.
Hiervoor moet u als volgt te werk:
- Een Iot-Hub maken.
- Docker en IoT Edge-runtime installeren op uw edge-apparaten.
- Stel uw apparaten in als **IOT edge apparaten** in IOT hub.

Deze stappen worden beschreven in de IoT Edge documentatie voor [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) of [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Implementatie ASA op uw IoT Edge-apparaten
##### <a name="add-asa-to-your-deployment"></a>ASA toevoegen aan uw implementatie
- Open in de Azure Portal IoT Hub, navigeer naar **IOT Edge** en klik op het apparaat dat u wilt instellen voor deze implementatie.
- Selecteer **modules instellen**, selecteer **+ toevoegen** en kies **Azure stream Analytics module**.
- Selecteer het abonnement en de ASA-Edge-taak die u hebt gemaakt. Klik op Opslaan.
![de module ASA toevoegen in uw implementatie](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Tijdens deze stap maakt de ASA een map met de naam 'EdgeJobs' in de storage-container (als deze niet al bestaat). Voor elke implementatie, wordt een nieuwe submap in de map 'EdgeJobs' gemaakt.
> Wanneer u uw taak op IoT Edge-apparaten implementeert, maakt ASA een Shared Access Signature (SAS) voor het definitie bestand van de taak. De SAS-sleutel wordt veilig naar de IoT Edge-apparaten met behulp van dubbele apparaat verzonden. De vervaldatum van deze sleutel is drie jaar van de dag van het is gemaakt. Wanneer u een IoT Edge taak bijwerkt, worden de SA'S gewijzigd, maar de versie van de installatie kopie wordt niet gewijzigd. Nadat u de **Update**hebt uitgevoerd, volgt u de implementatie werk stroom en wordt er een update melding op het apparaat vastgelegd.


Zie [Deze pagina](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)voor meer informatie over IOT Edge implementaties.


##### <a name="configure-routes"></a>Routes configureren
IoT Edge biedt een manier om declaratief routeren van berichten tussen modules en tussen modules en IoT-Hub. De volledige syntaxis wordt [hier](https://docs.microsoft.com/azure/iot-edge/module-composition)beschreven.
Namen van de invoer en uitvoer die zijn gemaakt in de ASA-taak kunnen worden gebruikt als eindpunten voor de routering.  

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
Dit voorbeeld toont de routes voor het scenario dat wordt beschreven in de volgende afbeelding. Het bevat een Edge-taak met de naam '**ASA**', met een invoer met de naam '**Tempe ratuur**' en een uitvoer met de naam '**Alert**'.
![diagram voor beeld van bericht routering](media/stream-analytics-edge/edge-message-routing-example.png)

In dit voorbeeld definieert de volgende routes:
- Elk bericht van de **temp sensor** wordt naar de module met de naam ' **ASA** ' verzonden naar de invoer met de naam **Tempe ratuur**,
- Alle uitvoer van de **ASA** -module worden verzonden naar de IOT hub die is gekoppeld aan dit apparaat ($upstream)
- Alle uitvoer van de **ASA** -module worden verzonden naar het **controle** -eind punt van de **temp sensor**.


## <a name="technical-information"></a>Technische informatie
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Huidige beperkingen voor IoT Edge-taken in vergelijking met cloudtaken
Het doel is dat pariteit tussen IoT Edge-taken en cloudtaken. De meeste functies van de SQL-query taal worden ondersteund, zodat dezelfde logica kan worden uitgevoerd in zowel de Cloud als IoT Edge.
Echter de volgende functies zijn nog niet ondersteund voor edge-taken:
* Gebruiker gedefinieerde functies (UDF's) in JavaScript. UDF is beschikbaar in [ C# voor IOT Edge-taken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (preview-versie).
* De gebruiker gedefinieerde verzamelingen (UDA).
* Azure ML-functies.
* Met behulp van meer dan 14 combinaties in één stap.
* AVRO-indeling voor invoer/uitvoer. Op dit moment worden alleen CSV en JSON ondersteund.
* De volgende SQL-operators:
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Runtime-en hardwarevereisten
Als u ASA wilt uitvoeren op IoT Edge, hebt u apparaten nodig waarop [Azure IOT Edge](https://azure.microsoft.com/campaigns/iot-edge/)kan worden uitgevoerd. 

ASA en Azure IoT Edge **docker** -containers gebruiken om een draag bare oplossing te bieden die op meerdere hostbesturingssysteem (Windows, Linux) wordt uitgevoerd.

ASA on IoT Edge wordt beschikbaar gesteld als Windows-en Linux-installatie kopieën die worden uitgevoerd op x86-64-of ARM-architecturen (Advanced RISC machines). 


### <a name="input-and-output"></a>Invoer en uitvoer
#### <a name="input-and-output-streams"></a>Invoer en uitvoer Streams
Edge ASA-taken kunnen krijgen in- en uitvoer van andere modules die worden uitgevoerd op IoT Edge-apparaten. Voor verbinding van en naar specifieke modules, kunt u de configuratie van de routering instellen tijdens de implementatie. Meer informatie vindt u in [de documentatie van de IOT Edge-module compositie](https://docs.microsoft.com/azure/iot-edge/module-composition).

Voor invoer en uitvoer, worden CSV en JSON-indelingen ondersteund.

Voor elke invoer en uitvoerstroom die u in de ASA-taak maken, wordt een bijbehorende eindpunt wordt gemaakt op uw geïmplementeerde module. Deze eindpunten kunnen worden gebruikt in de routes van uw implementatie.

Momenteel, de enige ondersteunde Stroominvoer en uitvoertypen van stream Edge Hub zijn. Verwijzen naar invoer ondersteunt verwijzing bestandstype. Andere uitvoer kunnen worden bereikt met behulp van een taak in de cloud downstream. Een Stream Analytics-taak die wordt gehost in Microsoft Edge wordt bijvoorbeeld uitvoer verzonden naar Edge Hub die u de uitvoer vervolgens naar IoT Hub verzenden kunt. U kunt een tweede in de cloud gehoste Azure Stream Analytics-taak met de invoer van IoT-Hub en de uitvoer naar Power BI of een andere uitvoertype.



##### <a name="reference-data"></a>Referentiegegevens
Referentiegegevens (ook wel bekend als een opzoektabel) is een eindige gegevensset die statisch of traag wijzigen in aard. Het wordt gebruikt om uit te voeren een zoekopdracht of correleren met de stroom van uw gegevens. Voor het gebruik van referentie gegevens in uw Azure Stream Analytics-taak gebruikt u meestal een [koppeling voor verwijzings gegevens](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) in uw query. Zie de [referentie gegevens gebruiken voor zoek acties in stream Analytics](stream-analytics-use-reference-data.md)voor meer informatie.

Alleen lokale referentiegegevens wordt ondersteund. Wanneer een taak wordt geïmplementeerd op IoT Edge-apparaat, wordt de referentiegegevens van het bestandspad van de gebruiker gedefinieerde geladen.

Een taak maakt met referentiegegevens voor edge-apparaten:

1. Maak een nieuwe invoer voor de taak.

2. Kies **referentie gegevens** als **bron type**.

3. Een bestand met referentiegegevens gereed op het apparaat hebben. Voor een Windows-container, plaats het bestand met referentiegegevens op de lokale schijf en delen van de lokale schijf met de Docker-container. Voor een Linux-container, maak een Docker-volume en vul in het gegevensbestand aan het volume.

4. Stel het bestandspad. Voor Windows host-besturings systeem en Windows-container gebruikt u het absolute pad: `E:\<PathToFile>\v1.csv`. Voor een Windows host OS-of Linux-container of een Linux-besturings systeem en een Linux-container gebruikt u het pad in het volume: `<VolumeName>/file1.txt`.

![Nieuwe gegevens van referentie-invoer voor Azure Stream Analytics-taak voor Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

De referentiegegevens op IoT Edge-update wordt geactiveerd door een implementatie. Wanneer wordt geactiveerd, kiest de ASA-module de bijgewerkte gegevens zonder de actieve taak is gestopt.

Er zijn twee manieren om bij te werken van de referentiegegevens:
* Gegevenspad van update-verwijzing in de ASA-taak in Azure portal.
* Bijwerken van de IoT Edge-implementatie.

## <a name="license-and-third-party-notices"></a>Licentie en kennisgevingen van derden
* [Azure stream Analytics op IOT Edge-licentie](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Kennisgeving van derden voor Azure stream Analytics op IOT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informatie over de installatie kopie van Azure Stream Analytics module 

Deze versie-informatie is voor het laatst bijgewerkt op 2019-06-27:

- Afbeelding: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-amd64`
   - basis installatie kopie: micro soft/DotNet: 2.1.6-runtime-Alpine 3.7
   - onafhankelijk
      - architectuur: amd64
      - besturings systeem: Linux
  
- Afbeelding: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-arm32v7`
   - basis installatie kopie: micro soft/DotNet: 2.1.6-runtime-Bionic-arm32v7
   - onafhankelijk
      - architectuur: arm
      - besturings systeem: Linux
  
- Afbeelding: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-windows-amd64`
   - basis installatie kopie: micro soft/DotNet: 2.1.6-runtime-nano server-1809
   - onafhankelijk
      - architectuur: amd64
      - besturings systeem: Windows
      
      
## <a name="get-help"></a>Help opvragen
Probeer het [Azure stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Zelf studie voor IoT Edge van ASA](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Stream Analytics Edge-taken ontwikkelen met behulp van Visual Studio Tools](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [CI/CD voor Stream Analytics implementeren met behulp van Api's](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
