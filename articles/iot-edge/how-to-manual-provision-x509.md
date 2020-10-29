---
title: Inrichten met X. 509-certificaten-Azure IoT Edge | Microsoft Docs
description: Nadat de installatie is geïnstalleerd, dient u een IoT Edge apparaat met de identiteits certificaten van het apparaat in te richten en te verifiëren voor IoT Hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: abb3aa9ca7c9697fef1cf456964154249f0d69f3
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913973"
---
# <a name="set-up-an-azure-iot-edge-device-with-x509-certificate-authentication"></a>Een Azure IoT Edge apparaat instellen met X. 509-certificaat verificatie

Dit artikel bevat de stappen voor het registreren van een nieuw IoT Edge apparaat in IoT Hub en het configureren van het apparaat voor verificatie met X. 509-certificaten.

De stappen in dit artikel begeleiden u bij het proces hand matig inrichten, waarbij u elk apparaat hand matig verbindt met de IoT-hub. Het alternatief wordt automatisch ingericht met behulp van de IoT Hub Device Provisioning Service. Dit is handig als u veel apparaten hebt om in te richten.

<!--TODO: Add auto-provision info/links-->

Voor hand matige inrichting hebt u twee opties voor het verifiëren van IoT Edge apparaten:

* **Symmetrische sleutel** : wanneer u een nieuwe apparaat-id in IOT hub maakt, maakt de service twee sleutels. U plaatst een van de sleutels op het apparaat en geeft de sleutel aan IoT Hub bij het verifiëren.

  Deze verificatie methode is sneller om aan de slag te gaan, maar niet zo veilig.

* **X. 509 zelfondertekend** : u maakt twee X. 509-identiteits certificaten en plaatst deze op het apparaat. Wanneer u een nieuwe apparaat-id in IoT Hub maakt, geeft u de vinger afdrukken van beide certificaten. Wanneer het apparaat wordt geverifieerd bij IoT Hub, worden de bijbehorende certificaten weer gegeven en kan IoT Hub controleren of ze overeenkomen met de vinger afdrukken.

  Deze verificatie methode is veiliger en aanbevolen voor productie scenario's.

Dit artikel begeleidt het registratie-en inrichtings proces met X. 509-certificaat verificatie. Als u meer wilt weten over het instellen van een apparaat met symmetrische sleutels, raadpleegt u [een Azure IOT edge apparaat instellen met symmetrische sleutel verificatie](how-to-manual-provision-symmetric-key.md).

## <a name="prerequisites"></a>Vereisten

Voordat u de stappen in dit artikel uitvoert, moet u een apparaat hebben waarop de IoT Edge runtime is geïnstalleerd. Als dat niet het geval is, volgt u de stappen in [de Azure IOT Edge runtime installeren of verwijderen](how-to-install-iot-edge.md).

Voor hand matige inrichting met X. 509-certificaten is IoT Edge versie 1.0.10 of nieuwer vereist.

## <a name="create-certificates-and-thumbprints"></a>Certificaten en vinger afdrukken maken

Het certificaat van de apparaat-id is een Leaf-certificaat dat is verbonden via een vertrouwens keten van een certificaat aan het hoogste X. 509 Certificate Authority (CA)-certificaat. Voor het apparaat-ID-certificaat moet de algemene naam (CN) zijn ingesteld op de apparaat-ID die u wilt dat het apparaat in uw IoT-hub moet hebben.

Certificaten voor apparaat-id's worden alleen gebruikt voor het inrichten van het IoT Edge apparaat en het verifiëren van het apparaat met Azure IoT Hub. Ze ondertekenen geen certificaten, in tegens telling tot de CA-certificaten die het IoT Edge apparaat aan modules of blad apparaten geeft voor verificatie. Zie [Azure IOT Edge details van certificaat gebruik](iot-edge-certs.md)voor meer informatie.

Nadat u het certificaat voor de apparaat-id hebt gemaakt, hebt u twee bestanden: een. CER-of. pem-bestand dat het open bare gedeelte van het certificaat bevat, en een. CER-of. pem-bestand met de persoonlijke sleutel van het certificaat.

U hebt de volgende bestanden nodig voor hand matige inrichting met X. 509:

* Twee sets certificaten voor apparaat-id's en certificaten voor persoonlijke sleutels. Er wordt één set certificaat/sleutel bestanden aan de IoT Edge-runtime gegeven.
* Vinger afdrukken van certificaten voor apparaat-id's. Vingerafdruk waarden zijn 40-Hex-tekens voor SHA-1-hashes of 64-Hex-tekens voor SHA-256-hashes. Beide vinger afdrukken worden IoT Hub op het moment van de registratie van het apparaat.

