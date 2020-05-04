---
title: 'Zelf studie: een gebruiker toegang verlenen tot Azure-resources met behulp van de Azure Portal-Azure RBAC'
description: In deze zelf studie leert u hoe u een gebruiker toegang verleent tot Azure-resources met behulp van de Azure Portal en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
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
ms.openlocfilehash: e70a3a1eedbc56754ad46fd99dddc0c7a3a6cf2f
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735841"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-the-azure-portal"></a>Zelf studie: een gebruiker toegang verlenen tot Azure-resources met behulp van de Azure Portal

[Azure RBAC (op rollen gebaseerd toegangs beheer)](overview.md) is de manier waarop u de toegang tot Azure-resources beheert. In deze zelfstudie verleent u een gebruiker toegang tot het maken en beheren van virtuele machines in een resourcegroep.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Toegang voor een gebruiker toewijzen op het niveau van een resourcegroep
> * Toegang intrekken

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

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

In azure RBAC kunt u een roltoewijzing maken om toegang te verlenen.

1. Klik in de lijst **Resourcegroepen** op de nieuwe resourcegroep **rbac-resource-group**.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **Roltoewijzingen** om de huidige lijst met roltoewijzingen te zien.

   ![De blade Toegangsbeheer (IAM) voor een resourcegroep](./media/quickstart-assign-role-user-portal/access-control.png)

1. Klik op**toewijzing van roltoewijzing** toevoegen om het deel venster roltoewijzing toevoegen te openen. **Add** > 

   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Toevoegen](./media/role-assignments-portal/add-menu.png)

   ![Deelvenster Roltoewijzing toevoegen](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. Selecteer in de vervolgkeuzelijst **Rol****Inzender voor virtuele machines**.

1. Selecteer in de lijst **Selecteren** uzelf of een andere gebruiker.

1. Klik op **Opslaan** om de roltoewijzing te maken.

   Na enkele ogenblikken krijgt de gebruiker de rol Inzender voor virtuele machines toegewezen in het bereik van de resourcegroep rbac-resource-group.

   ![Roltoewijzing Inzender voor virtuele machines](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Toegang intrekken

In azure RBAC kunt u een roltoewijzing verwijderen om de toegang te verwijderen.

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
> [Zelf studie: een gebruiker toegang verlenen tot Azure-resources met behulp van Azure PowerShell](tutorial-role-assignments-user-powershell.md)
