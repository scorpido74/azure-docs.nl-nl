---
title: Azure CLI-verwijzingen voor Azure-gegevens share
description: Azure CLI-referentie landings pagina voor Azure-gegevens share
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298464"
---
# <a name="azure-cli-for-azure-data-share"></a>Azure CLI voor Azure-gegevens share

De Azure-opdracht regel interface ([Azure cli](/cli/azure/what-is-azure-cli)) is een set opdrachten die wordt gebruikt om Azure-resources te maken en te beheren.  Het is beschikbaar in veel Azure-Services, waaronder Azure data share.  Er zijn meer dan 65 verschillende opdrachten voor het delen van gegevens.  Met deze opdrachten beschikt u over de mogelijkheid om effectief met de service te werken vanaf een opdracht regel.

## <a name="references-for-data-share"></a>Verwijzingen voor gegevens share

Alle Azure CLI-opdrachten voor Azure data share zijn momenteel uitbrei dingen voor de Azure CLI.  Een uitbrei ding geeft u toegang tot de opdrachten voor experimentele en voorlopige versies.  Meer informatie over uitbreidings verwijzingen vindt [u in extensies gebruiken met Azure cli](/cli/azure/azure-cli-extensions-overview).

|Naslag informatie voor Azure CLI |Beschrijving
|-|-|-|
| [AZ DataShare](/cli/azure/ext/datashare/datashare) | Alle opdrachten voor het beheren van de Azure-gegevens share.
| [AZ DataShare account](/cli/azure/ext/datashare/datashare/account) | Opdrachten voor het beheren van Azure-gegevens share-accounts.
| [AZ DataShare Consumer](/cli/azure/ext/datashare/datashare/consumer) | Opdrachten waarmee gebruikers Azure-gegevens shares kunnen beheren.
| [AZ DataShare-gegevensset](/cli/azure/ext/datashare/datashare/dataset) | Opdrachten voor providers voor het beheren van gegevens sets van Azure data share.
| [AZ DataShare-uitnodiging](/cli/azure/ext/datashare/datashare/invitation) | Opdrachten waarmee gebruikers uitnodigingen voor Azure-gegevens delen kunnen beheren.
| [AZ DataShare provider-share-abonnement](/cli/azure/ext/datashare/datashare/provider-share-subscription) | Opdrachten voor providers voor het beheren van Azure data share-abonnementen.
| [AZ DataShare Synchronization](/cli/azure/ext/datashare/datashare/synchronization)  | Opdrachten voor het beheren van de synchronisatie van Azure data share.
| [AZ DataShare Synchronization-setting](/cli/azure/ext/datashare/datashare/synchronization-setting)  | Opdrachten voor providers om synchronisatie-instellingen voor Azure data share te beheren.

## <a name="reference-examples"></a>Referentie voorbeelden

Er worden voor beelden gegeven van elke Naslag informatie voor Azure CLI. Hoewel u deze taken ook kunt volt ooien via de Azure Portal, moet u met de Azure CLI één opdracht regel gebruiken.  Hier volgen enkele code blokken om u een idee te geven van hoe eenvoudig het is om de Azure CLI te gebruiken.

Als u met Azure data share wilt werken, hebt u eerst een resource groep nodig.  Azure-resource groepen zijn eenvoudig te maken en te beheren met de Azure CLI.  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

Het is net zo eenvoudig om een gegevens share-account te maken.

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>Zie ook

* [Ga aan de slag met Azure cli](/cli/azure/get-started-with-azure-cli) voor meer informatie over de installatie en het aanmelden.

* Raadpleeg de documentatie van Azure CLI voor meer informatie over [kernen](/cli/azure/reference-index) en [extensies](/cli/azure/azure-cli-extensions-list) .
