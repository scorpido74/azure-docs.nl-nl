---
title: Azure Blockchain-service beheren met Azure CLI
description: Azure Blockchain-service beheren met Azure CLI
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455578"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Azure Blockchain-service beheren met Azure CLI

Naast de Azure-portal u Azure CLI gebruiken om blockchain-leden en transactieknooppunten te beheren voor uw Azure Blockchain-service.

Zorg ervoor dat u de nieuwste [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) hebt geïnstalleerd `az login`en ingelogd bent op een Azure-account met .

Vervang in de volgende `<parameter names>` voorbeelden voorbeeld door uw eigen waarden.

## <a name="create-blockchain-member"></a>Blockchain-lid maken

Voorbeeld maakt een blockchain-lid in Azure Blockchain Service dat het Quorum Ledger-protocol uitvoert in een nieuw consortium.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| Parameter | Beschrijving |
|---------|-------------|
| **resourcegroep** | Naam van resourcegroep waar Azure Blockchain Service-resources worden gemaakt. |
| **Naam** | Een unieke naam die uw Azure Blockchain Service blockchain-lid identificeert. De naam wordt gebruikt voor het adres van het openbare eindpunt. Bijvoorbeeld `myblockchainmember.blockchain.azure.com`. |
| **Locatie** | Azure-regio waar het blockchain-lid wordt gemaakt. Bijvoorbeeld `eastus`. Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt. |
| **wachtwoord** | Het wachtwoord van het ledenaccount. Het wachtwoord van het ledenaccount wordt gebruikt om te verifiëren voor het openbare eindpunt van het blockchain-lid met behulp van basisverificatie. Het wachtwoord moet voldoen aan drie van de volgende vier vereisten: de lengte moet tussen 12 & 72 tekens, 1 kleine lettersteken, 1 hoofdletterteken, 1 getal en 1\`speciaal teken dat geen getalteken is(#), procent(), komma(,), ster(*), terugquote( ), dubbele aanhaling("), enkele aanhaling('), dash(-) en semicolumn(;)|
| **Protocol** | Openbare voorbeeld ondersteunt Quorum. |
| **Consortium** | Naam van het consortium om toe te treden of te maken. |
| **consortiumManagementAccountPassword** | Het wachtwoord voor consortiumbeheer. Het wachtwoord wordt gebruikt voor deelname aan een consortium. |
| **regelNaam** | Regelnaam voor het whitelisten van een IP-adresbereik. Optionele parameter voor firewallregels.|
| **startIpAddress** | Begin met het IP-adresbereik voor whitelisting. Optionele parameter voor firewallregels. |
| **eindIpAdres** | Einde van het IP-adresbereik voor whitelisting. Optionele parameter voor firewallregels. |
| **skuName** | Niveautype. Gebruik S0 voor Standard en B0 voor Basic. |

## <a name="change-blockchain-member-password"></a>Het wachtwoord van blockchain-leden wijzigen

Voorbeeld verandert het wachtwoord van een blockchain-lid.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parameter | Beschrijving |
|---------|-------------|
| **resourcegroep** | Naam van resourcegroep waar Azure Blockchain Service-resources worden gemaakt. |
| **Naam** | Naam die uw Azure Blockchain Service-lid identificeert. |
| **wachtwoord** | Het wachtwoord van het ledenaccount. Het wachtwoord moet voldoen aan drie van de volgende vier vereisten: de lengte moet tussen 12 & 72 tekens, 1 kleine lettersteken, 1 hoofdletterteken, 1 getal en 1\`speciaal teken dat geen getalteken is(#), procent(), komma(,), ster(*), terugquote( ), dubbele offerte("), enkele offerte('), dash(-) en puntkomma(;)). |

## <a name="create-transaction-node"></a>Transactieknooppunt maken

Maak een transactieknooppunt in een bestaand blockchain-lid. Door transactieknooppunten toe te voegen, u de beveiligingsisolatie verhogen en de belasting verdelen. U bijvoorbeeld een eindpunt voor transactieknooppunt hebben voor verschillende clienttoepassingen.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| Parameter | Beschrijving |
|---------|-------------|
| **resourcegroep** | Naam van resourcegroep waar Azure Blockchain Service-resources worden gemaakt. |
| **Naam** | Naam van het Azure Blockchain Service blockchain-lid dat ook de nieuwe naam van het transactieknooppunt bevat. |
| **Locatie** | Azure-regio waar het blockchain-lid wordt gemaakt. Bijvoorbeeld `eastus`. Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt. |
| **wachtwoord** | Het wachtwoord van het transactieknooppunt. Het wachtwoord moet voldoen aan drie van de volgende vier vereisten: de lengte moet tussen 12 & 72 tekens, 1 kleine lettersteken, 1 hoofdletterteken, 1 getal en 1\`speciaal teken dat geen getalteken is(#), procent(), komma(,), ster(*), terugquote( ), dubbele offerte("), enkele offerte('), dash(-) en puntkomma(;)). |
| **regelNaam** | Regelnaam voor het whitelisten van een IP-adresbereik. Optionele parameter voor firewallregels. |
| **startIpAddress** | Begin met het IP-adresbereik voor whitelisting. Optionele parameter voor firewallregels. |
| **eindIpAdres** | Einde van het IP-adresbereik voor whitelisting. Optionele parameter voor firewallregels.|

## <a name="change-transaction-node-password"></a>Wachtwoord voor transactieknooppunt wijzigen

Als voorbeeld wordt het wachtwoord van een transactieknooppunt gewijzigd.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Parameter | Beschrijving |
|---------|-------------|
| **resourcegroep** | Naam van de brongroep waar Azure Blockchain Service-resources bestaan. |
| **Naam** | Naam van het Azure Blockchain Service blockchain-lid dat ook de nieuwe naam van het transactieknooppunt bevat. |
| **wachtwoord** | Het wachtwoord van het transactieknooppunt. Het wachtwoord moet voldoen aan drie van de volgende vier vereisten: de lengte moet tussen 12 & 72 tekens, 1 kleine lettersteken, 1 hoofdletterteken, 1 getal en 1\`speciaal teken dat geen getalteken is(#), procent(), komma(,), ster(*), terugquote( ), dubbele offerte("), enkele offerte('), dash(-) en puntkomma(;)). |

## <a name="change-consortium-management-account-password"></a>Wachtwoord van consortiumbeheeraccount wijzigen

De managementaccount van het consortium wordt gebruikt voor het beheer van het lidmaatschap van het consortium. Elk lid is uniek geïdentificeerd door een consortium management account en u het wachtwoord van dit account te wijzigen met de volgende opdracht.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parameter | Beschrijving |
|---------|-------------|
| **resourcegroep** | Naam van resourcegroep waar Azure Blockchain Service-resources worden gemaakt. |
| **Naam** | Naam die uw Azure Blockchain Service-lid identificeert. |
| **consortiumManagementAccountPassword** | Het wachtwoord van het consortiumbeheeraccount. Het wachtwoord moet voldoen aan drie van de volgende vier vereisten: de lengte moet tussen 12 & 72 tekens, 1 kleine lettersteken, 1 hoofdletterteken, 1 getal en 1\`speciaal teken dat geen getalteken is(#), procent(), komma(,), ster(*), terugquote( ), dubbele offerte("), enkele offerte('), dash(-) en puntkomma(;)). |
  
## <a name="update-firewall-rules"></a>Firewallregels bijwerken

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parameter | Beschrijving |
|---------|-------------|
| **resourcegroep** | Naam van de brongroep waar Azure Blockchain Service-resources bestaan. |
| **Naam** | Naam van het Azure Blockchain Service blockchain-lid. |
| **regelNaam** | Regelnaam voor het whitelisten van een IP-adresbereik. Optionele parameter voor firewallregels.|
| **startIpAddress** | Begin met het IP-adresbereik voor whitelisting. Optionele parameter voor firewallregels.|
| **eindIpAdres** | Einde van het IP-adresbereik voor whitelisting. Optionele parameter voor firewallregels.|

## <a name="list-api-keys"></a>API-sleutels aanbieden

API-sleutels kunnen worden gebruikt voor toegang tot het knooppunt, vergelijkbaar met gebruikersnaam en wachtwoord. Er zijn twee API-sleutels ter ondersteuning van sleutelrotatie. Gebruik de volgende opdracht om uw API-sleutels weer te geven.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beschrijving |
|---------|-------------|
| **resourcegroep** | Naam van de brongroep waar Azure Blockchain Service-resources bestaan. |
| **Naam** | Naam van het Azure Blockchain Service blockchain-lid dat ook de nieuwe naam van het transactieknooppunt bevat. |

## <a name="regenerate-api-keys"></a>API-sleutels regenereren

Gebruik de volgende opdracht om uw API-sleutels opnieuw te genereren.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| Parameter | Beschrijving |
|---------|-------------|
| **resourcegroep** | Naam van de brongroep waar Azure Blockchain Service-resources bestaan. |
| **Naam** | Naam van het Azure Blockchain Service blockchain-lid dat ook de nieuwe naam van het transactieknooppunt bevat. |
| **toetsNaam** | Vervang \<keyValue\> door key1 of key2. |

## <a name="delete-a-transaction-node"></a>Een transactieknooppunt verwijderen

Voorbeeld verwijdert een blockchain-lid transactieknooppunt.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beschrijving |
|---------|-------------|
| **resourcegroep** | Naam van de brongroep waar Azure Blockchain Service-resources bestaan. |
| **Naam** | Naam van het Azure Blockchain Service blockchain-lid dat ook de naam van het transactieknooppunt bevat dat moet worden verwijderd. |

## <a name="delete-a-blockchain-member"></a>Een blockchain-lid verwijderen

Voorbeeld verwijdert een blockchain-lid.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beschrijving |
|---------|-------------|
| **resourcegroep** | Naam van de brongroep waar Azure Blockchain Service-resources bestaan. |
| **Naam** | Naam van het Azure Blockchain Service blockchain-lid dat moet worden verwijderd. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Toegang verlenen aan Azure AD-gebruiker

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parameter | Beschrijving |
|---------|-------------|
| **Role** | Naam van de Azure AD-rol. |
| **Gevolmachtigde** | Azure AD-gebruikers-id. Bijvoorbeeld: `user@contoso.com` |
| **Scope** | Bereik van de roltoewijzing. Kan een blockchain-lid of transactieknooppunt zijn. |

**Voorbeeld:**

Node-toegang verlenen voor Azure AD-gebruiker aan **blockchain-lid:**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Voorbeeld:**

Node-toegang verlenen voor Azure AD-gebruiker tot **blockchain-transactieknooppunt:**

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Node-toegang verlenen voor Azure AD-groep of toepassingsrol

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parameter | Beschrijving |
|---------|-------------|
| **Role** | Naam van de Azure AD-rol. |
| **assigne-object-id** | Azure AD-groeps-id of toepassings-id. |
| **Scope** | Bereik van de roltoewijzing. Kan een blockchain-lid of transactieknooppunt zijn. |

**Voorbeeld:**

Toegang tot knooppunt verlenen voor **toepassingsrol**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD-knooppunttoegang verwijderen

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parameter | Beschrijving |
|---------|-------------|
| **Role** | Naam van de Azure AD-rol. |
| **Gevolmachtigde** | Azure AD-gebruikers-id. Bijvoorbeeld: `user@contoso.com` |
| **Scope** | Bereik van de roltoewijzing. Kan een blockchain-lid of transactieknooppunt zijn. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [configureren van Azure Blockchain Service-transactieknooppunten met de Azure-portal.](configure-transaction-nodes.md)
