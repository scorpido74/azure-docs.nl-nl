---
title: Apparaten offline bedienen - Azure IoT Edge | Microsoft Documenten
description: Begrijp hoe IoT Edge-apparaten en -modules langere tijd zonder internetverbinding kunnen werken en hoe IoT Edge reguliere IoT-apparaten ook offline kan laten werken.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 55512491121aee28404ab5f85b4223c67a2f0e1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80236066"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Uitgebreide offlinemogelijkheden voor IoT Edge-apparaten, -modules en onderliggende apparaten begrijpen

Azure IoT Edge ondersteunt uitgebreide offline bewerkingen op uw IoT Edge-apparaten en maakt ook offlinebewerkingen op niet-IoT Edge-onderliggende apparaten mogelijk. Zolang een IoT Edge-apparaat één mogelijkheid heeft gehad om verbinding te maken met IoT Hub, kunnen dat apparaat en alle onderliggende apparaten blijven functioneren met tussenpozen of zonder internetverbinding.

## <a name="how-it-works"></a>Hoe werkt het?

Wanneer een IoT Edge-apparaat offline wordt ingeschakeld, neemt de IoT Edge-hub drie rollen aan. Eerst slaat het alle berichten op die stroomopwaarts zouden gaan en ze opslaan totdat het apparaat opnieuw verbinding maakt. Ten tweede werkt het namens IoT Hub om modules en onderliggende apparaten te verifiëren, zodat ze kunnen blijven werken. Ten derde maakt het communicatie mogelijk tussen onderliggende apparaten die normaal gesproken via IoT Hub zouden gaan.

In het volgende voorbeeld ziet u hoe een IoT Edge-scenario in de offlinemodus werkt:

1. **Apparaten configureren**

   IoT Edge-apparaten hebben automatisch offlinemogelijkheden ingeschakeld. Als u deze mogelijkheid wilt uitbreiden naar andere IoT-apparaten, moet u een bovenliggende relatie tussen de apparaten in IoT Hub declareren. Vervolgens configureert u de onderliggende apparaten om hun toegewezen bovenliggende apparaat te vertrouwen en de communicatie tussen apparaat en cloud door de bovenliggende als gateway te leiden.

2. **Synchroniseren met IoT-hub**

   Ten minste eenmaal na de installatie van de IoT Edge-runtime moet het IoT Edge-apparaat online zijn om te synchroniseren met IoT Hub. In deze synchronisatie krijgt het IoT Edge-apparaat details over onderliggende apparaten die eraan zijn toegewezen. Het IoT Edge-apparaat werkt ook veilig de lokale cache bij om offline bewerkingen in te schakelen en haalt instellingen op voor lokale opslag van telemetrieberichten.

3. **Offline gaan**

   Hoewel de verbinding met IoT Hub is verbroken, kunnen het IoT Edge-apparaat, de geïmplementeerde modules en alle Mobiele IoT-apparaten voor onbepaalde tijd werken. Modules en onderliggende apparaten kunnen worden gestart en opnieuw worden opgestart door offline te authenticeren met de IoT Edge-hub. Telemetrie die stroomopwaarts aan IoT Hub is gebonden, wordt lokaal opgeslagen. De communicatie tussen modules of tussen onderliggende IoT-apparaten wordt onderhouden via directe methoden of berichten.

4. **Opnieuw verbinding maken en opnieuw synchroniseren met IoT Hub**

   Zodra de verbinding met iot-hub is hersteld, wordt het IoT Edge-apparaat opnieuw gesynchroniseerd. Lokaal opgeslagen berichten worden meteen naar de IoT Hub geleverd, maar zijn afhankelijk van de snelheid van de verbinding, iot-hublatentie en gerelateerde factoren. Ze worden geleverd in dezelfde volgorde waarin ze werden opgeslagen.

   Eventuele verschillen tussen de gewenste en gerapporteerde eigenschappen van de modules en apparaten worden verzoend. Het IoT Edge-apparaat werkt eventuele wijzigingen in de set toegewezen onderliggende IoT-apparaten bij.

## <a name="restrictions-and-limits"></a>Beperkingen en beperkingen

