---
title: CreateOptions schrijven voor modules - Azure IoT Edge | Microsoft Documenten
description: CreateOptions gebruiken in het implementatiemanifest om modules te configureren tijdens runtime
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550342"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Opties voor het maken van containers configureren voor IoT Edge-modules

Met de parameter **createOptions** in het implementatiemanifest u de modulecontainers configureren tijdens runtime. Met deze parameter wordt uw controle over de modules uitgebreid en kunnen taken worden toegestaan, zoals het toestaan of beperken van de toegang van de module tot de bronnen van het hostapparaat of het configureren van netwerken.

IoT Edge-modules worden geïmplementeerd als Docker-compatibele containers op uw IoT Edge-apparaat. Docker biedt veel opties voor het maken van containers, en die opties zijn ook van toepassing op IoT Edge-modules. Zie [Docker container maak opties voor](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)meer informatie .

## <a name="format-create-options"></a>Opties voor het maken van maken opmaken

Het IoT Edge-implementatiemanifest accepteert create-opties die zijn opgemaakt als JSON. Neem bijvoorbeeld de opties voor het maken die automatisch worden opgenomen voor elke edgeHub-module:

```json
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
```

In dit edgeHub-voorbeeld wordt de parameter **HostConfig.PortBindings** gebruikt om blootgestelde poorten op de container in kaart te brengen naar een poort op het hostapparaat.

Als u de Azure IoT Tools-extensies voor Visual Studio of Visual Studio Code gebruikt, u de opties voor het maken in JSON-indeling schrijven in het bestand **deployment.template.json.** Wanneer u vervolgens de extensie gebruikt om de IoT Edge-oplossing te bouwen of het implementatiemanifest te genereren, zal deze de JSON voor u aaneenstellen in de indeling die de Runtime van IoT Edge verwacht. Bijvoorbeeld:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Een tip voor het schrijven `docker inspect` van opties maken is het gebruik van de opdracht. Als onderdeel van uw ontwikkelingsproces voert `docker run <container name>`u de module lokaal uit met behulp van . Zodra u de module werkt zoals u `docker inspect <container name>`het wilt, uit te voeren. Met deze opdracht worden de moduledetails in JSON-indeling uitgevoerd. Zoek de parameters die u hebt geconfigureerd en kopieer de JSON. Bijvoorbeeld:

[![Resultaten van docker](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png) inspecteren edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Algemene scenario's

Opties voor het maken van containers maken veel scenario's mogelijk, maar hier zijn enkele die het vaakst naar boven komen bij het bouwen van IoT Edge-oplossingen:

* [Modules toegang geven tot hostopslag](how-to-access-host-storage-from-module.md)
* [Hostpoort toewijzen aan modulepoort](#map-host-port-to-module-port)
* [Modulegeheugen en CPU-gebruik beperken](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Hostpoort toewijzen aan modulepoort

Als uw module moet communiceren met een service buiten de IoT Edge-oplossing en hiervoor geen berichtroutering gebruikt, moet u een hostpoort toewijzen aan een modulepoort.

>[!TIP]
>Deze poorttoewijzing is niet vereist voor module-naar-module communicatie op hetzelfde apparaat. Als module A een API moet opvragen die op module B wordt gehost, kan deze dit doen zonder dat er een poorttoewijzing is. Module B moet bijvoorbeeld een poort in zijn `EXPOSE 8080`dockerbestand blootleggen: . Vervolgens kan module A de API opvragen met `http://ModuleB:8080/api`de naam van module B, bijvoorbeeld: .

Zorg er eerst voor dat een poort in de module wordt blootgesteld om naar verbindingen te luisteren. U dit doen met behulp van een [EXPOSE-instructie](https://docs.docker.com/engine/reference/builder/#expose) in het dockerbestand. Bijvoorbeeld `EXPOSE 8080`. De standaardinstructies blootstellen aan het TCP-protocol als deze niet zijn opgegeven, of u UDP opgeven.

Gebruik vervolgens de instelling **PortBindings** in de **hostconfig-groep** van de [Docker-container en maak opties](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) om de blootgestelde poort in de module in kaart te brengen naar een poort op het hostapparaat. Als u bijvoorbeeld poort 8080 in de module hebt blootgesteld en deze wilt toewijzen aan poort 80 van het hostapparaat, zien de opties voor het maken in het bestand template.json er als volgt uit:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Zodra u is gestouwd voor het implementatiemanifest, ziet dezelfde configuratie er als volgt uit:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Modulegeheugen en CPU-gebruik beperken

U aangeven hoeveel van de hostbronnen een module kan gebruiken. Dit besturingselement is handig om ervoor te zorgen dat één module niet te veel geheugen of CPU-gebruik kan verbruiken en voorkomt dat andere processen op het apparaat worden uitgevoerd. U deze instellingen beheren met [dockercontaineropties](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) in de **hostconfig-groep,** waaronder:

* **Geheugen:** geheugenlimiet in bytes. Bijvoorbeeld 268435456 bytes = 256 MB.
* **MemorySwap**: Totale geheugenlimiet (geheugen + swap). Bijvoorbeeld 536870912 bytes = 512 MB
* **CpuPeriod:** De lengte van een CPU-periode in microseconden. De standaardwaarde is 100000, zodat bijvoorbeeld een waarde van 25000 een container beperkt tot 25% van de CPU-resources.

In de indeling template.json zien deze waarden er als volgt uit:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Zodra deze waarden zijn getouwerd voor het definitieve implementatiemanifest, zien ze er als volgt uit:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende IoT Edge-voorbeelden voor meer voorbeelden van opties voor het maken in actie:

* [Aangepaste Vision en Azure IoT Edge op een Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Voorbeeld van Azure IoT Edge-blobopslag](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
