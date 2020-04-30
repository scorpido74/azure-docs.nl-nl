---
title: 'Quick Start: een Azure notification hub maken met behulp van Azure CLI | Microsoft Docs'
description: In deze zelf studie leert u hoe u een Azure notification hub maakt met behulp van de Azure CLI.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80082449"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Snelstartgids: een Azure notification hub maken met behulp van Azure CLI

Azure Notification Hubs biedt een eenvoudig te gebruiken en uitgeschaalde push-engine waarmee u vanuit elke back-end (cloud of on-premises) meldingen kunt verzenden naar ieder platform (iOS, Android, Windows, Kindle, Baidu, enzovoort). Zie voor meer informatie over de service [Wat is Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

In deze Quick Start maakt u een notification hub met behulp van de Azure CLI. In de eerste sectie vindt u de stappen voor het maken van een notification hub-naam ruimte en het opvragen van toegang tot beleids gegevens voor die naam ruimte. In het tweede gedeelte vindt u stappen voor het maken van een notification hub in een bestaande naam ruimte.  U leert ook hoe u een aangepast toegangs beleid maakt.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Voor Notification Hubs is versie 2.0.67 of hoger van de Azure CLI vereist. Voer `az --version` uit om de geïnstalleerde versie en afhankelijke bibliotheken te vinden. Als u wilt installeren of upgraden, raadpleegt u [Azure cli installeren](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

1. Aanmelden.

   Meld u aan met de opdracht [AZ login](/cli/azure/reference-index#az-login) als u een lokale installatie van de CLI gebruikt.

    ```azurecli-interactive
    az login
    ```

    Volg de stappen die in de terminal worden weer gegeven om het verificatie proces te volt ooien.

2. Installeer de Azure CLI-extensie.

   Als u de Azure CLI-opdrachten voor notification hubs wilt uitvoeren, installeert u de Azure CLI- [extensie voor notification hubs](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Maak een resourcegroep.

   Azure notification hubs, zoals alle Azure-resources, moeten worden geïmplementeerd in een resource groep. Met resourcegroepen kunt u gerelateerde Azure-resources organiseren en beheren.

   Voor deze Quick Start maakt u een resource groep met de naam *spnhubrg* op de locatie *eastus* met de volgende opdracht [AZ Group Create](/cli/azure/group#az-group-create) :

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Een notification hub-naam ruimte maken

1. Een naam ruimte maken voor uw notification hubs

   Een naam ruimte bevat een of meer hubs en de naam moet uniek zijn binnen alle Azure-abonnementen.  Als u de beschik baarheid van de opgegeven service naam ruimte wilt controleren, gebruikt u de opdracht [AZ notification-hub naam ruimte controleren-Beschik baarheid](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) .  Voer de opdracht [AZ notification-hub naam ruimte maken](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) uit om een naam ruimte te maken.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Sleutels en verbindings reeksen voor het toegangs beleid van de naam ruimte weer geven.

   Er wordt automatisch een toegangs beleid met de naam **RootManageSharedAccessKey** gemaakt voor een nieuwe naam ruimte.  Elk toegangs beleid heeft twee sets sleutels en verbindings reeksen.  Als u de sleutels en verbindings reeksen voor de naam ruimte wilt weer geven, voert u de opdracht [AZ notification-hub naam ruimte autorisatie-Rule List-Keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) uit.

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Notification hubs maken

1. Maak uw eerste notification hub.

   Er kan nu een notification hub worden gemaakt in de nieuwe naam ruimte.  Voer de opdracht [AZ notification-hub Create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) uit om een notification hub te maken.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Maak een tweede notification hub.

   Meerdere notification hubs kunnen in één naam ruimte worden gemaakt.  Voer de `az notification-hub create` opdracht opnieuw uit met een andere naam voor de hub om een tweede notification hub in dezelfde naam ruimte te maken.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Werken met toegangs beleid

1. Maak een nieuwe autorisatie-regel voor een notification hub.

   Er wordt automatisch een toegangs beleid gemaakt voor elke nieuwe notification hub.  Als u uw eigen toegangs beleid wilt maken en aanpassen, gebruikt u de opdracht [AZ notification-hub Authorization-Rule Create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) .

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Lijst met toegangs beleid voor een notification hub.

   Als u wilt controleren welk toegangs beleid voor een notification hub bestaat, gebruikt u de opdracht [AZ notification-hub Authorization-Rule List](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) .

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Gebruik het **DefaultFullSharedAccessSignature** -beleid niet in uw toepassing. Deze mag alleen in uw back-end worden gebruikt.  Gebruik alleen **toegangs** beleid in uw client toepassing.

3. Sleutels en verbindings reeksen voor een notification hub-toegangs beleid weer geven

   Er zijn twee sets sleutels en verbindings reeksen voor elk toegangs beleid.  U hebt deze later nodig voor het afhandelen van push meldingen.  Als u de sleutels en verbindings reeksen voor een notification hub-toegangs beleid wilt weer geven, gebruikt u de opdracht [AZ notification-hub Authorization-Rule List-Keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) .

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Een [Notification hub-naam ruimte](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) en een [Notification hub](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) hebben afzonderlijke toegangs beleidsregels.  Zorg ervoor dat u de juiste Azure CLI-referentie gebruikt bij het uitvoeren van een query op sleutels en verbindings reeksen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, gebruikt u de opdracht [AZ Group delete](/cli/azure/group) om de resource groep en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Zie ook

Ontdek de volledige mogelijkheden voor het beheren van Notification hubs met de Azure CLI.

* [Volledige referentie lijst van Azure CLI Notification Hubs](/cli/azure/ext/notification-hub/notification-hub)
* [Referentie lijst voor Notification Hubs naam ruimte Azure CLI](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Referentie lijst voor de Azure CLI-verificatie regel Notification Hubs](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Referentie lijst voor Azure CLI Notification Hubs referentie](/cli/azure/ext/notification-hub/notification-hub/credential)
