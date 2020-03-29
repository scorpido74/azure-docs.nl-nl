---
title: Een IP-firewall configureren voor uw Azure Cosmos DB-account
description: Meer informatie over het configureren van IP-toegangscontrolebeleid voor firewallondersteuning op Azure Cosmos-accounts.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 1c24782285ac9b06d5499351eebe1693ade07297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78162941"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>IP-firewall configureren in Azure Cosmos DB

U de gegevens die zijn opgeslagen in uw Azure Cosmos DB-account beveiligen met behulp van IP-firewalls. Azure Cosmos DB ondersteunt IP-gebaseerde toegangscontroles voor inkomende firewallondersteuning. U een IP-firewall instellen op het Azure Cosmos DB-account met behulp van een van de volgende manieren:

* Vanuit Azure Portal
* Declaratief met behulp van een Azure Resource Manager-sjabloon
* Programmatisch via de Azure CLI of Azure PowerShell door de eigenschap **ipRangeFilter** bij te werken

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a>Een IP-firewall configureren met behulp van de Azure-portal

Als u het IP-toegangsbeheerbeleid in de Azure-portal wilt instellen, gaat u naar de Azure Cosmos DB-accountpagina en selecteert u **Firewall en virtuele netwerken** in het navigatiemenu. Wijzig de **toegang toestaan van** waarde naar geselecteerde **netwerken**en selecteer **Opslaan**.

![Schermafbeelding van het openen van de Firewall-pagina in de Azure-portal](./media/how-to-configure-firewall/azure-portal-firewall.png)

Wanneer IP-toegangsbeheer is ingeschakeld, biedt de Azure-portal de mogelijkheid om IP-adressen, IP-adresbereiken en switches op te geven. Switches maken toegang tot andere Azure-services en de Azure-portal mogelijk. De volgende secties geven details over deze schakelaars.

> [!NOTE]
> Nadat u een IP-toegangsbeheerbeleid hebt ingeschakeld voor uw Azure Cosmos DB-account, worden alle aanvragen voor uw Azure Cosmos DB-account van machines buiten de toegestane lijst met IP-adresbereiken afgewezen. Browsen door de Azure Cosmos DB-bronnen van de portal is ook geblokkeerd om de integriteit van toegangscontrole te garanderen.

### <a name="allow-requests-from-the-azure-portal"></a>Aanvragen van de Azure-portal toestaan

Wanneer u een IP-toegangsbeheerbeleid programmatisch inschakelt, moet u het IP-adres voor de Azure-portal toevoegen aan de eigenschap **ipRangeFilter** om de toegang te behouden. De PORTAL IP-adressen zijn:

|Regio|IP-adres|
|------|----------|
|Duitsland|51.4.229.218|
|China|139.217.8.252|
|US Gov|52.244.48.71|
|Alle andere regio's|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

U aanvragen inschakelen om toegang te krijgen tot de Azure-portal door de optie **Toegang toestaan vanuit Azure-portal** te selecteren, zoals in de volgende schermafbeelding wordt weergegeven:

