---
title: Certificaten installeren op apparaat-Azure IoT Edge | Microsoft Docs
description: Maak test certificaten en leer hoe u deze kunt installeren op een Azure IoT Edge apparaat om de productie-implementatie voor te bereiden.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cf073572cd5b371ec484c99f14cbefb4cba75ce7
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509900"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>Productie certificaten op een IoT Edge apparaat installeren

Alle IoT Edge apparaten gebruiken certificaten voor het maken van beveiligde verbindingen tussen de runtime en alle modules die op het apparaat worden uitgevoerd.
IoT Edge apparaten als gateways functioneren, gebruiken deze zelfde certificaten ook om verbinding te maken met hun downstream-apparaten.

Wanneer u IoT Edge voor het eerst installeert en uw apparaat inricht, wordt het apparaat ingesteld met tijdelijke certificaten, zodat u de service kunt testen.
Deze tijdelijke certificaten verlopen over 90 dagen of kunnen opnieuw worden ingesteld door de computer opnieuw op te starten.
Wanneer u klaar bent om uw apparaten te verplaatsen naar een productie scenario of als u een gateway scenario wilt maken, moet u uw eigen certificaten opgeven.
In dit artikel worden de stappen beschreven voor het installeren van certificaten op uw IoT Edge-apparaten.

Zie [begrijpen hoe Azure IOT Edge certificaten gebruikt](iot-edge-certs.md)voor meer informatie over de verschillende typen certificaten en hun rollen in een IOT Edge scenario.

>[!NOTE]
>De term ' basis-CA ' die in dit artikel wordt gebruikt, verwijst naar het open bare certificaat van de bovenste instantie van de certificaat keten voor uw IoT-oplossing. U hoeft niet de hoofdmap van het certificaat van een extern gepubliceerde certificerings instantie of de basis van de certificerings instantie van uw organisatie te gebruiken. In veel gevallen is het eigenlijk een openbaar certificaat van een tussenliggend CA.

## <a name="prerequisites"></a>Vereisten

* Een IoT Edge apparaat, dat wordt uitgevoerd op [Windows](how-to-install-iot-edge-windows.md) of [Linux](how-to-install-iot-edge-linux.md).
* Beschikken over een basis certificaat voor een certificerings instantie (CA), ofwel zelf ondertekend of gekocht bij een vertrouwde commerciële certificerings instantie, zoals Baltimore, VeriSign, DigiCert of GlobalSign.

Als u nog geen basis certificerings instantie hebt, maar IoT Edge functies wilt uitproberen waarvoor productie certificaten zijn vereist (zoals de gateway scenario's), kunt u [demo certificaten maken om IOT Edge-apparaatfuncties te testen](how-to-create-test-certificates.md).

## <a name="create-production-certificates"></a>Productie certificaten maken

U moet uw eigen certificerings instantie gebruiken om de volgende bestanden te maken:

* Basis-CA
* Device CA-certificaat
* Persoonlijke sleutel van de apparaat-CA

In dit artikel wordt verwezen naar de *basis-CA* , maar niet de hoogste certificerings instantie voor een organisatie. Het is de bovenste certificerings instantie voor het IoT Edge scenario, die door de IoT Edge hub-module, gebruikers modules en downstream-apparaten wordt gebruikt om een vertrouwens relatie tussen elkaar te leggen.

Als u een voor beeld van deze certificaten wilt zien, bekijkt u de scripts die demo certificaten maken bij het [beheren van test-CA-certificaten voor voor beelden en zelf studies](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

## <a name="install-certificates-on-the-device"></a>Certificaten op het apparaat installeren

Installeer uw certificaat keten op het IoT Edge apparaat en configureer de IoT Edge-runtime om te verwijzen naar de nieuwe certificaten.

Als u bijvoorbeeld de voorbeeld scripts hebt gebruikt om [demo certificaten te maken](how-to-create-test-certificates.md), zijn de drie bestanden die u naar uw IOT edge-apparaat moet kopiëren, het volgende:

* CA-certificaat van apparaat: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Persoonlijke sleutel van de apparaat-CA: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Basis-CA: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Kopieer de drie certificaat-en sleutel bestanden naar uw IoT Edge-apparaat.

   U kunt een service zoals [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) of een functie zoals [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) gebruiken om de certificaat bestanden te verplaatsen.  Als u de certificaten op het IoT Edge apparaat zelf hebt gegenereerd, kunt u deze stap overs Laan en het pad naar de werkmap gebruiken.

2. Open het bestand met IoT Edge security daemon-configuratie.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Stel de **certificaat** eigenschappen in het bestand config. yaml in op de BESTANDS-URI van het certificaat en de sleutel bestanden op het IOT edge apparaat. Verwijder het `#` teken vóór de eigenschappen van het certificaat om de vier regels op te heffen. Zorg ervoor dat de regel **certificaten:** geen voor gaande witruimte heeft en dat geneste items met twee spaties worden inge sprongen. Bijvoorbeeld:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///c:/path/device-ca.cert.pem"
        device_ca_pk: "file:///c:/path/device-ca.key.pem"
        trusted_ca_certs: "file:///c:/path/root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///path/device-ca.cert.pem"
        device_ca_pk: "file:///path/device-ca.key.pem"
        trusted_ca_certs: "file:///path/root-ca.root.ca.cert.pem"
      ```

4. Controleer op Linux-apparaten of de gebruiker **iotedge** Lees machtigingen heeft voor de directory die de certificaten bevat.

## <a name="next-steps"></a>Volgende stappen

Het installeren van certificaten op een IoT Edge apparaat is een nood zakelijke stap voordat u uw oplossing in de productie omgeving implementeert. Meer informatie over het [voorbereiden van de implementatie van uw IOT EDGE oplossing in productie](production-checklist.md).
