---
title: De service-principal van een beheerde identiteit weer geven-Azure CLI-Azure AD
description: Stapsgewijze instructies voor het weer geven van de service-principal van een beheerde identiteit met behulp van Azure CLI.
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
ms.openlocfilehash: ba60fdfefb5d741e92a56d2dc61eb058ac26b5ba
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224612"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>De service-principal van een beheerde identiteit weer geven met behulp van Azure CLI

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie zonder referenties in uw code. 

In dit artikel leert u hoe u de service-principal van een beheerde identiteit kunt weer geven met behulp van Azure CLI.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md).
- Als u nog geen Azure-account hebt, [meldt u zich aan voor een gratis account](https://azure.microsoft.com/free/).
- [Systeem toegewezen identiteit inschakelen op een virtuele machine](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) of [toepassing](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Als u wilt de CLI-scriptvoorbeelden uitvoeren, hebt u drie opties:
    - Gebruik [Azure Cloud shell](../../cloud-shell/overview.md) van de Azure Portal (zie volgende sectie).
    - Gebruik de ingesloten Azure Cloud Shell via het 'Try It' de knop, zich in de rechterbovenhoek van elk codeblok.
    - [Installeer de nieuwste versie van de Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) als u liever een lokale cli-console gebruikt en zich met `az login` aanmeldt bij Azure.
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>De Service-Principal weer geven

De volgende opdracht laat zien hoe u de service-principal van een virtuele machine of toepassing kunt weer geven waarvoor beheerde identiteit is ingeschakeld. Vervang `<VM or application name>` door uw eigen waarden. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Volgende stappen

Zie [AZ AD SP](/cli/azure/ad/sp)voor meer informatie over het beheren van Azure AD-service-principals met behulp van Azure cli.


