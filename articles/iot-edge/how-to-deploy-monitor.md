---
title: Modules op schaal implementeren in Azure-portal - Azure IoT Edge
description: De Azure-portal gebruiken om automatische implementaties te maken voor groepen IoT Edge-apparaten
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271432"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>IoT Edge-modules op schaal implementeren en bewaken met behulp van de Azure-portal

Maak een **automatische implementatie van IoT Edge** in de Azure-portal om lopende implementaties voor veel apparaten tegelijk te beheren. Automatische implementaties voor IoT Edge maken deel uit van de [functie voor automatisch apparaatbeheer](/azure/iot-hub/iot-hub-automatic-device-management) van IoT Hub. Implementaties zijn dynamische processen waarmee u meerdere modules op meerdere apparaten implementeren, de status en status van de modules bijhouden en waar nodig wijzigingen aanbrengen.

Zie [IoT Edge-automatische implementaties voor afzonderlijke apparaten of op schaal begrijpen](module-deployment-monitoring.md)voor meer informatie.

## <a name="identify-devices-using-tags"></a>Apparaten identificeren met behulp van tags

Voordat u een implementatie maken, moet u kunnen opgeven welke apparaten u wilt beïnvloeden. Azure IoT Edge identificeert apparaten met behulp van **tags** in de apparaattweeling. Elk apparaat kan meerdere tags hebben die u definieert op een manier die zinvol is voor uw oplossing.

Als u bijvoorbeeld een campus met slimme gebouwen beheert, u locatie-, kamertypen en omgevingstags toevoegen aan een apparaat:

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

Zie [Apparaattweelingen begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)voor meer informatie over apparaattweelingen en tags.

## <a name="create-a-deployment"></a>Een implementatie maken

IoT Edge biedt twee verschillende typen automatische implementaties die u gebruiken om uw scenario aan te passen. U een *standaardimplementatie*maken, waaronder die runtime-modules van het systeem en eventuele extra modules en routes. Elk apparaat kan slechts één implementatie toepassen. U ook een *gelaagde implementatie*maken, die alleen aangepaste modules en routes bevat, niet de systeemruntime. Veel gelaagde implementaties kunnen worden gecombineerd op een apparaat, bovenop een standaardimplementatie. Zie Automatische implementaties van [IoT Edge begrijpen voor afzonderlijke apparaten of op schaal voor](module-deployment-monitoring.md)meer informatie over de manier waarop de twee typen automatische implementaties samenwerken.

De stappen voor het maken van een implementatie en een gelaagde implementatie zijn zeer vergelijkbaar. Eventuele verschillen worden opgeroepen in de volgende stappen.

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw IoT-hub.
1. Selecteer **IoT Edge** in het menu in het linkerdeelvenster onder **Automatisch apparaatbeheer**.
1. Selecteer op de bovenste balk **Implementatie maken** of **Gelaagde implementatie maken**.

Er zijn vijf stappen om een implementatie te maken. De volgende secties lopen door elk.

### <a name="step-1-name-and-label"></a>Stap 1: Naam en label

1. Geef uw implementatie een unieke naam die maximaal 128 kleine letters bedraagt. Vermijd spaties en de `& ^ [ ] { } \ | " < > /`volgende ongeldige tekens: .
1. U labels toevoegen als sleutelwaardeparen om uw implementaties bij te houden. Bijvoorbeeld **HostPlatform** en **Linux,** of **versie** en **3.0.1**.
1. Selecteer **Volgende: Modules** om naar stap twee te gaan.

### <a name="step-2-modules"></a>Stap 2: Modules

U maximaal 20 modules toevoegen aan een implementatie. Als u een implementatie zonder modules maakt, worden alle huidige modules van de doelapparaten verwijderd.

In implementaties u de instellingen voor de IoT Edge-agent en IoT Edge-hubmodules beheren. Selecteer **Runtime-instellingen** om de twee runtime-modules te configureren. In gelaagde implementatie zijn de runtime-modules niet inbegrepen, zodat ze niet kunnen worden geconfigureerd.

U drie typen modules toevoegen:

* IoT Edge-module
* Marketplace-module
* Azure Stream Analytics-module

#### <a name="add-an-iot-edge-module"></a>Een IoT Edge-module toevoegen

Voer de volgende stappen uit om aangepaste code als module toe te voegen of om handmatig een Azure-servicemodule toe te voegen:

