---
title: 'Zelfstudie: Gebruikers toegang verlenen tot Azure-bronnen met RBAC en de Azure-portal '
description: Lees in deze zelfstudie hoe u een gebruiker toegang verleent tot Azure-bronnen met behulp van RBAC (Role-based access control) in de Azure-portal.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/22/2019
ms.author: rolyon
ms.openlocfilehash: 6281c9a1818a3b46ac4f7a62f8ae76668db56887
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77138098"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Zelfstudie: Een gebruiker toegang verlenen tot Azure-bronnen met RBAC en de Azure-portal

[Op rollen gebaseerd toegangsbeheer (RBAC)](overview.md) is de manier waarop u de toegang tot Azure-resources beheert. In deze zelfstudie verleent u een gebruiker toegang tot het maken en beheren van virtuele machines in een resourcegroep.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Toegang voor een gebruiker toewijzen op het niveau van een resourcegroep
> * Toegang intrekken

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

1. Klik in de navigatielijst op **Resourcegroepen**.

1. Klik op **Toevoegen** om de blade **Resourcegroep** te openen.

   ![Een nieuwe resourcegroep toevoegen](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Voer bij **Resourcegroepnaam****rbac-resource-group** in.

1. Selecteer een abonnement en een locatie.

1. Klik op **Maken** om de resourcegroep te maken.

1. Klik op **Vernieuwen** om de lijst met resourcegroepen te vernieuwen.

   De nieuwe resourcegroep wordt weergegeven in de lijst met resourcegroepen.

   ![Resourcegroeplijst](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Toegang verlenen

In RBAC verleent u toegang door een roltoewijzing te maken.

1. Klik in de lijst **Resourcegroepen** op de nieuwe resourcegroep **rbac-resource-group**.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **Roltoewijzingen** om de huidige lijst met roltoewijzingen te zien.

   ![De blade Toegangsbeheer (IAM) voor een resourcegroep](./media/quickstart-assign-role-user-portal/access-control.png)

1. Klik **op** > **Roltoewijzing toevoegen toevoegen** om het deelvenster Roltoewijzing toevoegen te openen.

   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Toevoegen](./media/role-assignments-portal/add-menu.png)

   ![Deelvenster Roltoewijzing toevoegen](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. Selecteer in de vervolgkeuzelijst **Rol****Inzender voor virtuele machines**.

1. Selecteer in de lijst **Selecteren** uzelf of een andere gebruiker.

1. Klik op **Opslaan** om de roltoewijzing te maken.

   Na enkele ogenblikken krijgt de gebruiker de rol Inzender voor virtuele machines toegewezen in het bereik van de resourcegroep rbac-resource-group.

   ![Roltoewijzing Inzender voor virtuele machines](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Toegang intrekken

Als u in RBAC de toegang wilt intrekken voor een rol, verwijdert u de roltoewijzing.

1. Plaats in de lijst met roltoewijzingen een vinkje naast de gebruiker met de rol Inzender voor virtuele machines.

1. Klik op **Verwijderen**.

   ![Bericht bij verwijderen van roltoewijzing](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Klik op **Ja** om te bevestigen dat u de roltoewijzing inderdaad wilt verwijderen.

## <a name="clean-up"></a>Opruimen

1. Klik in de navigatielijst op **Resourcegroepen**.

1. Klik op **rbac-resource-group** om de resourcegroep te openen.

1. Klik op **Resourcegroep verwijderen** om de resourcegroep te verwijderen.

   ![Resourcegroep verwijderen](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. Typ in de blade **Weet u zeker dat u wilt verwijderen** de naam van de resourcegroep: **rbac-resource-group**.

1. Klik op **Verwijderen** om de resourcegroep te verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Een gebruiker toegang verlenen tot Azure-bronnen met RBAC en Azure PowerShell](tutorial-role-assignments-user-powershell.md)
