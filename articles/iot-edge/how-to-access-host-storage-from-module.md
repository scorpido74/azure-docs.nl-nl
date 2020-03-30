---
title: Lokale opslag van IoT Edge-apparaten gebruiken vanuit een module - Azure IoT Edge | Microsoft Documenten
description: Gebruik omgevingsvariabelen en maak opties om moduletoegang tot lokale opslag van IoT Edge-apparaten in te schakelen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434523"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Modules toegang geven tot de lokale opslag van een apparaat

Naast het opslaan van gegevens met Azure-opslagservices of in de containeropslag van uw apparaat, u ook opslag op het host IoT Edge-apparaat zelf besteden aan een betere betrouwbaarheid, vooral wanneer u offline werkt.

## <a name="link-module-storage-to-device-storage"></a>Moduleopslag koppelen aan apparaatopslag

Als u een koppeling wilt inschakelen van moduleopslag naar de opslag op het hostsysteem, maakt u een omgevingsvariabele voor uw module die verwijst naar een opslagmap in de container. Gebruik vervolgens de opties voor het maken om die opslagmap te binden aan een map op de hostmachine.

Als u bijvoorbeeld de IoT Edge-hub wilt inschakelen om berichten op te slaan in de lokale opslag van uw apparaat en ze later op te halen, u de omgevingsvariabelen en de opties voor het maken in de Azure-portal configureren in de sectie **Runtime-instellingen.**

1. Voeg voor zowel de IoT Edge-hub als de IoT Edge-agent een omgevingsvariabele toe die **storageFolder** wordt genoemd en die verwijst naar een map in de module.
1. Voeg voor zowel de IoT Edge-hub als de IoT Edge-agent bindingen toe om een lokale map op de hostmachine aan te sluiten op een map in de module. Bijvoorbeeld:

   ![Maakopties en omgevingsvariabelen toevoegen voor lokale opslag](./media/how-to-access-host-storage-from-module/offline-storage.png)

U de lokale opslag ook rechtstreeks configureren in het implementatiemanifest. Bijvoorbeeld:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Vervang `<HostStoragePath>` `<ModuleStoragePath>` en met uw host- en moduleopslagpad; beide waarden moeten een absoluut pad zijn.

Bijvoorbeeld, op een Linux-systeem, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` betekent dat de directory / etc / **iotedge / opslag** op uw host systeem is toegewezen aan de directory / **iotedge / opslag /** in de container. Op een Windows-systeem, `"Binds":["C:\\temp:C:\\contemp"]` als een ander voorbeeld, betekent de directory **\\C: temp** op uw host systeem is toegewezen aan de directory **C:\\contemp** in de container.

Zorg er bovendien voor dat op Linux-apparaten het gebruikersprofiel voor uw module de vereiste lees-, schrijf- en uitvoermachtigingen heeft voor de map van het hostsysteem. Als u terugkeert naar het eerdere voorbeeld van het inschakelen van iot edge-hub om berichten op te slaan in de lokale opslag van uw apparaat, moet u machtigingen verlenen aan het gebruikersprofiel, UID 1000. (De IoT Edge-agent werkt als root, dus het heeft geen extra machtigingen nodig.) Er zijn verschillende manieren om directorymachtigingen op `chown` Linux-systemen te beheren, waaronder het gebruik om de eigenaar van de directory te wijzigen en vervolgens `chmod` de machtigingen te wijzigen, zoals:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Meer informatie over maakopties vindt u van [dockerdocumenten.](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)

## <a name="next-steps"></a>Volgende stappen

Zie [Store-gegevens aan de rand met Azure Blob Storage op IoT Edge voor](how-to-store-data-blob.md)een extra voorbeeld van toegang tot hostopslag vanuit een module.
