---
title: Beheerde identiteiten configureren met behulp van de Azure Portal-Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een Azure-VM met behulp van de Azure Portal.
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
ms.date: 11/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75971dbd35a6b29306bc87988423ab662a343705
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84693733"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Beheerde identiteiten voor Azure-resources configureren op een virtuele machine met behulp van de Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie, zonder dat u referenties hebt in uw code. 

In dit artikel leert u hoe u door het systeem en de gebruiker toegewezen beheerde identiteiten voor een virtuele Azure-machine (VM) kunt in-en uitschakelen met behulp van de Azure Portal. 

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md).
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u de door het systeem toegewezen beheerde identiteit voor de virtuele machine inschakelt en uitschakelt met behulp van de Azure Portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een VM

Als u een door het systeem toegewezen beheerde identiteit wilt inschakelen op een virtuele machine tijdens het maken, heeft uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) nodig.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

- Schakel op het tabblad **beheer** van het gedeelte **identiteit** de optie **Managed Service Identity** in **op aan**.  

![Door het systeem toegewezen identiteit inschakelen tijdens het maken van de VM](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Raadpleeg de volgende Quick starts om een virtuele machine te maken: 

- [Een virtuele Windows-machine maken met Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Een virtuele Linux-machine maken met Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen op een bestaande VM

Als u door het systeem toegewezen beheerde identiteit wilt inschakelen op een virtuele machine die oorspronkelijk is ingericht zonder deze, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.

2. Ga naar de gewenste virtuele machine en selecteer **identiteit**.

3. Onder **toegewezen systeem**, **status**, selecteer **aan** en klik vervolgens op **Opslaan**:

   ![Scherm afbeelding configuratie pagina](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Door het systeem toegewezen beheerde identiteit verwijderen uit een VM

Als u een door het systeem toegewezen beheerde identiteit van een virtuele machine wilt verwijderen, moet uw account de roltoewijzing van de [virtuele machines](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

Als u een virtuele machine hebt waarvan de door het systeem toegewezen beheerde identiteit niet meer nodig is:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat. 

2. Ga naar de gewenste virtuele machine en selecteer **identiteit**.

3. Onder **toegewezen systeem**, **status**, selecteer **uit** en klik vervolgens op **Opslaan**:

   ![Scherm afbeelding configuratie pagina](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

 In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt toevoegen aan en verwijderen uit een virtuele machine met behulp van de Azure Portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Een door de gebruiker toegewezen identiteit toewijzen tijdens het maken van een virtuele machine

Als u een door de gebruiker toegewezen identiteit aan een VM wilt toewijzen, moet uw account de roltoewijzingen van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en de rollen voor de [beheerde identiteits operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) hebben. Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

Op dit moment biedt de Azure Portal geen ondersteuning voor het toewijzen van een door de gebruiker toegewezen beheerde identiteit tijdens het maken van een virtuele machine. In plaats daarvan raadpleegt u een van de volgende Quick Start-artikelen voor het maken van een virtuele machine en gaat u verder met de volgende sectie voor meer informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit aan de virtuele machine:

- [Een virtuele Windows-machine maken met Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Een virtuele Linux-machine maken met Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande virtuele machine

Als u een door de gebruiker toegewezen identiteit aan een VM wilt toewijzen, moet uw account de roltoewijzingen van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en de rollen voor de [beheerde identiteits operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) hebben. Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.
2. Ga naar de gewenste VM en klik op **identiteit**, **gebruiker toegewezen** en vervolgens ** \+ toevoegen**.

   ![Door de gebruiker toegewezen beheerde identiteit toevoegen aan VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Klik op de door de gebruiker toegewezen identiteit die u wilt toevoegen aan de virtuele machine en klik vervolgens op **toevoegen**.

    ![Door de gebruiker toegewezen beheerde identiteit toevoegen aan VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een VM

Als u een door de gebruiker toegewezen identiteit van een virtuele machine wilt verwijderen, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben. Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.
2. Ga naar de gewenste VM en klik op **identiteit**, **gebruiker toegewezen**, de naam van de door de gebruiker toegewezen beheerde identiteit die u wilt verwijderen en klik vervolgens op **verwijderen** (Klik op **Ja** in het bevestigings venster).

   ![Door de gebruiker toegewezen beheerde identiteit verwijderen uit een VM](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Volgende stappen

- Met behulp van de Azure Portal geeft u de beheerde identiteit van een Azure-VM door [aan een andere Azure-resource](howto-assign-access-portal.md).

