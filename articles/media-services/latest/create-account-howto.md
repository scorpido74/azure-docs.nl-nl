---
title: Een Azure Media Services-account maken
description: In deze zelfstudie u de stappen doorlopen bij het maken van een Azure Media Services-account.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 2f1694825319ed8b8682c044e7e2282ed4c43dcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478797"
---
# <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

Als u wilt beginnen met het versleutelen, coderen, analyseren, beheren en streamen van media-inhoud in Azure, moet u een Media Services-account maken. Het Media Services-account moet worden gekoppeld aan een of meer opslagaccounts.

> [!NOTE]
> Het Media Services-account en alle gekoppelde opslagaccounts moeten zich in hetzelfde Azure-abonnement bevinden. Het wordt sterk aangeraden opslagaccounts te gebruiken op dezelfde locatie als het Media Services-account om aanvullende kosten voor latentie en uitgaande data te vermijden.

In dit artikel worden stappen beschreven voor het maken van een nieuw Azure Media Services-account. Kies uit de volgende tabbladen.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

De Azure-portal biedt een manier om snel een Azure Media Services-account te maken. U kunt uw account gebruiken voor toegang tot Media Services waarmee u media-inhoud in Azure kunt opslaan, versleutelen, coderen, beheren en streamen.

Momenteel u de [Azure-portal](https://portal.azure.com/) gebruiken om:

* Media Services v3 [Live Events](live-events-outputs-concept.md)beheren, 
* bekijken (niet beheren) v3 [Activa](assets-concept.md), 
* [informatie krijgen over toegang tot API's](access-api-portal.md). 

Voor alle andere beheertaken (bijvoorbeeld [Transformaties en Taken](transforms-jobs-concept.md) en [Contentbescherming),](content-protection-overview.md)gebruikt u de [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).

In dit artikel wordt uitgelegd hoe u een Media Services-account maakt met de Azure Portal.

### <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Klik **op +Een bron** > **Media** > **Media Services maken**.
1. Voer in de sectie **Een Media Services-account maken** vereiste waarden in.
    
    | Name | Beschrijving |
    | ---|---|
    |**Accountnaam**|Voer de naam in van het nieuwe Media Services-account. Voor de naam van een Media Services-account mogen alleen cijfers en kleine letters worden gebruikt. Spaties zijn niet toegestaan. De naam mag 3 tot 24 tekens lang zijn.|
    |**Abonnement**|Als u meer dan één abonnement hebt, selecteert u er een in de lijst met Azure-abonnementen waartoe u toegang hebt.|
    |**Resourcegroep**|Selecteer de nieuwe of bestaande resource. Een resourcegroep is een verzameling resources met dezelfde levenscyclus, dezelfde machtigingen en hetzelfde beleid. Lees [hier](../../azure-resource-manager/management/overview.md#resource-groups)meer .|
    |**Locatie**|Selecteer de geografische regio die wordt gebruikt om de media- en metagegevensrecords voor uw Media Services-account op te slaan. Deze regio wordt gebruikt om uw media te verwerken en te streamen. Alleen de beschikbare Media Services-regio's worden in de vervolgkeuzelijst weergegeven. |
    |**Opslagaccount**|Selecteer een opslagaccount om blob-opslag van de media-inhoud uit uw Media Services-account te bieden. U kunt een bestaand opslagaccount in dezelfde geografische regio als uw Media Services-account selecteren of u kunt een nieuw opslagaccount maken. Een nieuw opslagaccount wordt in dezelfde regio gemaakt. De regels voor opslagaccountnamen zijn hetzelfde als voor Media Services-accounts.<br/><br/>U kunt maar één **primaire** opslagaccount koppelen aan uw Media Services-account, maar een onbeperkt aantal **secundaire** opslagaccounts. U de Azure-portal gebruiken om secundaire opslagaccounts toe te voegen. Zie [Azure Storage-accounts met Azure Media Services-accounts](storage-account-concept.md)voor meer informatie .<br/><br/>Het Media Services-account en alle gekoppelde opslagaccounts moeten zich in hetzelfde Azure-abonnement bevinden. Het wordt sterk aangeraden opslagaccounts te gebruiken op dezelfde locatie als het Media Services-account om aanvullende kosten voor latentie en uitgaande data te vermijden.|
    
1. Selecteer **Vastmaken aan dashboard** om de voortgang van de implementatie van het account te bekijken.
1. Klik op **Maken** onder in het formulier.

    Wanneer uw Media Services-account wordt gemaakt, wordt een **standaard** streamingeindpunt toegevoegd aan uw account in de status **Gestopt.** Om te beginnen met het streamen van uw inhoud en te profiteren van [dynamische verpakking](dynamic-packaging-overview.md) en [dynamische versleuteling,](content-protection-overview.md)moet het streaming eindpunt van waaruit u inhoud wilt streamen, in de **status Actief** zijn. 

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>Het Azure-abonnement instellen

Geef in de volgende opdracht de id van het Azure-abonnement op dat u wilt gebruiken voor het Media Services-account. U kunt een lijst met abonnementen bekijken waartoe u toegang hebt door naar [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) te gaan.

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Gebruik de volgende opdracht om een resourcegroep te maken. Een Azure-resourcegroep is een logische container waarin resources, zoals Azure Media Services-accounts en bijbehorende Storage-accounts worden geïmplementeerd en beheerd.

U kunt `amsResourceGroup` vervangen door uw waarde.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Een opslagaccount maken

Als u een Media Services-account gaat maken, moet u de naam van een Azure Storage-accountresource opgeven. Het opgegeven opslagaccount wordt gekoppeld aan uw Media Services-account. Zie [Storage-accounts](storage-account-concept.md) voor meer informatie over het gebruik van Storage-accounts in Media Services.

U moet één **primaire** opslagaccount hebben en u een willekeurig aantal **secundaire** opslagaccounts hebben die zijn gekoppeld aan uw Media Services-account. Media Services ondersteunt **GPv2**-accounts (General-purpose v2) of **GPv1**-accounts (General-purpose v1). Blob-accounts kunt u niet instellen als **primaire** account. Zie [Opties voor Azure Storage-account](../../storage/common/storage-account-options.md) voor meer informatie over opslagaccounts. 

In dit voorbeeld maakt u een Standard LRS-account voor algemeen gebruik (v2). Als u wilt experimenteren met opslagaccounts, gebruikt u `--sku Standard_LRS`. Als u echter een SKU voor productie selecteert, kunt u overwegen om `--sku Standard_RAGRS` te gebruiken. Deze biedt geografische replicatie voor bedrijfscontinuïteit. Zie [Opslagaccounts](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest) voor meer informatie.
 
Met de volgende opdracht maakt u een Storage-account die wordt gekoppeld aan de Media Services-account. In het onderstaande script kunt u `storageaccountforams` door uw waarde vervangen. `amsResourceGroup`moet overeenkomen met de waarde die u in de vorige stap voor de resourcegroep hebt opgegeven. De naam van het opslagaccount moet een lengte hebben van minder dan 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

Met de volgende Azure CLI-opdracht wordt een nieuwe Media Services-account gemaakt. U de volgende `amsaccount` `storageaccountforams` waarden vervangen: (moet overeenkomen met `amsResourceGroup` de waarde die u hebt opgegeven voor uw opslagaccount) en (moet overeenkomen met de waarde die u hebt opgegeven voor de resourcegroep).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>Zie ook

* [Azure-CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Een secundaire opslag koppelen aan een Media Services-account](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Volgende stappen

[Een bestand streamen](stream-files-dotnet-quickstart.md)
