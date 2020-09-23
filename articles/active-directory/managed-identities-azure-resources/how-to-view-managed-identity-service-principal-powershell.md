---
title: De service-principal van een beheerde identiteit weer geven met behulp van Power shell-Azure AD
description: Stapsgewijze instructies voor het weer geven van de service-principal van een beheerde identiteit met behulp van Power shell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2f9ac119a3365eff39fe1a65ff8b553d3900b117
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969324"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>De service-principal van een beheerde identiteit weer geven met behulp van Power shell

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel leert u hoe u de service-principal van een beheerde identiteit kunt weer geven met behulp van Power shell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md).
- Als u nog geen Azure-account hebt, [meldt u zich aan voor een gratis account](https://azure.microsoft.com/free/).
- [Systeem toegewezen identiteit inschakelen op een virtuele machine](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) of [toepassing](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- Als u de voorbeeld scripts wilt uitvoeren, hebt u twee opties:
    - Gebruik de [Azure Cloud shell](../../cloud-shell/overview.md), die u kunt openen met behulp van de knop **try it** in de rechter bovenhoek van code blokken.
    - Voer scripts lokaal uit door de nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps)te installeren en meld u vervolgens aan bij Azure met `Connect-AzAccount` .

## <a name="view-the-service-principal"></a>De Service-Principal weer geven

De volgende opdracht laat zien hoe u de service-principal van een virtuele machine of toepassing kunt bekijken waarbij de systeem-id is ingeschakeld. Vervang door `<VM or application name>` uw eigen waarden.

```azurepowershell-interactive
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Volgende stappen

Zie [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)voor meer informatie over het weer geven van Azure AD-service-principals met behulp van Power shell.
