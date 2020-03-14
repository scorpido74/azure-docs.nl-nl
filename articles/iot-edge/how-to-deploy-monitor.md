---
title: Modules op schaal implementeren in Azure Portal-Azure IoT Edge
description: De Azure portal gebruiken voor automatische implementaties voor groepen van IoT Edge-apparaten maken
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271432"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implementeren en bewaken van IoT Edge-modules op schaal met Azure portal

Maak een **IOT Edge automatische implementatie** in de Azure portal voor het beheren van doorlopende implementaties voor veel apparaten tegelijk. Automatische implementaties voor IoT Edge maken deel uit van de functie voor het [automatisch beheren van apparaten](/azure/iot-hub/iot-hub-automatic-device-management) van IOT hub. Implementaties zijn dynamische processen waarmee u meerdere modules op meerdere apparaten kunt implementeren, de status en status van de modules kunt volgen en waar nodig wijzigingen kunt aanbrengen.

Zie [informatie over IOT Edge automatische implementaties voor één apparaat of op schaal](module-deployment-monitoring.md)voor meer informatie.

## <a name="identify-devices-using-tags"></a>Identificatie van apparaten met behulp van tags

Voordat u een implementatie maken kunt, moet u opgeven welke apparaten die u wilt toepassen. Azure IoT Edge identificeert apparaten met behulp van **Tags** op het apparaat. Elk apparaat kan meerdere labels hebben die u op een wille keurige manier definieert voor uw oplossing.

