---
title: Webhooks om te reageren op registeracties
description: Meer informatie over het gebruik van webhooks om gebeurtenissen te activeren wanneer push- of pull-acties plaatsvinden in uw registerrepositories.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5e6fd2d9f4c7727365a8e2fe3893aafebfeb7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454372"
---
# <a name="using-azure-container-registry-webhooks"></a>Webhooks van Azure Container Registry gebruiken

Een Azure-containerregister slaat persoonlijke Docker-containerinstallatiekopieën op en beheert ze, vergelijkbaar met de manier waarop Docker Hub openbare Docker-installatiekopieën opslaat. Het kan ook repositories hosten voor [Helm-diagrammen](container-registry-helm-repos.md) (preview), een verpakkingsindeling om toepassingen naar Kubernetes te implementeren. U webhooks gebruiken om gebeurtenissen te activeren wanneer bepaalde acties plaatsvinden in een van uw registerrepositories. Webhooks kunnen reageren op gebeurtenissen op het registerniveau, of ze kunnen worden beperkt tot een specifieke repository-tag. Met een [geo-gerepliceerd](container-registry-geo-replication.md) register configureert u elke webhook om te reageren op gebeurtenissen in een specifieke regionale replica.

Zie [Webhook-schemaverwijzing](container-registry-webhook-reference.md)voor Azure Container Registry voor meer informatie over webhook-aanvragen.

## <a name="prerequisites"></a>Vereisten

* Azure-containerregister: maak een containerregister in uw Azure-abonnement. Gebruik bijvoorbeeld de [Azure-portal](container-registry-get-started-portal.md) of de [Azure CLI](container-registry-get-started-azure-cli.md). De [Azure Container Registry SKU's](container-registry-skus.md) hebben verschillende webhooks quota.
* Docker-CLI: installeer de [Docker-engine](https://docs.docker.com/engine/installation/) om uw lokale computer als een Docker-host in te stellen en de Docker-CLI-opdrachten te gebruiken.

## <a name="create-webhook---azure-portal"></a>Webhook maken - Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer naar het containerregister waarin u een webhook wilt maken.
1. Selecteer **Webhooks**onder **Services**.
1. Selecteer **Toevoegen op** de werkbalk webhook.
1. Vul het *webhookformulier maken* in met de volgende informatie:

| Waarde | Beschrijving |
|---|---|
| Webhook-naam | De naam die u aan de webhook wilt geven. Het mag alleen letters en cijfers bevatten, en moet 5-50 tekens in lengte. |
| Locatie | Geef voor een geo-gerepliceerd register het [Azure-gebied](container-registry-geo-replication.md) van de registerreplica op. 
| Service URI | De URI waar de webhook post meldingen moet sturen. |
| Aangepaste headers | Kopteksten die u wilt doorgeven aan het POST-verzoek. Ze moeten in "key: value" formaat. |
| Triggeracties | Acties die de webhook activeren. Acties omvatten afbeelding duwen, afbeelding verwijderen, Helm grafiek duwen, Helm grafiek verwijderen, en afbeelding quarantaine. U een of meer acties kiezen om de webhook te activeren. |
| Status | De status voor de webhook nadat deze is gemaakt. Het is standaard ingeschakeld. |
| Bereik | De scope waar de webhook mee werkt. Als dit niet is opgegeven, is het bereik voor alle gebeurtenissen in het register. Het kan worden opgegeven voor een repository of een tag met behulp van de indeling "repository:tag", of "repository:*" voor alle tags onder een repository. |

Voorbeeld webhook formulier:

![Gebruikersinterface voor het maken van ACR-webhooks in de Azure-portal](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Webhook maken - Azure CLI

Als u een webhook wilt maken met de Azure CLI, gebruikt u de opdracht [az acr-webhook create.](/cli/azure/acr/webhook#az-acr-webhook-create) Met de volgende opdracht wordt een webhook gemaakt voor alle gebeurtenissen voor het verwijderen van afbeeldingen in het register *mycontainerregistry:*

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook testen

### <a name="azure-portal"></a>Azure Portal

Voordat u de webhook gebruikt, u deze testen met de **Ping-knop.** Ping stuurt een algemeen POST-verzoek naar het opgegeven eindpunt en registreert het antwoord. Als u de ping-functie gebruikt, u controleren of u de webhook correct hebt geconfigureerd.

1. Selecteer de webhook die u wilt testen.
2. Selecteer **ping op**de bovenste werkbalk .
3. Controleer het antwoord van het eindpunt in de kolom **HTTP STATUS.**

![Gebruikersinterface voor het maken van ACR-webhooks in de Azure-portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure-CLI

Als u een ACR-webhook wilt testen met de Azure CLI, gebruikt u de opdracht [az acr webhook ping.](/cli/azure/acr/webhook#az-acr-webhook-ping)

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Gebruik de opdracht [az acr webhook list-events](/cli/azure/acr/webhook) om de resultaten te bekijken.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Webhook verwijderen

### <a name="azure-portal"></a>Azure Portal

Elke webhook kan worden verwijderd door de webhook te selecteren en vervolgens de knop **Verwijderen** in de Azure-portal.

### <a name="azure-cli"></a>Azure-CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Volgende stappen

### <a name="webhook-schema-reference"></a>Webhook-schemaverwijzing

Zie de naslaggids voor webhook-schema's voor meer informatie over de indeling en eigenschappen van de JSON-gebeurtenispayloads die worden uitgestoten door Azure Container Registry:

[Webhook-webhook-schemaverwijzing azure containerregister](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Gebeurtenisrastergebeurtenissen

Naast de native registry webhook-gebeurtenissen die in dit artikel worden besproken, kan Azure Container Registry gebeurtenissen uitzenden naar Gebeurtenisraster:

[Snelstart: containerregistergebeurtenissen naar gebeurtenisraster verzenden](container-registry-event-grid-quickstart.md)
