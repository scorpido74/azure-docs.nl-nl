---
title: Gebruik de Azure IoT-extensie voor Azure CLI om te communiceren met IoT Plug and Play Preview-apparaten | Microsoft Documenten
description: Installeer de Azure IoT-extensie voor Azure CLI en gebruik deze om te communiceren met de IoT Plug and Play-apparaten die zijn verbonden met mijn IoT-hub.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b5907c0fb127947e90352e68b2726a22f5afea0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234692"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>De Azure IoT-extensie voor de Azure CLI installeren en gebruiken

[De Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is een open-source cross-platform command-line tool voor het beheren van Azure-resources zoals IoT Hub. De Azure CLI is beschikbaar op Windows, Linux en MacOS. De Azure CLI is ook vooraf geïnstalleerd in de [Azure Cloud Shell.](https://shell.azure.com) Met Azure CLI u Azure IoT Hub-resources, device Provisioning Service-exemplaren en gekoppelde hubs beheren zonder extensies te installeren.

De Azure IoT-extensie voor azure CLI is een opdrachtregelhulpmiddel voor het communiceren met en testen van IoT Plug and Play Preview-apparaten. U de extensie gebruiken om:

- Maak verbinding met een apparaat.
- Bekijk de telemetrie die het apparaat verzendt.
- Werken met apparaateigenschappen.
- Opdrachten voor oproepapparaten.

In dit artikel leest u informatie over:

- Installeer en configureer de Azure IoT-extensie voor de Azure CLI.
- Gebruik de extensie om te communiceren met en test uw apparaten.
- Gebruik de extensie om interfaces in de modelopslagplaats te beheren.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Azure IoT-extensie voor de Azure CLI installeren

### <a name="step-1---install-the-azure-cli"></a>Stap 1 - Installeer de Azure CLI

Volg de [installatie-instructies](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om de Azure CLI in uw omgeving in te stellen. Als u alle onderstaande opdrachten wilt gebruiken, moet uw Azure CLI-versie versie versie 2.0.73 of hoger zijn. Gebruik `az -–version` om de versie te valideren.

### <a name="step-2---install-iot-extension"></a>Stap 2 - IoT-extensie installeren

[In het Leesmij-bestand bij de IoT-extensie](https://github.com/Azure/azure-iot-cli-extension) worden verschillende manieren voor het installeren van de extensie beschreven. De eenvoudigste manier is `az extension add --name azure-iot` uit te voeren. Na de installatie kunt u gebruikmaken van `az extension list` om de momenteel geïnstalleerde extensies te valideren of van `az extension show --name azure-iot` voor informatie over de IoT-extensie. U kunt `az extension remove --name azure-iot` gebruiken om de extensie te verwijderen.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Azure IoT-extensie gebruiken voor de Azure CLI

### <a name="prerequisites"></a>Vereisten

Als u zich wilt aanmelden bij uw Azure-abonnement, voert u de volgende opdracht uit:

```azurecli
az login
```

> [!NOTE]
> Als u de Azure-cloudshell gebruikt, bent u automatisch aangemeld en hoeft u de vorige opdracht niet uit te voeren.

Als u de Azure IoT-extensie voor de Azure CLI wilt gebruiken, hebt u het:

- Een Azure IoT-hub. Er zijn veel manieren om een IoT-hub toe te voegen aan uw Azure-abonnement, zoals [Een IoT-hub maken met de Azure CLI.](../iot-hub/iot-hub-create-using-cli.md) U hebt de verbindingstekenreeks van de IoT-hub nodig om de opdrachten voor Azure IoT-extensie uit te voeren. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- Een apparaat dat is geregistreerd in uw IoT-hub. U de volgende opdracht Azure CLI gebruiken om `{YourIoTHubName}` een `{YourDeviceID}` apparaat te registreren, zorg ervoor dat u de tijdelijke aanduidingen en tijdelijke aanduidingen vervangt door uw waarden:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Sommige opdrachten hebben de verbindingstekenreeks nodig voor een bedrijfsmodelopslagplaats. Er wordt een modelopslagplaats voor uw bedrijf gemaakt wanneer u [voor het eerst aan boord gaat van de Azure Certified for IoT-portal.](howto-onboard-portal.md) Een derde partij kan hun modelrepository-verbindingstekenreeks met u delen om u toegang te geven tot hun interfaces en modellen.

### <a name="interact-with-a-device"></a>Interactie met een apparaat

U de extensie gebruiken om IoT Plug and Play-apparaten te bekijken en ermee te werken die zijn verbonden met een IoT-hub. De extensie werkt met de digitale tweeling die het IoT Plug and Play-apparaat vertegenwoordigt.

#### <a name="list-devices-and-interfaces"></a>Apparaten en interfaces weergeven

Alle apparaten op een IoT-hub weergeven:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Vermeld alle interfaces die zijn geregistreerd door een IoT Plug and Play-apparaat:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Eigenschappen

Alle eigenschappen en eigenschappenwaarden voor een interface op een apparaat weergeven:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Stel de waarde in van een eigenschap lezen schrijven:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Een voorbeeld payload-bestand om de **eigenschap naam** op de **sensorinterface** van een apparaat in te stellen op **Contoso** ziet er als volgt uit:

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

Alle opdrachten voor een interface op een apparaat weergeven:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Zonder `--repo-login` de parameter maakt deze opdracht gebruik van de openbare modelopslagplaats.

Een opdracht aanroepen:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Digitale twin events

Monitor alle IoT Plug and Play digital twin-evenementen vanaf een specifiek apparaat en interface naar de **$Default-groep** van de eventhub:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Monitor alle IoT Plug and Play digital twin-gebeurtenissen vanaf een specifiek apparaat en interface die een specifieke consumentengroep vormen:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Interfaces beheren in een modelopslagplaats

De volgende opdrachten maken gebruik van de openbare IoT Plug and Play-modelrepository. Als u een bedrijfsmodelrepository `--login` wilt gebruiken, voegt u het argument toe met de tekenreeks van de modelrepository-verbinding.

Lijst interfaces in de openbare IoT Plug and Play model repository:

```azurecli
az iot pnp interface list
```

Een interface weergeven in de openbare IoT Plug and Play-modelopslagplaats:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Maak een interface in uw IoT Plug and Play-bedrijfsmodelrepository:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

U niet direct een interface maken in de openbare modelopslagplaats.

Werk een interface bij in uw IoT Plug and Play-bedrijfsmodelrepository:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

U een interface niet rechtstreeks bijwerken in de openbare modelopslagplaats.

Publiceer een interface van uw IoT Plug and Play-bedrijfsmodelrepository naar de openbare modelopslagplaats. Deze bewerking maakt de interface onveranderlijk:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Alleen Microsoft-partners kunnen interfaces publiceren naar de openbare modelopslagplaats.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Apparaatcapaciteitsmodellen beheren in een modelopslagplaats

De volgende opdrachten maken gebruik van de openbare IoT Plug and Play-modelrepository. Als u een bedrijfsmodelrepository `--login` wilt gebruiken, voegt u het argument toe met de tekenreeks van de modelrepository-verbinding.

Lijst apparaatmogelijkheden modellen in de IoT Plug and Play public model repository:

```azurecli
az iot pnp capability-model list
```

Een apparaatcapaciteitsmodel weergeven in de openbare iot-structuur voor Plug and Play:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Maak een apparaatcapaciteitsmodel in een IoT Plug and Play-bedrijfsmodelopslagplaats:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

U niet direct een model maken in de openbare modelopslagplaats.

Werk een apparaatcapaciteitsmodel bij in de iot-structuurvan het bedrijf Plug and Play:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

U een model niet rechtstreeks bijwerken in de openbare modelopslagplaats.

Publiceer een apparaatcapaciteitsmodel van uw IoT Plug and Play-bedrijfsmodelrepository naar de openbare modelopslagplaats. Deze bewerking maakt het model onveranderlijk:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Alleen Microsoft-partners kunnen modellen publiceren naar de openbare modelopslagplaats.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de Azure IoT-extensie voor de Azure CLI installeren en gebruiken om te communiceren met uw Plug and Play-apparaten. Een voorgestelde volgende stap is om te leren hoe modellen te [beheren](./howto-manage-models.md).
