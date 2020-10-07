---
title: 'Azure CLI: Open source FHIR server implementeren voor Azure-Azure API voor Azure'
description: In deze snelstartgids wordt uitgelegd hoe u de Open Source Microsoft FHIR-server voor Azure implementeert.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.custom: devx-track-azurecli
ms.openlocfilehash: 10af71afd8843e75d5df3be57c909c56a7abca01
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87843569"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Quickstart: Een Open Source FHIR-server implementeren met behulp van Azure CLI

In deze snelstartgids wordt uitgelegd hoe u een Open Source FHIR&reg;-server in Azure implementeert met behulp van Azure CLI.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Een resourcegroep maken

Kies een naam voor de resource groep die de ingerichte resources bevat en maak deze aan:

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>Sjabloon implementeren

De [GitHub-opslagplaats](https://github.com/Microsoft/fhir-server) van de Microsoft FHIR-server voor Azure bevat een sjabloon waarmee alle benodigde resources worden geïmplementeerd. Implementeer deze met:

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>controleren of de FHIR-server draait

Krijg een mogelijkheidsinstructie van de FHIR-server met:

```azurecli-interactive
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

het duurt een minuutje of iets langer voordat de server de eerste keer reageert.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijder dan de resourcegroep met de volgende stappen:

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de Microsoft Open Source FHIR-server voor Azure geïmplementeerd in uw abonnement. Als u wilt weten hoe u de FHIR-API kunt openen met behulp van Postman, gaat u verder met de zelfstudie Postman.
 
>[!div class="nextstepaction"]
>[Toegang tot FHIR-API met Postman](access-fhir-postman-tutorial.md)
