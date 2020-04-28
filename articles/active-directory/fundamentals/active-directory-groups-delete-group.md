---
title: Een groep-Azure Active Directory verwijderen | Microsoft Docs
description: Instructies voor het verwijderen van een groep met Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdba55e0655a13e65e403f5da73fcb69db5dbca5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "68561921"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Een groep verwijderen met Azure Active Directory
U kunt een Azure Active Directory-groep (Azure AD) om een aantal redenen verwijderen, maar dit komt meestal doordat:

- Het **groeps type** is onjuist ingesteld op de verkeerde optie.

- Er is per ongeluk een onjuiste of dubbele groep gemaakt. 

- De groep is niet meer nodig.

## <a name="to-delete-a-group"></a>Een groep verwijderen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**en selecteer vervolgens **groepen**.

3. Zoek en selecteer de groep die u wilt verwijderen op de pagina **groepen-alle groepen** . Voor deze stappen gebruiken we **MDM-beleid-Oost**.

    ![Groepen-pagina alle groepen, groeps naam gemarkeerd](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Op de **overzichts pagina MDM-beleid-Oost** en selecteer vervolgens **verwijderen**.

    De groep wordt verwijderd uit uw Azure Active Directory-Tenant.

    ![MDM-beleid-Oost-overzicht pagina, optie voor verwijderen gemarkeerd](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Volgende stappen

- Als u een groep per ongeluk verwijdert, kunt u deze opnieuw maken. Zie [een basis groep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)voor meer informatie.

- Als u een Office 365-groep per ongeluk verwijdert, kunt u deze mogelijk herstellen. Zie [een verwijderde Office 365-groep herstellen](../users-groups-roles/groups-restore-deleted.md)voor meer informatie.
