---
title: IoT Hub Device Provisioning Service beheren met Azure CLI-& IoT-extensie
description: Meer informatie over het gebruik van Azure CLI en de IoT-extensie voor het beheren van de DpS (IoT Hub Device Provisioning Service)
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 03ec0b41ad910ff0d1dcdc17148e01ec94ea9fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674517"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Azure CLI en de IoT-extensie gebruiken om de IoT Hub Device Provisioning Service te beheren

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is een open-source cross-platform command-line tool voor het beheren van Azure-bronnen zoals IoT Edge. Azure CLI is beschikbaar op Windows, Linux en MacOS. Met Azure CLI u Azure IoT Hub-resources, service-exemplaren voor apparaatvoorziening en gekoppelde hubs uit de doos beheren.

De IoT-extensie verrijkt Azure CLI met functies zoals apparaatbeheer en volledige IoT Edge-mogelijkheden.

In deze zelfstudie voert u eerst de stappen uit voor het instellen van Azure CLI en de IoT-extensie. Vervolgens leert u hoe u CLI-opdrachten uitvoert om basisbewerkingen van apparaatinrichtingsservice uit te voeren. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="installation"></a>Installeren 

### <a name="install-python"></a>Python installeren

[Python 2.7x of Python 3.x](https://www.python.org/downloads/) is vereist.

### <a name="install-the-azure-cli"></a>Azure-CLI installeren

Volg de [installatieinstructie](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om Azure CLI in uw omgeving in te stellen. Uw Azure CLI-versie moet minimaal 2.0.70 of hoger zijn. Gebruik `az –version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen. Een eenvoudige manier om Azure CLI 2.0 te installeren in Windows is de [MSI](https://aka.ms/InstallAzureCliWindows) te downloaden en installeren.

### <a name="install-iot-extension"></a>IoT-extensie installeren

[In het Leesmij-bestand bij de IoT-extensie](https://github.com/Azure/azure-iot-cli-extension) worden verschillende manieren voor het installeren van de extensie beschreven. De eenvoudigste manier is `az extension add --name azure-iot` uit te voeren. Na de installatie kunt u gebruikmaken van `az extension list` om de momenteel geïnstalleerde extensies te valideren of van `az extension show --name azure-iot` voor informatie over de IoT-extensie. U kunt `az extension remove --name azure-iot` gebruiken om de extensie te verwijderen.


## <a name="basic-device-provisioning-service-operations"></a>Basisservice voor het inrichten van apparaten

In het voorbeeld ziet u hoe u zich aanmeldt bij uw Azure-account, een Azure Resource Group maakt (een container met gerelateerde resources voor een Azure-oplossing), een IoT-hub maakt, een service voor apparaatinrichting maakt, de bestaande services voor apparaatinrichting weergeeft en maak een gekoppelde IoT-hub met CLI-opdrachten. 

Voer de hierboven beschreven installatiestappen uit voordat u begint. Als u geen Azure-account hebt, kunt u nu [een gratis account maken](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Inloggen bij het Azure-account
  
    az login

![aanmelding](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Maak een resourcegroep IoTHubBlogDemo in eastus

    az group create -l eastus -n IoTHubBlogDemo

![Een resourcegroep maken](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. Twee voorzieningen voor apparaten maken

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Service voor apparaatinrichting maken](./media/how-to-manage-dps-with-cli/create-dps.jpg)

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Alle bestaande diensten voor het inrichten van apparaten in deze resourcegroep vermelden

    az iot dps list --resource-group IoTHubBlogDemo

![Diensten voor het inrichten van apparaten aanbieden](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Maak een IoT Hub blogDemoHub onder de nieuw gemaakte resourcegroep

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![IoT Hub maken](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Een bestaande IoT-hub koppelen aan een apparaatinrichtingsservice

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Hub koppelen](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Het apparaat inschrijven
> * Het apparaat starten
> * Controleren of het apparaat is geregistreerd

Ga naar de volgende zelfstudie voor informatie over het inrichten van meerdere apparaten via hubs met gelijke taakverdeling. 

> [!div class="nextstepaction"]
> [Apparaten inrichten in meerdere IoT-hubs met gelijke taakverdeling](./tutorial-provision-multiple-hubs.md)
