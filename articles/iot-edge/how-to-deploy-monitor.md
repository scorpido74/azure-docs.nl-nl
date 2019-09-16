---
title: Automatische implementaties maken vanuit Azure portal - Azure IoT Edge | Microsoft Docs
description: De Azure portal gebruiken voor automatische implementaties voor groepen van IoT Edge-apparaten maken
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 83e2490821f59adeb37958c6c31403121a40274e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540890"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implementeren en bewaken van IoT Edge-modules op schaal met Azure portal

Maak een **automatische implementatie van IoT Edge** in Azure portal voor het beheren van doorlopende implementaties voor een groot aantal apparaten in één keer. Automatische implementaties voor IoT Edge maken deel uit van de [automatische Apparaatbeheer](/azure/iot-hub/iot-hub-automatic-device-management) functie van IoT-Hub. Implementaties zijn dynamische processen waarmee u kunt meerdere modules implementeren op meerdere apparaten, de status en integriteit van de modules volgen en wijzig indien nodig. 

Zie voor meer informatie, [inzicht in IoT Edge-automatische implementaties voor individuele apparaten of op schaal](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identificatie van apparaten met behulp van tags

Voordat u een implementatie maken kunt, moet u opgeven welke apparaten die u wilt toepassen. Azure IoT Edge-apparaten met identificeert **tags** op het dubbele apparaat. Elk apparaat kan hebben meerdere labels die u op een manier die zinvol is voor uw oplossing definieert. Als u een campus van slimme gebouwen beheert, kunt u bijvoorbeeld de volgende codes toevoegen aan een apparaat:

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

Zie voor meer informatie over apparaatdubbels en tags [apparaatdubbels begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Een implementatie maken

1. In de [Azure-portal](https://portal.azure.com), gaat u naar uw IoT-hub. 
1. Selecteer **IoT Edge**.
1. Selecteer **IoT Edge-implementatie toevoegen**.

Er zijn vijf stappen voor het maken van een implementatie. De volgende secties helpen bij elkaar. 

### <a name="step-1-name-and-label"></a>Stap 1: Naam en een Label

1. Geef uw implementatie een unieke naam die maximaal 128 kleine letters. Vermijd spaties en de volgende ongeldige tekens: `& ^ [ ] { } \ | " < > /`.
1. U kunt labels toevoegen als sleutel-waardeparen voor het bijhouden van uw implementaties. Bijvoorbeeld, **HostPlatform** en **Linux**, of **versie** en **3.0.1**.
1. Selecteer **volgende** verplaatsen naar stap 2. 

### <a name="step-2-add-modules-optional"></a>Stap 2: (Optioneel) Modules toevoegen

U kunt maximaal 20 modules toevoegen aan een implementatie. 

Als u een implementatie met geen modules maakt, worden alle huidige modules verwijderd uit de doelapparaten. 

Als u wilt toevoegen een module van Azure Stream Analytics, de volgende stappen uit:

1. In de **implementatie Modules** sectie van de pagina, klikt u op **toevoegen**.
1. Selecteer **Azure Stream Analytics-module**.
1. Kies uw **abonnement** uit de vervolgkeuzelijst.
1. Kies uw IoT **Edge-taak** uit de vervolgkeuzelijst.
1. Selecteer **opslaan** uw module toevoegen aan de implementatie. 

Aangepaste code als een module toevoegen of handmatig toevoegen van een Azure-service-module, als volgt te werk:

1. In de **Container Registry Settings** sectie van de pagina, geeft u de namen en referenties voor een persoonlijke containerregisters die de module-afbeeldingen voor deze implementatie bevatten. De IoT Edge-Agent wordt fout 500 rapporteren als deze de container registry-referentie niet voor een Docker-installatiekopie vinden kan.
1. In de **implementatie Modules** sectie van de pagina, klikt u op **toevoegen**.
1. Selecteer **IoT Edge-Module**.
1. Geef uw module een **naam**.
1. Voor de **URI installatiekopie** veld, voert u de container-installatiekopie voor uw module. 
1. Geef een **Container maken opties** die moeten worden doorgegeven aan de container. Zie voor meer informatie, [docker maken](https://docs.docker.com/engine/reference/commandline/create/).
1. Gebruik de vervolgkeuzelijst om te selecteren een **beleid voor opnieuw opstarten**. Kies in de volgende opties: 
   * **Altijd** -de module wordt altijd opnieuw opgestart als deze uitgeschakeld voor een bepaalde reden wordt.
   * **Nooit** -de module nooit wordt opnieuw opgestart als deze wordt afgesloten om een bepaalde reden.
   * **bij fout** -de module wordt opnieuw opgestart als deze vastloopt, maar niet als deze wordt afgesloten foutloos. 
   * **Op de slechte** -de module wordt opnieuw opgestart als het systeem vastloopt of een slechte status retourneert. Het is aan elke module voor het implementeren van de functie voor health-status. 
1. Gebruik de vervolgkeuzelijst om te selecteren de **gewenste Status** voor de module. Kies in de volgende opties:
   * **met** -wordt uitgevoerd, is de standaardoptie. De module wordt gestart onmiddellijk na de implementatie wordt uitgevoerd.
   * **Gestopt** -na de implementatie, de module blijft niet-actieve totdat opgevraagd door u of een andere module op te starten.
1. Selecteer **de gewenste eigenschappen van de moduledubbel Set** als u wilt toevoegen van labels of andere eigenschappen aan de moduledubbel.
1. Voer **omgevingsvariabelen** voor deze module. Omgevingsvariabelen bevatten configuratie-informatie aan een module.
1. Selecteer **opslaan** uw module toevoegen aan de implementatie. 

Zodra u alle modules voor een implementatie die is geconfigureerd hebt, selecteer **volgende** verplaatsen naar stap 3.

### <a name="step-3-specify-routes-optional"></a>Stap 3: (Optioneel) Routes opgeven

Routes definiëren hoe modules met elkaar communiceren binnen een implementatie. Standaard de wizard u geeft een route met de naam **route** en gedefinieerd als *FROM /in $upstream*\*, wat betekent dat de uitvoer van alle modules die berichten worden verzonden naar uw IoT-hub.  

Toevoegen of bijwerken van de routes met gegevens uit [declareren routes](module-composition.md#declare-routes)en selecteer vervolgens **volgende** om door te gaan naar de sectie controleren.

### <a name="step-4-specify-metrics-optional"></a>Stap 4: Geef de metrische gegevens (optioneel)

Metrische gegevens bieden samenvatting tellingen van de verschillende statussen aan die een apparaat rapporteren kan als gevolg van het toepassen van configuratie-inhoud.

1. Voer een naam in voor **metriek naam**.

1. Voer een query voor **metriek Criteria**. De query is gebaseerd op IoT Edge hub moduledubbel [gerapporteerde eigenschappen](module-edgeagent-edgehub.md#edgehub-reported-properties). De metrische waarde geeft het aantal rijen dat wordt geretourneerd door de query.

   Bijvoorbeeld:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Stap 5: Doelapparaten

Gebruik de eigenschap tags van uw apparaten gericht op de specifieke apparaten die deze implementatie moeten worden ontvangen. 

Omdat meerdere implementaties zijn op hetzelfde apparaat gericht kunnen, moet u elke implementatie enkele prioriteit geven. Als er een conflict optreedt is, wordt de implementatie met de hoogste prioriteit (hogere waarden geven hogere prioriteit) wins. Als twee implementaties hetzelfde prioriteitsnummer hebt, wordt het account waarmee de meeste is gemaakt onlangs wins. 

1. Voer een positief geheel getal voor de implementatie **prioriteit**.
1. Voer een **voorwaarde als doel** om te bepalen welke apparaten doelgroepen voor deze implementatie. De voorwaarde is gebaseerd op het apparaat apparaatdubbel-tags of apparaatdubbel gerapporteerde eigenschappen en moet overeenkomen met de indeling van de expressie. Bijvoorbeeld, `tags.environment='test'` of `properties.reported.devicemodel='4000x'`. 
1. Selecteer **volgende** om door te gaan naar de laatste stap.

### <a name="step-6-review-deployment"></a>Stap 6: Implementatie bekijken

Lees de informatie van uw implementatie, en selecteer vervolgens **indienen**.

## <a name="deploy-modules-from-azure-marketplace"></a>Implementeren van modules in Azure Marketplace

Azure Marketplace is een online-toepassingen en services waar u door een breed scala aan bedrijfstoepassingen en -oplossingen die zijn gecertificeerd en geoptimaliseerd bladeren kan voor het uitvoeren op Azure, met inbegrip van [IoT Edge-modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure Marketplace kan ook worden geopend via de Azure-portal onder **een Resource maken**.

U kunt een IoT Edge-module van Azure Marketplace of in Azure portal implementeren:

1. Zoek een module en met het implementatieproces begint.

   * Azure Portal: Zoek een module en selecteer **maken**.

   * Azure Marketplace:

     1. Zoek een module en selecteer **nu downloaden**.
     1. Bevestiging van de provider en de servicevoorwaarden en het privacybeleid door te selecteren **doorgaan**.

1. Kies uw abonnement en de IoT-Hub waarop het doelapparaat is aangesloten.

1. Kies **implementeren op schaal**.

1. Kies of u wilt toevoegen van de module aan een nieuwe implementatie of aan een kloon van een bestaande implementatie; Als het klonen, selecteert u de bestaande implementatie in de lijst.

1. Selecteer **maken** om door te gaan van het proces voor het maken van een implementatie op grote schaal. U moet mogelijk zijn om op te geven van de dezelfde gegevens, zoals u zou voor elke implementatie doen.

## <a name="monitor-a-deployment"></a>Controleer de implementatie van een

Bekijk de details van een implementatie en controleren van de apparaten waarop deze wordt uitgevoerd, gebruikt u de volgende stappen uit:

1. Aanmelden bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub. 
1. Selecteer **IoT Edge**.
1. Selecteer **IoT Edge-implementaties**. 

   ![IoT Edge-implementaties weergeven](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Controleer de implementatie-lijst. Voor elke implementatie, kunt u de volgende gegevens bekijken:
   * **ID** -de naam van de implementatie.
   * **Doel van de voorwaarde** -het label dat wordt gebruikt voor het definiëren van de betreffende apparaten.
   * **Prioriteit** -het getal prioriteit is toegewezen aan de implementatie.
   * **Systeemmeetgegevens** - **Targeted** Hiermee geeft u het aantal dubbele apparaten in IoT-Hub die overeenkomen met de doelitems voorwaarde, en **toegepast** geeft het aantal apparaten waarvoor was de implementatie-inhoud toegepast op hun moduledubbels in IoT Hub. 
   * **Metrische gegevens apparaat** -het aantal IoT Edge-apparaten in de implementatie is geslaagd of fouten in de IoT Edge-runtime client melden.
   * **Aangepaste metrische gegevens** -het aantal IoT Edge-apparaten in de implementatie rapportagegegevens voor alle metrische gegevens die u hebt gedefinieerd voor de implementatie.
   * **Aanmaaktijd** -de timestamp van wanneer de implementatie is gemaakt. Dit tijdstempel wordt gebruikt om ties afbreken wanneer twee implementaties dezelfde prioriteit hebben. 
1. Selecteer de implementatie die u wilt bewaken.  
1. Controleer de details van de implementatie. Tabbladen kunt u de details van de implementatie controleren.

## <a name="modify-a-deployment"></a>Een implementatie wijzigen

Wanneer u een implementatie wijzigt, worden de wijzigingen onmiddellijk gerepliceerd naar alle apparaten uit de doelgroep. 

Als u de doelvoorwaarde bijwerkt, gebeuren de volgende updates:

* Als een apparaat niet voldoet aan de oude doelvoorwaarde, maar voldoet aan de nieuwe doelvoorwaarde en deze implementatie de hoogste prioriteit voor het apparaat is, wordt klikt u vervolgens deze implementatie toegepast op het apparaat. 
* Als een apparaat op dit moment met deze implementatie niet meer voldoet aan de doelvoorwaarde, verwijdert deze implementatie en neemt de volgende implementatie met hoogste prioriteit. 
* Als een apparaat op dit moment met deze implementatie niet meer voldoet aan de doelvoorwaarde en niet voldoet aan de doelvoorwaarde van alle andere implementaties, treedt er geen wijziging op op het apparaat. Het apparaat wordt uitgevoerd de huidige modules in hun huidige status, maar niet als onderdeel van deze implementatie niet meer wordt beheerd. Als deze voldoet aan de doelvoorwaarde van elke andere implementatie, deze implementatie wordt verwijderd en wordt op de nieuwe computer. 

Als u wilt wijzigen in een implementatie, gebruikt u de volgende stappen uit: 

1. Aanmelden bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub. 
1. Selecteer **IoT Edge**.
1. Selecteer **IoT Edge-implementaties**. 

   ![IoT Edge-implementaties weergeven](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Selecteer de implementatie die u wilt wijzigen. 
1. Updates aanbrengen in de volgende velden: 
   * Doelvoorwaarde
   * Metrische gegevens over - die u kunt wijzigen of verwijderen van metrische gegevens u hebt gedefinieerd, of Voeg nieuwe toe.
   * Labels
   * Prioriteit
1. Selecteer **Opslaan**.
1. Volg de stappen in [Controleer de implementatie van een](#monitor-a-deployment) om te bekijken van de wijzigingen worden uitgerold. 

## <a name="delete-a-deployment"></a>Een implementatie verwijderen

Wanneer u een implementatie verwijdert, worden alle apparaten op de volgende implementatie met hoogste prioriteit. Als uw apparaten niet voldoen aan de doelvoorwaarde van elke andere implementatie, klikt u vervolgens de modules niet verwijderd wanneer de implementatie wordt verwijderd. 

1. Aanmelden bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub. 
1. Selecteer **IoT Edge**.
1. Selecteer **IoT Edge-implementaties**. 

   ![IoT Edge-implementaties weergeven](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Gebruik het selectievakje in om de implementatie die u wilt verwijderen. 
1. Selecteer **Verwijderen**.
1. Een prompt vertelt u dat deze actie wordt deze implementatie verwijderen en naar de vorige status voor alle apparaten terugkeren.  Dit betekent dat een implementatie met een lagere prioriteit wordt toegepast.  Als er geen andere implementatie is gericht, wordt geen modules worden verwijderd. Als u verwijderen van alle modules van het apparaat wilt, maakt u een implementatie met nul modules en deze implementeren in de dezelfde apparaten. Selecteer **Ja** om door te gaan. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [modules op IoT Edge-apparaten implementeren](module-deployment-monitoring.md).
