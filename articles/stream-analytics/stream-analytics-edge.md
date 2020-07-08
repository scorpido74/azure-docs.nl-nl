---
title: Azure Stream Analytics op IoT Edge
description: Maak Edge-taken in Azure Stream Analytics en implementeer ze op apparaten met Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 5213a13539ac028d3e2bfec3bc7476ac645f615b
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86043475"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics op IoT Edge
 
Met Azure Stream Analytics (ASA) in IoT Edge kunnen ontwikkelaars in bijna realtime analytische intelligence implementeren dichter in de buurt van IoT-apparaten. Op deze manier kan er gebruik worden gemaakt van de volledige waarde van de gegevens die door het apparaat worden gegenereerd. Azure Stream Analytics biedt een lage latentie, tolerantie en efficiënt gebruik van bandbreedte en naleving. Ondernemingen kunnen nu besturingslogica implementeren die dicht bij de industriële bewerkingen ligt en een aanvulling vormen op Big Data-analyses die worden uitgevoerd in de cloud.  

Azure Stream Analytics op IoT Edge worden uitgevoerd binnen het [Azure IOT Edge](https://azure.microsoft.com/campaigns/iot-edge/) Framework. Zodra de taak is gemaakt in ASA, kunt u deze implementeren en beheren met behulp van IoT Hub.

## <a name="scenarios"></a>Scenario's
![Diagram van IoT Edge op hoog niveau](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Opdracht en controle met een lage latentie**: productie veiligheids systemen moeten bijvoorbeeld reageren op operationele gegevens met een extreem lage latentie. Met ASA op IoT Edge kunt u sensor gegevens in bijna realtime analyseren en opdrachten verlenen wanneer u afwijkingen detecteert om een computer te stoppen of waarschuwingen te activeren.
*   **Beperkte connectiviteit met de Cloud**: essentiële systemen, zoals apparaten voor externe analyse, verbonden vaten of offshore-boren, moeten gegevens analyseren en reageren, zelfs wanneer de verbinding met de Cloud regel matig wordt uitgevoerd. Met ASA wordt uw streaming Logic onafhankelijk van de netwerk verbinding uitgevoerd en kunt u kiezen wat u naar de Cloud verzendt voor verdere verwerking of opslag.
* **Beperkte band breedte**: de hoeveelheid gegevens die wordt geproduceerd door Jet-engines of verbonden auto's kan zo groot zijn dat gegevens moeten worden gefilterd of vooraf moeten worden verwerkt voordat deze naar de cloud worden verzonden. Met ASA kunt u de gegevens die naar de Cloud moeten worden verzonden, filteren of samen voegen.
* **Naleving**: naleving van regelgeving kan vereisen dat sommige gegevens lokaal worden geanonimiseerd of worden geaggregeerd voordat ze naar de cloud worden verzonden.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-taken in Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Wat is een ' edge-taak '?

ASA Edge-taken worden uitgevoerd in containers die zijn geïmplementeerd op [Azure IOT edge apparaten](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Ze bestaan uit twee delen:
1.  Een Cloud onderdeel dat verantwoordelijk is voor de taak definitie: gebruikers definiëren de invoer, uitvoer, query en andere instellingen (op volg orde van gebeurtenissen, enzovoort) in de Cloud.
2.  Een module die wordt uitgevoerd op uw IoT-apparaten. Deze bevat de ASA-engine en ontvangt de taak definitie van de Cloud. 

ASA maakt gebruik van IoT Hub om Edge-taken op een of meer apparaten te implementeren. Meer informatie over [IOT Edge implementatie](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)vindt u hier.

![Taak voor Azure Stream Analytics Edge](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Installatie-instructies
De stappen op hoog niveau worden in de volgende tabel beschreven. Meer informatie vindt u in de volgende secties.
|Stap   | Notities   |
| ---   |  ---      |
| **Een opslag container maken**   | Opslag containers worden gebruikt voor het opslaan van uw taak definitie, waar ze toegankelijk zijn voor uw IoT-apparaten. <br>  U kunt elke bestaande opslag container opnieuw gebruiken.     |
| **Een ASA Edge-taak maken**   |  Maak een nieuwe taak, selecteer **Edge** als **hosting omgeving**. <br> Deze taken worden gemaakt/beheerd vanuit de Cloud en uitgevoerd op uw eigen IoT Edge apparaten.     |
| **Uw IoT Edge omgeving instellen op uw apparaat (s)**   | Instructies voor [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) of [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| **ASA implementeren op uw IoT Edge apparaat (en)**   |  De taak definitie van ASA wordt geëxporteerd naar de opslag container die u eerder hebt gemaakt.       |

U kunt [deze stapsgewijze zelf studie](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) volgen om uw eerste ASA-taak op IOT Edge te implementeren. De volgende video helpt u inzicht te krijgen in het proces om een Stream Analytics-taak uit te voeren op een IoT edge-apparaat:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Een opslag container maken
Een opslag container is vereist om de gecompileerde query van ASA en de taak configuratie te exporteren. Het wordt gebruikt om de ASA docker-installatie kopie te configureren met uw specifieke query. 
1. Volg [deze instructies](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) voor het maken van een opslag account vanuit het Azure Portal. U kunt alle standaard opties blijven gebruiken voor dit account met ASA.
2. Maak een BLOB storage-container in het zojuist gemaakte opslag account:
    1. Klik op **blobs**en vervolgens op **+ container**. 
    2. Voer een naam in en behoud de container als **privé**.

#### <a name="create-an-asa-edge-job"></a>Een ASA Edge-taak maken
> [!Note]
> Deze zelf studie is gericht op het maken van ASA-taken met behulp van Azure Portal. U kunt ook [de Visual Studio-invoeg toepassing gebruiken om een ASA Edge-taak te maken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Maak in de Azure Portal een nieuwe ' Stream Analytics-taak '. [Direct link om hier een nieuwe ASA-taak te maken](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Selecteer in het scherm maken de optie **Edge** as **Hosting Environment** (Zie de volgende afbeelding)

   ![Stream Analytics-taak maken aan rand](media/stream-analytics-edge/create-asa-edge-job.png)
3. Taak definitie
    1. **Geef een of meer invoer gegevens**op. Definieer een of meer invoer stromen voor uw taak.
    2. Referentie gegevens definiëren (optioneel).
    3. **Geef een of meer uitvoer stroom**op. Definieer een of meer uitvoer stromen voor uw taak. 
    4. **Query definiëren**. Definieer de ASA-query in de Cloud met behulp van de inline-editor. De compiler controleert automatisch de syntaxis ingeschakeld voor ASA-rand. U kunt ook uw query testen door voorbeeld gegevens te uploaden. 

4. Stel de gegevens van de opslag container in het menu **IOT Edge instellingen** in.

5. Optionele instellingen instellen
    1. **Gebeurtenis volgorde**. U kunt out-of-order beleid configureren in de portal. Documentatie is [hier](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)beschikbaar.
    2. **Land instelling**. Stel de internalization-indeling in.



> [!Note]
> Wanneer een implementatie wordt gemaakt, wordt de taak definitie door ASA naar een opslag container geëxporteerd. Deze taak definitie blijft hetzelfde tijdens de duur van een implementatie. Als u een taak die wordt uitgevoerd op de rand wilt bijwerken, moet u de taak in ASA bewerken en vervolgens een nieuwe implementatie in IoT Hub maken.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Uw IoT Edge omgeving instellen op uw apparaat (s)
Edge-taken kunnen worden geïmplementeerd op apparaten met Azure IoT Edge.
Hiervoor moet u de volgende stappen uitvoeren:
- Maak een IOT-hub.
- Installeer docker en IoT Edge runtime op uw edge-apparaten.
- Stel uw apparaten in als **IOT edge apparaten** in IOT hub.

Deze stappen worden beschreven in de IoT Edge documentatie voor [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) of [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Implementatie-ASA op uw IoT Edge apparaat (en)
##### <a name="add-asa-to-your-deployment"></a>ASA toevoegen aan uw implementatie
- Open in de Azure Portal IoT Hub, navigeer naar **IOT Edge** en klik op het apparaat dat u wilt instellen voor deze implementatie.
- Selecteer **modules instellen**, selecteer **+ toevoegen** en kies **Azure stream Analytics module**.
- Selecteer het abonnement en de ASA Edge-taak die u hebt gemaakt. Klik op Opslaan.
![De ASA-module toevoegen in uw implementatie](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Tijdens deze stap maakt ASA een map met de naam ' EdgeJobs ' in de opslag container (als deze nog niet bestaat). Voor elke implementatie wordt een nieuwe submap gemaakt in de map ' EdgeJobs '.
> Wanneer u uw taak op IoT Edge-apparaten implementeert, maakt ASA een Shared Access Signature (SAS) voor het definitie bestand van de taak. De SAS-sleutel wordt veilig verzonden naar de IoT Edge-apparaten met behulp van apparaat-twee. De verval datum van deze sleutel is drie jaar vanaf de dag waarop deze is gemaakt. Wanneer u een IoT Edge taak bijwerkt, worden de SA'S gewijzigd, maar de versie van de installatie kopie wordt niet gewijzigd. Nadat u de **Update**hebt uitgevoerd, volgt u de implementatie werk stroom en wordt er een update melding op het apparaat vastgelegd.


Zie [Deze pagina](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)voor meer informatie over IOT Edge implementaties.


##### <a name="configure-routes"></a>Routes configureren
IoT Edge biedt een manier om berichten op declaratieve wijze te routeren tussen modules en tussen modules en IoT Hub. De volledige syntaxis wordt [hier](https://docs.microsoft.com/azure/iot-edge/module-composition)beschreven.
Namen van de invoer en uitvoer die in de ASA-taak worden gemaakt, kunnen worden gebruikt als eind punten voor route ring.  

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
In dit voor beeld ziet u de routes voor het scenario dat wordt beschreven in de volgende afbeelding. Het bevat een Edge-taak met de naam '**ASA**', met een invoer met de naam '**Tempe ratuur**' en een uitvoer met de naam '**Alert**'.
![Diagram voorbeeld van bericht routering](media/stream-analytics-edge/edge-message-routing-example.png)

In dit voor beeld worden de volgende routes gedefinieerd:
- Elk bericht van de **temp sensor** wordt naar de module met de naam ' **ASA** ' verzonden naar de invoer met de naam **Tempe ratuur**,
- Alle uitvoer van de **ASA** -module worden verzonden naar de IOT hub die is gekoppeld aan dit apparaat ($upstream)
- Alle uitvoer van de **ASA** -module worden verzonden naar het **controle** -eind punt van de **temp sensor**.


## <a name="technical-information"></a>Technische informatie
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Huidige beperkingen voor IoT Edge taken vergeleken met Cloud taken
Het doel is om pariteit tussen IoT Edge taken en Cloud taken te hebben. De meeste functies van de SQL-query taal worden ondersteund, zodat dezelfde logica kan worden uitgevoerd in zowel de Cloud als IoT Edge.
De volgende functies worden echter nog niet ondersteund voor Edge-taken:
* Door de gebruiker gedefinieerde functies (UDF) in Java script. UDF is beschikbaar in [C# voor IOT Edge-taken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (preview-versie).
* Door de gebruiker gedefinieerde aggregaties (UDA).
* Azure ML-functies.
* Het gebruik van meer dan 14 statistische functies in één stap.
* AVRO-indeling voor invoer/uitvoer. Op dit moment worden alleen CSV en JSON ondersteund.
* De volgende SQL-Opera tors:
    * PARTITIONEREN OP
    * GetMetadataPropertyValue
* Beleid voor late aankomst

### <a name="runtime-and-hardware-requirements"></a>Runtime-en hardwarevereisten
Als u ASA wilt uitvoeren op IoT Edge, hebt u apparaten nodig waarop [Azure IOT Edge](https://azure.microsoft.com/campaigns/iot-edge/)kan worden uitgevoerd. 

ASA en Azure IoT Edge **docker** -containers gebruiken om een draag bare oplossing te bieden die op meerdere hostbesturingssysteem (Windows, Linux) wordt uitgevoerd.

ASA on IoT Edge wordt beschikbaar gesteld als Windows-en Linux-installatie kopieën die worden uitgevoerd op x86-64-of ARM-architecturen (Advanced RISC machines). 


### <a name="input-and-output"></a>Invoer en uitvoer
#### <a name="input-and-output-streams"></a>Invoer-en uitvoer stromen
ASA Edge-taken kunnen invoer en uitvoer ophalen van andere modules die op IoT Edge apparaten worden uitgevoerd. Als u verbinding wilt maken vanuit en naar specifieke modules, kunt u de routerings configuratie instellen tijdens de implementatie. Meer informatie vindt u in [de documentatie van de IOT Edge-module compositie](https://docs.microsoft.com/azure/iot-edge/module-composition).

Voor zowel invoer als uitvoer worden CSV-en JSON-indelingen ondersteund.

Voor elke invoer-en uitvoer stroom die u in uw ASA-taak maakt, wordt een bijbehorend eind punt op uw geïmplementeerde module gemaakt. Deze eind punten kunnen worden gebruikt in de routes van uw implementatie.

Momenteel zijn de enige ondersteunde stroom invoer en uitvoer typen van de stroom Edge hub. Verwijzings invoer ondersteunt verwijzings bestands type. Andere uitvoer kan worden bereikt met behulp van een Cloud taak downstream. Een Stream Analytics taak die in Edge wordt gehost, verzendt bijvoorbeeld uitvoer naar Edge hub, waardoor uitvoer naar IoT Hub kan worden verzonden. U kunt een tweede in de Cloud gehoste Azure Stream Analytics-taak met invoer van IoT Hub en uitvoer naar Power BI of een ander uitvoer type gebruiken.



##### <a name="reference-data"></a>Referentie gegevens
Referentie gegevens (ook wel een opzoek tabel genoemd) is een beperkte gegevensset die statisch of langzaam wordt gewijzigd. Deze wordt gebruikt om een zoek actie uit te voeren of om te correleren met uw gegevens stroom. Voor het gebruik van referentie gegevens in uw Azure Stream Analytics-taak gebruikt u meestal een [koppeling voor verwijzings gegevens](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) in uw query. Zie de [referentie gegevens gebruiken voor zoek acties in stream Analytics](stream-analytics-use-reference-data.md)voor meer informatie.

Alleen lokale referentie gegevens worden ondersteund. Wanneer een taak wordt geïmplementeerd op IoT Edge apparaat, worden er referentie gegevens uit het door de gebruiker gedefinieerde bestandspad geladen.

Een taak met referentie gegevens aan de rand maken:

1. Maak een nieuwe invoer voor uw taak.

2. Kies **referentie gegevens** als **bron type**.

3. Een bestand met referentie gegevens gereed is voor het apparaat. Plaats voor een Windows-container het bestand met referentie gegevens op de lokale schijf en deel het lokale station met de docker-container. Voor een Linux-container maakt u een docker-volume en vult u het gegevens bestand in op het volume.

4. Stel het bestandspad in. Voor Windows host-besturings systeem en Windows-container gebruikt u het absolute pad: `E:\<PathToFile>\v1.csv` . Voor een Windows host-besturings systeem en een Linux-container of een Linux-besturings systeem en een Linux-container gebruikt u het pad in het volume: `<VolumeName>/file1.txt` .

![Nieuwe invoer van referentie gegevens voor Azure Stream Analytics taak op IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

De referentie gegevens op IoT Edge update worden geactiveerd door een implementatie. Nadat de ASA-module is geactiveerd, worden de bijgewerkte gegevens verzameld zonder de actieve taak te stoppen.

Er zijn twee manieren om de referentie gegevens bij te werken:
* Het pad naar de referentie gegevens in uw ASA-taak bijwerken vanuit Azure Portal.
* Werk de IoT Edge-implementatie bij.

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
      
      
## <a name="get-help"></a>Hulp vragen
Ga voor meer hulp naar de [pagina micro soft Q&een vraag voor Azure stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

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
