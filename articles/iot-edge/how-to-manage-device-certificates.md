---
title: Apparaat certificaten beheren-Azure IoT Edge | Microsoft Docs
description: Test certificaten maken, installeren en beheren op een Azure IoT Edge apparaat om de productie-implementatie voor te bereiden.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b13944e30c339357997fbc5f0919e5eb8485a0a9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84308775"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Certificaten op een IoT Edge apparaat beheren

Alle IoT Edge apparaten gebruiken certificaten voor het maken van beveiligde verbindingen tussen de runtime en alle modules die op het apparaat worden uitgevoerd. IoT Edge apparaten als gateways functioneren, gebruiken deze zelfde certificaten ook om verbinding te maken met hun downstream-apparaten.

## <a name="install-production-certificates"></a>Productiecertificaten installeren

Wanneer u IoT Edge voor het eerst installeert en uw apparaat inricht, wordt het apparaat ingesteld met tijdelijke certificaten, zodat u de service kunt testen.
Deze tijdelijke certificaten verlopen over 90 dagen of kunnen opnieuw worden ingesteld door de computer opnieuw op te starten.
Wanneer u bent overstappen op een productie scenario of als u een gateway apparaat wilt maken, moet u uw eigen certificaten opgeven.
In dit artikel worden de stappen beschreven voor het installeren van certificaten op uw IoT Edge-apparaten.

Zie [begrijpen hoe Azure IOT Edge certificaten gebruikt](iot-edge-certs.md)voor meer informatie over de verschillende typen certificaten en hun rollen.

>[!NOTE]
>De term ' basis-CA ' die in dit artikel wordt gebruikt, verwijst naar het open bare certificaat van de bovenste instantie van de certificaat keten voor uw IoT-oplossing. U hoeft niet de hoofdmap van het certificaat van een extern gepubliceerde certificerings instantie of de basis van de certificerings instantie van uw organisatie te gebruiken. In veel gevallen is het eigenlijk een openbaar certificaat van een tussenliggend CA.

### <a name="prerequisites"></a>Vereisten

* Een IoT Edge apparaat, dat wordt uitgevoerd op [Windows](how-to-install-iot-edge-windows.md) of [Linux](how-to-install-iot-edge-linux.md).
* Beschikken over een basis certificaat voor een certificerings instantie (CA), ofwel zelf ondertekend of gekocht bij een vertrouwde commerciële certificerings instantie, zoals Baltimore, VeriSign, DigiCert of GlobalSign.

