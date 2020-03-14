---
title: Prijs categorie bijwerken van een Azure Event Grid onderwerp of-domein
description: In dit artikel wordt beschreven hoe u de prijs categorie van een Azure Event Grid onderwerp of domein (Basic naar Premium, Premium to Basic) bijwerkt met behulp van Azure Portal, Azure CLI en Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300139"
---
# <a name="update-pricing-tier"></a>Prijs categorie bijwerken 
In dit artikel wordt beschreven hoe u de prijs categorie van een Azure Event Grid onderwerp of domein bijwerkt met behulp van Azure Portal, Azure CLI en Azure PowerShell. 

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie ziet u hoe u de prijs categorie van een onderwerp of een domein in de Azure Portal kunt wijzigen. 

### <a name="overview-page"></a>Overzichts pagina
U kunt de prijs categorie van een onderwerp of een domein wijzigen op de pagina **overzicht** . In het volgende voor beeld ziet u hoe u een onderwerp van de Basic-laag bijwerkt naar de Premium-laag. De stappen voor het downgradeen van de Premium-laag naar de laag basis zijn vergelijkbaar.

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar het onderwerp of de domein pagina. 
2. Selecteer op de pagina **overzicht** de huidige prijs categorie (in het volgende voor beeld is dit de **basis**).
    
    ![De huidige prijs categorie selecteren](./media/update-tier/select-tier.png)
3. Op de pagina **prijs categorie** wijzigt u de laag en selecteert u **OK**. 

    ![De prijs categorie bijwerken](./media/update-tier/change-tier.png)
4. Controleer de status van de bewerking op de pagina **meldingen** .

    ![Update status](./media/update-tier/status.png)    
5. Controleer of de bijgewerkte laag wordt weer gegeven op de pagina **overzicht** . 

    ![Update status](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Netwerk pagina
U kunt een **upgrade uitvoeren** van de Basic-laag naar de Premium-laag op de pagina **netwerk** . U kunt geen downgrade van de Premium-laag naar de Basic-laag op deze pagina. 

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar het onderwerp of de domein pagina. 
2. Schakel op de pagina **netwerk** over naar het tabblad **Private endpoint Connections (preview)** . 
3. Als de huidige prijs categorie **Basic**is, wordt het volgende bericht weer gegeven. Selecteer deze. 

    ![Pagina laag bijwerken op privé-eindpunt verbindingen](./media/update-tier/private-endpoint-connections-page.png)
4. Selecteer op de pagina **Update to premium-prijs categorie** de optie **Ja**. 
    
    ![Upgrade bevestigen](./media/update-tier/message-private-endpoint-connection.png)
5. Controleer de status van de bewerking op de pagina **meldingen** .

    ![Update status](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Azure CLI gebruiken
In deze sectie wordt beschreven hoe u Azure CLI-opdrachten gebruikt om de prijs categorie van een onderwerp of domein te wijzigen. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Een onderwerp upgraden van Basic naar Premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Downgrade een onderwerp van Premium naar Basic

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Een domein upgraden van Basic naar Premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Downgrade een domein van Premium naar Basic

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken
In deze sectie wordt beschreven hoe u Power shell-opdrachten kunt gebruiken om de prijs categorie van een onderwerp of domein te wijzigen. 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Een onderwerp upgraden van Basic naar Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Downgrade een onderwerp van Premium naar Basic

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Een domein upgraden van Basic naar Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Downgrade een domein van Premium naar Basic

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Volgende stappen
Voor onderwerpen en domeinen van de Premium-laag kunt u privé-eind punten zo configureren dat de toegang alleen vanaf geselecteerde virtuele netwerken wordt beperkt. Zie voor stapsgewijze instructies [persoonlijke eind punten configureren](configure-private-endpoints.md).
