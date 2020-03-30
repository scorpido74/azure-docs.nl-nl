---
title: Beheerde identiteiten configureren met behulp van de Azure-portal - Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een Azure VM met behulp van de Azure-portal.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266661"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Beheerde identiteiten configureren voor Azure-resources op een VM met behulp van de Azure-portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben. 

In dit artikel leert u hoe u beheerde identiteiten voor een Azure Virtual Machine (VM) in- en uitschakelt met behulp van de Azure-portal. 

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md).
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u de door het systeem toegewezen beheerde identiteit voor VM in- en uitschakelt met behulp van de Azure-portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een VM

Als u de door het systeem toegewezen beheerde identiteit op een vm wilt inschakelen tijdens het maken ervan, heeft uw account de roltoewijzing [voor virtuele machineinzendernodig.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

- Schakel onder het tabblad **Beheer** in de sectie **Identiteit** de **identiteit van managed service** over naar **Aan**.  

![Systeemtoegewezen identiteit inschakelen tijdens het maken van vm's](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Raadpleeg de volgende Quickstarts om een vm te maken: 

- [Een virtuele Windows-machine maken met Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Een virtuele Linux-machine maken met Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen op een bestaande VM

Als u beheerde identiteit met systeemtoegewezen wilt inschakelen op een vm die oorspronkelijk zonder deze is ingericht, heeft uw account de roltoewijzing [voor virtuele machineinzender](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) nodig.  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat.

2. Navigeer naar de gewenste virtuele machine en selecteer **Identiteit**.

3. Selecteer **Onder Systeem toegewezen**, **Status**, selecteer **Aan** en klik vervolgens op **Opslaan:**

   ![Schermafbeelding van configuratiepagina](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Beheerde identiteit met systeemtoegewezen identiteit uit een virtuele machine verwijderen

Als u de door het systeem toegewezen beheerde identiteit uit een vm wilt verwijderen, heeft uw account de roltoewijzing [voor virtuele machineinzender nodig.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

Als u een virtuele machine hebt die geen door het systeem toegewezen beheerde identiteit nodig heeft:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat. 

2. Navigeer naar de gewenste virtuele machine en selecteer **Identiteit**.

3. Selecteer **Onder Systeem toegewezen**, **Status**, selecteer **Uit** en klik vervolgens op **Opslaan:**

   ![Schermafbeelding van configuratiepagina](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

 In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit toevoegt en verwijdert van een VM met behulp van de Azure-portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Een door de gebruiker toegewezen identiteit toewijzen tijdens het maken van een vm

Als u een door de gebruiker toegewezen identiteit aan een vm wilt toewijzen, heeft uw account de roltoewijzingen [Virtual Machine Contributor](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en Managed Identity [Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nodig. Er zijn geen extra Azure AD-maproltoewijzingen vereist.

Momenteel biedt de Azure-portal geen ondersteuning voor het toewijzen van een door de gebruiker toegewezen beheerde identiteit tijdens het maken van een vm. In plaats daarvan verwijst u naar een van de volgende snelstartartikelen voor het maken van VM om eerst een vm te maken en vervolgens naar de volgende sectie te gaan voor meer informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit aan de VM:

- [Een virtuele Windows-machine maken met Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Een virtuele Linux-machine maken met Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande virtuele machine

Als u een door de gebruiker toegewezen identiteit aan een vm wilt toewijzen, heeft uw account de roltoewijzingen [Virtual Machine Contributor](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en Managed Identity [Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nodig. Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat.
2. Navigeer naar de gewenste VM en klik op **Identiteit**, **Gebruiker toegewezen** en voeg vervolgens ** \+toe**.

   ![Beheerde identiteit waaraan door de gebruiker is toegewezen toetevoegen aan VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Klik op de door de gebruiker toegewezen identiteit die u aan de vm wilt toevoegen en klik vervolgens op **Toevoegen**.

    ![Beheerde identiteit waaraan door de gebruiker is toegewezen toetevoegen aan VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Een door de gebruiker toegewezen beheerde identiteit uit een virtuele machine verwijderen

Als u een door de gebruiker toegewezen identiteit uit een vm wilt verwijderen, heeft uw account de roltoewijzing [voor virtuele machineinzender](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) nodig. Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat.
2. Navigeer naar de gewenste VM en klik op **Identiteit**, **Toegewezen gebruiker**, de naam van de door de gebruiker toegewezen beheerde identiteit die u wilt verwijderen en klik vervolgens op **Verwijderen** (klik op **Ja** in het bevestigingsdeelvenster).

   ![Beheerde identiteit van de gebruiker verwijderen uit een virtuele machine](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Volgende stappen

- Geef de beheerde identiteit van een Azure VM [toegang tot een andere Azure-bron](howto-assign-access-portal.md)via de Azure-portal.

