---
title: Modules op schaal implementeren in Azure Portal-Azure IoT Edge
description: De Azure Portal gebruiken om automatische implementaties voor groepen van IoT Edge apparaten te maken
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 068845bf8cda7ce6abf11eefad0ed176688b34c5
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665845"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>IoT Edge modules op schaal implementeren en bewaken met behulp van de Azure Portal

Maak een **IOT Edge automatische implementatie** in de Azure portal voor het beheren van doorlopende implementaties voor veel apparaten tegelijk. Automatische implementaties voor IoT Edge maken deel uit van de functie voor het [automatisch beheren van apparaten](/azure/iot-hub/iot-hub-automatic-device-management) van IOT hub. Implementaties zijn dynamische processen waarmee u meerdere modules op meerdere apparaten kunt implementeren, de status en status van de modules kunt volgen en waar nodig wijzigingen kunt aanbrengen. 

Zie [informatie over IOT Edge automatische implementaties voor één apparaat of op schaal](module-deployment-monitoring.md)voor meer informatie.

## <a name="identify-devices-using-tags"></a>Apparaten identificeren met behulp van Tags

Voordat u een implementatie kunt maken, moet u kunnen opgeven welke apparaten u wilt beïnvloeden. Azure IoT Edge identificeert apparaten met behulp van **Tags** op het apparaat. Elk apparaat kan meerdere labels hebben die u op een wille keurige manier definieert voor uw oplossing. Als u bijvoorbeeld een universiteit van Smart gebouwen beheert, kunt u de volgende Tags toevoegen aan een apparaat:

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

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw IOT-hub. 
1. Selecteer **IOT Edge**.
1. Selecteer **IOT Edge-implementatie toevoegen**.

Er zijn vijf stappen voor het maken van een implementatie. In de volgende secties wordt elke stap uitgelegd. 

### <a name="step-1-name-and-label"></a>Stap 1: naam en label

1. Geef uw implementatie een unieke naam van Maxi maal 128 kleine letters. Vermijd spaties en de volgende ongeldige tekens: `& ^ [ ] { } \ | " < > /`.
1. U kunt labels toevoegen als sleutel-waardeparen om uw implementaties bij te houden. Bijvoorbeeld **HostPlatform** en **Linux**, of **versie** en **3.0.1**.
1. Selecteer **volgende** om naar stap twee te gaan. 

### <a name="step-2-add-modules-optional"></a>Stap 2: modules toevoegen (optioneel)

U kunt Maxi maal 20 modules aan een implementatie toevoegen. 

Als u een implementatie zonder modules maakt, worden alle huidige modules van de doel apparaten verwijderd. 

Voer de volgende stappen uit om een module van Azure Stream Analytics toe te voegen:

1. Klik in de sectie **implementatie modules** van de pagina op **toevoegen**.
1. Selecteer **Azure stream Analytics module**.
1. Kies uw **abonnement** in de vervolg keuzelijst.
1. Kies uw IoT **Edge-taak** in de vervolg keuzelijst.
1. Selecteer **Opslaan** om uw module toe te voegen aan de implementatie. 

Voer de volgende stappen uit om aangepaste code als een module toe te voegen of hand matig een Azure-service module toe te voegen:

