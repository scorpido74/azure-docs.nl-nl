---
title: Protocollen en coderingen beheren in Azure API Management | Microsoft Documenten
description: Meer informatie over het beheren van protocollen (TLS) en ciphers (DES) in Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: 7f87389016286c9f1b91abc77155be9e94005371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335871"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Protocollen en ciphers beheren in Azure API Management

Azure API Management ondersteunt meerdere versies van tls-protocol voor zowel client- als backend-kanten, evenals de 3DES-code.

In deze handleiding ziet u hoe u protocollen en coderingsconfiguraties voor een Azure API Management-exemplaar beheert.

![Protocollen en cijfers beheren in APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt volgen, moet u het volgende hebben:

* Een API-beheerexemplaar

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Tls-protocollen en 3DES-code beheren

1. Navigeer naar uw **api-beheerexemplaar** in de Azure-portal.
2. Selecteer **Protocol-instellingen** in het menu.  
3. Schakel gewenste protocollen of cijfers in of uit.
4. Klik op **Opslaan**. Wijzigingen worden binnen een uur toegepast.  

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Bekijk meer [video's](https://azure.microsoft.com/documentation/videos/index/?services=api-management) over API Management.