---
title: Gebruik Azure CLI om een Azure AD-app te maken en deze te configureren voor toegang tot Azure Media Services-API | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u de Azure CLI gebruikt om een Azure AD-app te maken en deze te configureren voor toegang tot Azure Media Services-API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: f136fb666e93adc0fe92aee014e3da9a37bbd6aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "70035797"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Azure CLI gebruiken om een Azure AD-app te maken en deze te configureren voor toegang tot Media Services-API 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

In dit onderwerp wordt beschreven hoe u de Azure CLI gebruikt om een Azure Active Directory (Azure AD)-toepassing en Service-Principal te maken voor toegang tot Azure Media Services resources. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Zie [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie. 
- Een Media Services-account. Zie [een Azure Media Services-account maken met behulp van de Azure Portal](media-services-portal-create-account.md)voor meer informatie.

## <a name="use-the-azure-cloud-shell"></a>De Azure Cloud Shell gebruiken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Start de Cloud Shell vanuit het bovenste navigatie deel venster van de portal.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Zie [overzicht van Azure Cloud shell](../../cloud-shell/overview.md)voor meer informatie.

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Een Azure AD-app maken en toegang tot het Media account configureren met Azure CLI
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Bijvoorbeeld:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

In dit voor beeld is de **Scope** het volledige bronpad voor het Media Services-account. Het **bereik** kan echter op elk niveau worden toegepast.

Dit kan bijvoorbeeld een van de volgende niveaus zijn:
 
* Het **abonnements** niveau.
* Het niveau van de **resource groep** .
* Het **resource** niveau (bijvoorbeeld een media-account).

Zie [een Azure-service-principal maken met de Azure cli](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) voor meer informatie.

Zie ook [Access Control op basis van rollen beheren met de Azure-opdracht regel interface](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met [het uploaden van bestanden naar uw account](media-services-portal-upload-files.md).
