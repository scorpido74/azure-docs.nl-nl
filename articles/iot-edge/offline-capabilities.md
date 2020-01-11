---
title: Offline - apparaten werkt Azure IoT Edge | Microsoft Docs
description: Begrijp hoe IoT Edge-apparaten en -modules kunnen werken zonder internetverbinding gedurende langere tijd wordt opgelost, en hoe normale IoT-apparaten offline te werken op IoT Edge kunt inschakelen.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 951c81b2d65fe17f6e79dbdd699051ba43b86c49
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867376"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Uitgebreide offline mogelijkheden voor IoT Edge apparaten, modules en onderliggende apparaten begrijpen

Azure IoT Edge ondersteunt uitgebreide offline bewerkingen op uw IoT Edge apparaten en schakelt offline bewerkingen op niet-IoT Edge onderliggende apparaten uit. Zolang een IoT Edge apparaat één mogelijkheid heeft om verbinding te maken met IoT Hub, kunnen het apparaat en eventuele onderliggende apparaten blijven functioneren met een onregelmatige of geen Internet verbinding.

## <a name="how-it-works"></a>Het werkt als volgt

Wanneer een IoT Edge apparaat in de offline modus wordt gezet, neemt de IoT Edge hub drie rollen in beslag. Eerst worden opgeslagen berichten die heengaat upstream en slaat ze op totdat het apparaat opnieuw verbinding maakt. Ten tweede, fungeert deze namens IoT Hub voor verificatie van modules en onderliggende apparaten zodat ze kunnen blijven functioneren. Ten slotte maakt communicatie tussen de apparaten van de onderliggende die normaal zou gaan via IoT Hub mogelijk.

Het volgende voorbeeld ziet u de werking van een IoT Edge-scenario in de offlinemodus:

1. **Apparaten configureren**

   IoT Edge-apparaten hebben automatisch offline-mogelijkheden ingeschakeld. Om uit te breiden die mogelijkheid met andere IoT-apparaten, moet u een bovenliggende / onderliggende relatie tussen de apparaten in IoT Hub declareren. Vervolgens configureert u de onderliggende apparaten om hun toegewezen bovenliggende apparaat te vertrouwen en de apparaat-naar-Cloud-communicatie te routeren via de bovenliggende computer als gateway.

2. **Synchroniseren met IoT Hub**

   Ten minste eenmaal na de installatie van de IoT Edge-runtime, de IoT Edge-apparaat moet online zijn om te synchroniseren met IoT Hub. In deze synchronisatie haalt de IoT Edge-apparaat meer informatie over alle onderliggende apparaten zijn toegewezen. De IoT Edge-apparaat wordt ook veilig updates van de lokale cache om in te schakelen van offline-bewerkingen en worden instellingen voor lokale opslag van berichten over telemetrie opgehaald.

3. **Offline gaan**

   Terwijl niet verbonden met IoT Hub, kunnen de IoT Edge-apparaat, de geïmplementeerde modules en alle onderliggende items IoT-apparaten werken voor onbepaalde tijd. Modules en onderliggende apparaten kunnen worden gestart en opnieuw worden opgestart door te verifiëren met de IoT Edge hub terwijl ze offline zijn. Telemetrie upstream gebonden aan IoT Hub worden lokaal opgeslagen. Communicatie tussen modules of onderliggende IoT-apparaten wordt onderhouden door directe methoden of berichten.

4. **Opnieuw verbinding maken en opnieuw synchroniseren met IoT Hub**

   Zodra de verbinding met IoT Hub is hersteld, wordt de IoT Edge-apparaat weer wordt gesynchroniseerd. Lokaal opgeslagen berichten worden direct aan het IoT Hub bezorgd, maar zijn afhankelijk van de snelheid van de verbinding, IoT Hub latentie en gerelateerde factoren. Deze worden bezorgd in dezelfde volg orde waarin ze zijn opgeslagen.

   Eventuele verschillen tussen de gewenste en gerapporteerde eigenschappen van de modules en -apparaten zijn afgestemd. De IoT Edge-apparaat bijwerken eventuele wijzigingen in een set toegewezen onderliggende IoT-apparaten.

## <a name="restrictions-and-limits"></a>Beperkingen en limieten