![Schermafbeelding van hoe u Azure-portaltoegang inschakelen](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Aanvragen van globale Azure-datacenters of andere bronnen in Azure toestaan

Als u toegang krijgt tot uw Azure Cosmos DB-account van services die geen statisch IP bieden (bijvoorbeeld Azure Stream Analytics en Azure Functions), u de IP-firewall nog steeds gebruiken om de toegang te beperken. U toegang vanuit andere bronnen binnen azure inschakelen door de optie **Verbindingen accepteren vanuit Azure-datacenters** te selecteren, zoals in de volgende schermafbeelding wordt weergegeven:

![Schermafbeelding van het openen van de Firewall-pagina in de Azure-portal](./media/how-to-configure-firewall/enable-azure-services.png)

Wanneer u deze optie inschakelt, wordt het IP-adres `0.0.0.0` toegevoegd aan de lijst met toegestane IP-adressen. Het `0.0.0.0` IP-adres beperkt aanvragen tot uw Azure Cosmos DB-account vanuit het IP-bereik van Azure-datacenter. Met deze instelling wordt geen toegang van andere IP-adresbereiken tot uw Azure Cosmos DB-account toegestaan.

> [!NOTE]
> Met deze optie configureert u de firewall zodat alle aanvragen van Azure, inclusief aanvragen van de abonnementen van andere klanten die in Azure zijn geïmplementeerd, kunnen worden uitgevoerd. De lijst met IP's die door deze optie zijn toegestaan, is breed, dus het beperkt de effectiviteit van een firewallbeleid. Gebruik deze optie alleen als uw aanvragen niet afkomstig zijn van statische IP's of subnetten in virtuele netwerken. Als u deze optie kiest, u automatisch toegang krijgen vanuit de Azure-portal omdat de Azure-portal is geïmplementeerd in Azure.

### <a name="requests-from-your-current-ip"></a>Aanvragen van uw huidige IP

Om de ontwikkeling te vereenvoudigen, helpt de Azure-portal u bij het identificeren en toevoegen van het IP-adres van uw clientmachine aan de toegestane lijst. Apps waarop uw machine wordt uitgevoerd, hebben vervolgens toegang tot uw Azure Cosmos DB-account.

De portal detecteert automatisch het IP-adres van de client. Het kan het IP-adres van uw computer of het IP-adres van uw netwerkgateway zijn. Zorg ervoor dat u dit IP-adres verwijdert voordat u uw workloads in productie neemt.

Als u uw huidige IP wilt toevoegen aan de lijst met IP's, selecteert u **Mijn huidige IP toevoegen**. Selecteer vervolgens **Opslaan**.

![Schermafbeelding van een manier om firewall-instellingen voor het huidige IP-adres te configureren](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Aanvragen van cloudservices

In Azure zijn cloudservices een veelvoorkomende manier voor het hosten van servicelogica op het middenniveau met Azure Cosmos DB. Als u toegang tot uw Azure Cosmos DB-account vanuit een cloudservice wilt inschakelen, moet u het openbare IP-adres van de cloudservice toevoegen aan de toegestane lijst met IP-adressen die zijn gekoppeld aan uw Azure Cosmos DB-account door [het IP-toegangscontrolebeleid te configureren.](#configure-ip-policy) Dit zorgt ervoor dat alle rolexemplaren van cloudservices toegang hebben tot uw Azure Cosmos DB-account.

U IP-adressen voor uw cloudservices ophalen in de Azure-portal, zoals in de volgende schermafbeelding wordt weergegeven:

![Schermafbeelding van het openbare IP-adres voor een cloudservice die wordt weergegeven in de Azure-portal](./media/how-to-configure-firewall/public-ip-addresses.png)

Wanneer u uw cloudservice schaalt door rolexemplaren toe te voegen, hebben deze nieuwe exemplaren automatisch toegang tot het Azure Cosmos DB-account omdat ze deel uitmaken van dezelfde cloudservice.

### <a name="requests-from-virtual-machines"></a>Aanvragen van virtuele machines

U ook [virtuele machines](https://azure.microsoft.com/services/virtual-machines/) of [virtuele machineschaalsets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) gebruiken om services op het middenniveau te hosten met Azure Cosmos DB. Als u uw Cosmos DB-account zo wilt configureren dat het toegang biedt vanaf virtuele machines, moet u het openbare IP-adres van de virtuele machine en/of virtuele machineschaal configureren als een van de toegestane IP-adressen voor uw Azure Cosmos DB-account door [het IP-toegangsbeheerbeleid te configureren.](#configure-ip-policy)

U IP-adressen voor virtuele machines ophalen in de Azure-portal, zoals in de volgende schermafbeelding wordt weergegeven:

![Schermafbeelding van een openbaar IP-adres voor een virtuele machine die wordt weergegeven in de Azure-portal](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Wanneer u virtuele machine-exemplaren aan de groep toevoegt, krijgen ze automatisch toegang tot uw Azure Cosmos DB-account.

### <a name="requests-from-the-internet"></a>Verzoeken van het internet

Wanneer u uw Azure Cosmos DB-account vanaf een computer op internet bevindt, moet het IP-adres van de client of het IP-adresbereik van de machine worden toegevoegd aan de toegestane lijst met IP-adressen voor uw account.

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>Een IP-firewall configureren met behulp van een resourcebeheersjabloon

Als u toegangsbeheer wilt configureren voor uw Azure Cosmos DB-account, controleert u of de sjabloon Resourcemanager het **kenmerk ipRangeFilter** opgeeft met een lijst met toegestane IP-bereiken. Als u IP Firewall configureert naar een `locations` al geïmplementeerd Cosmos-account, moet u ervoor zorgen dat de array overeenkomt met wat momenteel wordt geïmplementeerd. U de `locations` array en andere eigenschappen niet tegelijkertijd wijzigen. Voor meer informatie en voorbeelden van Azure Resource Manager-sjablonen voor Azure Cosmos DB zie [Azure Resource Manager-sjablonen voor Azure Cosmos DB](resource-manager-samples.md)

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>Een IP-toegangsbeheerbeleid configureren met behulp van azure cli

In de volgende opdracht ziet u hoe u een Azure Cosmos DB-account maakt met IP-toegangscontrole:

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>Een IP-toegangsbeheerbeleid configureren met PowerShell

In het volgende script ziet u hoe u een Azure Cosmos DB-account maakt met IP-toegangscontrole:

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRangeFilter = "192.168.221.17,183.240.196.255,40.76.54.131"

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRangeFilter"=$ipRangeFilter
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Problemen met een IP-toegangsbeheerbeleid oplossen

U problemen met een IP-toegangsbeheerbeleid oplossen met de volgende opties:

### <a name="azure-portal"></a>Azure Portal

Door een IP-toegangscontrolebeleid in te schakelen voor uw Azure Cosmos DB-account, blokkeert u alle aanvragen voor uw account van machines buiten de toegestane lijst met IP-adresbereiken. Als u portalgegevensvlakbewerkingen wilt inschakelen, zoals het browsen door containers en het opvragen van documenten, moet u Azure-portalexpliciet toegang geven met behulp van het **firewalldeelvenster** in de portal.

### <a name="sdks"></a>SDK's

Wanneer u azure cosmos DB-bronnen gebruikt met Behulp van SDK's van machines die niet in de toegestane lijst staan, wordt een algemene **403 Verboden** reactie geretourneerd zonder aanvullende details. Controleer de toegestane IP-lijst voor uw account en controleer of de juiste beleidsconfiguratie wordt toegepast op uw Azure Cosmos DB-account.

### <a name="source-ips-in-blocked-requests"></a>Bron IP's in geblokkeerde aanvragen

Diagnostische logboekregistratie inschakelen op uw Azure Cosmos DB-account. Deze logboeken tonen elk verzoek en antwoord. De firewall-gerelateerde berichten worden geregistreerd met een 403 retourcode. Door deze berichten te filteren, u de bron-IP's voor de geblokkeerde aanvragen zien. Zie [diagnostische logboekregistratie van Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Aanvragen van een subnet met een serviceeindpunt voor Azure Cosmos DB ingeschakeld

Aanvragen van een subnet in een virtueel netwerk met een serviceeindpunt voor Azure Cosmos DB ingeschakeld, worden het virtuele netwerk en subnetidentiteit naar Azure Cosmos DB-accounts geleid. Deze aanvragen hebben niet het openbare IP van de bron, dus IP-filters weigeren ze. Als u toegang wilt verlenen vanaf specifieke subnetten in virtuele netwerken, voegt u een lijst met toegangscontrole toe zoals beschreven in [Het configureren van virtuele netwerk- en subnetgebaseerde toegang voor uw Azure Cosmos DB-account](how-to-configure-vnet-service-endpoint.md). Het kan tot 15 minuten duren voordat firewallregels van toepassing zijn.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor het configureren van een eindpunt voor virtuele netwerkservice voor uw Azure Cosmos DB-account:

* [Toegangsbeheer voor virtueel netwerk en subnet voor uw Azure Cosmos DB-account](vnet-service-endpoint.md)
* [Virtuele netwerk- en subnetgebaseerde toegang configureren voor uw Azure Cosmos DB-account](how-to-configure-vnet-service-endpoint.md)
