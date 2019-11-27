---
title: De service-principal van een beheerde identiteit weer geven met behulp van Power shell-Azure AD
description: Stapsgewijze instructies voor het weer geven van de service-principal van een beheerde identiteit met behulp van Power shell.
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
ms.openlocfilehash: 9ac2d1d4fc5e669f63bff49a7948db74ec1baa62
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184126"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>De service-principal van een beheerde identiteit weer geven met behulp van Power shell

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie, zonder dat u referenties hebt in uw code. 

In dit artikel leert u hoe u de service-principal van een beheerde identiteit kunt weer geven met behulp van Power shell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md).
- Als u nog geen Azure-account hebt, [meldt u zich aan voor een gratis account](https://azure.microsoft.com/free/).
- [Systeem toegewezen identiteit inschakelen op een virtuele machine](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) of [toepassing](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Installeer de nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>De Service-Principal weer geven

De volgende opdracht laat zien hoe u de service-principal van een virtuele machine of toepassing kunt bekijken waarbij de systeem-id is ingeschakeld. Vervang `<VM or application name>` door uw eigen waarden.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Volgende stappen

Zie [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)voor meer informatie over het weer geven van Azure AD-service-principals met behulp van Power shell.


