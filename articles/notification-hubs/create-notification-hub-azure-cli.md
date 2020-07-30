---
title: Quickstart - Een Azure notification hub maken met behulp van de Azure-CLI | Microsoft Docs
description: In deze zelfstudie leert u hoe u een Azure notification hub (meldingenhub) kunt maken met behulp van de Azure-CLI.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: d32bae48348e482e0e175760a416097ffbc17a1c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080944"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Quickstart: Een Azure notification hub maken met behulp van de Azure-CLI

Azure Notification Hubs biedt een eenvoudig te gebruiken en uitgeschaalde push-engine waarmee u vanuit elke back-end (cloud of on-premises) meldingen kunt verzenden naar ieder platform (iOS, Android, Windows, Kindle, Baidu, enzovoort). Zie voor meer informatie over de service [Wat is Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

In deze quickstart maakt u een notification hub met behulp van de Azure-CLI. De eerste sectie bevat de stappen voor het maken van een Notification Hubs-naamruimte.  De tweede sectie bevat de stappen voor het maken van een notification hub in een bestaande naamruimte.  U leert ook hoe u een aangepast toegangsbeleid kunt maken.  

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Voor Notification Hubs is versie 2.0.67 of hoger van de Azure-CLI vereist. Voer `az --version` uit om de versie en afhankelijke bibliotheken te vinden die zijn geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u CLI wilt installeren of upgraden.

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

1. Meld u aan.

   Meld u aan met behulp van de opdracht [az login](/cli/azure/reference-index#az-login) als u een lokale installatie van de CLI gebruikt.

    ```azurecli
    az login
    ```

    Volg de weergegeven stappen in uw terminal om het verificatieproces te voltooien.

2. Installeer de Azure CLI-extensie.

   Wanneer u met extensieverwijzingen voor de Azure-CLI werkt, moet u eerst de extensie installeren.  Azure CLI-extensies geven u toegang tot experimentele opdrachten en opdrachten in een evaluatieversie die nog niet zijn verzonden als onderdeel van de kern-CLI.  Zie [Extensies gebruiken met Azure CLI](/cli/azure/azure-cli-extensions-overview) voor meer informatie over extensies, waaronder het bijwerken en verwijderen ervan.

   Installeer de [extensie voor Notification Hubs](/cli/azure/ext/notification-hub/notification-hub) door de volgende opdracht uit te voeren:

    ```azurecli
    az extension add --name notification-hub
   ```

3. Maak een resourcegroep.

   Azure Notification Hubs moeten, zoals alle Azure-resources, worden geïmplementeerd in een resourcegroep. Met resourcegroepen kunt u gerelateerde Azure-resources organiseren en beheren.

   Maak voor deze quickstart een resourcegroep met de naam _spnhubrg_ op de locatie _eastus_ met behulp van de volgende [az group create](/cli/azure/group#az-group-create)-opdracht:

   ```azurecli
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Een Notification Hub-naamruimte maken

1. Maak een naamruimte voor uw notification hubs.

   Een naamruimte bevat één of meer hubs, en **de naam moet uniek zijn in alle Azure-abonnementen en ten minste zes tekens lang zijn**.  Gebruik de opdracht [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) om te controleren of een naam beschikbaar is.

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   De Azure-CLI beantwoordt uw beschikbaarheidsaanvraag door de volgende console-uitvoer weer te geven:

   ```output
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Let op de tweede regel in het Azure CLI-antwoord: `"isAvailable": true`.  Op deze regel staat `false` als de gewenste naam die u voor de naamruimte hebt opgegeven, beschikbaar is.  Zodra u hebt bevestigd dat de naam beschikbaar is, voert u de opdracht [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) uit om uw naamruimte te maken.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Als de `--name` die u bij de opdracht `az notification-hub namespace create` hebt opgegeven, niet beschikbaar is of niet voldoet aan [Naamgevingsregels en -beperkingen voor Azure-resources](../azure-resource-manager/management/resource-name-rules.md), antwoordt de Azure-CLI met de volgende console-uitvoer:

   ```output
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalied
   The specified service namespace is invalid.
   ```

   Als de eerste naam die u hebt geprobeerd niet lukt, selecteert u een andere naam voor uw nieuwe naamruimte en voert u de opdracht `az notification-hub namespace create` nogmaals uit.

   > [!NOTE]
   > Vanaf deze stap moet u de waarde van de parameter `--namespace` vervangen in elke Azure CLI-opdracht die u kopieert uit deze quickstart.

2. Haal een lijst met naamruimten op.

   Gebruik de opdracht [az notification-hub namespace list](/cli/azure/ext/notification-hub/notification-hub/namespace?view=azure-cli-latest#ext-notification-hub-az-notification-hub-namespace-list) om de details van uw nieuwe naamruimte te bekijken.  De parameter `--resource-group` is optioneel als u alle naamruimten voor een abonnement wilt zien.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Notification hubs maken

1. Maak uw eerste notification hub.

   Er kunnen nu één of meer notification hubs worden gemaakt in uw nieuwe naamruimte.  Voer de opdracht [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) uit om een notification hub te maken.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Maak een tweede notification hub.

   Er kunnen meerdere notification hubs worden gemaakt in één naamruimte.  Als u een tweede notification hub wilt maken in dezelfde naamruimte, voert u de opdracht `az notification-hub create` nogmaals uit met een andere hubnaam.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Haal een lijst met notification hubs op.

   Met elke uitgevoerde opdracht retourneert de Azure-CLI een bericht of deze is geslaagd, maar het is geruststellend een lijst met notification hubs te kunnen ophalen.  De opdracht [az notification-hub list](/cli/azure/ext/notification-hub/notification-hub?view=azure-cli-latest#ext-notification-hub-az-notification-hub-list) is ontworpen voor dit doeleinde.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-notification-hub-access-policies"></a>Werken met toegangsbeleid voor een notification hub

1. Vermeld toegangsbeleid voor een notification hub.

   Azure Notification Hubs maakt gebruik van [Shared Access Signature-beveiliging](./notification-hubs-push-notification-security.md) door middel van toegangsbeleid.  Er worden automatisch twee soorten beleid gemaakt wanneer u een notification hub maakt.  De verbindingsreeksen uit dit beleid zijn nodig om pushmeldingen te configureren.  De opdracht [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) biedt een lijst met beleidsnamen en hun respectieve resourcegroepen.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Gebruik niet het beleid _DefaultFullSharedAccessSignature_ in uw toepassing. Deze mag alleen in uw back-end worden gebruikt.  Gebruik alleen `Listen`-toegangsbeleid in uw clienttoepassing.

2. Maak een nieuwe autorisatieregel voor een notification hub.

   Als u aanvullende autorisatieregels met betekenisvolle namen wilt maken, kunt u uw eigen toegangsbeleid maken en aanpassen door de opdracht [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) te gebruiken.  De parameter `--rights` is een door spaties gescheiden lijst met de machtigingen die u wilt toewijzen.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Sleutels en verbindingsreeksen weergeven voor een toegangsbeleid voor een notification hub

   Er zijn twee sets sleutels en verbindingsreeksen voor elk toegangsbeleid.  U hebt ze later nodig om [een notification hub mee te configureren](./configure-notification-hub-portal-pns-settings.md).  Als u de sleutels en verbindingsreeksen wilt weergeven voor een toegangsbeleid voor een notification hub, gebruikt u de opdracht [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys).

   ```azurecli
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   #query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Een [Notification Hub-naamruimte](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) en een [notification hub](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) hebben elk een ander toegangsbeleid.  Zorg ervoor dat u het juiste referentiemateriaal over de Azure-CLI gebruikt wanneer u query’s uitvoert voor sleutels en verbindingsreeksen.

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [az group delete](/cli/azure/group) om de resourcegroep en alle bijbehorende resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Volgende stappen

* In deze quickstart hebt u een notification hub gemaakt. Zie [Pushmeldingen instellen in een notification hub](configure-notification-hub-portal-pns-settings.md) voor meer informatie over het configureren van de hub met PNS-instellingen (Platform Notification System).

* Ontdek de uitgebreide mogelijkheden voor het beheren van notification hubs met de Azure-CLI.

  [Volledige referentielijst voor Notification Hubs](/cli/azure/ext/notification-hub/notification-hub)

  [Referentielijst met Notification Hubs-naamruimten](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Referentielijst met Notification Hubs-autorisatieregels](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Referentielijst met Notification Hubs-referenties](/cli/azure/ext/notification-hub/notification-hub/credential)
