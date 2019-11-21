---
title: Manage IoT Hub Device Provisioning Service using Azure CLI & IoT extension
description: Learn how to use Azure CLI and the IoT extension to manage the IoT Hub Device Provisioning Service
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0e276ca32d7cd02f9668b33b3729757404b13b00
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229744"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>How to use Azure CLI and the IoT extension to manage the IoT Hub Device Provisioning Service

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is an open-source cross platform command-line tool for managing Azure resources such as IoT Edge. Azure CLI is available on Windows, Linux, and MacOS. Azure CLI enables you to manage Azure IoT Hub resources, Device Provisioning service instances, and linked-hubs out of the box.

The IoT extension enriches Azure CLI with features such as device management and full IoT Edge capability.

In this tutorial, you first complete the steps to setup Azure CLI and the IoT extension. Then you learn how to run CLI commands to perform basic Device Provisioning Service operations. 

## <a name="installation"></a>Installatie 

### <a name="step-1---install-python"></a>Stap 1: Python installeren

[Python 2.7x of Python 3.x](https://www.python.org/downloads/) is vereist.

### <a name="step-2---install-the-azure-cli"></a>Step 2 - Install the Azure CLI

Follow the [installation instruction](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) to setup Azure CLI in your environment. At a minimum, your Azure CLI version must be 2.0.24 or above. Gebruik `az –version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen. Een eenvoudige manier om Azure CLI 2.0 te installeren in Windows is de [MSI](https://aka.ms/InstallAzureCliWindows) te downloaden en installeren.

### <a name="step-3---install-iot-extension"></a>Stap 3: IoT-extensie installeren

[In het Leesmij-bestand bij de IoT-extensie](https://github.com/Azure/azure-iot-cli-extension) worden verschillende manieren voor het installeren van de extensie beschreven. De eenvoudigste manier is `az extension add --name azure-cli-iot-ext` uit te voeren. Na de installatie kunt u gebruikmaken van `az extension list` om de momenteel geïnstalleerde extensies te valideren of van `az extension show --name azure-cli-iot-ext` voor informatie over de IoT-extensie. U kunt `az extension remove --name azure-cli-iot-ext` gebruiken om de extensie te verwijderen.


## <a name="basic-device-provisioning-service-operations"></a>Basic Device Provisioning Service operations
The example shows you how to log in to your Azure account, create an Azure Resource Group (a container that holds related resources for an Azure solution), create an IoT Hub, create a Device Provisioning service, list the existing Device Provisioning services and create a linked IoT hub with CLI commands. 

Voer de hierboven beschreven installatiestappen uit voordat u begint. Als u geen Azure-account hebt, kunt u nu [een gratis account maken](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Log in to the Azure account
  
    az login

![aanmelding][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Create a resource group IoTHubBlogDemo in eastus

    az group create -l eastus -n IoTHubBlogDemo

![Een resourcegroep maken][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Create two Device Provisioning services

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Create Device Provisioning Service][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. List all the existing Device Provisioning services under this resource group

    az iot dps list --resource-group IoTHubBlogDemo

![List Device Provisioning Services][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Create an IoT Hub blogDemoHub under the newly created resource group

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![IoT Hub maken][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Link one existing IoT Hub to a Device Provisioning service

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Hub koppelen][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Het apparaat inschrijven
> * Het apparaat starten
> * Controleren of het apparaat is geregistreerd

Ga naar de volgende zelfstudie voor informatie over het inrichten van meerdere apparaten via hubs met gelijke taakverdeling. 

> [!div class="nextstepaction"]
> [Apparaten inrichten in meerdere IoT-hubs met gelijke taakverdeling](./tutorial-provision-multiple-hubs.md)
