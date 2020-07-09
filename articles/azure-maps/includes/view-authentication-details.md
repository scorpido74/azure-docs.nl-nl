---
title: Verificatie Details van Azure Maps weer geven
description: Gebruik de Azure Portal om de verificatie Details van Azure Maps weer te geven.
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9146844a6b83f78ad99ef7cd1aec4b028daf3ff6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988566"
---
U kunt de verificatie Details van het Azure Maps account bekijken in de Azure Portal. In uw account selecteert u in het menu **instellingen** de optie **verificatie**.

![Verificatie gegevens](../media/how-to-manage-authentication/how-to-view-auth.png)

Zodra een Azure Maps account is gemaakt, is de Azure Maps `x-ms-client-id` waarde aanwezig op de pagina Azure Portal verificatie Details. Deze waarde vertegenwoordigt het account dat wordt gebruikt voor REST API aanvragen. Deze waarde moet worden opgeslagen in de configuratie van de toepassing en worden opgehaald voordat u HTTP-aanvragen maakt wanneer u Azure AD-verificatie gebruikt met Azure Maps.
