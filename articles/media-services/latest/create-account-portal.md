---
title: Een Azure Media Services-account maken via de Azure Portal
description: In deze zelfstudie wordt stapsgewijs uitgelegd hoe u een Azure Media Services-account maakt via de Azure Portal.
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
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 5b05cd31a1747da0170556003e7a8534752e2fde
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137191"
---
# <a name="use-the-azure-portal-to-create-a-media-services-account"></a>De Azure Portal gebruiken om een Media Services-account te maken

De Azure Portal biedt een manier om snel een Azure Media Services-account te maken. U kunt uw account gebruiken voor toegang tot Media Services waarmee u media-inhoud in Azure kunt opslaan, versleutelen, coderen, beheren en streamen.

Op dit moment kunt u het [Azure Portal](https://portal.azure.com/) gebruiken voor het volgende:

* Media Services v3 [Live-gebeurtenissen](live-events-outputs-concept.md)beheren, 
* V3- [assets](assets-concept.md)weer geven (niet beheren), 
* [krijg informatie over het openen van api's](access-api-portal.md). 

Gebruik de [rest API](https://aka.ms/ams-v3-rest-ref), [cli](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [sdk's](media-services-apis-overview.md#sdks)voor alle andere beheer taken (bijvoorbeeld [trans formaties en taken](transforms-jobs-concept.md) en [inhouds beveiliging](content-protection-overview.md)).

In dit artikel wordt uitgelegd hoe u een Media Services-account maakt met de Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie. 

## <a name="create-a-media-services-account"></a>Een Media Services-account maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Klik op **+ een resource maken** > **Media** - > **Media Services**.
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

    Wanneer uw Media Services-account is gemaakt, wordt er een **standaard** streaming-eindpunt met de status **Gestopt** aan uw account toegevoegd. Als u de inhoud wilt streamen en gebruik wilt maken van [dynamische pakketten](dynamic-packaging-overview.md) en [dynamische versleuteling](content-protection-overview.md), moet het streaming-eind punt van waar u inhoud wilt streamen, de status **wordt uitgevoerd** hebben. 

## <a name="next-steps"></a>Volgende stappen

Zie [toegang tot de Azure Media Services API met Azure AD-verificatie](access-api-portal.md)als u via een programma toegang wilt krijgen tot Media Services-API.

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

