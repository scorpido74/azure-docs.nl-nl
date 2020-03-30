---
title: IP-firewall configureren voor azure-gebeurtenisrasteronderwerpen of -domeinen (voorbeeld)
description: In dit artikel wordt beschreven hoe u firewall-instellingen configureert voor gebeurtenisrasteronderwerpen of -domeinen.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299866"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>IP-firewall configureren voor azure-gebeurtenisrasteronderwerpen of -domeinen (voorbeeld)
Standaard zijn onderwerp en domein toegankelijk vanaf internet, zolang de aanvraag wordt geleverd met geldige verificatie en autorisatie. Met IP-firewall u deze verder beperken tot alleen een set IPv4-adressen of IPv4-adresbereiken in [CIDR-notatie (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Uitgevers die afkomstig zijn van een ander IP-adres worden afgewezen en ontvangen een reactie van 403 (Verboden). Zie [Netwerkbeveiliging voor gebeurtenisraster voor](network-security.md)meer informatie over netwerkbeveiligingsfuncties die worden ondersteund door gebeurtenisraster.

In dit artikel wordt beschreven hoe u IP-firewall-instellingen configureert voor onderwerpen of domeinen in Azure Event Grid.

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie ziet u hoe u de Azure-portal gebruiken om inkomende IP-firewallregels te maken. De stappen in deze sectie zijn voor onderwerpen. U vergelijkbare stappen gebruiken om binnenkomende IP-regels voor **domeinen**te maken. 

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar het onderwerp of domein van het gebeurtenisraster en ga naar het tabblad **Netwerken.**
2. Selecteer **Openbare netwerken** om alle netwerken, inclusief het internet, toegang te geven tot de bron. 

    U het verkeer beperken met behulp van IP-gebaseerde firewallregels. Geef één IPv4-adres of een reeks IP-adressen op in CIDR-notatie (Classless inter-domain routing). 

    ![Pagina openbare netwerken](./media/configure-firewall/public-networks-page.png)
3. Selecteer **Alleen privéeindpunten** om alleen privéeindpuntverbindingen toe te staan die toegang krijgen tot deze bron. Gebruik het tabblad **Privéeindpuntverbindingen** op deze pagina om verbindingen te beheren. 

    ![Pagina openbare netwerken](./media/configure-firewall/private-endpoints-page.png)
4. Selecteer **Opslaan** op de werkbalk. 



## <a name="use-azure-cli"></a>Azure CLI gebruiken
In deze sectie ziet u hoe u Azure CLI-opdrachten gebruikt om onderwerpen met binnenkomende IP-regels te maken. De stappen in deze sectie zijn voor onderwerpen. U vergelijkbare stappen gebruiken om binnenkomende IP-regels voor **domeinen**te maken. 


### <a name="enable-public-network-access-for-an-existing-topic"></a>Toegang tot openbare netwerken inschakelen voor een bestaand onderwerp
Standaard is de toegang tot het openbare netwerk ingeschakeld voor onderwerpen en domeinen. U verkeer beperken door binnenkomende IP-firewallregels te configureren. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Toegang tot openbare netwerken uitschakelen voor een bestaand onderwerp
Wanneer toegang tot openbare netwerken is uitgeschakeld voor een onderwerp of domein, is verkeer via openbaar internet niet toegestaan. Alleen privéeindpuntverbindingen hebben toegang tot deze bronnen. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>Onderwerp maken met binnenkomende ip-regels
Met de volgende voorbeeld-opdracht CLI maakt u in één stap een gebeurtenisrasteronderwerp met binnenkomende IP-regels. 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>Eerst onderwerp maken en vervolgens binnenkomende ip-regels toevoegen
In dit voorbeeld wordt eerst een gebeurtenisrasteronderwerp gemaakt en worden vervolgens binnenkomende IP-regels voor het onderwerp in een afzonderlijke opdracht toegevoegd. Het werkt ook de binnenkomende IP-regels die zijn ingesteld in de tweede opdracht. 

```azurecli-interactive

# create the event grid topic first
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\""}

# add inbound IP rules
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}

# later, update topic with additional ip rules or remove them. 
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""}, {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```


## <a name="use-powershell"></a>PowerShell gebruiken
In deze sectie ziet u hoe u Azure PowerShell-opdrachten gebruiken om Azure Event Grid-onderwerpen te maken met inkomende IP-firewallregels. De stappen in deze sectie zijn voor onderwerpen. U vergelijkbare stappen gebruiken om binnenkomende IP-regels voor **domeinen**te maken. 

### <a name="prerequisite"></a>Vereiste
Volg instructies van [How to: Gebruik de portal om een Azure AD-toepassing en serviceprincipal te maken die toegang hebben tot bronnen](../active-directory/develop/howto-create-service-principal-portal.md) om een Azure Active Directory-toepassing te maken en de volgende waarden op te merken:

- (Tenant-)id van de map
- Toepassings-id (Client)
- Toepassing (client) geheim

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Token- en kopteksten voorbereiden voor REST API-aanroepen 
Voer de volgende vereiste opdrachten uit om een verificatietoken te gebruiken met REST API-aanroepen en autorisatie- en andere headergegevens. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>Toegang tot openbare netwerken inschakelen voor een bestaand onderwerp
Standaard is de toegang tot het openbare netwerk ingeschakeld voor onderwerpen en domeinen. U verkeer beperken door binnenkomende IP-firewallregels te configureren. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Toegang tot openbare netwerken uitschakelen voor een bestaand onderwerp
Wanneer toegang tot openbare netwerken is uitgeschakeld voor een onderwerp of domein, is verkeer via openbaar internet niet toegestaan. Alleen privéeindpuntverbindingen hebben toegang tot deze bronnen. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Een gebeurtenisrasteronderwerp maken met binnenkomende regels in één stap

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Eerst een gebeurtenisrasteronderwerp maken en vervolgens binnenkomende ip-regels toevoegen

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"}, @{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Volgende stappen

* Zie [Gebeurtenisnetbezorging controleren](monitor-event-delivery.md)voor informatie over het bewaken van gebeurtenisleveringen.
* Zie [Beveiliging en verificatie van gebeurtenisraster](security-authentication.md)voor meer informatie over de verificatiesleutel.
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
