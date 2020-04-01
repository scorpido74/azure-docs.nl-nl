---
title: Snelstart - Een Azure-meldingshub maken met azure cli | Microsoft Documenten
description: In deze zelfstudie leert u hoe u een Azure-meldingshub maakt met de Azure CLI.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082449"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Snelstart: een Azure-meldingshub maken met de Azure CLI

Azure Notification Hubs biedt een eenvoudig te gebruiken en uitgeschaalde push-engine waarmee u vanuit elke back-end (cloud of on-premises) meldingen kunt verzenden naar ieder platform (iOS, Android, Windows, Kindle, Baidu, enzovoort). Zie voor meer informatie over de service [Wat is Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

In deze quickstart maakt u een meldingshub met de Azure CLI. In de eerste sectie u een naamruimte voor de meldingshub maken en beleidsgegevens voor querytoegang voor die naamruimte. In het tweede gedeelte u een meldingshub maken in een bestaande naamruimte.  U leert ook hoe u een aangepast toegangsbeleid maakt.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Notification Hubs vereist versie 2.0.67 of hoger van de Azure CLI. Voer `az --version` uit om de geïnstalleerde versie en afhankelijke bibliotheken te vinden. Zie Azure CLI [installeren](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

1. Aanmelden.

   Meld je aan met de [az-inlogopdracht](/cli/azure/reference-index#az-login) als je een lokale installatie van de CLI gebruikt.

    ```azurecli-interactive
    az login
    ```

    Volg de stappen die in uw terminal worden weergegeven om het verificatieproces te voltooien.

2. Installeer de Azure CLI-extensie.

   Als u de Azure CLI-opdrachten voor meldingshubs wilt uitvoeren, installeert u de Azure [CLI-extensie voor meldingshubs](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Maak een resourcegroep.

   Azure-meldingshubs moeten, net als alle Azure-bronnen, worden geïmplementeerd in een brongroep. Met resourcegroepen kunt u gerelateerde Azure-resources organiseren en beheren.

   Maak hiervoor een resourcegroep met de naam *spnhubrg* op de *locatie Eastus* met de volgende opdracht [az-groep maken:](/cli/azure/group#az-group-create)

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Een naamruimte voor een meldingshub maken

1. Een naamruimte maken voor uw meldingshubs

   Een naamruimte bevat een of meer hubs en de naam moet uniek zijn voor alle Azure-abonnementen.  Als u de beschikbaarheid van de opgegeven servicenaamruimte wilt controleren, gebruikt u de opdracht voor de controle-beschikbaarheid van de [az-meldingshub.](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability)  Voer de [naamruimte az-meldingshub uit](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) om een naamruimte te maken.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Lijstsleutels en verbindingstekenreeksen voor uw naamruimtetoegangsbeleid.

   Er wordt automatisch een toegangsbeleid met de naam **RootManageSharedAccessKey** gemaakt voor een nieuwe naamruimte.  Elk toegangsbeleid heeft twee sets sleutels en verbindingstekenreeksen.  Als u de sleutels en verbindingstekenreeksen voor de naamruimte wilt weergeven, voert u de opdracht [lijst-sleutels voor de autorisatieregel van az-melding-hub](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) uit.

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Meldingshubs maken

1. Maak je eerste meldingshub.

   Er kan nu een meldingshub worden gemaakt in de nieuwe naamruimte.  Voer de opdracht [az-meldingshub uit](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) om een meldingshub te maken.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Maak een tweede meldingshub.

   Meerdere meldingshubs kunnen in één naamruimte worden gemaakt.  Als u een tweede meldingshub in `az notification-hub create` dezelfde naamruimte wilt maken, voert u de opdracht opnieuw uit met een andere hubnaam.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Werken met toegangsbeleid

1. Maak een nieuwe autorisatieregel voor een meldingshub.

   Voor elke nieuwe meldingshub wordt automatisch een toegangsbeleid gemaakt.  Als u uw eigen toegangsbeleid wilt maken en aanpassen, gebruikt u de opdracht [autorisatie-regels voor az-melding-hub maken.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create)

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Lijsttoegangsbeleid voor een meldingshub.

   Als u wilt opvragen welke toegangsbeleidsregels er bestaan voor een meldingshub, gebruikt u de [opdracht lijst met autorisatieregel voor az-melding-hub.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list)

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Gebruik het **defaultFullSharedAccessSignature-beleid** niet in uw toepassing. Deze mag alleen in uw back-end worden gebruikt.  Gebruik alleen **het beleid voor toegang** tot luisteren in uw clienttoepassing.

3. Lijstsleutels en verbindingstekenreeksen voor een toegangsbeleid voor meldingshub

   Er zijn twee sets sleutels en verbindingstekenreeksen voor elk toegangsbeleid.  Je hebt ze later nodig om pushmeldingen te verwerken.  Als u de sleutels en verbindingstekenreeksen voor een toegangsbeleid voor meldingshub wilt vermelden, gebruikt u de [opdracht lijst-sleutels voor autorisatie-regels voor AZ-meldingen-hub.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Een [naamruimte voor meldingshub en](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) een [meldingshub](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) hebben een afzonderlijk toegangsbeleid.  Zorg ervoor dat u de juiste Azure CLI-verwijzing gebruikt bij het zoeken naar sleutels en verbindingstekenreeksen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, gebruikt u de opdracht verwijderen van de [AZ-groep](/cli/azure/group) om de brongroep en alle gerelateerde bronnen te verwijderen.

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Zie ook

Ontdek volledige mogelijkheden voor het beheren van meldingenhubs met de Azure CLI.

* [Volledige Azure CLI-referentielijst voor meldingenhubs](/cli/azure/ext/notification-hub/notification-hub)
* [Referentielijst voor naamruimte Azure CLI van meldingshubs](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Azure CLI-referentielijst voor autorisatievan meldingen](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Azure CLI-referentielijst voor meldingshubs](/cli/azure/ext/notification-hub/notification-hub/credential)
