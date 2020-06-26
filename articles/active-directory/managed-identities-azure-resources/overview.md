---
title: Beheerde identiteiten voor Azure-resources
description: Een overzicht van de beheerde identiteiten voor Azure-resources.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 05/20/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeeb7b2704474e030e00eda03e73fd523434a207
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976114"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Wat zijn beheerde identiteiten voor Azure-resources?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Als cloudontwikkelaar zoekt u waarschijnlijk naar de eenvoudigste en veiligste manier om Azure-resources te openen in uw code. 

De beheerde identiteiten voor Azure-resources helpen u hiermee, omdat beheerde identiteiten:

- De noodzaak voor referenties in uw code **elimeneren**.
- De referenties automatisch **roteren**.
- Uw betrokkenheid in het beheer van identiteiten **beperken** tot een minimum.


## <a name="how-it-works"></a>Hoe werkt het? 

Alle Azure-resources die beheerde identiteiten ondersteunen, kunnen tokens verkrijgen om gegevens uit te wisselen zonder referenties in code. Het proces bestaat uit de volgende stappen:

 
1.  **Inschakelen**: maak de beheerde identiteit voor de resource.
2.  **Toegang verlenen**: sta toegang tot de resource toe met Azure RBAC.
3.  **Toegang**: voer de toegestane acties uit.
4.  **Uitschakelen**: schakel de beheerde identiteit uit. 

## <a name="managed-identity-types"></a>Typen beheerde identiteiten

Er zijn twee typen beheerde identiteit:

- Door het systeem toegewezen beheerde identiteit

- Door een gebruiker toegewezen beheerde identiteit


Voor zelfstandige Azure-resources kunt u **door het systeem toegewezen** beheerde identiteiten inschakelen. Door het systeem toegewezen identiteiten bieden de handigste ondersteuning vanuit het perspectief van identiteitsbeheer. Met slechts één klik kunt u het geautomatiseerde levenscyclusbeheer van een identiteit inschakelen voor uw resource.   

 ![Door het systeem toegewezen beheerde identiteit](./media/overview/system-assigned.png)  

Hoewel door het systeem toegewezen beheerde identiteiten de handigste oplossing bieden voor zelfstandige resources, zien zaken er anders uit als u een groep Azure-resources voor dezelfde taak moet beheren. In dit scenario is het handig om handmatig een identiteit te maken en deze hoofdidentiteit toe te wijzen aan alle Azure-resources die u moet groeperen. Deze toewijzing staat bekend als **door de gebruik toegewezen** beheerde identiteit. 
  

## <a name="supported-services"></a>Ondersteunde services

U kunt beheerde identiteiten gebruiken voor Azure-resources voor verificatie bij services die ondersteuning bieden voor Azure AD-verificatie. Zie [Services die ondersteuning bieden voor beheerde identiteiten voor Azure-resources](services-support-msi.md) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende snelstartgidsen om aan de slag te gaan met de functie Beheerde identiteiten voor Azure-resources:

* [Een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Resource Manager](tutorial-windows-vm-access-arm.md)
* [Een door het Linux-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Resource Manager](tutorial-linux-vm-access-arm.md)
