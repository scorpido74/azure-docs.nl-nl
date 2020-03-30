---
title: Privéeindpunten configureren voor Azure Event Grid-onderwerpen of -domeinen
description: In dit artikel wordt beschreven hoe u privéeindpunten configureert voor Azure Event Grid-onderwerpen of -domein.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: d08afe00c13a3f96b9526c3cb29804cfad688ddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299905"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains-preview"></a>Privéeindpunten configureren voor Azure Event Grid-onderwerpen of -domeinen (Voorbeeld)
U [privéeindpunten](../private-link/private-endpoint-overview.md) gebruiken om het binnendringen van gebeurtenissen rechtstreeks van uw virtuele netwerk toe te staan om uw onderwerpen en domeinen veilig via een [privéverbinding](../private-link/private-link-overview.md) toe te staan zonder via het openbare internet te gaan. Het privéeindpunt gebruikt een IP-adres uit de VNet-adresruimte voor uw onderwerp of domein. Zie [Netwerkbeveiliging](network-security.md)voor meer conceptuele informatie.

In dit artikel wordt beschreven hoe u privéeindpunten configureert voor onderwerpen of domeinen.

> [!IMPORTANT]
> De functie Privéeindpunten is alleen beschikbaar voor onderwerpen en domeinen in premiumlagen. Zie het artikel [Prijsprijsniveau bijwerken](update-tier.md) als u wilt upgraden van basislaag naar premiumlaag. 

## <a name="use-azure-portal"></a>Azure Portal gebruiken 
In deze sectie ziet u hoe u de Azure-portal gebruiken om een privéeindpunt voor een onderwerp of een domein te maken.

> [!NOTE]
> De stappen in deze sectie zijn meestal voor onderwerpen. U vergelijkbare stappen gebruiken om privéeindpunten voor **domeinen**te maken. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw onderwerp of domein.
2. Ga naar het tabblad **Netwerken** van uw onderwerppagina. Selecteer **+ Privéeindpunt** op de werkbalk.

    ![Privéeindpunt toevoegen](./media/configure-private-endpoints/add-button.png)
2. Voer de volgende stappen uit op de pagina **Basics:** 
    1. Selecteer een **Azure-abonnement** waarin u het privéeindpunt wilt maken. 
    2. Selecteer een **Azure-brongroep** voor het privéeindpunt. 
    3. Voer een **naam** in voor het eindpunt. 
    4. Selecteer het **gebied** voor het eindpunt. Uw privéeindpunt moet zich in dezelfde regio bevinden als uw virtuele netwerk, maar kan in een andere regio dan de bron voor privékoppelingen (in dit voorbeeld een gebeurtenisrasteronderwerp). 
    5. Selecteer vervolgens **volgende: >** knop Bron onder aan de pagina. 

      ![Privéeindpunt - basispagina](./media/configure-private-endpoints/basics-page.png)
