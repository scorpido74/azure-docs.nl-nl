---
title: Beheerde identiteit in Azure Synapse-werkruimte
description: Een artikel waarin beheerde identiteit in Azure Synapse-werkruimte wordt uitgelegd
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ee0e6249acf3fbbab369d42ae691a5a826df1ee8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425117"
---
# <a name="azure-synapse-workspace-managed-identity-preview"></a>Beheerde identiteit van Azure Synapse-werkruimte (voorbeeld)

In dit artikel vindt u meer informatie over beheerde identiteit in azure synapsewerkruimte.

## <a name="managed-identities"></a>Beheerde identiteiten

Beheerde identiteit voor Azure-resources is een functie van Azure Active Directory. De functie biedt Azure-services met een automatisch beheerde identiteit in Azure AD. U de beheerde identiteitsmogelijkheid gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt.

Beheerde identiteiten voor Azure-resources zijn de nieuwe naam voor de service die voorheen managed service identity (MSI) wordt genoemd. Zie [Beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie.

## <a name="azure-synapse-workspace-managed-identity"></a>Beheerde identiteit azure Synapse-werkruimte

Er wordt een door het systeem toegewezen beheerde identiteit gemaakt voor uw Azure Synapse-werkruimte wanneer u de werkruimte maakt.

>[!NOTE]
>Deze door de werkruimte beheerde identiteit wordt via de rest van dit document als beheerde identiteit aangeduid.

Azure Synapse gebruikt de beheerde identiteit om pijplijnen te orkestreren. De levenscyclus van beheerde identiteit is rechtstreeks gekoppeld aan de Azure Synapse-werkruimte. Als u de Azure Synapse-werkruimte verwijdert, wordt ook de beheerde identiteit opgeschoond.

De door de werkruimte beheerde identiteit heeft machtigingen nodig om bewerkingen in de pijplijnen uit te voeren. U de object-id of de naam van uw Azure Synapse-werkruimte gebruiken om de beheerde identiteit te vinden bij het verlenen van machtigingen.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Beheerde identiteit ophalen in Azure-portal

U de beheerde identiteit ophalen in azure-portal. Open uw Azure Synapse-werkruimte in Azure-portal en selecteer **Overzicht** in de linkernavigatie. De object-id van de beheerde identiteit wordt weergegeven in het hoofdscherm.

![Gemanaged identiteitsobject-id](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

De beheerde identiteitsgegevens worden ook weergegeven wanneer u een gekoppelde service maakt die beheerde identiteitsverificatie van Azure Synapse Studio ondersteunt.

Start **Azure Synapse Studio** en selecteer het tabblad **Beheren** op de linkernavigatie. Selecteer vervolgens **Gekoppelde services** en kies de optie **+ Nieuw** om een nieuwe gekoppelde service te maken.

![Gekoppelde servicecreatie 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

Typ Azure Data Lake *Storage Gen2*in het **venster Nieuw gekoppelde service** . Selecteer het brontype **Van Azure Data Lake Storage Gen2** in de onderstaande lijst en kies **Doorgaan**.

![Gekoppelde servicecreatie 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

Kies in het volgende venster **Beheerde identiteit** voor **verificatiemethode**. U ziet de **naam** en **object-id**van de beheerde identiteit.

![Gekoppelde servicecreatie 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>Volgende stappen

[Machtigingen verlenen aan de beheerde identiteit van Azure Synapse Workspace](./how-to-grant-workspace-managed-identity-permissions.md)
