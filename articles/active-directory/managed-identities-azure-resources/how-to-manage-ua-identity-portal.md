---
title: Een door de gebruiker toegewezen beheerde identiteit beheren in de Azure-portal - Azure AD
description: Stapsgewijze instructies voor het maken, aanbieden, verwijderen en toewijzen van een rol aan een door de gebruiker toegewezen beheerde identiteit.
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
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10caa9e5e61dc1dd0c1062583f55a7357c643ce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244132"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Een rol maken, aanbieden, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure-portal

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor services die Azure AD-verificatie ondersteunen, zonder dat u referenties in uw code nodig hebt. 

In dit artikel leert u hoe u een rol maakt, vermeldt, verwijdert of toewijst aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, heeft uw account de roltoewijzing [Voor beheerde identiteitsbijdrager](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) nodig.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement om de door de gebruiker toegewezen beheerde identiteit te maken.
2. Typ *Beheerde identiteiten*en klik onder **Services**op **Beheerde identiteiten**.
3. Klik **op Waarden toevoegen** en invoeren in de volgende velden onder het door gebruiker toegewezen **identiteitsvenster maken:**
   - **Resourcenaam:** dit is de naam voor uw door de gebruiker toegewezen beheerde identiteit, bijvoorbeeld UAI1.
   - **Abonnement:** kies het abonnement om de door de gebruiker toegewezen beheerde identiteit te maken onder
   - **Resourcegroep:** maak een nieuwe resourcegroep om uw door de gebruiker toegewezen beheerde identiteit te bevatten of kies **Bestaand gebruiken** om de door de gebruiker toegewezen beheerde identiteit in een bestaande resourcegroep te maken.
   - **Locatie:** kies een locatie voor het implementeren van de door de gebruiker toegewezen beheerde identiteit, bijvoorbeeld **West US**.
4. Klik **op Maken**.

![Een door de gebruiker toegewezen beheerde identiteit maken](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Door de gebruiker toegewezen beheerde identiteiten weergeven

Als u een door de gebruiker toegewezen beheerde identiteit wilt aanbieden/lezen, heeft uw account de taaktoewijzing [Managed Identity Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) of Managed Identity [Contributor](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) nodig.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement om de door de gebruiker toegewezen beheerde identiteiten weer te geven.
2. Typ *Beheerde identiteiten*en klik onder Services op **Beheerde identiteiten**.
3. Een lijst met door de gebruiker toegewezen beheerde identiteiten voor uw abonnement wordt geretourneerd.  Klik op de naam van de gebruiker om de details van een door de gebruiker toegewezen beheerde identiteit te bekijken.

![Door de gebruiker toegewezen beheerde identiteit weergeven](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, heeft uw account de roltoewijzing [Voor beheerde identiteitsbijdrager](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) nodig.

Als u een door een gebruiker toegewezen identiteit verwijdert, wordt deze niet verwijderd uit de vm of resource waaraan deze is toegewezen.  Als u de door de gebruiker toegewezen identiteit wilt verwijderen uit een [VM-zie, verwijdert u een door de gebruiker toegewezen beheerde identiteit uit een vm](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement om een door de gebruiker toegewezen beheerde identiteit te verwijderen.
2. Selecteer de door de gebruiker toegewezen beheerde identiteit en klik op **Verwijderen**.
3. Kies onder het bevestigingsvak **Ja.**

![Beheerde identiteit die door de gebruiker is toegewezen](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Een rol toewijzen aan een door de gebruiker toegewezen beheerde identiteit 

Als u een rol wilt toewijzen aan een door de gebruiker toegewezen beheerde identiteit, heeft uw account de functietoewijzing [voor gebruikerstoegangsbeheerders](/azure/role-based-access-control/built-in-roles#user-access-administrator) nodig.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement om de door de gebruiker toegewezen beheerde identiteiten weer te geven.
2. Typ *Beheerde identiteiten*en klik onder Services op **Beheerde identiteiten**.
3. Een lijst met door de gebruiker toegewezen beheerde identiteiten voor uw abonnement wordt geretourneerd.  Selecteer de door de gebruiker toegewezen beheerde identiteit die u een rol wilt toewijzen.
4. Selecteer **Toegangsbesturingselement (IAM)** en selecteer **Vervolgens Roltoewijzing toevoegen**.

   ![Gestart met door de gebruiker toegewezen beheerde identiteit](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Configureer in het blad Roltoewijzing toevoegen de volgende waarden en klik op **Opslaan:**
   - **Rol** - de rol die moet worden toegewezen
   - **Toegang toewijzen aan** - de bron om de door de gebruiker toegewezen beheerde identiteit toe te wijzen
   - **Selecteren** - het lid dat toegang moet toewijzen
   
   ![Door de gebruiker toegewezen beheerde identiteit IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
