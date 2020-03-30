---
title: Controleren en rapporteren van een B2B-samenwerkingsgebruiker - Azure AD
description: Eigenschappen van gastgebruikers kunnen worden geconfigureerd in Azure Active Directory B2B-samenwerking
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74d2473d37dcf0d435ab61763e72a64111845956
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273300"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Controle en rapportage van een B2B-samenwerkingsgebruiker
Met gastgebruikers hebt u controlemogelijkheden die vergelijkbaar zijn met die van leden. 

## <a name="access-reviews"></a>Toegangsbeoordelingen
U toegangsbeoordelingen gebruiken om periodiek te controleren of gastgebruikers nog steeds toegang tot uw bronnen nodig hebben. De functie **Toegangsrecensies** is beschikbaar in **Azure Active Directory** onder**Organisatierelaties** **beheren** > . (U ook zoeken naar 'toegangsbeoordelingen' van **alle services** in de Azure-portal.) Zie [Gasttoegang beheren met Azure AD-toegangsbeoordelingen](../governance/manage-guest-access-with-access-reviews.md)voor meer informatie over het gebruik van toegangsbeoordelingen.

## <a name="audit-logs"></a>Auditlogboeken

De Azure AD-controlelogboeken bieden records van systeem- en gebruikersactiviteiten, inclusief activiteiten die zijn geïnitieerd door gastgebruikers. Als u controlelogboeken wilt openen, selecteert u in **Azure Active Directory**onder **Controle**de optie **Controlelogboeken**. Hier is een voorbeeld van de uitnodiging en verlossing geschiedenis van genodigde Sam Oogle:

![Schermafbeelding en voorbeeld van de uitvoer van het controlelogboek](./media/auditing-and-reporting/audit-log.png)

U duiken in elk van deze evenementen om de details te krijgen. Laten we bijvoorbeeld eens kijken naar de acceptatiedetails.

![Schermafbeelding en voorbeeld van de uitvoer van activiteitsdetails](./media/auditing-and-reporting/activity-details.png)

U deze logboeken ook exporteren vanuit Azure AD en het rapportageprogramma van uw keuze gebruiken om aangepaste rapporten te krijgen.

### <a name="next-steps"></a>Volgende stappen

- [Eigenschappen van gebruikers van B2B-samenwerking](user-properties.md)

