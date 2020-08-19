---
title: Een Azure Media Services-account maken
description: In deze zelf studie wordt u begeleid bij de stappen voor het maken van een Azure Media Services-account.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 23a26e01c8329fa384d29443a143b173c58a2c6e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539109"
---
# <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

Als u wilt beginnen met het versleutelen, coderen, analyseren, beheren en streamen van media-inhoud in Azure, moet u een Media Services-account maken. Het Media Services-account moet worden gekoppeld aan een of meer opslagaccounts. In dit artikel worden de stappen beschreven voor het maken van een nieuw Azure Media Services-account.

> [!NOTE]
> Het Media Services-account en alle gekoppelde opslagaccounts moeten zich in hetzelfde Azure-abonnement bevinden. Het wordt sterk aangeraden opslagaccounts te gebruiken op dezelfde locatie als het Media Services-account om aanvullende kosten voor latentie en uitgaande data te vermijden.

 U kunt de Azure Portal of de CLI gebruiken om een Media Services-account te maken. Kies het tabblad voor de methode die u wilt gebruiken.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portal](#tab/portal/)

## <a name="use-the-azure-portal"></a>Azure Portal gebruiken

<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->
De Azure Portal biedt een manier om snel een Azure Media Services-account te maken. U kunt uw account gebruiken voor toegang tot Media Services waarmee u media-inhoud in Azure kunt opslaan, versleutelen, coderen, beheren en streamen.

Op dit moment kunt u het [Azure Portal](https://portal.azure.com/) gebruiken voor het volgende:

* Media Services v3 [Live-gebeurtenissen](live-events-outputs-concept.md)beheren, 
* V3- [assets](assets-concept.md)weer geven (niet beheren), 
* [krijg informatie over het openen van api's](./access-api-howto.md). 

Gebruik voor alle andere beheertaken (bijvoorbeeld [transformaties en taken](transforms-jobs-concept.md) en [inhoudsbeveiliging](content-protection-overview.md)), de [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).
<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->

### <a name="use-the-azure-portal-to-create-a-media-services-account"></a>De Azure Portal gebruiken om een Media Services-account te maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Klik op **+ een resource maken**  >  **Media**  >  **Media Services**.
1. Voer in het gedeelte **een Media Services account maken de** vereiste waarden in.

    | Naam | Beschrijving |
    | ---|---|
    |**Accountnaam**|Voer de naam van het nieuwe Media Services-account in. Voor de naam van een Media Services-account mogen alleen cijfers en kleine letters worden gebruikt. Spaties zijn niet toegestaan. De naam mag 3 tot 24 tekens lang zijn.|
    |**Abonnement**|Als u meer dan één abonnement hebt, selecteert u er een in de lijst met Azure-abonnementen waartoe u toegang hebt.|
    |**Resourcegroep**|Selecteer de nieuwe of bestaande resource. Een resourcegroep is een verzameling resources met dezelfde levenscyclus, dezelfde machtigingen en hetzelfde beleid. Klik [hier](../../azure-resource-manager/management/overview.md#resource-groups) voor meer informatie.|
    |**Locatie**|Selecteer de geografische regio die wordt gebruikt om de media-en meta gegevens records voor uw Media Services-account op te slaan. Deze regio wordt gebruikt om uw media te verwerken en te streamen. Alleen de beschikbare Media Services-regio's worden in de vervolgkeuzelijst weergegeven. |
    |**Opslagaccount**|Selecteer een opslag account om Blob Storage van de media-inhoud van uw Media Services-account op te geven. U kunt een bestaand opslagaccount in dezelfde geografische regio als uw Media Services-account selecteren of u kunt een nieuw opslagaccount maken. Een nieuw opslagaccount wordt in dezelfde regio gemaakt. De regels voor opslagaccountnamen zijn hetzelfde als voor Media Services-accounts.<br/><br/>U kunt maar één **primaire** opslagaccount koppelen aan uw Media Services-account, maar een onbeperkt aantal **secundaire** opslagaccounts. U kunt de Azure Portal gebruiken om secundaire opslag accounts toe te voegen. Zie [Azure Storage accounts met Azure Media Services accounts](storage-account-concept.md)voor meer informatie.<br/><br/>Het Media Services-account en alle gekoppelde opslagaccounts moeten zich in hetzelfde Azure-abonnement bevinden. Het wordt sterk aangeraden opslagaccounts te gebruiken op dezelfde locatie als het Media Services-account om aanvullende kosten voor latentie en uitgaande data te vermijden.|

1. Selecteer **Vastmaken aan dashboard** om de voortgang van de implementatie van het account te bekijken.
1. Klik op **Maken** onder in het formulier.

    Wanneer uw Media Services-account is gemaakt, wordt er een **standaard** streaming-eind punt aan uw account toegevoegd met de status **gestopt** . Als u de inhoud wilt streamen en gebruik wilt maken van [dynamische pakketten](dynamic-packaging-overview.md) en [dynamische versleuteling](content-protection-overview.md), moet het streaming-eind punt van waar u inhoud wilt streamen, de status **wordt uitgevoerd** hebben. 

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

### <a name="set-the-azure-subscription"></a>Het Azure-abonnement instellen

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

### <a name="create-a-storage-account"></a>Een opslagaccount maken

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

### <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

### <a name="see-also"></a>Zie ook

* [Azure-CLI](/cli/azure/ams?view=azure-cli-latest)
* [Een secundaire opslag toevoegen aan een Media Services-account](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Volgende stappen

[Een bestand streamen](stream-files-dotnet-quickstart.md)
