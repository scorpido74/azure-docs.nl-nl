---
title: IP-Firewall configureren voor Azure Event Grid onderwerpen of domeinen (preview-versie)
description: In dit artikel wordt beschreven hoe u Firewall instellingen configureert voor Event Grid onderwerpen of domeinen.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299866"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>IP-Firewall configureren voor Azure Event Grid onderwerpen of domeinen (preview-versie)
Het onderwerp en het domein zijn standaard toegankelijk vanaf internet, zolang de aanvraag geldig is voor verificatie en autorisatie. Met IP-firewall kunt u dit nog verder beperken tot een aantal IPv4-adressen of IPv4-adresbereiken in CIDR-notatie [(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Uitgevers die afkomstig zijn van een ander IP-adres, worden geweigerd en ontvangen een 403 (verboden)-antwoord. Zie [netwerk beveiliging voor Event grid](network-security.md)voor meer informatie over de beveiligings functies van het netwerk die door Event grid worden ondersteund.

In dit artikel wordt beschreven hoe u IP-Firewall instellingen configureert voor Azure Event Grid onderwerpen of domeinen.

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie wordt beschreven hoe u de Azure Portal gebruikt voor het maken van binnenkomende IP-firewall regels. De stappen die in deze sectie worden weer gegeven, zijn voor onderwerpen. U kunt soort gelijke stappen gebruiken om inkomende IP-regels voor **domeinen**te maken. 

1. Ga in het [Azure Portal](https://portal.azure.com)naar het onderwerp of het domein van het gebeurtenis raster en schakel over naar het tabblad **netwerken** .
2. Selecteer **open bare netwerken** om alle netwerken, inclusief Internet, toegang te geven tot de resource. 

    U kunt het verkeer beperken met behulp van firewall regels op basis van IP. Geef één IPv4-adres of een bereik van IP-adressen in een CIDR-notatie (Classless Inter-Domain Routing) op. 

    ![Pagina open bare netwerken](./media/configure-firewall/public-networks-page.png)
3. Selecteer **privé-eind punten alleen** om alleen privé-eindpunt verbindingen toe te staan om toegang te krijgen tot deze bron. Gebruik het tabblad **verbindingen met privé-eind punten** op deze pagina om verbindingen te beheren. 

    ![Pagina open bare netwerken](./media/configure-firewall/private-endpoints-page.png)
4. Selecteer **Opslaan** op de werkbalk. 



## <a name="use-azure-cli"></a>Azure CLI gebruiken
In deze sectie wordt beschreven hoe u Azure CLI-opdrachten gebruikt om onderwerpen te maken met inkomende IP-regels. De stappen die in deze sectie worden weer gegeven, zijn voor onderwerpen. U kunt soort gelijke stappen gebruiken om inkomende IP-regels voor **domeinen**te maken. 


### <a name="enable-public-network-access-for-an-existing-topic"></a>Open bare toegang tot het netwerk inschakelen voor een bestaand onderwerp
De toegang tot het open bare netwerk is standaard ingeschakeld voor onderwerpen en domeinen. U kunt het verkeer beperken door binnenkomende IP-firewall regels te configureren. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Open bare netwerk toegang voor een bestaand onderwerp uitschakelen
Wanneer open bare netwerk toegang is uitgeschakeld voor een onderwerp of domein, is verkeer via open bare Internet niet toegestaan. Alleen verbindingen met een privé-eind punt hebben toegang tot deze resources. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>Onderwerp met inkomende IP-regels maken
Met de volgende voorbeeld CLI-opdracht maakt u een event grid-onderwerp met inkomende IP-regels in één stap. 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>Maak eerst een onderwerp en voeg vervolgens inkomende IP-regels toe
In dit voor beeld wordt eerst een event grid-onderwerp gemaakt en worden vervolgens binnenkomende IP-regels voor het onderwerp in een afzonderlijke opdracht toegevoegd. Ook worden de binnenkomende IP-regels bijgewerkt die in de tweede opdracht zijn ingesteld. 

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
In deze sectie wordt beschreven hoe u Azure PowerShell-opdrachten gebruikt om Azure Event Grid onderwerpen te maken met binnenkomende IP-firewall regels. De stappen die in deze sectie worden weer gegeven, zijn voor onderwerpen. U kunt soort gelijke stappen gebruiken om inkomende IP-regels voor **domeinen**te maken. 

### <a name="prerequisite"></a>Vereiste
Volg de instructies [voor het maken van een Azure AD-toepassing en Service-Principal](../active-directory/develop/howto-create-service-principal-portal.md) met behulp van de portal om een Azure Active Directory-toepassing te maken en noteer de volgende waarden:

- Directory-ID (Tenant)
- Toepassings-ID (client)
- Geheim van toepassing (client)

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Token en headers voorbereiden voor REST API-aanroepen 
Voer de volgende vereiste opdrachten uit om een verificatie token op te halen dat moet worden gebruikt met REST API-aanroepen en autorisatie-en andere header-informatie. 

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

### <a name="enable-public-network-access-for-an-existing-topic"></a>Open bare toegang tot het netwerk inschakelen voor een bestaand onderwerp
De toegang tot het open bare netwerk is standaard ingeschakeld voor onderwerpen en domeinen. U kunt het verkeer beperken door binnenkomende IP-firewall regels te configureren. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Open bare netwerk toegang voor een bestaand onderwerp uitschakelen
Wanneer open bare netwerk toegang is uitgeschakeld voor een onderwerp of domein, is verkeer via open bare Internet niet toegestaan. Alleen verbindingen met een privé-eind punt hebben toegang tot deze resources. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Een event grid-onderwerp maken met binnenkomende regels in één stap

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


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Eerst Event grid-onderwerp maken en vervolgens binnenkomende IP-regels toevoegen

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

* Zie [Event grid bericht bezorging bewaken](monitor-event-delivery.md)voor meer informatie over het bewaken van gebeurtenis leveringen.
* Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie over de verificatie sleutel.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
