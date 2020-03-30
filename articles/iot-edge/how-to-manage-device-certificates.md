---
title: Apparaatcertificaten beheren - Azure IoT Edge | Microsoft Documenten
description: Maak testcertificaten, installeer en beheer ze op een Azure IoT Edge-apparaat om zich voor te bereiden op productie-implementatie.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c18c3d560adb3c3cae54bda808ee5842c260fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79539206"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Certificaten beheren op een IoT Edge-apparaat

Alle IoT Edge-apparaten gebruiken certificaten om veilige verbindingen te maken tussen de runtime en alle modules die op het apparaat worden uitgevoerd. IoT Edge-apparaten die functioneren als gateways gebruiken dezelfde certificaten om ook verbinding te maken met hun downstream-apparaten.

## <a name="install-production-certificates"></a>Productiecertificaten installeren

Wanneer u IoT Edge voor het eerst installeert en uw apparaat indient, is het apparaat ingesteld met tijdelijke certificaten, zodat u de service testen.
Deze tijdelijke certificaten verlopen binnen 90 dagen of kunnen opnieuw worden ingesteld door uw machine opnieuw op te starten.
Zodra u klaar bent om uw apparaten naar een productiescenario te verplaatsen of u een gatewayscenario wilt maken, moet u uw eigen certificaten verstrekken.
In dit artikel worden de stappen getoond om certificaten op uw IoT Edge-apparaten te installeren.

Zie [Begrijpen hoe Azure IoT Edge certificaten gebruikt](iot-edge-certs.md)voor meer informatie over de verschillende typen certificaten en hun rollen in een IoT Edge-scenario.

>[!NOTE]
>De term "root CA" die in dit artikel wordt gebruikt, verwijst naar het hoogste overheidscertificaat van de certificaatketen voor uw IoT-oplossing. U hoeft de certificaatbasis van een gesyndiceerde certificaatautoriteit of de hoofdmap van de certificeringsinstantie van uw organisatie niet te gebruiken. In veel gevallen is het eigenlijk een tussentijds ca-openbaar certificaat.

### <a name="prerequisites"></a>Vereisten

* Een IoT Edge-apparaat dat wordt uitgevoerd op [Windows](how-to-install-iot-edge-windows.md) of [Linux.](how-to-install-iot-edge-linux.md)
* Laat een CA-certificaat (root certificate authority) certificeren, zelf ondertekend of gekocht bij een vertrouwde commerciële certificaatautoriteit zoals Baltimore, Verisign, DigiCert of GlobalSign.