3. Voer op de pagina **Resource** de volgende stappen uit: 
    1. Als u Verbinding maken met een Azure-bron in mijn map selecteert, voert u deze stappen uit als u Verbinding maakt **met een Azure-bron in mijn map.** In dit voorbeeld ziet u hoe u verbinding maakt met een Azure-bron in uw map. 
        1. Selecteer het **Azure-abonnement** waarin uw **onderwerp/domein** bestaat. 
        1. Selecteer **Microsoft.EventGrid/topics** of **Microsoft.EventGrid/domains** voor het **type Resource**voor **Resourcetype**.
        2. Selecteer **voor Resource**een onderwerp/domein in de vervolgkeuzelijst. 
        3. Controleer of de **subresource Target** is ingesteld op **onderwerp** of **domein** (op basis van het geselecteerde brontype).    
        4. Selecteer **Volgende: Configuratie >** knop onder aan de pagina. 

            ![Privéeindpunt - resourcepagina](./media/configure-private-endpoints/resource-page.png)
    2. Als u **Verbinding maken met een resource selecteert met een bron-id of een alias,** voert u de volgende stappen uit:
        1. Voer de id van de resource in. Bijvoorbeeld: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Voer **voor Resource** **onderwerp** of **domein**in. 
        3. (facultatief) Voeg een verzoekbericht toe. 
        4. Selecteer **Volgende: Configuratie >** knop onder aan de pagina. 

            ![Privéeindpunt - resourcepagina](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Op de pagina **Configuratie** selecteert u het subnet in een virtueel netwerk naar de plaats waar u het privéeindpunt wilt implementeren. 
    1. Selecteer een **virtueel netwerk**. Alleen virtuele netwerken in het momenteel geselecteerde abonnement en locatie worden vermeld in de vervolgkeuzelijst. 
    2. Selecteer een **subnet** in het virtuele netwerk dat u hebt geselecteerd. 
    3. Selecteer **Volgende: Tags >** knop onder aan de pagina. 

    ![Privéeindpunt - configuratiepagina](./media/configure-private-endpoints/configuration-page.png)
5. Maak op de pagina **Labels** tags tags (namen en waarden) die u wilt koppelen aan de private endpoint-bron. Selecteer vervolgens De knop **Controleren + maken** onder aan de pagina. 
6. Bekijk in de **optieR aan het maken ,** controleer alle instellingen en selecteer **Maken** om het privéeindpunt te maken. 

    ![Privéeindpunt - pagina & maken bekijken](./media/configure-private-endpoints/review-create-page.png)
    

## <a name="manage-private-link-connection"></a>Verbinding met privékoppelingen beheren

Wanneer u een privéeindpunt maakt, moet de verbinding worden goedgekeurd. Als de bron waarvoor u een privéeindpunt maakt, zich in uw map bevindt, u de verbindingsaanvraag goedkeuren, mits u over voldoende machtigingen beschikt. Als u verbinding maakt met een Azure-bron in een andere map, moet u wachten tot de eigenaar van die bron uw verbindingsaanvraag goedkeurt.

Er zijn vier provisioning staten:

| Serviceactie | Particuliere eindpuntstaat voor serviceconsumenten | Beschrijving |
|--|--|--|
| Geen | In behandeling | De verbinding wordt handmatig gemaakt en is in afwachting van goedkeuring van de eigenaar van de privékoppelingsbron. |
| Goedkeuren | Goedgekeurd | De verbinding is automatisch of handmatig goedgekeurd en is klaar om te worden gebruikt. |
| Afwijzen | Geweigerd | Verbinding is geweigerd door de eigenaar van de private link resource. |
| Verwijderen | De verbinding verbroken | Verbinding is verwijderd door de eigenaar van de privékoppelingsbron, het privéeindpunt wordt informatief en moet worden verwijderd voor het opschonen. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Een privé-eindpuntverbinding beheren
In de volgende secties ziet u hoe u een privé-eindpuntverbinding goedkeurt of afwijst. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Typ in de zoekbalk **gebeurtenisrasteronderwerpen** of **gebeurtenisrasterdomeinen**.
1. Selecteer het **onderwerp** of **domein** dat u wilt beheren.
1. Selecteer het tabblad **Netwerken.**
1. Als er verbindingen in behandeling zijn, wordt er een verbinding weergegeven met **In behandeling in** de inrichtingsstatus. 

### <a name="to-approve-a-private-endpoint"></a>Een privéeindpunt goedkeuren
U een privéeindpunt goedkeuren dat zich in de status in behandeling bevindt. Voer de volgende stappen uit om het goed te keuren: 

> [!NOTE]
> De stappen in deze sectie zijn meestal voor onderwerpen. U vergelijkbare stappen gebruiken om privéeindpunten voor **domeinen**goed te keuren. 

1. Selecteer het **privéeindpunt** dat u wilt goedkeuren en selecteer **Goedkeuren** op de werkbalk.

    ![Privéeindpunt - status in behandeling](./media/configure-private-endpoints/pending.png)
1. Voer in het dialoogvenster **Verbinding goedkeuren** een opmerking in (optioneel) en selecteer **Ja**. 

    ![Privé eindpunt - goedkeuren](./media/configure-private-endpoints/approve.png)
1. Controleer of u de status van het eindpunt als **goedgekeurd**ziet. 

    ![Privéeindpunt - goedgekeurde status](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Een privéeindpunt afwijzen
U een privéeindpunt weigeren dat zich in de status in behandeling of goedgekeurde status bevindt. Als u de volgende stappen wilt afwijzen, voert u de volgende stappen uit: 

> [!NOTE]
> De stappen in deze sectie zijn voor onderwerpen. U vergelijkbare stappen gebruiken om privéeindpunten voor **domeinen**af te wijzen. 

1. Selecteer het **privéeindpunt** dat u wilt weigeren en selecteer **Weigeren** op de werkbalk.

    ![Privé eindpunt - weigeren](./media/configure-private-endpoints/reject-button.png)
1. Voer in het dialoogvenster **Verbinding weigeren** een opmerking in (optioneel) en selecteer **Ja**. 

    ![Privé eindpunt - weigeren](./media/configure-private-endpoints/reject.png)
1. Controleer of u de status van het eindpunt als **Afgewezen ziet**. 

    ![Privéeindpunt - afgewezen status](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > U een privéeindpunt in de Azure-portal niet goedkeuren nadat het is afgewezen. 


## <a name="use-azure-cli"></a>Azure CLI gebruiken
Als u een privéeindpunt wilt maken, gebruikt u de [methode voor het maken van privéeindpunten van het AZ-netwerk,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) zoals in het volgende voorbeeld wordt weergegeven:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Zie documentatie voor het [privé-eindpunt van az-netwerk maken](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)voor beschrijvingen van de parameters die in het voorbeeld worden gebruikt. Een paar punten om op te merken in dit voorbeeld zijn: 

- Geef `private-connection-resource-id`bijvoorbeeld de bron-id van het **onderwerp** of **domein op**. In het voorgaande voorbeeld wordt het type: onderwerp gebruikt.
- voor `group-ids`, `topic` `domain`opgeven of . In het voorgaande `topic` voorbeeld wordt gebruikt. 

Als u een privéeindpunt wilt verwijderen, gebruikt u de methode voor [het verwijderen van privéeindpunten van het AZ-netwerk,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) zoals in het volgende voorbeeld wordt weergegeven:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> De stappen in deze sectie zijn voor onderwerpen. U vergelijkbare stappen gebruiken om privéeindpunten voor **domeinen**te maken. 

### <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken
Hier volgt een voorbeeldscript dat de volgende Azure-bronnen maakt:

- Resourcegroep
- Virtueel netwerk
- Subnet in het virtuele netwerk
- Azure-gebeurtenisrasteronderwerp (premiumlaag)
- Privéeindpunt voor het onderwerp

> [!NOTE]
> De stappen in deze sectie zijn voor onderwerpen. U vergelijkbare stappen gebruiken om privéeindpunten voor domeinen te maken.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# URI for the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicUri="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>?api-version=2020-04-01-preview"

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az rest --method put \
    --uri $topicUri \
    --body "{\""location\"":\""LOCATION\"", \""sku\"": {\""name\"": \""premium\""}, \""properties\"": {\""publicNetworkAccess\"":\""Disabled\""}}"

# verify that the topic was created.
az rest --method get \
    --uri $topicUri

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az rest --method get \
    --uri $topicUri

```

### <a name="approve-a-private-endpoint-connection"></a>Een privé-eindpuntverbinding goedkeuren
In het volgende voorbeeld VAN CLI-fragment ziet u hoe u een privé-eindpuntverbinding goedkeurt. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""approved\"",\""description\"":\""connection approved\"", \""actionsRequired\"": \""none\""}}}"
```


### <a name="reject-a-private-endpoint-connection"></a>Een privé-eindpuntverbinding weigeren
In het volgende voorbeeld VAN CLI-fragment ziet u hoe u een privé-eindpuntverbinding weigeren. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""rejected\"",\""description\"":\""connection rejected\"", \""actionsRequired\"": \""none\""}}}"
```


## <a name="use-powershell"></a>PowerShell gebruiken
In deze sectie ziet u hoe u een privéeindpunt voor een onderwerp of domein maakt met PowerShell. 

### <a name="prerequisite"></a>Vereiste
Volg instructies van [How to: Gebruik de portal om een Azure AD-toepassing en serviceprincipal te maken die toegang hebben tot bronnen](../active-directory/develop/howto-create-service-principal-portal.md) om een Azure Active Directory-toepassing te maken en de waarden voor Directory **(tenant) ID,** **Application (Client) ID**en Application **(client) geheim**te noteren. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Token- en kopteksten voorbereiden voor REST API-aanroepen 
Voer de volgende vereiste opdrachten uit om een verificatietoken te gebruiken met REST API-aanroepen en autorisatie en andere header-informatie. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Een subnet maken waarbij eindpuntnetwerkbeleid is uitgeschakeld

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Een gebeurtenisrasteronderwerp maken met een privéeindpunt

> [!NOTE]
> De stappen in deze sectie zijn voor onderwerpen. U vergelijkbare stappen gebruiken om privéeindpunten voor **domeinen**te maken. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="premium"}; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-04-01-preview"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Wanneer u controleert of het eindpunt is gemaakt, ziet u het resultaat vergelijkbaar met het volgende:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Een privé-eindpuntverbinding goedkeuren
In het volgende voorbeeld powershell-fragment ziet u hoe u een privéeindpunt goedkeurt. 

> [!NOTE]
> De stappen in deze sectie zijn voor onderwerpen. U vergelijkbare stappen gebruiken om privéeindpunten voor **domeinen**goed te keuren. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Een privé-eindpuntverbinding weigeren
In het volgende voorbeeld ziet u hoe u een privéeindpunt weigeren met PowerShell. U de GUID voor het privéeindpunt ophalen op basis van de vorige opdracht GET. 

> [!NOTE]
> De stappen in deze sectie zijn voor onderwerpen. U vergelijkbare stappen gebruiken om privéeindpunten voor **domeinen**af te wijzen. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" ` 
    -Headers $Headers
```

U de verbinding goedkeuren, zelfs nadat deze via API is geweigerd. Als u Azure-portal gebruikt, u een eindpunt dat is afgewezen, niet goedkeuren. 

## <a name="next-steps"></a>Volgende stappen
Zie [IP-firewall configureren voor azure event grid-onderwerpen of -domeinen voor](configure-firewall.md)meer informatie over het configureren van IP-firewall-instellingen.