---
title: Gebruik de Azure IoT-extensie voor Azure CLI om te communiceren met IoT Plug en Play preview-apparaten | Microsoft Docs
description: Installeer de Azure IoT-extensie voor Azure CLI en gebruik deze om te communiceren met de IoT-Plug en Play apparaten die zijn verbonden met mijn IoT-hub.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: dadb1f044547acd6e5f0d274143123e89d7dae46
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475478"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>De Azure IoT-extensie voor de Azure CLI installeren en gebruiken

[Azure cli](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is een open source-opdracht regel programma voor meerdere platformen voor het beheer van Azure-resources, zoals IOT hub. De Azure CLI is beschikbaar in Windows, Linux en macOS. Met de Azure CLI kunt u Azure IoT Hub-resources, Device Provisioning Service-instanties en gekoppelde hubs beheren zonder de extensies te installeren.

De Azure IoT-extensie voor Azure CLI is een opdracht regel programma voor interactie met en het testen van IoT Plug en Play preview-apparaten. U kunt de extensie gebruiken voor het volgende:

- Verbinding maken met een apparaat.
- De telemetrie weer geven die het apparaat verzendt.
- Werken met Apparaateigenschappen.
- Roep de opdrachten van het apparaat aan.

In dit artikel leest u informatie over:

- Installeer en configureer de Azure IoT-extensie voor de Azure CLI.
- Gebruik de uitbrei ding om uw apparaten te gebruiken en te testen.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Azure IoT-extensie voor Azure CLI installeren

### <a name="step-1---install-the-azure-cli"></a>Stap 1: de Azure CLI installeren

Volg de [installatie-instructies](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om de Azure cli in uw omgeving in te stellen. Voor de beste ervaring moet uw Azure CLI-versie versie 2.9.1 of hoger zijn. Gebruik `az -–version` om de versie te valideren.

### <a name="step-2---install-iot-extension"></a>Stap 2: IoT-extensie installeren

[In het Leesmij-bestand bij de IoT-extensie](https://github.com/Azure/azure-iot-cli-extension) worden verschillende manieren voor het installeren van de extensie beschreven. De eenvoudigste manier is `az extension add --name azure-iot` uit te voeren. Na de installatie kunt u gebruikmaken van `az extension list` om de momenteel geïnstalleerde extensies te valideren of van `az extension show --name azure-iot` voor informatie over de IoT-extensie. Op het moment van schrijven is het versie nummer van de uitbrei ding `0.9.7` .

U kunt `az extension remove --name azure-iot` gebruiken om de extensie te verwijderen.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Azure IoT-extensie gebruiken voor de Azure CLI

### <a name="prerequisites"></a>Vereisten

Voer de volgende opdracht uit om u aan te melden bij uw Azure-abonnement:

```azurecli
az login
```

Als u de Azure IoT-extensie voor Azure CLI wilt gebruiken, hebt u het volgende nodig:

- Een Azure IoT hub. Er zijn veel manieren om een IoT-hub toe te voegen aan uw Azure-abonnement, zoals [het maken van een IOT-hub met behulp van de Azure cli](../iot-hub/iot-hub-create-using-cli.md). U hebt de connection string van de IoT-hub nodig om de Azure IoT-extensie opdrachten uit te voeren. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

- Een apparaat dat is geregistreerd in uw IoT-hub. U kunt de volgende Azure CLI-opdracht gebruiken om een apparaat te registreren. Vervang de `{YourIoTHubName}` `{YourDeviceID}` tijdelijke aanduidingen door uw waarden.

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>Interactie met een apparaat

U kunt de extensie gebruiken voor het weer geven en gebruiken van IoT Plug en Play-apparaten die zijn verbonden met een IoT-hub. De uitbrei ding werkt met de digitale dubbele shape waarmee het IoT-Plug en Play apparaat wordt aangeduid.

#### <a name="list-devices"></a>Apparaten vermelden

Alle apparaten op een IoT Hub weer geven:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>Bekijk digitale dubbele

Bekijk het digitale dubbele van een apparaat:

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>Eigenschappen

Stel de waarde van een eigenschap voor lezen/schrijven in:

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>Opdracht

Een opdracht aanroepen:

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>Digitale dubbele gebeurtenissen

Bewaak alle IoT Plug en Play digitale dubbele gebeurtenissen van een specifiek apparaat en een specifieke interface naar de **$Default** Event hub consumenten groep:

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

### <a name="manage-models-in-the-model-repository"></a>Modellen in de model opslagplaats beheren

U kunt de opslag opdrachten van het Azure CLI-model gebruiken voor het beheren van modellen in de opslag plaats.

#### <a name="create-model-repository"></a>Model opslagplaats maken

Maak een nieuwe IoT-Plug en Play bedrijfs opslagplaats voor uw Tenant als u de eerste gebruiker van uw Tenant bent:

```azurecli
az iot pnp repo create
```

#### <a name="manage-model-repository-tenant-roles"></a>Tenant rollen voor model opslagplaats beheren

Een roltoewijzing voor een gebruiker of service-principal maken voor een specifieke resource.

Geef bijvoorbeeld user@consoso.com de rol van **ModelsCreator** voor de Tenant:

```azurecli
az iot pnp role-assignment create --resource-id {tenant_id} --resource-type Tenant --subject-id {user@contoso.com} --subject-type User --role ModelsCreator
```

Of geef user@consoso.com de rol van **ModelAdministrator** voor een specifiek model:

```azurecli
az iot pnp role-assignment create --resource-id {model_id} --resource-type Model --subject-id {user@contoso.com} --subject-type User --role ModelAdministrator
```

#### <a name="create-a-model"></a>Een model maken

Maak een nieuw model in de bedrijfs opslagplaats:

```azurecli
az iot pnp model create --model {model_json or path_to_file}
```

#### <a name="search-a-model"></a>Een model doorzoeken

Lijst met modellen die overeenkomen met een specifiek tref woord:

```azurecli
az iot pnp model list -q {search_keyword}
```

#### <a name="publish-a-model"></a>Een model publiceren

Een model van een apparaat in de bedrijfs opslagplaats publiceren naar de open bare opslag plaats.

Maak bijvoorbeeld openbaar voor het model met de ID `dtmi:com:example:ClimateSensor;1` :

```azurecli
az iot pnp model publish --dtmi "dtmi:com:example:ClimateSensor;1"
```

Voor het publiceren van een model moeten aan de volgende vereisten worden voldaan:

- De Tenant van het bedrijf of de organisatie moet een micro soft-partner zijn. 
- De gebruiker of Service-Principal moet lid zijn van de **Publisher** -rol van de opslagplaats Tenant.

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u hoe u de Azure IoT-extensie voor Azure CLI kunt installeren en gebruiken om te communiceren met uw IoT Plug en Play-apparaten. Een voorgestelde volgende stap is om te leren hoe u [Azure IOT Explorer kunt gebruiken met uw apparaten](./howto-use-iot-explorer.md).