Als u nog geen basiscertificaatautoriteit hebt, maar IoT Edge-functies wilt uitproberen waarvoor productiecertificaten nodig zijn (zoals gatewayscenario's), u [democertificaten maken om de functies van iot edge-apparaten te testen.](how-to-create-test-certificates.md)

### <a name="create-production-certificates"></a>Productiecertificaten maken

U moet uw eigen certificaatautoriteit gebruiken om de volgende bestanden te maken:

* Basis-CA
* Apparaat CA-certificaat
* De privésleutel van apparaat CA

In dit artikel is wat we de *hoofd-CA* noemen niet de hoogste certificaatinstantie voor een organisatie. Het is de hoogste certificaatautoriteit voor het IoT Edge-scenario, dat de IoT Edge-hubmodule, gebruikersmodules en downstream-apparaten gebruiken om vertrouwen tussen elkaar te vestigen.

Als u een voorbeeld van deze certificaten wilt zien, controleert u de scripts waarmee democertificaten worden gemaakt in [CA-certificaten beheren voor voorbeelden en zelfstudies.](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)

### <a name="install-certificates-on-the-device"></a>Certificaten installeren op het apparaat

Installeer uw certificaatketen op het IoT Edge-apparaat en configureer de IoT Edge-runtime om naar de nieuwe certificaten te verwijzen.

Als u bijvoorbeeld de voorbeeldscripts hebt gebruikt om democertificaten te [maken,](how-to-create-test-certificates.md)kopieert u de volgende bestanden naar uw IoT-Edge-apparaat:

* Apparaat CA-certificaat:`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* De privésleutel van apparaat CA:`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Wortel CA:`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Kopieer de drie certificaat- en sleutelbestanden naar uw IoT Edge-apparaat.

   U een service zoals [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) of een functie zoals Secure copy [protocol](https://www.ssh.com/ssh/scp/) gebruiken om de certificaatbestanden te verplaatsen.  Als u de certificaten hebt gegenereerd op het IoT Edge-apparaat zelf, u deze stap overslaan en het pad naar de werkmap gebruiken.

1. Open het IoT Edge security daemon config-bestand.

   * Windows:`C:\ProgramData\iotedge\config.yaml`
   * Linux:`/etc/iotedge/config.yaml`

1. Stel de **certificaateigenschappen** in het config.yaml-bestand in op het volledige pad naar het certificaat en de belangrijkste bestanden op het IoT Edge-apparaat. Verwijder `#` het teken voordat de certificaateigenschappen de vier regels ongedaan maken. Zorg ervoor dat de **certificaten:** lijn heeft geen voorafgaande witruimte en dat geneste items worden ingesprongen door twee spaties. Bijvoorbeeld:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "c:\\<path>\\device-ca.cert.pem"
        device_ca_pk: "c:\\<path>\\device-ca.key.pem"
        trusted_ca_certs: "c:\\<path>\\root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "<path>/device-ca.cert.pem"
        device_ca_pk: "<path>/device-ca.key.pem"
        trusted_ca_certs: "<path>/root-ca.root.ca.cert.pem"
      ```

1. Zorg er op Linux-apparaten voor dat de **gebruiker leesmachtigingen** heeft voor de map met de certificaten.

1. Als u al eerder andere certificaten voor IoT Edge op het apparaat hebt gebruikt, verwijdert u de bestanden in de volgende twee mappen voordat u IoT Edge start of opnieuw start:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` en`C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` en`/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>Certificaatlevensduur aanpassen

IoT Edge genereert in verschillende gevallen automatisch certificaten op het apparaat, waaronder:

* Als u geen eigen productiecertificaten verstrekt bij het installeren en inrichten van IoT Edge, genereert de IoT Edge-beveiligingsmanager automatisch een **CA-certificaat voor apparaten.** Dit zelfondertekende certificaat is alleen bedoeld voor ontwikkelings- en testscenario's, niet voor productie. Dit certificaat verloopt na 90 dagen.
* De IoT Edge-beveiligingsmanager genereert ook een **CA-certificaat voor werkbelasting** dat is ondertekend door het CA-certificaat van het apparaat

Zie [Begrijpen hoe Azure IoT Edge certificaten gebruikt](iot-edge-certs.md)voor meer informatie over de functie van de verschillende certificaten op een IoT Edge-apparaat.

Voor deze twee automatisch gegenereerde certificaten u de **auto_generated_ca_lifetime_days** vlag instellen in config.yaml om het aantal dagen voor de levensduur van de certificaten te configureren.

>[!NOTE]
>Er is een derde automatisch gegenereerd certificaat dat de IoT Edge-beveiligingsbeheer maakt, het **IoT Edge-hubservercertificaat.** Dit certificaat heeft altijd een 90-dagen, maar wordt automatisch verlengd voordat het verloopt. De **waarde auto_generated_ca_lifetime_days** heeft geen invloed op dit certificaat.

Als u de vervaldatum van het certificaat wilt configureren op iets anders dan de standaard90-dagen, voegt u de waarde in dagen toe aan de **sectie certificaten** van het config.yaml-bestand.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

Als u uw eigen CA-certificaten voor apparaat hebt opgegeven, is deze waarde nog steeds van toepassing op het CA-certificaat voor werkbelasting, mits de door u ingestelde levensduur korter is dan de levensduur van het CA-certificaat van het apparaat.

Voer de volgende stappen uit nadat u de vlag in het bestand config.yaml hebt opgegeven:

1. Verwijder de inhoud `hsm` van de map.

   Windows: `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux:`/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. Start de IoT Edge-service opnieuw.

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. Bevestig de instelling voor de levensduur.

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   Controleer de uitvoer van de **productiegereedheid: certificaten** controleren, waarbij het aantal dagen wordt weergegeven totdat de automatisch gegenereerde CA-certificaten van het apparaat verlopen.

## <a name="next-steps"></a>Volgende stappen

Het installeren van certificaten op een IoT Edge-apparaat is een noodzakelijke stap voordat u uw oplossing in productie implementeert. Meer informatie over hoe u u voorbereidt op het [implementeren van uw IoT Edge-oplossing in productie.](production-checklist.md)
