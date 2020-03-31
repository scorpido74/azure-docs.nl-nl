---
title: Microsoft Graph API's voor PIM (Preview) - Azure AD | Microsoft Documenten
description: Bevat informatie over het gebruik van de Microsoft Graph API's voor Azure AD Privileged Identity Management (PIM) (Preview).
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75638660"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Microsoft Graph API's voor privileged identity management (preview)

U alle privileged identity management-taken uitvoeren met de [Microsoft Graph API's](https://developer.microsoft.com/graph/docs/concepts/overview) voor Azure Active Directory. In dit artikel worden belangrijke concepten beschreven voor het gebruik van de Microsoft Graph API's voor privileged identity management.

Zie de [naslag bij Azure AD Privileged Identity Management-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root) voor meer informatie over de Microsoft Graph-API's.

> [!IMPORTANT]
> API's onder de /bèta-versie in Microsoft Graph zijn beschikbaar als preview en kunnen worden gewijzigd. Het gebruik van deze API's in productie-apps wordt niet ondersteund.
>
> Hoewel alle PIM-bewerkingen worden ondersteund via onze Graph API-opdrachten, gebruiken we een ander systeem om activeringsaanvragen goed te keuren. Graph API voor goedkeuring wordt momenteel ontwikkeld en zal worden vrijgegeven in de komende maanden.

## <a name="required-permissions"></a>Vereiste machtigingen

Als u de Microsoft Graph API's voor privileged identity management wilt aanroepen, moet u **over een of meer** van de volgende machtigingen beschikken:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Machtigingen instellen

Als toepassingen de Microsoft Graph API's voor privileged identity management moeten aanroepen, moeten ze over de vereiste machtigingen beschikken. De eenvoudigste manier om de vereiste machtigingen op te geven, is door het [toestemmingsframework van Azure AD-](../develop/consent-framework.md) te gebruiken.

### <a name="set-permissions-in-graph-explorer"></a>Machtigingen instellen in Graph Explorer

Als u Graph Explorer gebruikt om uw aanroepen te testen, kunt u de machtigingen opgeven in het hulpprogramma.

1. Meld u als globale beheerder aan bij [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

1. Klik op **Machtigingen wijzigen**.

    ![Graph Explorer - machtigingen wijzigen](./media/pim-apis/graph-explorer.png)

1. Schakel de selectievakjes in naast de machtigingen die u wilt opnemen. `PrivilegedAccess.ReadWrite.AzureAD` is nog niet beschikbaar in Graph Explorer.

    ![Graph Explorer - machtigingen wijzigen](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Klik op **Machtigingen wijzigen** om de wijzigingen in de machtigingen toe te passen.

## <a name="next-steps"></a>Volgende stappen

- [Naslag bij Azure AD Privileged Identity Management-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
