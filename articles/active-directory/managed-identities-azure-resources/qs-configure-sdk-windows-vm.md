---
title: Een SDK gebruiken om beheerde identiteiten te configureren op een VM - Azure AD
description: Stapsgewijze instructies voor het configureren en gebruiken van beheerde identiteiten voor Azure-resources op een Azure VM, met behulp van een Azure SDK.
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
ms.date: 09/28/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d11cd51984f82bc20c02669e796d9ba21b9ed5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74183483"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Een vm configureren met beheerde identiteiten voor Azure-resources met behulp van een Azure SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory (AD). U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben. 

In dit artikel leert u hoe beheerde identiteiten voor Azure-resources voor een Azure-vm kunnen worden ingeschakeld en verwijderd met behulp van een Azure SDK.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure SDKs met beheerde identiteiten voor azure-resourcesondersteuning 

Azure ondersteunt meerdere programmeerplatforms via een reeks [Azure SDK's.](https://azure.microsoft.com/downloads) Verschillende van hen zijn bijgewerkt om beheerde identiteiten voor Azure-bronnen te ondersteunen en bijbehorende voorbeelden te bieden om het gebruik aan te tonen. Deze lijst wordt bijgewerkt als extra ondersteuning wordt toegevoegd:

| SDK | Voorbeeld |
| --- | ------ | 
| .NET   | [Resource beheren vanaf een VM die is ingeschakeld met beheerde identiteiten voor Azure-resources ingeschakeld](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Opslag beheren vanaf een VM die is ingeschakeld met beheerde identiteiten voor Azure-resources](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Een VM maken met systeemtoegewezen beheerde identiteit ingeschakeld](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Een VM maken met systeemtoegewezen beheerde identiteit ingeschakeld](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Azure VM maken met een systeemtoegewezen identiteit ingeschakeld](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Volgende stappen

- Zie gerelateerde artikelen onder **Identiteit configureren voor een Azure VM**voor meer informatie over hoe u ook de Azure-portal, PowerShell, CLI en resourcesjablonen gebruiken.
