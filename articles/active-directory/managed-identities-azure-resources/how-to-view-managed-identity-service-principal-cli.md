---
title: Serviceprincipal van een beheerde identiteit weergeven - Azure CLI - Azure AD
description: Stapsgewijze instructies voor het bekijken van de serviceprincipal van een beheerde identiteit met Azure CLI.
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
ms.openlocfilehash: 6fedef003c6d4143d3ad1814081d55b61d5ee020
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298696"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>De serviceprincipal van een beheerde identiteit weergeven met Azure CLI

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt zonder referenties in uw code te hebben. 

In dit artikel leert u hoe u de serviceprincipal van een beheerde identiteit weergeven met Azure CLI.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md).
- Als u nog geen Azure-account hebt, [meldt u zich aan voor een gratis account.](https://azure.microsoft.com/free/)
- Systeemtoegewezen [identiteit inschakelen op een virtuele machine](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) of [toepassing.](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity)
- Als u de voorbeelden van CLI-scripten wilt uitvoeren, hebt u drie opties:
    - [Azure Cloud Shell gebruiken](../../cloud-shell/overview.md) vanuit de Azure-portal (zie volgende sectie).
    - Gebruik de ingesloten Azure Cloud Shell via de knop 'Probeer het' in de rechterbovenhoek van elk codeblok.
    - [Installeer de nieuwste versie van de Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) als u liever een lokale CLI-console gebruikt en zich aanmeldt bij Azure met behulp van`az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Bekijk de serviceprincipal

Deze volgende opdracht laat zien hoe u de serviceprincipal van een VM of toepassing weergeven met een beheerde identiteit ingeschakeld. Vervang `<VM or application name>` door je eigen waarden. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Volgende stappen

Zie [az ad sp](/cli/azure/ad/sp)voor meer informatie over het beheren van Azure AD-serviceprincipals met Azure CLI.