1. Geef in de sectie **Referenties containerregister** van de pagina de namen en referenties op voor alle registers van privécontainers die de moduleafbeeldingen voor deze implementatie bevatten. De IoT Edge-agent rapporteert fout 500 als deze de referentie van het containerregister voor een Docker-afbeelding niet kan vinden.
1. Klik in het gedeelte **IoT Edge Modules** van de pagina op **Toevoegen**.
1. Selecteer **IoT Edge Module** in het vervolgkeuzemenu.
1. Geef uw module een naam van de **IoT Edge-module.**
1. Voer voor het veld **Beeld URI** de containerafbeelding voor uw module in.
1. Gebruik het vervolgkeuzemenu om een **beleid opnieuw opstarten**te selecteren . Kies uit de volgende opties:
   * **altijd** - De module wordt altijd opnieuw opgestart als deze om welke reden dan ook wordt afgesloten.
   * **nooit** - De module wordt nooit opnieuw opgestart als deze om welke reden dan ook wordt afgesloten.
   * **on-failure** - De module wordt opnieuw opgestart als deze vastloopt, maar niet als deze netjes wordt afgesloten.
   * **on-ongezond** - De module wordt opnieuw opgestart als deze vastloopt of een ongezonde status retourneert. Het is aan elke module om de statusfunctie te implementeren.
1. Gebruik het vervolgkeuzemenu om de **gewenste status** voor de module te selecteren. Kies uit de volgende opties:
   * **actief** - Uitvoeren is de standaardoptie. De module wordt onmiddellijk na de implementatie gestart.
   * **gestopt** - Nadat de module is geïmplementeerd, blijft deze inactief totdat de module wordt ingeschakeld om door u of een andere module te starten.
