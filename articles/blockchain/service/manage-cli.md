---
title: Azure Block Chain service beheren met Azure CLI
description: Azure Block Chain service beheren met Azure CLI
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 36b012c486c0c7d3303a81998e88f1605999c899
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170851"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Azure Block Chain service beheren met Azure CLI

Naast de Azure Portal, kunt u Azure CLI gebruiken voor het beheren van Block Chain leden en transactie knooppunten voor uw Azure Block Chain-service.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/bash](https://shell.azure.com/bash) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Als u liever de CLI lokaal installeert en gebruikt, raadpleegt u [Azure cli installeren](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

1. Meld u aan.

    Meld u aan met behulp van de opdracht [az login](/cli/azure/reference-index#az-login) als u een lokale installatie van de CLI gebruikt.

    ```azurecli
    az login
    ```

    Volg de weergegeven stappen in uw terminal om het verificatieproces te voltooien.

1. Installeer de Azure CLI-extensie.

    Wanneer u met extensieverwijzingen voor de Azure-CLI werkt, moet u eerst de extensie installeren.  Azure CLI-extensies geven u toegang tot experimentele opdrachten en opdrachten in een evaluatieversie die nog niet zijn verzonden als onderdeel van de kern-CLI.  Zie [Extensies gebruiken met Azure CLI](/cli/azure/azure-cli-extensions-overview) voor meer informatie over extensies, waaronder het bijwerken en verwijderen ervan.

    Installeer de [uitbrei ding voor de Azure Block Chain-Service](/cli/azure/ext/blockchain/blockchain) door de volgende opdracht uit te voeren:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Block Chain-lid maken

In het voor beeld [wordt een Block Chain-lid gemaakt](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create) in de Azure Block Chain-service die het quorum grootboek protocol in een nieuw consortium uitvoert.

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resource groep waar de Azure Block Chain-Service resources worden gemaakt. |
| **naam** | Een unieke naam die uw Azure Block Chain Service Block Chain-lid aanduidt. De naam wordt gebruikt voor het adres van het open bare eind punt. Bijvoorbeeld `myblockchainmember.blockchain.azure.com`. |
| **locatie** | Azure-regio waar het block Chain-lid wordt gemaakt. Bijvoorbeeld `eastus`. Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt. Functies zijn mogelijk niet beschikbaar in sommige regio's. |
| **wachtwoord** | Het wacht woord voor het standaard transactie knooppunt van het lid. Gebruik het wacht woord voor basis verificatie bij het maken van verbinding met het open bare eind punt van het standaard transactie knooppunt van het block Chain-lid. Het wacht woord moet aan drie van de volgende vier vereisten voldoen: de lengte moet tussen 12 & 72 tekens, 1 kleine letter, 1 hoofd letter, 1 cijfer en 1 speciaal teken zijn dat geen hekje (#), procent (%), komma (,), ster (*), back-quote ( \` ), dubbele aanhalings tekens ("), één aanhalings teken (), streepje (-) en semicolumn (;)|
| **Protocolsubstatus** | Block Chain-protocol. Op dit moment wordt het *quorum* protocol ondersteund. |
| **verband** | Naam van het consortium dat u wilt toevoegen of maken. Zie [Azure Blockchain Service-consortium](consortium.md) voor meer informatie over consortiums. |
| **consortium-Management-account-wacht woord** | Het wacht woord van het consortium account wordt ook wel het wacht woord van het lid-account genoemd. Het wachtwoord van het lidaccount wordt gebruikt voor het versleutelen van de persoonlijke sleutel voor het Ethereum-account dat voor het lid wordt gemaakt. U gebruikt het lidaccount en het wachtwoord van het lidaccount voor het beheer van consortiums. |
| **SKU** | Type laag. *Standard* of *Basic*. Gebruik de categorie *Basic* voor ontwikkeling, tests en het testen van concepten. Gebruik de categorie *Standard* voor implementaties van productiekwaliteit. U moet de categorie *Standard* ook gebruiken als u Blockchain Data Manager gebruikt of een groot aantal privé transacties verzendt. Wanneer een lid is gemaakt, kan de prijscategorie niet meer worden gewijzigd van Basic in Standard en andersom. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Wacht woorden of firewall regels voor Block Chain wijzigen

Voor beeld wordt het wacht woord van het [Block Chain-lid](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update), het consortium beheer wachtwoord en de firewall regel bijgewerkt.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resource groep waar de Azure Block Chain-Service resources worden gemaakt. |
| **naam** | Naam die uw Azure Block Chain service-lid aanduidt. |
| **wachtwoord** | Het wacht woord voor het standaard transactie knooppunt van het lid. Gebruik het wacht woord voor basis verificatie bij het maken van verbinding met het open bare eind punt van het standaard transactie knooppunt van het block Chain-lid. Het wacht woord moet aan drie van de volgende vier vereisten voldoen: de lengte moet tussen 12 & 72 tekens, 1 kleine letter, 1 hoofd letter, 1 cijfer en 1 speciaal teken zijn dat geen hekje (#), procent (%), komma (,), ster (*), back-quote ( \` ), dubbele aanhalings tekens ("), één aanhalings teken (), streepje (-) en semicolumn (;)|
| **consortium-Management-account-wacht woord** | Het wacht woord van het consortium account wordt ook wel het wacht woord van het lid-account genoemd. Het wachtwoord van het lidaccount wordt gebruikt voor het versleutelen van de persoonlijke sleutel voor het Ethereum-account dat voor het lid wordt gemaakt. U gebruikt het lidaccount en het wachtwoord van het lidaccount voor het beheer van consortiums. |
| **firewall-regels** | Begin-en eind-IP-adres voor de lijst met toegestane IP-adressen. |

## <a name="create-transaction-node"></a>Transactie knooppunt maken

[Maak een transactie knooppunt](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create) in een bestaand Block Chain-lid. Door transactie knooppunten toe te voegen, kunt u de beveiligings isolatie verhogen en de belasting distribueren. U kunt bijvoorbeeld een eind punt voor een trans actie-knoop punt hebben voor verschillende client toepassingen.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resource groep waar de Azure Block Chain-Service resources worden gemaakt. |
| **locatie** | Azure-regio van het block Chain-lid. |
| **lid-naam** | Naam die uw Azure Block Chain service-lid aanduidt. |
| **wachtwoord** | Het wacht woord voor het transactie knooppunt. Gebruik het wacht woord voor basis verificatie bij het maken van verbinding met het open bare eind punt van het transactie knooppunt. Het wacht woord moet aan drie van de volgende vier vereisten voldoen: de lengte moet tussen 12 & 72 tekens, 1 kleine letter, 1 hoofd letter, 1 cijfer en 1 speciaal teken zijn dat geen hekje (#), procent (%), komma (,), ster (*), back-quote ( \` ), dubbele aanhalings tekens ("), één aanhalings teken (), streepje (-) en semicolumn (;)|
| **naam** | Naam van het transactie knooppunt. |

## <a name="change-transaction-node-password"></a>Wacht woord van transactie knooppunt wijzigen

Voor beeld wordt het wacht woord van [een transactie knooppunt bijgewerkt](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update) .

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resource groep waarin de Azure Block Chain-Service resources bestaan. |
| **lid-naam** | Naam die uw Azure Block Chain service-lid aanduidt. |
| **wachtwoord** | Het wacht woord voor het transactie knooppunt. Gebruik het wacht woord voor basis verificatie bij het maken van verbinding met het open bare eind punt van het transactie knooppunt. Het wacht woord moet aan drie van de volgende vier vereisten voldoen: de lengte moet tussen 12 & 72 tekens, 1 kleine letter, 1 hoofd letter, 1 cijfer en 1 speciaal teken zijn dat geen hekje (#), procent (%), komma (,), ster (*), back-quote ( \` ), dubbele aanhalings tekens ("), één aanhalings teken (), streepje (-) en semicolumn (;)|
| **naam** | Naam van het transactie knooppunt. |

## <a name="list-api-keys"></a>API-sleutels weer geven

API-sleutels kunnen worden gebruikt voor toegang tot knoop punten, vergelijkbaar met de gebruikers naam en het wacht woord. Er zijn twee API-sleutels ter ondersteuning van het draaien van sleutels. Gebruik de volgende opdracht om [de API-sleutels weer te geven](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key).

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resource groep waarin de Azure Block Chain-Service resources bestaan. |
| **naam** | Naam van het block Chain-lid van de Azure Block Chain-Service |

## <a name="regenerate-api-keys"></a>API-sleutels opnieuw genereren

Gebruik de volgende opdracht om [uw API-sleutels opnieuw te genereren](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key).

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resource groep waarin de Azure Block Chain-Service resources bestaan. |
| **naam** | De naam van het block Chain-lid van de Azure Block Chain-service. |
| **keyName** | Vervang door \<keyValue\> Key1, Key2 of beide. |

## <a name="delete-a-transaction-node"></a>Een transactie knooppunt verwijderen

Voor beeld wordt [een trans actie-knoop punt van het block Chain-lid verwijderd](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resource groep waarin de Azure Block Chain-Service resources bestaan. |
| **lid-naam** | De naam van het block Chain-lid van de Azure Block Chain-service dat ook de naam van het transactie knooppunt bevat dat moet worden verwijderd. |
| **naam** | Naam van het transactie knooppunt dat moet worden verwijderd. |

## <a name="delete-a-blockchain-member"></a>Een Block Chain-lid verwijderen

Voor beeld wordt [een Block Chain-lid verwijderd](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resource groep waarin de Azure Block Chain-Service resources bestaan. |
| **naam** | De naam van het block Chain-lid van de Azure Block Chain-service dat moet worden verwijderd. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Toegang verlenen voor Azure AD-gebruiker

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parameter | Beschrijving |
|---------|-------------|
| **rolvak** | De naam van de Azure AD-rol. |
| **toegewezen gebruiker** | Gebruikers-ID voor Azure AD. Bijvoorbeeld: `user@contoso.com` |
| **ligt** | Het bereik van de roltoewijzing. Dit kan een Block Chain-lid of een transactie knooppunt zijn. |

**Voorbeeld:**

Toegang tot knoop punten verlenen aan block Chain- **lid**van Azure AD-gebruiker:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Voorbeeld:**

Toegang tot knoop punten verlenen voor Azure AD-gebruiker aan block Chain- **transactie knooppunt**:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Toegang tot knoop punten verlenen voor een Azure AD-groep of-toepassingsrol

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parameter | Beschrijving |
|---------|-------------|
| **rolvak** | De naam van de Azure AD-rol. |
| **toegewezen gebruiker-object-id** | Groeps-ID of toepassings-ID van Azure AD. |
| **ligt** | Het bereik van de roltoewijzing. Dit kan een Block Chain-lid of een transactie knooppunt zijn. |

**Voorbeeld:**

Toegang tot knoop punten **verlenen voor toepassingsrol**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD-knooppunt toegang verwijderen

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parameter | Beschrijving |
|---------|-------------|
| **rolvak** | De naam van de Azure AD-rol. |
| **toegewezen gebruiker** | Gebruikers-ID voor Azure AD. Bijvoorbeeld: `user@contoso.com` |
| **ligt** | Het bereik van de roltoewijzing. Dit kan een Block Chain-lid of een transactie knooppunt zijn. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [configureren van Azure Block Chain Service Trans Action-knoop punten met de Azure Portal](configure-transaction-nodes.md).
