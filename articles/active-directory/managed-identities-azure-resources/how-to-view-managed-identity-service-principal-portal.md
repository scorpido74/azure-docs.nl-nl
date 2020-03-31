---
title: Serviceprincipal van een beheerde identiteit weergeven in de Azure-portal - Azure AD
description: Stapsgewijze instructies voor het bekijken van de serviceprincipal van een beheerde identiteit in de Azure-portal.
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
ms.openlocfilehash: c12f15cc79d5329d028239ade4e18a853000bf01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298594"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>De serviceprincipal van een beheerde identiteit weergeven in de Azure-portal

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben. 

In dit artikel leert u hoe u de serviceprincipal van een beheerde identiteit weergeven met behulp van de Azure-portal.

 > [!NOTE] 
 > Serviceprincipals zijn Enterprise Applications. 

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md).
- Als u nog geen Azure-account hebt, [meldt u zich aan voor een gratis account.](https://azure.microsoft.com/free/)
- Systeemtoegewezen [identiteit inschakelen op een virtuele machine](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) of [toepassing.](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity)

## <a name="view-the-service-principal"></a>Bekijk de serviceprincipal

Deze procedure laat zien hoe de serviceprincipal van een VM met systeemtoegewezen identiteit is ingeschakeld (dezelfde stappen gelden voor een toepassing).

1. Klik op **Azure Active Directory** en klik vervolgens op **Bedrijfstoepassingen**.
2. Kies **Onder Toepassingstype** **alle toepassingen** en klik op **Toepassen**.
3. Typ in het zoekfiltervak de naam van de VM of toepassing die de identiteit heeft beheerd ingeschakeld of kies deze uit de weergegeven lijst.

   ![Hoofd van beheerde identiteitsservice weergeven in portal](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Volgende stappen

[Beheerde identiteiten voor Azure-resources](/azure/active-directory/managed-identities-azure-resources/overview)