1. Geef **opties voor het maken van containers** op die aan de container moeten worden doorgegeven. Zie [docker maken voor](https://docs.docker.com/engine/reference/commandline/create/)meer informatie .
1. Selecteer **Module tweeinstellingen** als u tags of andere eigenschappen aan de moduletwee wilt toevoegen.
1. Voer **Omgevingsvariabelen** in voor deze module. Omgevingsvariabelen geven configuratie-informatie aan een module.
1. Selecteer **Toevoegen** om uw module toe te voegen aan de implementatie.

#### <a name="add-a-module-from-the-marketplace"></a>Een module toevoegen vanaf de Marketplace

Voer de volgende stappen uit om een module toe te voegen vanuit de Azure Marketplace:

1. Klik in het gedeelte **IoT Edge Modules** van de pagina op **Toevoegen**.
1. Selecteer **Marketplace-module** in het vervolgkeuzemenu.
1. Kies een module op de **marketplace-pagina van iot edge-modules.** De module die u selecteert, wordt automatisch geconfigureerd voor uw abonnement, resourcegroep en apparaat. Het verschijnt dan in uw lijst met IoT Edge-modules. Voor sommige modules is mogelijk een extra configuratie vereist. Zie [Modules implementeren vanuit Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)voor meer informatie.

#### <a name="add-a-stream-analytics-module"></a>Een Stream Analytics-module toevoegen

Voer de volgende stappen uit om een module toe te voegen vanuit Azure Stream Analytics:

1. Klik in het gedeelte **IoT Edge Modules** van de pagina op **Toevoegen**.
1. Selecteer **de Azure Stream Analytics-module** in het vervolgkeuzemenu.
1. Kies in het rechterdeelvenster uw **abonnement**.
1. Kies uw IoT **Edge-taak.**
1. Selecteer **Opslaan** om uw module toe te voegen aan de implementatie.

#### <a name="configure-module-settings"></a>Module-instellingen configureren

Nadat u een module aan een implementatie hebt toegevoegd, u de naam selecteren om de pagina **IoT Edge-module bijwerken** te openen. Op deze pagina u de module-instellingen, omgevingsvariabelen, opties en moduletwee bewerken. Als u een module van de marktplaats hebt toegevoegd, kan het zijn dat sommige van deze parameters al zijn ingevuld.

Als u een gelaagde implementatie maakt, configureert u mogelijk een module die bestaat in andere implementaties die zich op dezelfde apparaten richten. Als u de moduletwee wilt bijwerken zonder andere versies te overschrijven, opent u het tabblad **Tweeinstellingen module.** Maak bijvoorbeeld `properties.desired.settings`een nieuwe **module-tweelingeigenschap** met een unieke naam voor een onderafdeling binnen de gewenste eigenschappen van de moduletweeling. Als u eigenschappen binnen `properties.desired` het veld definieert, worden de gewenste eigenschappen voor de module die is gedefinieerd in implementaties met een lagere prioriteit overschreven.

![Dubbele eigenschap module instellen voor gelaagde implementatie](./media/how-to-deploy-monitor/module-twin-property.png)

Zie [Gelaagde implementatie](module-deployment-monitoring.md#layered-deployment)voor meer informatie over de dubbele configuratie van modules in gelaagde implementaties.

Zodra u alle modules voor een implementatie hebt geconfigureerd, selecteert u **Volgende: Routes** om naar stap drie te gaan.

### <a name="step-3-routes"></a>Stap 3: Routes

Routes bepalen hoe modules met elkaar communiceren binnen een implementatie. Standaard geeft de wizard u een route die **stroomopwaarts wordt** genoemd en gedefinieerd als **FROM /messages/\* INTO $upstream,** wat betekent dat berichten die door modules worden uitgevoerd, naar uw IoT-hub worden verzonden.  

Voeg de routes toe of werk deze bij met informatie uit [De routes declareren](module-composition.md#declare-routes)en selecteer **Volgende** om door te gaan naar de beoordelingssectie.

Selecteer **Volgende: Statistieken**.

### <a name="step-4-metrics"></a>Stap 4: Statistieken

Statistieken bieden overzichtstellingen van de verschillende statussen die een apparaat kan rapporteren als gevolg van het toepassen van configuratie-inhoud.

1. Voer een naam in voor **metrische naam**.

1. Voer een query in voor **metrische criteria**. De query is gebaseerd op iot Edge hub module twin [gerapporteerde eigenschappen](module-edgeagent-edgehub.md#edgehub-reported-properties). De statistiek vertegenwoordigt het aantal rijen dat door de query wordt geretourneerd.

   Bijvoorbeeld:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Selecteer **Volgende: Doelapparaten**.

### <a name="step-5-target-devices"></a>Stap 5: Doelapparaten

Gebruik de eigenschap labels van uw apparaten om de specifieke apparaten te targeten die deze implementatie moeten ontvangen.

Aangezien meerdere implementaties zich op hetzelfde apparaat kunnen richten, moet u elke implementatie een prioriteitsnummer geven. Als er ooit een conflict is, wint de implementatie met de hoogste prioriteit (grotere waarden duiden op hogere prioriteit). Als twee implementaties hetzelfde prioriteitsnummer hebben, wint degene die onlangs is gemaakt.

Als meerdere implementaties hetzelfde apparaat targeten, wordt alleen het apparaat met de hogere prioriteit toegepast. Als meerdere gelaagde implementaties op hetzelfde apparaat zijn gericht, worden ze allemaal toegepast. Als er echter eigenschappen worden gedupliceerd, zoals wanneer er twee routes met dezelfde naam zijn, overschrijft de eigenschappen van de gelaagde implementatie met hogere prioriteit de rest.

Elke gelaagde implementatie die gericht is op een apparaat, moet een hogere prioriteit hebben dan de basisimplementatie om te kunnen worden toegepast.

1. Voer een positief geheel getal in voor de **implementatieprioriteit**.
1. Voer een **doelvoorwaarde** in om te bepalen welke apparaten met deze implementatie worden getarget.De voorwaarde is gebaseerd op apparaat twin tags of apparaat twin gerapporteerde eigenschappen en moet overeenkomen met de expressie-indeling.Bijvoorbeeld `tags.environment='test'` of `properties.reported.devicemodel='4000x'`.

Selecteer **Volgende: Controleren + Maken** om door te gaan naar de laatste stap.

### <a name="step-6-review-and-create"></a>Stap 6: Bekijken en maken

Controleer uw implementatiegegevens en selecteer **Vervolgens Maken**.

## <a name="monitor-a-deployment"></a>Een implementatie controleren

Als u de details van een implementatie wilt weergeven en de apparaten wilt controleren waarop deze wordt uitgevoerd, voert u de volgende stappen uit:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.
1. Selecteer **IoT-rand**.
1. Selecteer het tabblad **IoT Edge-implementaties.**

   ![IoT Edge-implementaties weergeven](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Controleer de implementatielijst.Voor elke implementatie u de volgende details bekijken:
   * **ID** - de naam van de implementatie.
   * **Typ** - het type implementatie, **implementatie** of **gelaagde implementatie**.
   * **Doelvoorwaarde** - de tag die wordt gebruikt om gerichte apparaten te definiëren.
   * **Prioriteit** - het prioriteitsnummer dat aan de implementatie is toegewezen.
   * **Systeemstatistieken** - **Targeted** geeft het aantal apparaattweelingen in IoT Hub op dat overeenkomt met de targetingvoorwaarde en **Applied** geeft het aantal apparaten op waarop de implementatie-inhoud is toegepast op hun moduletweeling in IoT Hub.
   * **Apparaatstatistieken** - het aantal IoT Edge-apparaten in de implementatie rapportage succes of fouten van de IoT Edge client runtime.
   * **Aangepaste statistieken** : het aantal IoT Edge-apparaten in de implementatierapportagegegevens voor alle statistieken die u hebt gedefinieerd voor de implementatie.
   * **Aanmaaktijd** - de tijdstempel van toen de implementatie is gemaakt. Deze tijdstempel wordt gebruikt om banden te verbreken wanneer twee implementaties dezelfde prioriteit hebben.
1. Selecteer de implementatie die u wilt controleren.  
1. Controleer de implementatiegegevens. U tabbladen gebruiken om de details van de implementatie te bekijken.

## <a name="modify-a-deployment"></a>Een implementatie wijzigen

Wanneer u een implementatie wijzigt, worden de wijzigingen onmiddellijk gerepliceerd naar alle beoogde apparaten.

Als u de doelvoorwaarde bijwerkt, vinden de volgende updates plaats:

* Als een apparaat niet voldoet aan de oude doelvoorwaarde, maar voldoet aan de nieuwe doelvoorwaarde en deze implementatie de hoogste prioriteit heeft voor dat apparaat, wordt deze implementatie toegepast op het apparaat.
* Als een apparaat dat deze implementatie uitvoert niet meer voldoet aan de doelvoorwaarde, wordt deze implementatie ongedaan en wordt de volgende implementatie met de hoogste prioriteit uitgevoerd.
* Als een apparaat dat momenteel deze implementatie uitvoert niet meer voldoet aan de doelvoorwaarde en niet voldoet aan de doelvoorwaarde van andere implementaties, vindt er geen wijziging plaats op het apparaat. Het apparaat blijft de huidige modules in hun huidige staat uitvoeren, maar wordt niet meer beheerd als onderdeel van deze implementatie. Zodra deze voldoet aan de doelvoorwaarde van een andere implementatie, verwijdert het deze implementatie en neemt het de nieuwe aan.

Als u een implementatie wilt wijzigen, gebruikt u de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.
1. Selecteer **IoT-rand**.
1. Selecteer het tabblad **IoT Edge Deployments.**

   ![IoT Edge-implementaties weergeven](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Selecteer de implementatie die u wilt wijzigen.
1. Breng updates uit op de volgende tabbladen:
   * **Doelvoorwaarde**
   * **Statistieken** : u statistieken wijzigen of verwijderen die u hebt gedefinieerd of nieuwe statistieken toevoegen.
   * **Labels**
   * **Modules**
   * **Routes**
   * **Implementatie**

1. Selecteer **Opslaan**.
1. Volg de stappen in [Monitor een implementatie](#monitor-a-deployment) om de wijzigingen uit te rollen.

## <a name="delete-a-deployment"></a>Een implementatie verwijderen

Wanneer u een implementatie verwijdert, krijgen alle geïmplementeerde apparaten hun volgende implementatie met de hoogste prioriteit. Als uw apparaten niet voldoen aan de doelvoorwaarde van een andere implementatie, worden de modules niet verwijderd wanneer de implementatie wordt verwijderd.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.
1. Selecteer **IoT-rand**.
1. Selecteer het tabblad **IoT Edge Deployments.**

   ![IoT Edge-implementaties weergeven](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Gebruik het selectievakje om de implementatie in te schakelen die u wilt verwijderen.
1. Selecteer **Verwijderen**.
1. Een prompt zal u laten weten dat deze actie deze implementatie zal verwijderen en zal terugkeren naar de vorige status voor alle apparaten.Dit betekent dat een implementatie met een lagere prioriteit van toepassing is.Als er geen andere implementatie is gericht, worden er geen modules verwijderd. Als u alle modules van uw apparaat wilt verwijderen, maakt u een implementatie met nulmodules en implementeert u deze op dezelfde apparaten.Selecteer **Ja** om door te gaan.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het implementeren van modules naar IoT Edge-apparaten](module-deployment-monitoring.md).
