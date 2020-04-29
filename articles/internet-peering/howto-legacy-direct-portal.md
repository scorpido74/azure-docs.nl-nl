---
title: Een verouderde directe peering converteren naar een Azure-resource met behulp van de Azure Portal
titleSuffix: Azure
description: Een verouderde directe peering converteren naar een Azure-resource met behulp van de Azure Portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678829"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Een verouderde directe peering converteren naar een Azure-resource met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u een bestaande verouderde directe peering converteert naar een Azure-resource met behulp van de Azure Portal.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van [Power shell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en de [Stapsgewijze instructies voor direct peering](walkthrough-direct-all.md) voordat u begint met de configuratie.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Een verouderde directe peering converteren naar een Azure-resource

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Meld u aan bij de portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Een verouderde directe peering converteren

U kunt verouderde peering-verbindingen converteren met behulp van de **peering** -resource.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>De resource starten en basis instellingen configureren
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Directe peering controleren
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Aanvullende bronnen

Zie [Veelgestelde vragen over Internet peering](faqs.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een directe peering maken of wijzigen met behulp van de portal](howto-direct-portal.md)
