---
title: Microsoft Graph Api's voor PIM (preview)-Azure AD | Microsoft Docs
description: Bevat informatie over het gebruik van de Microsoft Graph Api's voor Azure AD Privileged Identity Management (PIM) (preview).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6da6bffbc54bfa6e9c39ddace665eb7cfec58614
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638660"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Microsoft Graph-Api's voor Privileged Identity Management (preview-versie)

U kunt alle Privileged Identity Management taken uitvoeren met behulp van de [Microsoft Graph-api's](https://developer.microsoft.com/graph/docs/concepts/overview) voor Azure Active Directory. In dit artikel worden belang rijke concepten beschreven voor het gebruik van de Microsoft Graph-Api's voor Privileged Identity Management.

Zie de [naslag bij Azure AD Privileged Identity Management-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root) voor meer informatie over de Microsoft Graph-API's.

> [!IMPORTANT]
> API's onder de /bèta-versie in Microsoft Graph zijn beschikbaar als preview en kunnen worden gewijzigd. Het gebruik van deze API's in productie-apps wordt niet ondersteund.
>
> Hoewel alle PIM-bewerkingen worden ondersteund via onze Graph API-opdrachten, gebruiken we een ander systeem voor het goed keuren van activerings aanvragen. Graph API voor goed keuring is momenteel ontwikkeld en zal in de komende maanden worden vrijgegeven.

## <a name="required-permissions"></a>Vereiste machtigingen

Als u de Microsoft Graph-Api's voor Privileged Identity Management wilt aanroepen, moet u **een of meer** van de volgende machtigingen hebben:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Machtigingen instellen

Als u wilt dat toepassingen de Microsoft Graph-Api's aanroepen voor Privileged Identity Management, moeten ze over de vereiste machtigingen beschikken. De eenvoudigste manier om de vereiste machtigingen op te geven, is door het [toestemmingsframework van Azure AD-](../develop/consent-framework.md) te gebruiken.

### <a name="set-permissions-in-graph-explorer"></a>Machtigingen instellen in Graph Explorer

Als u Graph Explorer gebruikt om uw aanroepen te testen, kunt u de machtigingen opgeven in het hulpprogramma.

1. Meld u als globale beheerder aan bij [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

1. Klik op **Machtigingen wijzigen**.

    ![Graph Explorer - machtigingen wijzigen](./media/pim-apis/graph-explorer.png)

1. Schakel de selectie vakjes in naast de machtigingen die u wilt toevoegen. `PrivilegedAccess.ReadWrite.AzureAD` is nog niet beschikbaar in Graph Explorer.

    ![Graph Explorer - machtigingen wijzigen](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Klik op **Machtigingen wijzigen** om de wijzigingen in de machtigingen toe te passen.

## <a name="next-steps"></a>Volgende stappen

- [Naslag bij Azure AD Privileged Identity Management-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
