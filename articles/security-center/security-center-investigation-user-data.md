---
title: Gebruikersgegevens beheren die zijn gevonden in een Azure Security Center-onderzoek
description: " Meer informatie over het beheren van gebruikersgegevens in de onderzoeksfunctie van Azure Security Center. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 9e4c6577f0b8b18aff343ac54b31ff292632f5d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979218"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Gebruikersgegevens beheren die zijn gevonden in een Azure Security Center-onderzoek
In dit artikel vindt u informatie over het beheren van de gebruikersgegevens in de onderzoeksfunctie van Azure Security Center. Onderzoeksgegevens worden opgeslagen in [Azure Monitor-logboeken](../log-analytics/log-analytics-overview.md) en worden weergegeven in security center. Het beheren van gebruikersgegevens omvat de mogelijkheid om gegevens te verwijderen of te exporteren.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Persoonsgegevens zoeken en identificeren
In de Azure-portal u de [onderzoeksfunctie](../security-center/security-center-investigation.md) van Security Center gebruiken om naar persoonlijke gegevens te zoeken. De onderzoeksfunctie is beschikbaar onder **beveiligingswaarschuwingen**.

De onderzoeksfunctie toont alle entiteiten, gebruikersgegevens en gegevens onder het tabblad **Entiteiten.**

## <a name="securing-and-controlling-access-to-personal-information"></a>Toegang tot persoonsgegevens beveiligen en controleren
Een gebruiker van het Beveiligingscentrum die de rol van reader-, eigenaar-, inzender- of accountbeheerder heeft toegewezen, heeft toegang tot klantgegevens in de tool.

Zie [Ingebouwde rollen voor Azure-toegangsbeheer op basis van azure-rollen](../role-based-access-control/built-in-roles.md) voor meer informatie over de rollen Reader, Owner en Inzender. Zie [Azure-abonnementsbeheerders](../cost-management-billing/manage/add-change-subscription-administrator.md) voor meer informatie over de functie Accountbeheerder.

## <a name="deleting-personal-data"></a>Persoonlijke gegevens verwijderen
Een gebruiker van het Beveiligingscentrum die de rol van eigenaar, inzender of accountbeheerder heeft toegewezen, kan de onderzoeksgegevens verwijderen.

Als u een onderzoek wilt `DELETE` verwijderen, u een aanvraag indienen bij de AZURE Resource Manager REST API:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

De `incidentName` input is te vinden door `GET` alle incidenten aan te geven met behulp van een verzoek:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Persoonlijke gegevens exporteren
Een gebruiker van het Beveiligingscentrum die de rol van eigenaar, inzender of accountbeheerder heeft toegewezen, kan de onderzoeksgegevens exporteren. Als u onderzoeksgegevens wilt exporteren, gaat u naar het tabblad **Entiteiten** om de relevante informatie te kopiëren en te plakken.

## <a name="next-steps"></a>Volgende stappen
Zie Gebruikersgegevens beheren in Azure Security Center voor meer informatie over het beheren van [gebruikersgegevens.](security-center-privacy.md)
Zie [Privégegevens exporteren en verwijderen](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data)voor meer informatie over het verwijderen van privégegevens in Azure Monitor-logboeken.