De uitgebreide offline mogelijkheden die in dit artikel worden beschreven, zijn beschikbaar in [IoT Edge-versie 1.0.7 of hoger.](https://github.com/Azure/azure-iotedge/releases) Eerdere versies hebben een subset van offline functies. Bestaande IoT Edge-apparaten die geen uitgebreide offlinemogelijkheden hebben, kunnen niet worden geüpgraded door de runtime-versie te wijzigen, maar moeten opnieuw worden geconfigureerd met een nieuwe IoT Edge-apparaatidentiteit om deze functies te verkrijgen.

Alleen niet-IoT Edge-apparaten kunnen worden toegevoegd als onderliggende apparaten.

IoT Edge-apparaten en hun toegewezen onderliggende apparaten kunnen na de eerste, eenmalige synchronisatie voor onbepaalde tijd offline functioneren. De opslag van berichten is echter afhankelijk van de tijd om te leven (TTL) en de beschikbare schijfruimte voor het opslaan van de berichten.

## <a name="set-up-parent-and-child-devices"></a>Bovenliggende en onderliggende apparaten instellen

Als u een IoT Edge-apparaat wilt uitbreiden naar de uitgebreide offline mogelijkheden voor kinderen, moet u twee stappen uitvoeren. Declareer eerst de bovenliggende-onderliggende relaties in de Azure-portal. Maak ten tweede een vertrouwensrelatie tussen het bovenliggende apparaat en onderliggende apparaten en configureer vervolgens apparaat-naar-cloudcommunicatie om door de bovenliggende als gateway te gaan.

### <a name="assign-child-devices"></a>Onderliggende apparaten toewijzen

Onderliggende apparaten kunnen elk niet-IoT Edge-apparaat zijn dat is geregistreerd op dezelfde IoT-hub. Bovenliggende apparaten kunnen meerdere onderliggende apparaten hebben, maar een onderliggend apparaat heeft slechts één ouder. Er zijn drie opties om onderliggende apparaten in te stellen op een edge-apparaat: via de Azure-portal, met behulp van de Azure CLI of via de IoT Hub-service SDK.

In de volgende secties vindt u voorbeelden van hoe u de bovenliggende/onderliggende relatie in IoT Hub declareren voor bestaande IoT-apparaten. Zie [Een downstream-apparaat verifiëren naar Azure IoT Hub](how-to-authenticate-downstream-device.md) voor meer informatie als u nieuwe apparaatidentiteiten voor uw onderliggende apparaten maakt.

#### <a name="option-1-iot-hub-portal"></a>Optie 1: IoT Hub Portal

U de bovenliggende-onderliggende relatie declareren bij het maken van een nieuw apparaat. Of voor bestaande apparaten u de relatie declareren vanaf de pagina apparaatgegevens van het bovenliggende IoT Edge-apparaat of het onderliggende IoT-apparaat.

   ![Onderliggende apparaten beheren vanaf de pagina Met de gegevens van het IoT Edge-apparaat](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>Optie 2: `az` Het gereedschap opdrachtregel gebruiken

Met behulp van de [Azure-opdrachtregelinterface](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) met [IoT-extensie](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 of nieuwer) u bovenliggende onderliggende relaties met de subopdrachten [voor apparaatidentiteit](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) beheren. In het onderstaande voorbeeld wordt een query gebruikt om alle niet-IoT Edge-apparaten in de hub toe te wijzen als onderliggende apparaten van een IoT Edge-apparaat.

```azurecli
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

U de [query](../iot-hub/iot-hub-devguide-query-language.md) wijzigen om een andere subset van apparaten te selecteren. De opdracht kan enkele seconden duren als u een grote set apparaten opgeeft.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Optie 3: IoT Hub Service SDK gebruiken

Ten slotte u bovenliggende onderliggende relaties programmatisch beheren met behulp van C#, Java of Node.js IoT Hub Service SDK. Hier is een [voorbeeld van het toewijzen van een onderliggend apparaat](https://aka.ms/set-child-iot-device-c-sharp) met behulp van de C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Het bovenliggende apparaat instellen als gateway

U een bovenliggende/onderliggende relatie zien als een transparante gateway, waarbij het onderliggende apparaat een eigen identiteit heeft in IoT Hub, maar via de cloud communiceert via de bovenliggende. Voor veilige communicatie moet het onderliggende apparaat kunnen controleren of het bovenliggende apparaat afkomstig is van een vertrouwde bron. Anders kunnen derden schadelijke apparaten instellen om zich voor te doen als ouders en communicatie te onderscheppen.

Een manier om deze vertrouwensrelatie te maken wordt in detail beschreven in de volgende artikelen:

* [Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
* [Een downstreamapparaat (onderliggend) verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>DNS-servers opgeven

Om de robuustheid te verbeteren, is het ten zeerste aan te raden om de DNS-serveradressen op te geven die in uw omgeving worden gebruikt. Als u uw DNS-server voor IoT Edge wilt instellen, raadpleegt u de oplossing voor [de Edge Agent-module en rapporteert u voortdurend 'leeg config-bestand' en starten er geen modules op het apparaat](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) in het probleemoplossingsartikel.

## <a name="optional-offline-settings"></a>Optionele offlineinstellingen

Als uw apparaten offline gaan, slaat het IE Edge-bovenliggende apparaat alle device-to-cloud-berichten op totdat de verbinding is hersteld. De IoT Edge hub module beheert de opslag en doorsturen van offline berichten. Voor apparaten die langere tijd offline kunnen gaan, optimaliseert u de prestaties door twee IoT Edge-hub-instellingen te configureren.

Verhoog eerst de tijd tot live-instelling, zodat de IoT Edge-hub berichten lang genoeg bewaart zodat uw apparaat opnieuw verbinding kan maken. Voeg vervolgens extra schijfruimte toe voor berichtopslag.

### <a name="time-to-live"></a>Time To Live

De tijd tot live-instelling is de hoeveelheid tijd (in seconden) die een bericht kan wachten om te worden afgeleverd voordat het verloopt. De standaardinstelling is 7200 seconden (twee uur). De maximale waarde wordt alleen beperkt door de maximale waarde van een gehele variabele, die ongeveer 2 miljard bedraagt.

Deze instelling is een gewenste eigenschap van de IoT Edge-hub, die is opgeslagen in de moduletwin. U het configureren in de Azure-portal of rechtstreeks in het implementatiemanifest.

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

### <a name="host-storage-for-system-modules"></a>Hostopslag voor systeemmodules

Berichten en informatie over de status van de module worden standaard opgeslagen in het lokale containerbestandssysteem van de IoT Edge-hub. Voor een betere betrouwbaarheid, vooral wanneer u offline werkt, u ook opslag op het host IoT Edge-apparaat gebruiken. Zie [Modules toegang geven tot de lokale opslag van een apparaat voor](how-to-access-host-storage-from-module.md) meer informatie

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van een transparante gateway voor uw bovenliggende/onderliggende apparaatverbindingen:

* [Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
* [Een downstream-apparaat verifiëren voor Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Een downstreamapparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
