---
title: De service-principal van een beheerde identiteit weer geven-Azure CLI-Azure AD
description: Stapsgewijze instructies voor het weer geven van de service-principal van een beheerde identiteit met behulp van Azure CLI.
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
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1c0f167a36ceff6a9b62e2a3ccf24ca6e13e6294
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611908"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>De service-principal van een beheerde identiteit weer geven met behulp van Azure CLI

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie zonder referenties in uw code. 

In dit artikel leert u hoe u de service-principal van een beheerde identiteit kunt weer geven met behulp van Azure CLI.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md).
- Als u nog geen Azure-account hebt, [meldt u zich aan voor een gratis account](https://azure.microsoft.com/free/).
- [Systeem toegewezen identiteit inschakelen op een virtuele machine](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) of [toepassing](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- Als u de voorbeeldscripts wilt uitvoeren, hebt u twee opties:
    - Gebruik de [Azure Cloud Shell](../../cloud-shell/overview.md), die u kunt openen met behulp van de knop **Probeer het nu** in de rechterbovenhoek van Code::Blocks.
    - Voer scripts lokaal uit door de nieuwste versie van de [Azure CLI](/cli/azure/install-azure-cli) te installeren en u vervolgens aan te melden bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement waarin u resources wilt maken.   

## <a name="view-the-service-principal"></a>De Service-Principal weer geven

De volgende opdracht laat zien hoe u de service-principal van een virtuele machine of toepassing kunt weer geven waarvoor beheerde identiteit is ingeschakeld. Vervang door `<Azure resource name>` uw eigen waarden.

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>Volgende stappen

Zie [AZ AD SP](/cli/azure/ad/sp)voor meer informatie over het beheren van Azure AD-service-principals met behulp van Azure cli.
