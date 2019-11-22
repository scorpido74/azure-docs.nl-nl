---
title: Een B2B-samenwerkings gebruiker controleren en rapporteren-Azure AD
description: Eigenschappen van gast gebruikers kunnen worden geconfigureerd in Azure Active Directory B2B-samen werking
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273300"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Een B2B-samenwerkings gebruiker controleren en rapporteren
Met gast gebruikers hebt u controle mogelijkheden die vergelijkbaar zijn met gebruikers van leden. 

## <a name="access-reviews"></a>Toegangsbeoordelingen
U kunt toegangs beoordelingen gebruiken om periodiek te controleren of gast gebruikers nog steeds toegang tot uw resources nodig hebben. De functie **toegangs beoordelingen** is beschikbaar in **Azure Active Directory** onder > **organisatie relaties** **beheren** . (U kunt ook zoeken naar ' toegangs beoordelingen ' van **alle services** in de Azure Portal.) Zie [gast toegang beheren met Azure AD-toegangs beoordelingen](../governance/manage-guest-access-with-access-reviews.md)voor meer informatie over het gebruik van toegangs Beoordelingen.

## <a name="audit-logs"></a>Auditlogboeken

De audit logboeken van Azure AD bieden records van systeem-en gebruikers activiteiten, waaronder activiteiten die worden geïnitieerd door gast gebruikers. Als u de controle logboeken wilt openen, selecteert u in **Azure Active Directory** **controle logboeken**onder **bewaking**. Hier volgt een voor beeld van de uitnodiging en de geschiedenis van de genodigden van de SAM oogle:

![Scherm afbeelding met een voor beeld van een controle logboek uitvoer](./media/auditing-and-reporting/audit-log.png)

U kunt aan elk van deze gebeurtenissen beginnen om de details op te halen. Laten we bijvoorbeeld de acceptatie gegevens bekijken.

![Scherm afbeelding met een voor beeld van uitvoer van de activiteit Details](./media/auditing-and-reporting/activity-details.png)

U kunt deze logboeken ook exporteren vanuit Azure AD en het rapportage programma gebruiken om aangepaste rapporten op te halen.

### <a name="next-steps"></a>Volgende stappen

- [Eigenschappen van gebruikers van B2B-samenwerking](user-properties.md)