Als er geen certificaten beschikbaar zijn, kunt u [demo certificaten maken om IOT Edge apparaatfuncties te testen](how-to-create-test-certificates.md). Volg de instructies in dit artikel voor het instellen van scripts voor het maken van certificaten, het maken van een basis-CA-certificaat en het maken van twee IoT Edge certificaten voor apparaat-id's.

Een van de manieren om de vinger afdruk op te halen uit een certificaat is met de volgende openssl-opdracht:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

## <a name="register-a-new-device"></a>Een nieuw apparaat registreren

Elk apparaat dat verbinding maakt met een IoT Hub heeft een apparaat-ID die wordt gebruikt voor het bijhouden van Cloud-naar-apparaat-en apparaat-naar-Cloud-communicatie. U configureert een apparaat met de verbindings gegevens die de hostnaam van de IoT-hub, de apparaat-ID en de informatie die het apparaat gebruikt om te verifiëren bij IoT Hub.

Voor X. 509-certificaat authenticatie wordt deze informatie verstrekt in de vorm van de *vinger afdrukken* die zijn gemaakt op basis van de identiteits certificaten van uw apparaat. Deze vinger afdrukken worden IoT Hub op het moment van de registratie van het apparaat, zodat de service het apparaat kan herkennen wanneer het verbinding maakt.

U kunt verschillende hulpprogram ma's gebruiken om een nieuw IoT Edge apparaat te registreren in IoT Hub en de certificaat vingerafdrukken te uploaden.

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Vereisten voor de Azure Portal

