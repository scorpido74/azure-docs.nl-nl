---
title: Azure CLI gebruiken om een Azure AD-app te maken en deze te configureren voor toegang tot de Azure Media Services API | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u de Azure CLI gebruiken om een Azure AD-app te maken en deze te configureren om toegang te krijgen tot de Azure Media Services API.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70035797"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Azure CLI gebruiken om een Azure AD-app te maken en deze te configureren om toegang te krijgen tot de Media Services API 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

In dit onderwerp ziet u hoe u de Azure CLI gebruiken om een Azure Active Directory-toepassing en serviceprincipal te maken om toegang te krijgen tot Azure Media Services-bronnen. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Zie gratis [proefversie van Azure voor](https://azure.microsoft.com/pricing/free-trial/)meer informatie. 
- Een Media Services-account. Zie [Een Azure Media Services-account maken met de Azure-portal](media-services-portal-create-account.md)voor meer informatie.

## <a name="use-the-azure-cloud-shell"></a>De Azure Cloud Shell gebruiken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Start de Cloud Shell vanuit het bovenste navigatiedeelvenster van de portal.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Zie [Overzicht van Azure Cloud Shell](../../cloud-shell/overview.md)voor meer informatie.

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Een Azure AD-app maken en toegang tot het media-account configureren met Azure CLI
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Bijvoorbeeld:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

In dit voorbeeld is het **bereik** het volledige resourcepad voor het mediaservicesaccount. De **scope** kan echter op elk niveau zijn.

Het kan bijvoorbeeld een van de volgende niveaus zijn:
 
* Het **abonnementsniveau.**
* Het **niveau van de resourcegroep.**
* Het **resourceniveau** (bijvoorbeeld een mediaaccount).

Zie [Een Azure-serviceprincipal maken met de Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) voor meer informatie

Zie ook [Toegangsbeheer op basis van rollen beheren met de Azure-opdrachtregelinterface](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met [het uploaden van bestanden naar je account.](media-services-portal-upload-files.md)
