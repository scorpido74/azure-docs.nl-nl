---
title: Een Azure Media Services-account maken
description: In deze zelf studie wordt u begeleid bij de stappen voor het maken van een Azure Media Services-account.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79478797"
---
# <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

Als u wilt beginnen met het versleutelen, coderen, analyseren, beheren en streamen van media-inhoud in Azure, moet u een Media Services-account maken. Het Media Services-account moet worden gekoppeld aan een of meer opslagaccounts.

> [!NOTE]
> Het Media Services-account en alle gekoppelde opslagaccounts moeten zich in hetzelfde Azure-abonnement bevinden. Het wordt sterk aangeraden opslagaccounts te gebruiken op dezelfde locatie als het Media Services-account om aanvullende kosten voor latentie en uitgaande data te vermijden.

In dit artikel worden de stappen beschreven voor het maken van een nieuw Azure Media Services-account. Kies een van de volgende tabbladen.

## <a name="use-the-azure-portal"></a>Azure Portal gebruiken

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

De Azure Portal biedt een manier om snel een Azure Media Services-account te maken. U kunt uw account gebruiken voor toegang tot Media Services waarmee u media-inhoud in Azure kunt opslaan, versleutelen, coderen, beheren en streamen.

Op dit moment kunt u het [Azure Portal](https://portal.azure.com/) gebruiken voor het volgende:

* Media Services v3 [Live-gebeurtenissen](live-events-outputs-concept.md)beheren, 
* V3- [assets](assets-concept.md)weer geven (niet beheren), 
* [krijg informatie over het openen van api's](access-api-portal.md). 

Gebruik de [rest API](https://aka.ms/ams-v3-rest-ref), [cli](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [sdk's](media-services-apis-overview.md#sdks)voor alle andere beheer taken (bijvoorbeeld [trans formaties en taken](transforms-jobs-concept.md) en [inhouds beveiliging](content-protection-overview.md)).

In dit artikel wordt uitgelegd hoe u een Media Services-account maakt met de Azure Portal.

### <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Klik op **+ een resource** > maken**Media** > **Media Services**.
1. Voer in het gedeelte **een Media Services account maken de** vereiste waarden in.
    
    | Naam | Beschrijving |
    | ---|---|
    |**Accountnaam**|Voer de naam van het nieuwe Media Services-account in. Voor de naam van een Media Services-account mogen alleen cijfers en kleine letters worden gebruikt. Spaties zijn niet toegestaan. De naam mag 3 tot 24 tekens lang zijn.|
    |**Abonnement**|Als u meer dan één abonnement hebt, selecteert u er een in de lijst met Azure-abonnementen waartoe u toegang hebt.|
    |**Resource groep**|Selecteer de nieuwe of bestaande resource. Een resourcegroep is een verzameling resources met dezelfde levenscyclus, dezelfde machtigingen en hetzelfde beleid. Meer informatie [vindt u hier](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Locatie**|Selecteer de geografische regio die wordt gebruikt om de media-en meta gegevens records voor uw Media Services-account op te slaan. Deze regio wordt gebruikt om uw media te verwerken en te streamen. Alleen de beschikbare Media Services-regio's worden in de vervolgkeuzelijst weergegeven. |
    |**Opslag account**|Selecteer een opslag account om Blob Storage van de media-inhoud van uw Media Services-account op te geven. U kunt een bestaand opslagaccount in dezelfde geografische regio als uw Media Services-account selecteren of u kunt een nieuw opslagaccount maken. Een nieuw opslagaccount wordt in dezelfde regio gemaakt. De regels voor opslagaccountnamen zijn hetzelfde als voor Media Services-accounts.<br/><br/>U kunt maar één **primaire** opslagaccount koppelen aan uw Media Services-account, maar een onbeperkt aantal **secundaire** opslagaccounts. U kunt de Azure Portal gebruiken om secundaire opslag accounts toe te voegen. Zie [Azure Storage accounts met Azure Media Services accounts](storage-account-concept.md)voor meer informatie.<br/><br/>Het Media Services-account en alle gekoppelde opslagaccounts moeten zich in hetzelfde Azure-abonnement bevinden. Het wordt sterk aangeraden opslagaccounts te gebruiken op dezelfde locatie als het Media Services-account om aanvullende kosten voor latentie en uitgaande data te vermijden.|
    
1. Selecteer **Vastmaken aan dashboard** om de voortgang van de implementatie van het account te bekijken.
1. Klik op **Maken** onder in het formulier.

    Wanneer uw Media Services-account is gemaakt, wordt er een **standaard** streaming-eind punt aan uw account toegevoegd met de status **gestopt** . Als u de inhoud wilt streamen en gebruik wilt maken van [dynamische pakketten](dynamic-packaging-overview.md) en [dynamische versleuteling](content-protection-overview.md), moet het streaming-eind punt van waar u inhoud wilt streamen, de status **wordt uitgevoerd** hebben. 

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>Het Azure-abonnement instellen

Geef in de volgende opdracht de id van het Azure-abonnement op dat u wilt gebruiken voor het Media Services-account. U kunt een lijst met abonnementen bekijken waartoe u toegang hebt door naar [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) te gaan.

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Gebruik de volgende opdracht om een resourcegroep te maken. Een Azure-resourcegroep is een logische container waarin resources, zoals Azure Media Services-accounts en bijbehorende Storage-accounts worden geïmplementeerd en beheerd.

U kunt vervangen `amsResourceGroup` door uw waarde.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Create a storage account

Als u een Media Services-account gaat maken, moet u de naam van een Azure Storage-accountresource opgeven. Het opgegeven opslagaccount wordt gekoppeld aan uw Media Services-account. Zie [Storage-accounts](storage-account-concept.md) voor meer informatie over het gebruik van Storage-accounts in Media Services.

U moet één **primair** opslag account hebben en u kunt een onbeperkt aantal **secundaire** opslag accounts aan uw Media Services-account koppelen. Media Services ondersteunt **GPv2**-accounts (General-purpose v2) of **GPv1**-accounts (General-purpose v1). Blob-accounts kunt u niet instellen als **primaire** account. Zie [Opties voor Azure Storage-account](../../storage/common/storage-account-options.md) voor meer informatie over opslagaccounts. 

In dit voorbeeld maakt u een Standard LRS-account voor algemeen gebruik (v2). Als u wilt experimenteren met opslagaccounts, gebruikt u `--sku Standard_LRS`. Als u echter een SKU voor productie selecteert, kunt u overwegen om `--sku Standard_RAGRS` te gebruiken. Deze biedt geografische replicatie voor bedrijfscontinuïteit. Zie [Opslagaccounts](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest) voor meer informatie.
 
Met de volgende opdracht maakt u een Storage-account die wordt gekoppeld aan de Media Services-account. In het onderstaande script kunt u `storageaccountforams` door uw waarde vervangen. `amsResourceGroup`in de vorige stap moet overeenkomen met de waarde die u hebt opgegeven voor de resource groep. De naam van het opslag account moet minder dan 24 tekens lang zijn.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

Met de volgende Azure CLI-opdracht wordt een nieuwe Media Services-account gemaakt. U kunt de volgende waarden vervangen: `amsaccount` `storageaccountforams` (moet overeenkomen met de waarde die u hebt opgegeven voor uw opslag `amsResourceGroup` account) en (moet overeenkomen met de waarde die u hebt opgegeven voor de resource groep).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>Zie ook

* [Azure-CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Een secundaire opslag toevoegen aan een Media Services-account](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Volgende stappen

[Een bestand streamen](stream-files-dotnet-quickstart.md)