Een gratis of standaard [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Een IoT Edge apparaat maken in de Azure Portal

In uw IoT Hub in de Azure Portal worden IoT Edge apparaten afzonderlijk gemaakt en beheerd van IOT-apparaten die niet Edge zijn ingeschakeld.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw IoT Hub.

1. Selecteer in het linkerdeel venster **IOT Edge** in het menu en selecteer vervolgens **een IOT edge apparaat toevoegen** .

   ![Een IoT Edge apparaat toevoegen vanuit de Azure Portal](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Geef op de pagina **een apparaat maken** de volgende informatie op:

   * Een beschrijvende apparaat-ID maken. Noteer deze apparaat-ID, zoals u deze in de volgende sectie gebruikt.
   * Selecteer **X. 509 zelf ondertekend** als verificatie type.
   * Geef de primaire en secundaire vinger afdrukken voor identiteits certificaten op. Vingerafdruk waarden zijn 40-Hex-tekens voor SHA-1-hashes of 64-Hex-tekens voor SHA-256-hashes.

1. Selecteer **Opslaan** .

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>IoT Edge apparaten weer geven in de Azure Portal

Alle apparaten met rand mogelijkheden die verbinding maken met uw IoT-hub, worden weer gegeven op de pagina **IOT Edge** .

![Alle IoT Edge apparaten in uw IoT-hub weer geven](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Vereisten voor de Azure CLI

* Een [IOT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* [Azure cli](/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet mini maal 2.0.70 of hoger zijn. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.
* De [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Een IoT Edge-apparaat maken met de Azure CLI

Gebruik de opdracht [AZ IOT hub apparaat-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) om een nieuwe apparaat-id te maken in uw IOT-hub. Bijvoorbeeld:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Deze opdracht bevat verschillende para meters:

* `--device-id` of `-d` : Geef een beschrijvende naam op die uniek is voor uw IOT-hub. Noteer deze apparaat-ID, zoals u deze in de volgende sectie gebruikt.
* `hub-name` of `-n` : Geef de naam op van uw IOT-hub.
* `--edge-enabled` of `--ee` : Declareer dat het apparaat een IOT edge apparaat is.
* `--auth-method` of `--am` : Declareer het autorisatie type dat het apparaat gaat gebruiken. In dit geval gebruiken we X. 509-certificaat vingerafdrukken.
* `--primary-thumbprint` of `--ptp` : Geef een vinger afdruk van een X. 509-certificaat op om te gebruiken als primaire sleutel.
* `--secondary-thumbprint` of `--stp` : Geef een vinger afdruk van een X. 509-certificaat op dat als secundaire sleutel moet worden gebruikt.

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>IoT Edge apparaten weer geven met de Azure CLI

Gebruik de opdracht [AZ IOT hub Device-Identity List](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) om alle apparaten in uw IOT-hub weer te geven. Bijvoorbeeld:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Voeg de vlag toe `--edge-enabled` of geef `--ee` alleen IOT edge apparaten in uw IOT-hub op.

Elk apparaat dat is geregistreerd als IoT Edge apparaat heeft de eigenschaps **mogelijkheden. iotEdge** ingesteld op **True** .

--- 

## <a name="configure-an-iot-edge-device"></a>Een IoT Edge-apparaat configureren

Zodra het IoT Edge apparaat een identiteit heeft in IoT Hub, moet u het apparaat configureren met de Cloud identiteit en de bijbehorende identiteits certificaten.

Op een Linux-apparaat geeft u deze informatie op door een config. yaml-bestand te bewerken. Op een Windows-apparaat kunt u deze informatie opgeven door een Power shell-script uit te voeren.

# <a name="linux"></a>[Linux](#tab/linux)

1. Open het configuratie bestand op het IoT Edge apparaat.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Zoek de sectie inrichtings configuraties van het bestand. 

1. Commentaar bij de **hand matige inrichtings configuratie met behulp van een Connection String** sectie.

1. Verwijder de opmerking bij de **hand matige inrichtings configuratie met behulp van een X. 509-identiteits certificaat** sectie. Zorg ervoor dat het **inrichten:** de regel heeft geen voorafgaande spatie en dat geneste items met twee spaties worden inge sprongen.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

1. Werk de volgende velden bij:

   * **iothub_hostname** : de hostnaam van de IOT-hub waarmee het apparaat verbinding maakt. Bijvoorbeeld `{IoT hub name}.azure-devices.net`.
   * **device_id** : de id die u hebt ingevoerd tijdens het registreren van het apparaat.
   * **identity_cert** : de URI naar een identiteits certificaat op het apparaat. Bijvoorbeeld `file:///path/identity_certificate.pem`.
   * **identity_pk** : de URI naar het persoonlijke sleutel bestand voor het gegeven identiteits certificaat. Bijvoorbeeld `file:///path/identity_key.pem`.

1. Sla het bestand op en sluit het.

   `CTRL + X`, `Y`, `Enter`

1. Nadat u de inrichtings gegevens in het configuratie bestand hebt ingevoerd, start u de daemon opnieuw op:

   ```bash
   sudo systemctl restart iotedge
   ```

# <a name="windows"></a>[Windows](#tab/windows)

1. Voer Power shell uit als beheerder op het IoT Edge-apparaat.

2. Gebruik de opdracht [initialiseren-IoTEdge](reference-windows-scripts.md#initialize-iotedge) om de IOT Edge runtime op uw computer te configureren.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Als u Linux-containers gebruikt, voegt u de `-ContainerOs` para meter toe aan de vlag. Wees consistent met de container optie die u hebt gekozen met de `Deploy-IoTEdge` opdracht die u eerder hebt uitgevoerd.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * Als u het IoTEdgeSecurityDaemon.ps1 script op uw apparaat hebt gedownload voor offline of installatie van een specifieke versie, moet u ervoor zorgen dat u naar de lokale kopie van het script verwijst.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Geef de volgende informatie op wanneer u daarom wordt gevraagd:

   * **IotHubHostName** : de hostnaam van de IOT-hub waarmee het apparaat verbinding maakt. Bijvoorbeeld `{IoT hub name}.azure-devices.net`.
   * **DeviceID** : de id die u hebt ingevoerd tijdens het registreren van het apparaat.
   * **X509IdentityCertificate** : absoluut pad naar een identiteits certificaat op het apparaat. Bijvoorbeeld `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey** : het absolute pad naar het bestand met de persoonlijke sleutel voor het gegeven identiteits certificaat. Bijvoorbeeld `C:\path\identity_key.pem`.

Wanneer u een apparaat hand matig inricht, kunt u extra para meters gebruiken om het proces te wijzigen, met inbegrip van:

* Direct verkeer om een proxy server te passeren
* Een specifieke edgeAgent-container installatie kopie declareren en referenties opgeven als deze zich in een persoonlijk REGI ster bevinden

Zie [Power shell-scripts voor IOT Edge in Windows](reference-windows-scripts.md)voor meer informatie over deze aanvullende para meters.

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Volgende stappen

Ga door met het [implementeren van IOT Edge-modules](how-to-deploy-modules-portal.md) voor meer informatie over het implementeren van modules op uw apparaat.