1. Geef in de sectie **container Registry-instellingen** van de pagina de namen en referenties op voor de persoonlijke container registers die de module installatie kopieën voor deze implementatie bevatten. De IoT Edge-agent rapporteert fout 500 als de container register referentie voor een docker-installatie kopie niet kan worden gevonden.
1. Klik in de sectie **implementatie modules** van de pagina op **toevoegen**.
1. Selecteer **IOT Edge module**.
1. Geef een **naam**op voor de module.
1. Voer voor het veld **afbeeldings-URI** de container installatie kopie voor uw module in. 
1. Geef de **Opties** voor het maken van containers op die moeten worden door gegeven aan de container. Zie [docker Create](https://docs.docker.com/engine/reference/commandline/create/)(Engelstalig) voor meer informatie.
1. Gebruik de vervolg keuzelijst om een **beleid voor opnieuw opstarten**te selecteren. Kies uit de volgende opties: 
   * **Altijd** : de module wordt altijd opnieuw opgestart als deze om welke reden dan ook wordt afgesloten.
   * **nooit** : de module wordt nooit opnieuw opgestart als deze om welke reden dan ook wordt afgesloten.
   * **on-failure** -de module wordt opnieuw opgestart als deze crasht, maar niet als deze wordt uitgeschakeld. 
   * **bij een slechte** status: de module wordt opnieuw opgestart als deze niet meer reageert of een slecht resultaat heeft. Het is Maxi maal elke module voor het implementeren van de functie Health status. 
1. Gebruik de vervolg keuzelijst om de **gewenste status** voor de module te selecteren. Kies uit de volgende opties:
   * **actieve** uitvoering is de standaard optie. De module wordt direct na de implementatie gestart.
   * **gestopt** : na de implementatie blijft de module inactief totdat het starten van u of een andere module wordt aangeroepen.
1. Selecteer de **gewenste eigenschappen van module configureren** als u labels of andere eigenschappen wilt toevoegen aan de module dubbele.
1. Voer **omgevings variabelen** voor deze module in. Omgevings variabelen bieden configuratie-informatie aan een module.
1. Selecteer **Opslaan** om uw module toe te voegen aan de implementatie. 

Zodra u alle modules voor een implementatie hebt geconfigureerd, selecteert u **volgende** om naar stap 3 te gaan.

### <a name="step-3-specify-routes-optional"></a>Stap 3: routes opgeven (optioneel)

Routes bepalen hoe modules met elkaar communiceren binnen een implementatie. De wizard biedt standaard een route met de naam **route** en gedefinieerd als **van/* naar $upstream * *. Dit betekent dat alle berichten die door modules worden uitgevoerd, worden verzonden naar uw IOT-hub.  

Voeg de routes toe of werk deze bij met informatie van de [Declareer routes](module-composition.md#declare-routes)en selecteer vervolgens **volgende** om door te gaan naar de sectie beoordeling.

### <a name="step-4-specify-metrics-optional"></a>Stap 4: metrische gegevens opgeven (optioneel)

Metrische gegevens geven samen vattingen van de verschillende statussen die een apparaat kan terugsturen als gevolg van het Toep assen van configuratie-inhoud.

1. Voer een naam in voor de naam van de **metrische gegevens**.

1. Voer een query in voor **metrische criteria**. De query is gebaseerd op de dubbele [gerapporteerde eigenschappen](module-edgeagent-edgehub.md#edgehub-reported-properties)van IOT Edge hub-module. De metriek vertegenwoordigt het aantal rijen dat door de query wordt geretourneerd.

   Bijvoorbeeld:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Stap 5: doel apparaten

Gebruik de eigenschap labels van uw apparaten om te richten op de specifieke apparaten die deze implementatie moeten ontvangen. 

Omdat meerdere implementaties hetzelfde apparaat kunnen bereiken, moet u elke implementatie een prioriteits nummer geven. Als er ooit een conflict optreedt, wordt de implementatie met de hoogste prioriteit (hogere waarden duiden op een hogere prioriteit) WINS. Als twee implementaties hetzelfde prioriteits nummer hebben, is het het meest recent gemaakte WINS-account. 

1. Voer een positief geheel getal in voor de implementatie **prioriteit**.
1. Geef een **doel voorwaarde** op om te bepalen welke apparaten worden bedoeld voor deze implementatie. De voor waarde is gebaseerd op apparaatspecifieke Tags of dubbele gerapporteerde eigenschappen van het apparaat en moet overeenkomen met de indeling van de expressie. Bijvoorbeeld `tags.environment='test'` of `properties.reported.devicemodel='4000x'`. 
1. Selecteer **volgende** om door te gaan naar de laatste stap.

### <a name="step-6-review-deployment"></a>Stap 6: de implementatie controleren

Controleer uw implementatie gegevens en selecteer vervolgens **verzenden**.

## <a name="deploy-modules-from-azure-marketplace"></a>Modules implementeren vanuit Azure Marketplace

Azure Marketplace is een online Marketplace voor toepassingen en services waar u kunt bladeren door een breed scala aan bedrijfs toepassingen en oplossingen die zijn gecertificeerd en geoptimaliseerd om te worden uitgevoerd op Azure, met inbegrip van [IOT Edge-modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure Marketplace kan ook worden geopend via de Azure Portal onder **een resource maken**.

U kunt een IoT Edge module implementeren vanuit Azure Marketplace of via de Azure Portal:

1. Zoek een module en begin met het implementatie proces.

   * Azure Portal: u vindt een module en selecteert u **maken**.

   * Azure Marketplace:

     1. Zoek een module en selecteer **nu ophalen**.
     1. Bevestig de gebruiks voorwaarden en het privacybeleid van de provider door **door gaan**te selecteren.

1. Kies uw abonnement en het IoT Hub waaraan het doel apparaat is gekoppeld.

1. Kies **implementeren op schaal**.

1. Kies of u de module wilt toevoegen aan een nieuwe implementatie of aan een kloon van een bestaande implementatie. Als u wilt klonen, selecteert u de bestaande implementatie in de lijst.

1. Selecteer **maken** om het proces van het maken van een implementatie op schaal voort te zetten. U kunt dezelfde Details opgeven als voor elke implementatie.

## <a name="monitor-a-deployment"></a>Een implementatie bewaken

Voer de volgende stappen uit om de details van een implementatie weer te geven en te controleren welke apparaten worden uitgevoerd:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw IOT-hub. 
1. Selecteer **IOT Edge**.
1. Selecteer **IOT Edge implementaties**. 

   ![IoT Edge-implementaties weer geven](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Inspecteer de implementatie lijst. Voor elke implementatie kunt u de volgende gegevens bekijken:
   * **Id** : de naam van de implementatie.
   * **Doel voorwaarde** : de tag die wordt gebruikt voor het definiëren van de doel apparaten.
   * **Prioriteit** : het prioriteits nummer dat is toegewezen aan de implementatie.
   * Met de **metrische gegevens van systeem** - **doel** wordt het aantal apparaatdubbels in IOT hub aangegeven dat overeenkomt met de doel voorwaarde en **toegepast** , wordt het aantal apparaten aangegeven waarop de implementatie-inhoud is toegepast op hun module apparaatdubbels in IOT hub. 
   * **Metrische gegevens van apparaat** : het aantal IOT edge-apparaten in het geslaagde of fouten rapport van de IOT Edge-client runtime.
   * **Aangepaste metrieken** : het aantal IOT edge-apparaten in de implementatie rapport gegevens voor de metrieken die u voor de implementatie hebt gedefinieerd.
   * **Aanmaak tijd** : de tijds tempel van het moment waarop de implementatie is gemaakt. Deze tijds tempel wordt gebruikt om bindingen te verstoren wanneer twee implementaties dezelfde prioriteit hebben. 
1. Selecteer de implementatie die u wilt bewaken.  
1. Controleer de implementatie details. U kunt tabbladen gebruiken om de details van de implementatie te bekijken.

## <a name="modify-a-deployment"></a>Een implementatie wijzigen

Wanneer u een implementatie wijzigt, worden de wijzigingen onmiddellijk gerepliceerd naar alle doel apparaten. 

Als u de doel voorwaarde bijwerkt, worden de volgende updates uitgevoerd:

* Als een apparaat niet voldoet aan de oude doel voorwaarde, maar voldoet aan de nieuwe doel voorwaarde en deze implementatie de hoogste prioriteit voor dat apparaat is, wordt deze implementatie toegepast op het apparaat. 
* Als een apparaat waarop deze implementatie momenteel wordt uitgevoerd niet meer voldoet aan de doel voorwaarde, wordt deze implementatie verwijderd en neemt de implementatie van de volgende met de hoogste prioriteit in beslag. 
* Als een apparaat dat deze implementatie momenteel uitvoert niet meer voldoet aan de doel voorwaarde en niet voldoet aan de doel voorwaarde van andere implementaties, vindt er geen wijzigingen plaats op het apparaat. Het apparaat blijft de huidige modules in hun huidige staat uitvoeren, maar wordt niet meer beheerd als onderdeel van deze implementatie. Zodra de app aan de doel voorwaarde van een andere implementatie voldoet, wordt deze implementatie verwijderd en wordt de nieuwe geïmplementeerd. 

Als u een implementatie wilt wijzigen, gebruikt u de volgende stappen: 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw IOT-hub. 
1. Selecteer **IOT Edge**.
1. Selecteer **IOT Edge implementaties**. 

   ![IoT Edge-implementaties weer geven](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Selecteer de implementatie die u wilt wijzigen. 
1. Wijzigingen aanbrengen in de volgende velden: 
   * Doel voorwaarde
   * Metrische gegevens: u kunt metrische gegevens die u hebt gedefinieerd, wijzigen of verwijderen of nieuwe toevoegen.
   * Labels
   * Prioriteit
1. Selecteer **Opslaan**.
1. Volg de stappen in [een implementatie controleren](#monitor-a-deployment) om de wijzigingen door te voeren. 

## <a name="delete-a-deployment"></a>Een implementatie verwijderen

Wanneer u een implementatie verwijdert, nemen alle apparaten de volgende implementatie van de hoogste prioriteit. Als uw apparaten niet voldoen aan de doel voorwaarde van een andere implementatie, worden de modules niet verwijderd wanneer de implementatie wordt verwijderd. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw IOT-hub. 
1. Selecteer **IOT Edge**.
1. Selecteer **IOT Edge implementaties**. 

   ![IoT Edge-implementaties weer geven](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Gebruik het selectie vakje om de implementatie te selecteren die u wilt verwijderen. 
1. Selecteer **Verwijderen**.
1. Er wordt een prompt weer gegeven waarin wordt vermeld dat deze implementatie wordt verwijderd en teruggezet naar de vorige status voor alle apparaten.  Dit betekent dat een implementatie met een lagere prioriteit van toepassing is.  Als er geen andere implementatie is gericht, worden er geen modules verwijderd. Als u alle modules van uw apparaat wilt verwijderen, maakt u een implementatie met nul modules en implementeert u deze op dezelfde apparaten. Selecteer **Ja** om door te gaan. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het implementeren van modules voor het IOT Edge van apparaten](module-deployment-monitoring.md).