Als u bijvoorbeeld een campus van Smart gebouwen beheert, kunt u locatie, type room en omgevings Tags toevoegen aan een apparaat:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Zie voor meer informatie over apparaatdubbels en tags voor apparaten [inzicht in de apparaatdubbels in IOT hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Een implementatie maken

IoT Edge biedt twee verschillende soorten automatische implementaties die u kunt gebruiken om uw scenario aan te passen. U kunt een standaard *implementatie*maken, die de systeem runtime modules en eventuele extra modules en routes bevat. Op elk apparaat kan slechts één implementatie worden toegepast. U kunt ook een *gelaagde implementatie*maken, die alleen aangepaste modules en routes bevat, niet de systeem runtime. Veel gelaagde implementaties kunnen worden gecombineerd op een apparaat, bovenop een standaard implementatie. Zie voor meer informatie over de manier waarop de twee typen automatische implementaties samen werken [inzicht IOT Edge automatische implementaties voor afzonderlijke apparaten of op schaal](module-deployment-monitoring.md).

De stappen voor het maken van een implementatie en een gelaagde implementatie zijn vergelijkbaar. Eventuele verschillen worden in de volgende stappen genoemd.

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw IOT hub.
1. Selecteer in het menu in het linkerdeel venster **IOT Edge** onder **automatische Apparaatbeheer**.
1. Selecteer **implementatie maken** of **gelaagde implementatie maken**op de bovenste balk.

Er zijn vijf stappen voor het maken van een implementatie. De volgende secties helpen bij elkaar.

### <a name="step-1-name-and-label"></a>Stap 1: naam en label

1. Geef uw implementatie een unieke naam die maximaal 128 kleine letters. Vermijd spaties en de volgende ongeldige tekens: `& ^ [ ] { } \ | " < > /`.
1. U kunt labels toevoegen als sleutel-waardeparen om uw implementaties bij te houden. Bijvoorbeeld **HostPlatform** en **Linux**, of **versie** en **3.0.1**.
1. Selecteer **volgende: modules** om door te gaan naar stap 2.

### <a name="step-2-modules"></a>Stap 2: modules

U kunt Maxi maal 20 modules aan een implementatie toevoegen. Als u een implementatie zonder modules maakt, worden alle huidige modules van de doel apparaten verwijderd.

In implementaties kunt u de instellingen voor de IoT Edge agent en IoT Edge hub-modules beheren. Selecteer **runtime-instellingen** om de twee runtime modules te configureren. In gelaagde implementaties zijn de runtime modules niet opgenomen en kunnen niet worden geconfigureerd.

U kunt drie soorten modules toevoegen:

* Module IoT Edge
* Marketplace-module
* Module Azure Stream Analytics

#### <a name="add-an-iot-edge-module"></a>Een IoT Edge module toevoegen

Aangepaste code als een module toevoegen of handmatig toevoegen van een Azure-service-module, als volgt te werk:

1. Geef in de sectie **container Registry referenties** van de pagina de namen en referenties op voor de persoonlijke container registers die de module installatie kopieën voor deze implementatie bevatten. De IoT Edge-agent rapporteert fout 500 als de container register referentie voor een docker-installatie kopie niet kan worden gevonden.
1. Klik in de sectie **IOT Edge modules** van de pagina op **toevoegen**.
1. Selecteer **IOT Edge module** in de vervolg keuzelijst.
1. Geef uw module een **IOT Edge module naam**.
1. Voer voor het veld **afbeeldings-URI** de container installatie kopie voor uw module in.
1. Gebruik de vervolg keuzelijst om een **beleid voor opnieuw opstarten**te selecteren. Kies in de volgende opties:
   * **altijd** : de module wordt altijd opnieuw opgestart als deze om welke reden dan ook wordt afgesloten.
   * **nooit** : de module wordt nooit opnieuw opgestart als deze om welke reden dan ook wordt afgesloten.
   * **on-failure** -de module wordt opnieuw opgestart als deze crasht, maar niet als deze wordt uitgeschakeld.
   * **bij een slechte** status: de module wordt opnieuw opgestart als deze niet meer reageert of een slecht resultaat heeft. Het is aan elke module voor het implementeren van de functie voor health-status.
1. Gebruik de vervolg keuzelijst om de **gewenste status** voor de module te selecteren. Kies in de volgende opties:
   * **actieve** uitvoering is de standaard optie. De module wordt gestart onmiddellijk na de implementatie wordt uitgevoerd.
   * **gestopt** : na de implementatie blijft de module inactief totdat het starten van u of een andere module wordt aangeroepen.
1. Geef de **Opties** voor het maken van containers op die moeten worden door gegeven aan de container. Zie [docker Create](https://docs.docker.com/engine/reference/commandline/create/)(Engelstalig) voor meer informatie.
1. Selecteer de **dubbele instellingen** voor de module als u labels of andere eigenschappen wilt toevoegen aan de module dubbele.
1. Voer **omgevings variabelen** voor deze module in. Omgevings variabelen bieden configuratie-informatie aan een module.
1. Selecteer **toevoegen** om uw module toe te voegen aan de implementatie.

#### <a name="add-a-module-from-the-marketplace"></a>Een module toevoegen vanuit Marketplace

Voer de volgende stappen uit om een module toe te voegen vanuit Azure Marketplace:

1. Klik in de sectie **IOT Edge modules** van de pagina op **toevoegen**.
1. Selecteer **Marketplace module** in de vervolg keuzelijst.
1. Kies een module op de **Marketplace** -pagina van de IOT Edge-module. De module die u selecteert, wordt automatisch geconfigureerd voor uw abonnement, de resource groep en het apparaat. Deze wordt vervolgens weer gegeven in de lijst met IoT Edge-modules. Voor sommige modules is mogelijk aanvullende configuratie vereist. Zie [Deploying modules from Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)(Engelstalig) voor meer informatie.

#### <a name="add-a-stream-analytics-module"></a>Een Stream Analytics module toevoegen

Als u wilt toevoegen een module van Azure Stream Analytics, de volgende stappen uit:

1. Klik in de sectie **IOT Edge modules** van de pagina op **toevoegen**.
1. Selecteer **Azure stream Analytics module** in de vervolg keuzelijst.
1. Kies uw **abonnement**in het rechterdeel venster.
1. Kies uw IoT **Edge-taak**.
1. Selecteer **Opslaan** om uw module toe te voegen aan de implementatie.

#### <a name="configure-module-settings"></a>Module-instellingen configureren

Nadat u een module aan een implementatie hebt toegevoegd, kunt u de naam ervan selecteren om de pagina **IOT Edge module bijwerken** te openen. Op deze pagina kunt u de module-instellingen, omgevings variabelen, Create-opties en module twee bewerken. Als u een module van de Marketplace hebt toegevoegd, is het mogelijk dat sommige van deze para meters al zijn ingevuld.

Als u een gelaagde implementatie maakt, kunt u een module configureren die zich in andere implementaties bevindt die gericht zijn op dezelfde apparaten. Als u de module wilt bijwerken, ongeacht of andere versies worden overschreven, opent u het tabblad voor de **module dubbele instellingen** . Maak een nieuwe **module dubbele eigenschap** met een unieke naam voor een Subsectie binnen de gewenste eigenschappen van de module, bijvoorbeeld `properties.desired.settings`. Als u in het veld `properties.desired` eigenschappen definieert, worden de gewenste eigenschappen overschreven voor de module die is gedefinieerd in een implementatie met een lagere prioriteit.

![Module dubbele eigenschap instellen voor gelaagde implementatie](./media/how-to-deploy-monitor/module-twin-property.png)

Zie [gelaagde implementatie](module-deployment-monitoring.md#layered-deployment)voor meer informatie over de dubbele configuratie van een module in gelaagde implementaties.

Zodra u alle modules voor een implementatie hebt geconfigureerd, selecteert u **volgende: routes** om naar stap 3 te gaan.

### <a name="step-3-routes"></a>Stap 3: routes

Routes definiëren hoe modules met elkaar communiceren binnen een implementatie. De wizard geeft standaard een route met de naam **upstream** en gedefinieerd als **van/messages/\* naar $upstream**. Dit betekent dat alle berichten die door modules worden uitgevoerd, worden verzonden naar uw IOT-hub.  

Voeg de routes toe of werk deze bij met informatie van de [Declareer routes](module-composition.md#declare-routes)en selecteer vervolgens **volgende** om door te gaan naar de sectie beoordeling.

Selecteer **volgende: metrische gegevens**.

### <a name="step-4-metrics"></a>Stap 4: metrische gegevens

Metrische gegevens geven samen vattingen van de verschillende statussen die een apparaat kan terugsturen als gevolg van het Toep assen van configuratie-inhoud.

1. Voer een naam in voor de naam van de **metrische gegevens**.

1. Voer een query in voor **metrische criteria**. De query is gebaseerd op de dubbele [gerapporteerde eigenschappen](module-edgeagent-edgehub.md#edgehub-reported-properties)van IOT Edge hub-module. De metriek vertegenwoordigt het aantal rijen dat door de query wordt geretourneerd.

   Bijvoorbeeld:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Selecteer **volgende: doel apparaten**.

### <a name="step-5-target-devices"></a>Stap 5: doel apparaten

Gebruik de eigenschap tags van uw apparaten gericht op de specifieke apparaten die deze implementatie moeten worden ontvangen.

Omdat meerdere implementaties zijn op hetzelfde apparaat gericht kunnen, moet u elke implementatie enkele prioriteit geven. Als er ooit een conflict optreedt, wordt de implementatie met de hoogste prioriteit (hogere waarden duiden op een hogere prioriteit) WINS. Als twee implementaties hetzelfde prioriteitsnummer hebt, wordt het account waarmee de meeste is gemaakt onlangs wins.

Als meerdere implementaties gericht zijn op hetzelfde apparaat, wordt er alleen een met de hogere prioriteit toegepast. Als meerdere gelaagde implementaties gericht zijn op hetzelfde apparaat, worden ze allemaal toegepast. Als er echter eigenschappen worden gedupliceerd, bijvoorbeeld als er twee routes met dezelfde naam zijn, wordt de rest van de implementatie met een hogere prioriteit overschreven.

Een gelaagde implementatie die gericht is op een apparaat, moet een hogere prioriteit hebben dan de basis implementatie om toe te passen.

1. Voer een positief geheel getal in voor de implementatie **prioriteit**.
1. Geef een **doel voorwaarde** op om te bepalen welke apparaten worden bedoeld voor deze implementatie. De voor waarde is gebaseerd op apparaatspecifieke Tags of dubbele gerapporteerde eigenschappen van het apparaat en moet overeenkomen met de indeling van de expressie. Bijvoorbeeld `tags.environment='test'` of `properties.reported.devicemodel='4000x'`.

Selecteer **volgende: controleren + maken** om door te gaan naar de laatste stap.

### <a name="step-6-review-and-create"></a>Stap 6: controleren en maken

Controleer uw implementatie gegevens en selecteer vervolgens **maken**.

## <a name="monitor-a-deployment"></a>Controleer de implementatie van een

Bekijk de details van een implementatie en controleren van de apparaten waarop deze wordt uitgevoerd, gebruikt u de volgende stappen uit:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw IOT hub.
1. Selecteer **IOT Edge**.
1. Selecteer het tabblad **IOT Edge implementaties** .

   ![IoT Edge-implementaties weergeven](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Controleer de implementatie-lijst. Voor elke implementatie kunt u de volgende gegevens bekijken:
   * **Id** : de naam van de implementatie.
   * **Type** : het type implementatie, **implementatie** of **gelaagde implementatie**.
   * **Doel voorwaarde** : de tag die wordt gebruikt voor het definiëren van de doel apparaten.
   * **Prioriteit** : het prioriteits nummer dat is toegewezen aan de implementatie.
   * Met de **metrische gegevens van systeem** - **doel** wordt het aantal apparaatdubbels in IOT hub aangegeven dat overeenkomt met de doel voorwaarde en **toegepast** , wordt het aantal apparaten aangegeven waarop de implementatie-inhoud is toegepast op hun module apparaatdubbels in IOT hub.
   * **Metrische gegevens van apparaat** : het aantal IOT edge-apparaten in het geslaagde of fouten rapport van de IOT Edge-client runtime.
   * **Aangepaste metrieken** : het aantal IOT edge-apparaten in de implementatie rapport gegevens voor de metrieken die u voor de implementatie hebt gedefinieerd.
   * **Aanmaak tijd** : de tijds tempel van het moment waarop de implementatie is gemaakt. Dit tijdstempel wordt gebruikt om ties afbreken wanneer twee implementaties dezelfde prioriteit hebben.
1. Selecteer de implementatie die u wilt bewaken.  
1. Controleer de details van de implementatie. Tabbladen kunt u de details van de implementatie controleren.

## <a name="modify-a-deployment"></a>Een implementatie wijzigen

Wanneer u een implementatie wijzigt, worden de wijzigingen onmiddellijk gerepliceerd naar alle apparaten uit de doelgroep.

Als u de doelvoorwaarde bijwerkt, gebeuren de volgende updates:

* Als een apparaat niet voldoet aan de oude doelvoorwaarde, maar voldoet aan de nieuwe doelvoorwaarde en deze implementatie de hoogste prioriteit voor het apparaat is, wordt klikt u vervolgens deze implementatie toegepast op het apparaat.
* Als een apparaat op dit moment met deze implementatie niet meer voldoet aan de doelvoorwaarde, verwijdert deze implementatie en neemt de volgende implementatie met hoogste prioriteit.
* Als een apparaat op dit moment met deze implementatie niet meer voldoet aan de doelvoorwaarde en niet voldoet aan de doelvoorwaarde van alle andere implementaties, treedt er geen wijziging op op het apparaat. Het apparaat wordt uitgevoerd de huidige modules in hun huidige status, maar niet als onderdeel van deze implementatie niet meer wordt beheerd. Als deze voldoet aan de doelvoorwaarde van elke andere implementatie, deze implementatie wordt verwijderd en wordt op de nieuwe computer.

Als u wilt wijzigen in een implementatie, gebruikt u de volgende stappen uit:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw IOT hub.
1. Selecteer **IOT Edge**.
1. Selecteer het tabblad **IOT Edge implementaties** .

   ![IoT Edge-implementaties weergeven](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Selecteer de implementatie die u wilt wijzigen.
1. Updates maken op de volgende tabbladen:
   * **Doel voorwaarde**
   * **Metrische gegevens** : u kunt metrische gegevens die u hebt gedefinieerd, wijzigen of verwijderen of nieuwe toevoegen.
   * **Labels**
   * **Modules**
   * **Stuurt**
   * **Implementatie**

1. Selecteer **Opslaan**.
1. Volg de stappen in [een implementatie controleren](#monitor-a-deployment) om de wijzigingen door te voeren.

## <a name="delete-a-deployment"></a>Een implementatie verwijderen

Wanneer u een implementatie verwijdert, nemen alle geïmplementeerde apparaten de volgende implementatie van de hoogste prioriteit. Als uw apparaten niet voldoen aan de doelvoorwaarde van elke andere implementatie, klikt u vervolgens de modules niet verwijderd wanneer de implementatie wordt verwijderd.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw IOT hub.
1. Selecteer **IOT Edge**.
1. Selecteer het tabblad **IOT Edge implementaties** .

   ![IoT Edge-implementaties weergeven](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Gebruik het selectievakje in om de implementatie die u wilt verwijderen.
1. Selecteer **Verwijderen**.
1. Een prompt vertelt u dat deze actie wordt deze implementatie verwijderen en naar de vorige status voor alle apparaten terugkeren.  Dit betekent dat een implementatie met een lagere prioriteit van toepassing is. Als er geen andere implementatie is gericht, worden er geen modules verwijderd. Als u verwijderen van alle modules van het apparaat wilt, maakt u een implementatie met nul modules en deze implementeren in de dezelfde apparaten. Selecteer **Ja** om door te gaan.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het implementeren van modules voor het IOT Edge van apparaten](module-deployment-monitoring.md).
