---
title: Prijscategorie bijwerken van een onderwerp of domein van een Azure Event Grid
description: In dit artikel wordt beschreven hoe u de prijslaag van een Azure Event Grid-onderwerp of -domein (basic to premium, premium tot basic) bijwerkt met Azure-portal, Azure CLI en Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300139"
---
# <a name="update-pricing-tier"></a>Prijslaag bijwerken 
In dit artikel ziet u hoe u de prijscategorie van een Azure Event Grid-onderwerp of -domein bijwerkt met Azure-portal, Azure CLI en Azure PowerShell. 

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie ziet u hoe u de prijscategorie van een onderwerp of een domein in de Azure-portal wijzigt. 

### <a name="overview-page"></a>Overzichtspagina
U de prijscategorie van een onderwerp of een domein wijzigen op de pagina **Overzicht.** In het volgende voorbeeld ziet u hoe u een onderwerp upgraden van de basislaag naar de hoogste laag. De stappen om te downgraden van de premiumlaag naar de basislaag zijn vergelijkbaar.

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar het onderwerp of de domeinpagina. 
2. Selecteer **op** de pagina Overzicht de huidige prijscategorie (in het volgende voorbeeld is deze **basis**.)
    
    ![De huidige prijscategorie selecteren](./media/update-tier/select-tier.png)
3. Wijzig op de pagina **Prijscategorie** de laag en selecteer **OK**. 

    ![De prijslaag bijwerken](./media/update-tier/change-tier.png)
4. Controleer de status van de bewerking op de pagina **Meldingen.**

    ![Status bijwerken](./media/update-tier/status.png)    
5. Controleer of u de bijgewerkte laag op de **overzichtspagina** ziet. 

    ![Status bijwerken](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Netwerkpagina
U **upgraden** van de basislaag naar de premiumlaag op de **netwerkpagina.** U echter niet downgraden van de premiumlaag naar de basislaag op deze pagina. 

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar het onderwerp of de domeinpagina. 
2. Ga op de pagina **Netwerken** over naar het tabblad **Privé-eindpuntverbindingen (voorbeeld).** 
3. Als de huidige prijscategorie **eenvoudig**is, ziet u het volgende bericht. Selecteer het. 

    ![Laag bijwerken op pagina privéeindpuntverbindingen](./media/update-tier/private-endpoint-connections-page.png)
4. Selecteer Op de pagina **Update naar premium prijscategorie** de optie **Ja**. 
    
    ![Upgrade bevestigen](./media/update-tier/message-private-endpoint-connection.png)
5. Controleer de status van de bewerking op de pagina **Meldingen.**

    ![Status bijwerken](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Azure CLI gebruiken
In deze sectie ziet u hoe u Azure CLI-opdrachten gebruikt om de prijscategorie van een onderwerp of domein te wijzigen. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Een onderwerp upgraden van basic naar premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Een onderwerp downgraden van premium naar basic

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Een domein upgraden van basic naar premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Een domein downgraden van premium naar basic

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken
In deze sectie ziet u hoe u PowerShell-opdrachten gebruiken om de prijscategorie van een onderwerp of domein te wijzigen. 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Een onderwerp upgraden van basic naar premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Een onderwerp downgraden van premium naar basic

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Een domein upgraden van basic naar premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Een domein downgraden van premium naar basic

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Volgende stappen
Voor onderwerpen en domeinen van premiumlagen u privéeindpunten configureren om de toegang alleen vanuit geselecteerde virtuele netwerken te beperken. Zie [Privéeindpunten configureren](configure-private-endpoints.md)voor stapsgewijze instructies.