Als u nog geen basis certificerings instantie hebt, maar IoT Edge functies wilt uitproberen waarvoor productie certificaten zijn vereist (zoals de gateway scenario's), kunt u [demo certificaten maken om IOT Edge-apparaatfuncties te testen](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Productie certificaten maken

U moet uw eigen certificerings instantie gebruiken om de volgende bestanden te maken:

* Basis-CA
* CA-certificaat van apparaat
* Persoonlijke sleutel van de apparaat-CA

In dit artikel wordt verwezen naar de *basis-CA* , maar niet de hoogste certificerings instantie voor een organisatie. Het is de bovenste certificerings instantie voor het IoT Edge scenario, die door de IoT Edge hub-module, gebruikers modules en downstream-apparaten wordt gebruikt om een vertrouwens relatie tussen elkaar te leggen.

Als u een voor beeld van deze certificaten wilt zien, bekijkt u de scripts die demo certificaten maken bij het [beheren van test-CA-certificaten voor voor beelden en zelf studies](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Certificaten op het apparaat installeren

Installeer uw certificaat keten op het IoT Edge apparaat en configureer de IoT Edge-runtime om te verwijzen naar de nieuwe certificaten.

Als u bijvoorbeeld de voorbeeld scripts hebt gebruikt om [demo certificaten te maken](how-to-create-test-certificates.md), kopieert u de volgende bestanden naar uw IOT-edge-apparaat:

* CA-certificaat van apparaat:`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Persoonlijke sleutel van de apparaat-CA:`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Basis-CA:`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Kopieer de drie certificaat-en sleutel bestanden naar uw IoT Edge-apparaat.

   U kunt een service zoals [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) of een functie zoals [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) gebruiken om de certificaat bestanden te verplaatsen.  Als u de certificaten op het IoT Edge apparaat zelf hebt gegenereerd, kunt u deze stap overs Laan en het pad naar de werkmap gebruiken.

1. Open het IoT Edge Security daemon config-bestand.

   * Windows`C:\ProgramData\iotedge\config.yaml`
   * Spreek`/etc/iotedge/config.yaml`

1. Stel de **certificaat** eigenschappen in config. yaml in op het pad naar het bestand-URI naar het certificaat en de sleutel bestanden op het IOT edge apparaat. Verwijder het `#` teken vóór de eigenschappen van het certificaat om de vier regels op te heffen. Zorg ervoor dat de regel **certificaten:** geen voor gaande witruimte heeft en dat geneste items met twee spaties worden inge sprongen. Bijvoorbeeld:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

1. Controleer op Linux-apparaten of de gebruiker **iotedge** Lees machtigingen heeft voor de directory die de certificaten bevat.

1. Als u eerder andere certificaten voor IoT Edge op het apparaat hebt gebruikt, verwijdert u de bestanden in de volgende twee directory's voordat u IoT Edge start of opnieuw opstart:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` en`C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` en`/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>Levens duur van certificaten aanpassen

IoT Edge automatisch certificaten op het apparaat genereren in verschillende gevallen, waaronder:

* Als u geen eigen productie certificaten opgeeft wanneer u IoT Edge installeert en inricht, wordt door de IoT Edge Security Manager automatisch een **CA-certificaat**voor een apparaat gegenereerd. Dit zelfondertekende certificaat is alleen bedoeld voor ontwikkelings-en test scenario's, niet voor productie. Dit certificaat verloopt na 90 dagen.
* De IoT Edge Security Manager genereert ook een **CA-certificaat** dat is ondertekend door het CA-certificaat van de apparaat

Zie [begrijpen hoe Azure IOT Edge certificaten gebruikt](iot-edge-certs.md)voor meer informatie over de functie van de verschillende certificaten op een IOT edge apparaat.

Voor deze twee automatisch gegenereerde certificaten hebt u de optie om de **auto_generated_ca_lifetime_days** vlag in config. yaml in te stellen om het aantal dagen voor de levens duur van de certificaten te configureren.

>[!NOTE]
>Er is een derde automatisch gegenereerd certificaat dat IoT Edge Security Manager maakt, het **IOT Edge hub-server certificaat**. Dit certificaat heeft altijd een 90 dag, maar wordt automatisch verlengd voordat het verloopt. De waarde van de **auto_generated_ca_lifetime_days** heeft geen invloed op dit certificaat.

Als u het verlopen van het certificaat wilt configureren naar een andere waarde dan de standaard 90 dagen, voegt u het gedeelte dagen aan de sectie **certificaten** van het bestand config. yaml toe.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

Als u uw eigen CA-certificaten voor het apparaat hebt opgegeven, is deze waarde nog steeds van toepassing op het CA-certificaat van de werk belasting, op voor waarde dat de ingestelde levens duur korter is dan de levens duur van het CA-certificaat van de apparaat.

Nadat u de vlag hebt opgegeven in het bestand config. yaml, voert u de volgende stappen uit:

1. De inhoud van de `hsm` map verwijderen.

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

1. Bevestig de instelling voor de levens duur.

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   Controleer de uitvoer van de **gereedheid voor productie: certificaten** controle, waarin het aantal dagen wordt vermeld waarna de automatisch GEGENEREERDe CA-certificaten verlopen.

## <a name="next-steps"></a>Volgende stappen

Het installeren van certificaten op een IoT Edge apparaat is een nood zakelijke stap voordat u uw oplossing in de productie omgeving implementeert. Meer informatie over het [voorbereiden van de implementatie van uw IOT EDGE oplossing in productie](production-checklist.md).
