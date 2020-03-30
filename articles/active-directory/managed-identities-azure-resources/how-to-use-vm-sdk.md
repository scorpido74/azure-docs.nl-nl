---
title: Beheerde identiteiten gebruiken op een Azure VM met Azure SDKs - Azure AD
description: Codevoorbeelden voor het gebruik van Azure SDKs met een Azure VM die identiteiten voor Azure-resources heeft beheerd.
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
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 422b4c63a86904721fc6910193ffa63a8fe46f0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184154"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Beheerde identiteiten voor Azure-resources gebruiken op een Azure VM met Azure SDKs 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
In dit artikel vindt u een lijst met SDK-voorbeelden, die het gebruik van de ondersteuning van hun respectievelijke Azure SDK voor beheerde identiteiten voor Azure-resources aantonen.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - In dit artikel wordt ervan uitgegaan dat alle voorbeeldcode/script in dit artikel ervan uitgaat dat de client wordt uitgevoerd op een vm met beheerde identiteiten voor Azure-resources ingeschakeld. Gebruik de functie VM 'Verbinden' in de Azure-portal om op afstand verbinding te maken met uw vm. Zie [Beheerde identiteiten voor Azure-resources op een VM configureren met behulp van de Azure-portal](qs-configure-portal-windows-vm.md)of een van de varianten (met PowerShell, CLI, een sjabloon of een Azure SDK) voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources op een vm. 

## <a name="sdk-code-samples"></a>Voorbeelden van SDK-codes

| SDK             | Codevoorbeeld |
| --------------- | ----------- |
| .NET            | [Een Azure Resource Manager-sjabloon implementeren vanuit een Windows-vm met beheerde identiteiten voor Azure-resources](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Azure-services aanroepen vanaf een Linux-VM met beheerde identiteiten voor Azure-resources](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Resources beheren met beheerde identiteiten voor Azure-resources](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Beheerde identiteiten voor Azure-bronnen gebruiken om eenvoudig vanuit een VM te verifiëren](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Resources beheren van een vm met beheerde identiteiten voor Azure-resources ingeschakeld](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure SDK's](https://azure.microsoft.com/downloads/) voor de volledige lijst met Azure SDK-bronnen, inclusief bibliotheekdownloads, documentatie en meer.
- Zie [Beheerde identiteiten configureren voor Azure-resources op een vm configureren met behulp van de Azure-portal](qs-configure-portal-windows-vm.md)als u beheerde identiteiten voor Azure-resources op een Azure-vm wilt inschakelen.








