---
title: Levering van gebeurtenissen met beheerde service-identiteit
description: In dit artikel wordt beschreven hoe u de beheerde service-identiteit voor een Azure Event grid-onderwerp inschakelt. Gebruik dit om gebeurtenissen door te sturen naar ondersteunde bestemmingen.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 4d96f28b98cccada2ac5c77589acc6df1430bb02
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700652"
---
# <a name="event-delivery-with-managed-identity"></a>Levering van gebeurtenissen met beheerde identiteit
In dit artikel wordt beschreven hoe u de [beheerde service-identiteit](../active-directory/managed-identities-azure-resources/overview.md) voor een event grid-onderwerp of-domein inschakelt. Gebruik dit om gebeurtenissen door te sturen naar ondersteunde bestemmingen, zoals Service Bus-wacht rijen en-onderwerpen, Event hubs en opslag accounts.

Hier volgen de stappen die in dit artikel worden besproken:
1. Een onderwerp of domein maken met een door het systeem toegewezen identiteit (of) een bestaand onderwerp of domein bijwerken om identiteit in te scha kelen. 
2. Voeg de identiteit toe aan een geschikte rol (bijvoorbeeld: Service Bus gegevens verzender) op de bestemming (bijvoorbeeld: een Service Bus wachtrij)
3. Wanneer u gebeurtenis abonnementen maakt, moet u het gebruik van de identiteit inschakelen voor het leveren van gebeurtenissen aan de bestemming. 

## <a name="create-a-topic-or-domain-with-an-identity"></a>Een onderwerp of domein maken met een identiteit
Laten we eerst eens kijken hoe u een onderwerp of een domein met een door een systeem beheerde identiteit maakt.

### <a name="using-azure-portal"></a>Azure Portal gebruiken
U kunt de door het systeem toegewezen identiteit voor een onderwerp/domein inschakelen tijdens het maken van de Azure Portal. In de volgende afbeelding ziet u hoe u door het systeem beheerde identiteit voor een onderwerp inschakelt. In principe selecteert u de optie **systeem toegewezen identiteit inschakelen** op de pagina **Geavanceerd** van de wizard voor het maken van het onderwerp. U ziet deze optie ook op de pagina **Geavanceerd** van de wizard voor het maken van het domein. 

