---
title: Voor beeld van API management-beleid-Shared Access Signature genereren
titleSuffix: Azure API Management
description: 'Voor beeld van Azure API management-beleid: demonstreert hoe u Shared Access Signature kunt genereren met behulp van expressies en de aanvraag kunt door sturen naar Azure Storage met herschrijf-URI-beleid..'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: f90c1e492d6da4f85fe86e4c5c76b89bdfaa4797
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078422"
---
# <a name="generate-shared-access-signature"></a>Shared Access Signature genereren

In dit artikel wordt een voor beeld van een Azure API management-beleid weer gegeven waarin wordt getoond hoe u [Shared Access Signature](../../storage/common/storage-sas-overview.md) genereert met behulp van expressies en de aanvraag doorstuurt naar Azure Storage met herschrijf-URI-beleid. Volg de stappen die worden beschreven in [een beleid instellen of bewerken](../set-edit-policies.md)om een beleids code in te stellen of te bewerken. Zie voor andere voor beelden [beleids voorbeelden](../policy-reference.md).

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Voor beelden van beleid](../policy-reference.md)