De uitgebreide offline mogelijkheden die in dit artikel worden beschreven, zijn beschikbaar in [IOT Edge versie 1.0.7 of hoger](https://github.com/Azure/azure-iotedge/releases). Eerdere versies hebben een subset van de offline functies. IoT Edge bestaande apparaten waarvoor geen uitgebreide mogelijkheden voor offline kunnen niet worden bijgewerkt door het veranderen van de runtimeversie, maar moeten opnieuw worden geconfigureerd met een nieuwe IoT Edge-apparaat-id te krijgen van deze functies.

Alleen apparaten die niet IoT Edge zijn, kunnen als onderliggende apparaten worden toegevoegd.

IoT Edge apparaten en hun toegewezen onderliggende apparaten kunnen tijdens de eerste, eenmalige synchronisatie oneindig offline werken. De opslag van berichten is echter afhankelijk van de TTL-instelling (time to Live) en de beschik bare schijf ruimte voor het opslaan van de berichten.

## <a name="set-up-parent-and-child-devices"></a>Bovenliggende en onderliggende apparaten instellen

Als u wilt dat een IoT Edge apparaat de uitgebreide offline mogelijkheden van de onderliggende IoT-apparaten uitbreidt, moet u twee stappen volt ooien. Declareer eerst de relaties tussen bovenliggende en onderliggende items in de Azure Portal. Vervolgens maakt u een vertrouwens relatie tussen het bovenliggende apparaat en eventuele onderliggende apparaten en configureert u apparaat-naar-Cloud-communicatie om door de bovenliggende site als gateway te gaan. 

### <a name="assign-child-devices"></a>Onderliggende apparaten toewijzen

Onderliggende apparaten kunnen elk niet-IoT Edge apparaat zijn dat is geregistreerd bij dezelfde IoT Hub. Bovenliggende apparaten kunnen meerdere onderliggende apparaten hebben, maar een onderliggend apparaat heeft slechts één bovenliggend object. Er zijn drie opties voor het instellen van onderliggende apparaten op een edge-apparaat: via de Azure Portal, met behulp van de Azure CLI of de IoT Hub Service-SDK. 

De volgende secties bevatten voor beelden van de manier waarop u de bovenliggende/onderliggende relatie in IoT Hub kunt declareren voor bestaande IoT-apparaten. Als u nieuwe apparaat-id's voor uw onderliggende apparaten wilt maken, raadpleegt u [een downstream-apparaat verifiëren bij Azure IOT hub](how-to-authenticate-downstream-device.md) voor meer informatie.

#### <a name="option-1-iot-hub-portal"></a>Optie 1: IoT Hub Portal

U kunt de relatie bovenliggend/onderliggend declareren bij het maken van een nieuw apparaat. Voor bestaande apparaten kunt u de relatie declareren vanaf de pagina Details van apparaat van het bovenliggende IoT Edge apparaat of het onderliggende IoT-apparaat. 

   ![Onderliggende apparaten beheren vanaf de pagina met details van IoT Edge-apparaat](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Optie 2: gebruik het `az` opdracht regel programma

Met de [Azure-opdracht regel interface](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) met [IOT-extensie](https://github.com/azure/azure-iot-cli-extension) (v 0.7.0 of hoger) kunt u bovenliggende onderliggende relaties beheren met de subopdrachten van het [apparaat-id](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) . In het onderstaande voor beeld wordt met behulp van een query alle niet-IoT Edge apparaten in de hub aan onderliggende apparaten van een IoT Edge apparaat toegewezen. 

```shell
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name 
```

U kunt de [query](../iot-hub/iot-hub-devguide-query-language.md) wijzigen om een andere subset van apparaten te selecteren. De opdracht kan enkele seconden duren als u een grote set apparaten opgeeft.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Optie 3: de IoT Hub Service-SDK gebruiken 

Ten slotte kunt u de C#bovenliggende onderliggende relaties programmatisch beheren via een Java-of node. js-IOT hub Service-SDK. Hier volgt een [voor beeld van het toewijzen van een onderliggend apparaat](https://aka.ms/set-child-iot-device-c-sharp) met behulp van de C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Het bovenliggende apparaat instellen als een gateway

U kunt een bovenliggende/onderliggende relatie beschouwen als een transparante gateway, waarbij het onderliggende apparaat een eigen identiteit heeft in IoT Hub, maar via de Cloud communiceert via de bovenliggende site. Voor beveiligde communicatie moet het onderliggende apparaat kunnen controleren of het bovenliggende apparaat afkomstig is van een vertrouwde bron. Anders kunnen derden schadelijke apparaten instellen om ouders te imiteren en communicatie te onderscheppen. 

Een manier om deze vertrouwens relatie te maken, wordt gedetailleerd beschreven in de volgende artikelen:

* [Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
* [Een downstream (onderliggend) apparaat verbinden met een Azure IoT Edge gateway](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>DNS-servers opgeven 

Ter verbetering van de robuustheid wordt u ten zeerste aangeraden de DNS-server adressen op te geven die in uw omgeving worden gebruikt. Als u uw DNS-server voor IoT Edge wilt instellen, raadpleegt u de oplossing voor de [module Edge agent doorlopend het ' lege configuratie bestand ' en worden er geen modules gestart op het apparaat](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) in het artikel over probleem oplossing.

## <a name="optional-offline-settings"></a>Optionele offline-instellingen

Als uw apparaten offline gaan, slaat het IoT Edge bovenliggende apparaat alle apparaat-naar-Cloud-berichten op totdat de verbinding opnieuw tot stand is gebracht. De module IoT Edge hub beheert de opslag en het door sturen van offline berichten. Voor apparaten die gedurende lange tijd offline kunnen gaan, optimaliseert u de prestaties door twee IoT Edge hub-instellingen te configureren. 

Verg root de time-to-Live-instelling, zodat de IoT Edge hub berichten lang genoeg blijft voordat het apparaat opnieuw verbinding maakt. Voeg vervolgens extra schijf ruimte toe voor bericht opslag. 

### <a name="time-to-live"></a>Time To Live

Time to live-instelling is de hoeveelheid tijd (in seconden) die een bericht wachten kunt moet worden geleverd voordat deze verloopt. De standaardwaarde is 7200 seconden (twee uur). De maximum waarde wordt alleen beperkt door de maximum waarde van een variabele geheel getal, ongeveer 2.000.000.000. 

Deze instelling is een gewenste eigenschap van de IoT Edge hub, die wordt opgeslagen in de module dubbele. U kunt deze configureren in de Azure Portal of rechtstreeks in het implementatie manifest. 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>Host Storage voor systeem modules

Berichten en informatie over de status van de module worden standaard opgeslagen in het lokale container systeem van de IoT Edge hub. Voor een betere betrouw baarheid, met name wanneer u offline werkt, kunt u ook opslag op de host toewijzen IoT Edge apparaat. Zie [modules toegang bieden tot de lokale opslag van een apparaat](how-to-access-host-storage-from-module.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van een transparante gateway voor de verbindingen van een bovenliggend/onderliggend apparaat: 

* [Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
* [Een downstreamapparaat verifiëren voor Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Een downstreamapparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