![Identiteit inschakelen tijdens het maken van een onderwerp](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Azure CLI gebruiken
U kunt ook Azure CLI gebruiken om een onderwerp of domein te maken met een door het systeem toegewezen identiteit. Gebruik de `az eventgrid topic create` opdracht met de `--identity` para meter ingesteld op `systemassigned` . Als u geen waarde voor deze para meter opgeeft, wordt de standaard waarde `noidentity` gebruikt. 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Op dezelfde manier kunt u de `az eventgrid domain create` opdracht gebruiken om een domein te maken met een door het systeem beheerde identiteit.

## <a name="enable-identity-for-an-existing-topic-or-domain"></a>Identiteit voor een bestaand onderwerp of domein inschakelen
In de laatste sectie hebt u geleerd hoe u door het systeem beheerde identiteit kunt inschakelen tijdens het maken van een onderwerp of een domein. In deze sectie leert u hoe u door het systeem beheerde identiteit kunt inschakelen voor een bestaand onderwerp of domein. 

### <a name="using-azure-portal"></a>Azure Portal gebruiken
1. Ga naar [Azure Portal](https://portal.azure.com)
2. Zoek naar **Event grid-onderwerpen** in de zoek balk.
3. Selecteer het **onderwerp** waarvoor u de beheerde identiteit wilt inschakelen. 
4. Ga naar het tabblad **identiteit** . 
5. Schakel de switch in om de identiteit in te scha kelen. 

    U kunt soort gelijke stappen gebruiken om identiteit in te scha kelen voor een event grid-domein.

### <a name="using-azure-cli"></a>Azure CLI gebruiken
Gebruik de `az eventgrid topic update` opdracht met `--identity` ingesteld op `systemassigned` om de door het systeem toegewezen identiteit in te scha kelen voor een bestaand onderwerp. Als u de identiteit wilt uitschakelen, geeft u `noidentity` de waarde op. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

De opdracht voor het bijwerken van een bestaand domein is vergelijkbaar ( `az eventgrid domain update` ).

## <a name="supported-destinations-and-role-based-access-check-rbac-roles"></a>Ondersteunde doelen en functies op basis van op rollen gebaseerde toegangs controle (RBAC)
Nadat u de identiteit voor het onderwerp of het domein van het gebeurtenis raster hebt ingeschakeld, maakt Azure automatisch een identiteit in de Azure Active Directory (Azure AD). Voeg deze identiteit toe aan de juiste RBAC-rollen zodat het onderwerp of het domein gebeurtenissen naar ondersteunde bestemmingen kan door sturen. U kunt bijvoorbeeld de identiteit toevoegen aan de rol **Azure Event hubs data Sender** voor een event hubs naam ruimte, zodat het event grid-onderwerp gebeurtenissen kan door sturen naar Event hubs in die naam ruimte.  

Azure Event Grid ondersteunt momenteel onderwerpen of domeinen die zijn geconfigureerd met door het systeem toegewezen beheerde identiteit voor het door sturen van gebeurtenissen naar de volgende bestemmingen. In deze tabel vindt u ook de rollen waarin de identiteit zich moet bevinden, zodat het onderwerp de gebeurtenissen kan door sturen.

| Doel | RBAC-rol | 
| ----------- | --------- | 
| Service Bus-wacht rijen en-onderwerpen | [Afzender van Azure Service Bus gegevens](../service-bus-messaging/authenticate-application.md#built-in-rbac-roles-for-azure-service-bus) |
| Event Hub | [Afzender van Azure Event Hubs gegevens](../event-hubs/authorize-access-azure-active-directory.md#built-in-rbac-roles-for-azure-event-hubs) | 
| Blob Storage | [Inzender voor Storage BLOB-gegevens](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) |
| Queue Storage |[Afzender gegevens bericht van opslag wachtrij](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) | 

## <a name="add-identity-to-rbac-roles-on-destinations"></a>Identiteit toevoegen aan RBAC-rollen op bestemmingen
In deze sectie wordt beschreven hoe u de identiteit voor uw onderwerp of domein toevoegt aan een RBAC-rol. 

### <a name="using-azure-portal"></a>Azure Portal gebruiken
U kunt de **Azure Portal** gebruiken om het onderwerp/de domein-id toe te wijzen aan een geschikte rol, zodat het onderwerp/het domein gebeurtenissen naar de bestemming kan door sturen. 

In het volgende voor beeld wordt een beheerde identiteit voor een event grid-onderwerp met de naam **msitesttopic** toegevoegd aan de rol voor het **verzenden van Azure Service Bus gegevens** voor een service bus **naam ruimte** die een wachtrij of onderwerp-resource bevat. Wanneer u aan de rol op het niveau van de naam ruimte toevoegt, kan het onderwerp gebeurtenissen door sturen naar alle entiteiten in de naam ruimte. 

1. Navigeer naar uw **Service Bus-naam ruimte** in de [Azure Portal](https://portal.azure.com). 
2. Selecteer **Access Control** in het linkerdeel venster. 
3. Selecteer **toevoegen** in de sectie **toewijzing van een rol toevoegen** . 
4. Voer op de pagina **een roltoewijzing toevoegen** de volgende stappen uit:
    1. Selecteer de rol. In dit geval is het **Azure Service Bus gegevens verzender**. 
    2. Selecteer de **identiteit** voor uw onderwerp of domein. 
    3. Selecteer **Opslaan** om de configuratie op te slaan.

De stappen zijn vergelijkbaar voor het toevoegen van een identiteit aan andere rollen die in de tabel worden genoemd. 

### <a name="using-azure-cli"></a>Azure CLI gebruiken
In het voor beeld in deze sectie wordt beschreven hoe u **Azure cli** gebruikt om een identiteit toe te voegen aan een RBAC-rol. De voorbeeld opdrachten zijn voor Event grid-onderwerpen. De opdrachten voor Event grid-domeinen zijn vergelijkbaar. 

#### <a name="get-principal-id-for-the-topics-system-identity"></a>Principal-ID ophalen voor de systeem identiteit van het onderwerp 
Haal eerst de principal-ID op van de door het systeem beheerde identiteit van het onderwerp en wijs de identiteit toe aan de juiste rollen.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Een roltoewijzing maken voor Event hubs in verschillende bereiken 
In het volgende CLI-voor beeld ziet u hoe u de identiteit van een onderwerp kunt toevoegen aan de rol van **Azure Event hubs data Sender** op het niveau van de naam ruimte of op Event hub niveau. Als u de roltoewijzing maakt in de naam ruimte, kan het onderwerp gebeurtenissen door sturen naar alle Event hubs in die naam ruimte. Als u op Event Hub niveau maakt, kan het onderwerp alleen gebeurtenissen door sturen naar die specifieke Event Hub. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-service-bus-topic-at-various-scopes"></a>Een roltoewijzing maken voor Service Bus onderwerp in verschillende bereiken 
In het volgende CLI-voor beeld ziet u hoe u de identiteit van een onderwerp kunt toevoegen aan de rol van de **Azure Service Bus gegevens afzender** op het niveau van de naam ruimte of op het niveau van service bus onderwerp. Als u de roltoewijzing in de naam ruimte maakt, kan het event grid-onderwerp gebeurtenissen voor alle entiteiten (Service Bus wacht rijen of onderwerpen) in die naam ruimte door sturen. Als u op het niveau van de Service Bus wachtrij of onderwerp maakt, kan het event grid-onderwerp alleen gebeurtenissen door sturen naar die specifieke Service Bus wachtrij of het onderwerp. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-identity"></a>Gebeurtenis abonnementen maken die gebruikmaken van de identiteit
Nadat u een onderwerp of een domein met een door een systeem beheerde identiteit hebt gemaakt en de identiteit hebt toegevoegd aan de juiste rol op de bestemming, kunt u abonnementen maken die gebruikmaken van de identiteit. 

### <a name="using-azure-portal"></a>Azure Portal gebruiken
Bij het maken van een gebeurtenis abonnement ziet u een optie om het gebruik van door het systeem toegewezen identiteit voor een eind punt in de sectie **EINDPUNT Details** in te scha kelen. 

![Identiteit inschakelen tijdens het maken van een gebeurtenis abonnement voor Service Bus wachtrij](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

U kunt ook het gebruik van door het systeem toegewezen identiteiten inschakelen voor onbestelbare berichten op het tabblad **extra functies** . 

![Door het systeem toegewezen identiteit inschakelen voor onbestelbare berichten](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="using-azure-cli---service-bus-queue"></a>Azure CLI-Service Bus wachtrij gebruiken 
In deze sectie leert u hoe u **Azure cli** kunt gebruiken om het gebruik van door het systeem toegewezen identiteit in te scha kelen voor het leveren van gebeurtenissen in een service bus wachtrij. De identiteit moet lid zijn van de rol **Azure Service Bus gegevens afzender** . Het moet ook lid zijn van de rol **Storage BLOB data Inzender** op het opslag account dat wordt gebruikt voor het onbestelen van berichten. 

#### <a name="define-variables"></a>Variabelen definiëren
Geef eerst waarden op voor de volgende variabelen die moeten worden gebruikt in de CLI-opdracht. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery"></a>een gebeurtenis abonnement maken met behulp van beheerde identiteit voor levering 
Met deze voorbeeld opdracht maakt u een gebeurtenis abonnement voor een event grid-onderwerp waarvoor het eindpunt type is ingesteld op **Service Bus wachtrij**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery-and-dead-lettering"></a>een gebeurtenis abonnement maken met behulp van beheerde identiteit voor levering en onbestelbare berichten
Met deze voorbeeld opdracht maakt u een gebeurtenis abonnement voor een event grid-onderwerp waarvoor het eindpunt type is ingesteld op **Service Bus wachtrij**. Er wordt ook aangegeven dat de door het systeem beheerde identiteit moet worden gebruikt voor onbestelbare berichten. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="azure-cli---event-hubs"></a>Azure CLI-Event Hubs 
In deze sectie leert u hoe u **Azure cli** kunt gebruiken om het gebruik van door het systeem toegewezen identiteit in te scha kelen voor het leveren van gebeurtenissen aan een event hub. De identiteit moet lid zijn van de rol **Azure Event hubs data Sender** . Het moet ook lid zijn van de rol **Storage BLOB data Inzender** op het opslag account dat wordt gebruikt voor het onbestelen van berichten. 

#### <a name="define-variables"></a>Variabelen definiëren
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>gebeurtenis abonnement maken met beheerde identiteit voor levering 
Met deze voorbeeld opdracht maakt u een gebeurtenis abonnement voor een event grid-onderwerp waarvoor het eindpunt type is ingesteld op **Event hubs**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Gebeurtenis abonnement maken met beheerde identiteit voor levering en deadletter 
Met deze voorbeeld opdracht maakt u een gebeurtenis abonnement voor een event grid-onderwerp waarvoor het eindpunt type is ingesteld op **Event hubs**. Er wordt ook aangegeven dat de door het systeem beheerde identiteit moet worden gebruikt voor onbestelbare berichten. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="azure-cli---azure-storage-queue"></a>Azure CLI-Azure Storage wachtrij 
In deze sectie leert u hoe u **Azure cli** kunt gebruiken om het gebruik van door het systeem toegewezen identiteit in te scha kelen voor het leveren van gebeurtenissen in een Azure Storage wachtrij. De identiteit moet lid zijn van de rol **Storage BLOB data Inzender** op het opslag account.

#### <a name="define-variables"></a>Variabelen definiëren  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>Gebeurtenis abonnement maken met beheerde identiteit voor levering 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Gebeurtenis abonnement maken met beheerde identiteit voor levering en deadletter 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```



## <a name="next-steps"></a>Volgende stappen
Zie [Wat zijn beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde service-identiteiten. 