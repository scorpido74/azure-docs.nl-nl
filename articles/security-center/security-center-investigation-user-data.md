---
title: Gebruikers gegevens beheren die zijn gevonden in een Azure Security Center onderzoek
description: " Meer informatie over het beheren van gebruikers gegevens die zijn gevonden in de onderzoek functie van Azure Security Center. "
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979218"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Gebruikers gegevens beheren die zijn gevonden in een Azure Security Center onderzoek
Dit artikel bevat informatie over het beheren van de gebruikers gegevens die worden gevonden in de onderzoek functie van Azure Security Center. Onderzoek gegevens worden opgeslagen in [Azure monitor logboeken](../log-analytics/log-analytics-overview.md) en beschikbaar gesteld in Security Center. Het beheren van gebruikers gegevens omvat de mogelijkheid om gegevens te verwijderen of te exporteren.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Persoonsgegevens zoeken en identificeren
In de Azure Portal kunt u de [functie onderzoek](../security-center/security-center-investigation.md) van Security Center gebruiken om te zoeken naar persoonlijke gegevens. De functie voor onderzoek is beschikbaar onder **beveiligings waarschuwingen**.

In de functie onderzoek worden alle entiteiten, gebruikers gegevens en gegevens op het tabblad **entiteiten** weer gegeven.

## <a name="securing-and-controlling-access-to-personal-information"></a>Toegang tot persoonsgegevens beveiligen en controleren
Een Security Center gebruiker de rol van lezer, eigenaar, Inzender of account beheerder heeft toegewezen, heeft toegang tot klant gegevens in het hulp programma.

Zie [ingebouwde rollen voor op rollen gebaseerd toegangs beheer voor Azure](../role-based-access-control/built-in-roles.md) voor meer informatie over de rollen lezer, eigenaar en Inzender. Zie [beheerders van Azure-abonnementen](../cost-management-billing/manage/add-change-subscription-administrator.md) voor meer informatie over de rol account beheerder.

## <a name="deleting-personal-data"></a>Het verwijderen van persoonlijke gegevens
Een Security Center gebruiker aan wie de rol van eigenaar, bijdrager of account beheerder is toegewezen, kan de onderzoek gegevens verwijderen.

Als u een onderzoek wilt verwijderen, kunt u een `DELETE` aanvraag indienen bij de Azure Resource Manager REST API:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

U kunt de `incidentName` invoer vinden door alle incidenten te vermelden met behulp van een `GET` aanvraag:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Exporteren van persoonlijke gegevens
Een Security Center gebruiker aan wie de rol van eigenaar, bijdrager of account beheerder is toegewezen, kan de onderzoek gegevens exporteren. Als u onderzoek gegevens wilt exporteren, gaat u naar het tabblad **entiteiten** om de relevante informatie te kopiëren en plakken.

## <a name="next-steps"></a>Volgende stappen
Zie [gebruikers gegevens beheren in azure Security Center](security-center-privacy.md)voor meer informatie over het beheren van gebruikers gegevens.
Zie [persoonlijke gegevens exporteren en verwijderen](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data)voor meer informatie over het verwijderen van persoonlijke gegevens in azure monitor Logboeken.
