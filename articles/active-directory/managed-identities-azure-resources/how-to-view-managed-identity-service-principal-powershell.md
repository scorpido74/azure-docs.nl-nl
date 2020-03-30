---
title: De serviceprincipal van een beheerde identiteit weergeven met PowerShell - Azure AD
description: Stapsgewijze instructies voor het bekijken van de serviceprincipal van een beheerde identiteit met PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33fc6fbfd7c47b5809e8aff8ee9806c9eeac1162
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298645"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>De serviceprincipal van een beheerde identiteit weergeven met PowerShell

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben. 

In dit artikel leert u hoe u de serviceprincipal van een beheerde identiteit weergeven met PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md).
- Als u nog geen Azure-account hebt, [meldt u zich aan voor een gratis account.](https://azure.microsoft.com/free/)
- Systeemtoegewezen [identiteit inschakelen op een virtuele machine](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) of [toepassing.](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity)
- Installeer de nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Bekijk de serviceprincipal

Deze volgende opdracht laat zien hoe u de serviceprincipal van een VM of toepassing weergeven waarbij de door het systeem toegewezen identiteit is ingeschakeld. Vervang `<VM or application name>` door je eigen waarden.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Volgende stappen

Zie [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)voor meer informatie over het weergeven van Azure AD-serviceprincipals met PowerShell.


