---
title: Gebruik de Azure IoT-extensie voor Azure CLI om te communiceren met IoT Plug en Play preview-apparaten | Microsoft Docs
description: Installeer de Azure IoT-extensie voor Azure CLI en gebruik deze om te communiceren met de IoT-Plug en Play apparaten die zijn verbonden met mijn IoT-hub.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 09/08/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: eb4f607672c39d45b7791ccaeeb6f7cff9393cb9
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571020"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>De Azure IoT-extensie voor de Azure CLI installeren en gebruiken

[Azure cli](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is een open source-opdracht regel programma voor meerdere platformen voor het beheer van Azure-resources, zoals IOT hub. De Azure CLI is beschikbaar in Windows, Linux en MacOS. De Azure CLI is ook vooraf geïnstalleerd in de [Azure Cloud shell](https://shell.azure.com). Met de Azure CLI kunt u Azure IoT Hub-resources, Device Provisioning Service-instanties en gekoppelde hubs beheren zonder de extensies te installeren.

De Azure IoT-extensie voor Azure CLI is een opdracht regel programma voor interactie met en het testen van IoT Plug en Play preview-apparaten. U kunt de extensie gebruiken voor het volgende:

- Verbinding maken met een apparaat.
- De telemetrie weer geven die het apparaat verzendt.
- Werken met Apparaateigenschappen.
- Roep de opdrachten van het apparaat aan.

In dit artikel wordt beschreven hoe u:

- Installeer en configureer de Azure IoT-extensie voor de Azure CLI.
- Gebruik de uitbrei ding om uw apparaten te gebruiken en te testen.
- Gebruik de uitbrei ding voor het beheren van interfaces in de model opslagplaats.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Azure IoT-extensie voor Azure CLI installeren

### <a name="step-1---install-the-azure-cli"></a>Stap 1: de Azure CLI installeren

Volg de [installatie-instructies](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om de Azure cli in uw omgeving in te stellen. Als u alle onderstaande opdrachten wilt gebruiken, moet u versie 2.0.73 of hoger van uw versie van Azure CLI. Gebruik `az -–version` om de versie te valideren.

### <a name="step-2---install-iot-extension"></a>Stap 2: IoT-extensie installeren

[In het Leesmij-bestand bij de IoT-extensie](https://github.com/Azure/azure-iot-cli-extension) worden verschillende manieren voor het installeren van de extensie beschreven. De eenvoudigste manier is `az extension add --name azure-cli-iot-ext` uit te voeren. Na de installatie kunt u gebruikmaken van `az extension list` om de momenteel geïnstalleerde extensies te valideren of van `az extension show --name azure-cli-iot-ext` voor informatie over de IoT-extensie. U kunt `az extension remove --name azure-cli-iot-ext` gebruiken om de extensie te verwijderen.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Azure IoT-extensie gebruiken voor de Azure CLI

### <a name="prerequisites"></a>Vereisten

Voer de volgende opdracht uit om u aan te melden bij uw Azure-abonnement:

```cmd/sh
az login
```

> [!NOTE]
> Als u de Azure Cloud shell gebruikt, bent u automatisch aangemeld en hoeft u de vorige opdracht niet uit te voeren.

Als u de Azure IoT-extensie voor Azure CLI wilt gebruiken, hebt u het volgende nodig:

- Een Azure IoT hub. Er zijn veel manieren om een IoT-hub toe te voegen aan uw Azure-abonnement, zoals [het maken van een IOT-hub met behulp van de Azure cli](../iot-hub/iot-hub-create-using-cli.md). U hebt de connection string van de IoT-hub nodig om de Azure IoT-extensie opdrachten uit te voeren. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

    > [!NOTE]
    > Tijdens de open bare preview zijn IoT-Plug en Play-functies alleen beschikbaar voor IoT-hubs die zijn gemaakt in de regio's **VS-centraal**, **Europa-Noord**en **Japan-Oost** .

- Een apparaat dat is geregistreerd in uw IoT-hub. U kunt de volgende Azure CLI-opdracht gebruiken om een apparaat te registreren. Zorg er daarom voor dat u de `{YourIoTHubName}` en `{YourDeviceID}` tijdelijke aanduidingen vervangt door de waarden:

    ```cmd/sh
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Sommige opdrachten hebben de connection string nodig voor een bedrijfs model opslagplaats. Er wordt een model opslagplaats voor uw bedrijf gemaakt wanneer u [de Azure Certified voor IOT-portal voor het eerst onboardt](howto-onboard-portal.md). Een derde partij kan hun model opslagplaats connection string met u delen om u toegang te geven tot hun interfaces en modellen.

### <a name="interact-with-a-device"></a>Interactie met een apparaat

U kunt de extensie gebruiken voor het weer geven en gebruiken van IoT Plug en Play-apparaten die zijn verbonden met een IoT-hub. De uitbrei ding werkt met de digitale dubbele shape waarmee het IoT-Plug en Play apparaat wordt aangeduid.

#### <a name="list-devices-and-interfaces"></a>Apparaten en interfaces weer geven

Alle apparaten op een IoT Hub weer geven:

```cmd/sh
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Alle interfaces weer geven die zijn geregistreerd door een IoT-Plug en Play apparaat:

```cmd/sh
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Eigenschappen

Alle eigenschappen en eigenschaps waarden weer geven voor een interface op een apparaat:

```cmd/sh
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Stel de waarde van een eigenschap voor lezen/schrijven in:

```cmd/sh
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Een voor beeld van een payload-bestand voor het instellen van de eigenschap **name** op de **sensor** interface van een apparaat naar **Contoso** ziet er als volgt uit:

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>Opdrachten

Alle opdrachten weer geven voor een interface op een apparaat:

```cmd/sh
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Zonder de para meter `--repo-login` wordt met deze opdracht de open bare model opslagplaats gebruikt.

Een opdracht aanroepen:

```cmd/sh
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Digitale dubbele gebeurtenissen

Bewaak alle IoT Plug en Play digitale dubbele gebeurtenissen van een specifiek apparaat en een specifieke interface naar de **$Default** Event hub consumenten groep:

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Bewaak alle IoT Plug en Play digitale dubbele gebeurtenissen van een specifiek apparaat en een specifieke interface naar een specifieke consumenten groep:

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Interfaces in een model opslagplaats beheren

De volgende opdrachten gebruiken de open bare IoT Plug en Play-model opslagplaats. Als u een bedrijfs model opslagplaats wilt gebruiken, voegt u het `--login` argument toe aan uw model opslagplaats connection string.

Interfaces in de open bare IoT-Plug en Play model opslagplaats weer geven:

```cmd/sh
az iot pnp interface list
```

Een interface weer geven in de opslag plaats Public IoT Plug en Play model:

```cmd/sh
az iot pnp interface show --interface {YourInterfaceId}
```

Maak een interface in uw IoT Plug en Play-bedrijfs model-opslag plaats:

```cmd/sh
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

U kunt geen directe interface maken in de open bare model opslagplaats.

Een interface bijwerken in uw IoT Plug en Play bedrijfs model-opslag plaats:

```cmd/sh
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

U kunt een interface niet rechtstreeks bijwerken in de open bare model opslagplaats.

Publiceer een interface vanuit uw IoT Plug en Play-bedrijfs model opslagplaats naar de open bare model opslagplaats. Met deze bewerking wordt de interface onveranderbaar:

```cmd/sh
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Alleen micro soft-partners kunnen interfaces publiceren naar de open bare model opslagplaats.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Hulp modellen voor apparaten beheren in een model opslagplaats

De volgende opdrachten gebruiken de open bare IoT Plug en Play-model opslagplaats. Als u een bedrijfs model opslagplaats wilt gebruiken, voegt u het `--login` argument toe aan uw model opslagplaats connection string.

Geef een overzicht van de mogelijkheden van apparaten in de IoT Plug en Play Public model-opslag plaats:

```cmd/sh
az iot pnp capability-model list
```

Een mogelijkheidsprofiel weer geven in de open bare model opslagplaats van IoT Plug en Play:

```cmd/sh
az iot pnp capability-model show --model {YourModelID}
```

Maak een hulp model voor het maken van een apparaat in een IoT Plug en Play-bedrijfs model opslagplaats:

```cmd/sh
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

U kunt niet rechtstreeks een model maken in de open bare model opslagplaats.

Een mogelijkheidsprofiel bijwerken in de opslag voor IoT Plug en Play-bedrijfs model:

```cmd/sh
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

U kunt een model niet rechtstreeks bijwerken in de open bare model opslagplaats.

Publiceer een mogelijkheidsprofiel van uw IoT Plug en Play-bedrijfs model-opslag plaats naar de open bare model opslagplaats. Met deze bewerking wordt het model onveranderbaar:

```cmd/sh
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Alleen micro soft-partners kunnen modellen publiceren naar de open bare model opslagplaats.

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u hoe u de Azure IoT-extensie voor Azure CLI kunt installeren en gebruiken om te communiceren met uw Plug en Play-apparaten. Een voorgestelde volgende stap is informatie over het [beheren van modellen](./howto-manage-models.md).
