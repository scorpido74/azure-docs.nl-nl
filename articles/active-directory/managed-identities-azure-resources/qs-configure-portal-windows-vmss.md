---
title: Beheerde identiteiten configureren in azure VMSS-Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een schaalset voor virtuele machines met behulp van de Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39e2d72ba63633d7c5d3da3094cfa00e076513af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609109"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Configureer beheerde identiteiten voor Azure-resources op een schaalset voor virtuele machines met behulp van de Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel, met behulp van Power shell, leert u hoe u de volgende beheerde identiteiten voor Azure-bronnen bewerkingen kunt uitvoeren op een schaalset voor virtuele machines:

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md).
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de beheer bewerkingen in dit artikel wilt uitvoeren, moet uw account de volgende toegangs beheer toewijzingen op basis van Azure-rollen hebben:

    > [!NOTE]
    > Er zijn geen aanvullende Azure AD Directory-roltoewijzingen vereist.

    - [Inzender voor virtuele machines](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) om door het systeem toegewezen beheerde identiteit in te scha kelen en te verwijderen uit een schaalset met virtuele machines.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie wordt beschreven hoe u de door het systeem toegewezen beheerde identiteit in-en uitschakelt met behulp van de Azure Portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een schaalset voor virtuele machines

Op dit moment biedt de Azure Portal geen ondersteuning voor het inschakelen van door het systeem toegewezen beheerde identiteit tijdens het maken van een schaalset voor virtuele machines. In plaats daarvan raadpleegt u het artikel Snelstartgids voor het maken van virtuele-machine schaal sets om eerst een schaalset voor virtuele machines te maken en gaat u verder met de volgende sectie voor meer informatie over het inschakelen van door het systeem toegewezen beheerde identiteit op een schaalset voor virtuele machines:

- [Een Schaalset voor virtuele machines maken in de Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen op een bestaande schaalset voor virtuele machines

Om de door het systeem toegewezen beheerde identiteit in te scha kelen op een schaalset voor virtuele machines die oorspronkelijk is ingericht zonder deze:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement dat de schaalset voor virtuele machines bevat.

2. Navigeer naar de gewenste schaalset voor virtuele machines.

3. Onder **toegewezen systeem**, **status**, selecteer **aan** en klik vervolgens op **Opslaan**:

   ![Scherm afbeelding configuratie pagina](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit verwijderen uit een schaalset voor virtuele machines

Als u een schaalset voor virtuele machines hebt waarvoor geen door het systeem toegewezen beheerde identiteit meer nodig is, doet u het volgende:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement dat de schaalset voor virtuele machines bevat. Zorg er ook voor dat uw account deel uitmaakt van een rol waarmee u schrijf machtigingen hebt voor de schaalset van de virtuele machine.

2. Navigeer naar de gewenste schaalset voor virtuele machines.

3. Onder **toegewezen systeem**, **status**, selecteer **uit** en klik vervolgens op **Opslaan**:

   ![Scherm afbeelding configuratie pagina](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt toevoegen aan en verwijderen uit een schaalset voor virtuele machines met behulp van de Azure Portal.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen tijdens het maken van een schaalset voor virtuele machines

Op dit moment biedt de Azure Portal geen ondersteuning voor het toewijzen van een door de gebruiker toegewezen beheerde identiteit tijdens het maken van een schaalset voor virtuele machines. In plaats daarvan raadpleegt u het artikel Snelstartgids voor het maken van virtuele-machine schaal sets om eerst een schaalset voor virtuele machines te maken en gaat u verder met de volgende sectie voor meer informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit:

- [Een Schaalset voor virtuele machines maken in de Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande schaalset voor virtuele machines

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement dat de schaalset voor virtuele machines bevat.
2. Ga naar de gewenste schaalset voor virtuele machines en klik op **identiteit**, **gebruiker toegewezen** en vervolgens ** \+ toevoegen**.

   ![Door gebruiker toegewezen identiteit toevoegen aan VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Klik op de door de gebruiker toegewezen identiteit die u wilt toevoegen aan de schaalset voor virtuele machines en klik vervolgens op **toevoegen**.
   
   ![Door gebruiker toegewezen identiteit toevoegen aan VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een schaalset voor virtuele machines

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.
2. Ga naar de gewenste schaalset voor virtuele machines en klik op **identiteit**, **gebruiker toegewezen**, de naam van de door de gebruiker toegewezen beheerde identiteit die u wilt verwijderen en klik vervolgens op **verwijderen** (Klik op **Ja** in het bevestigings venster).

   ![Door gebruiker toegewezen identiteit verwijderen uit een VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Volgende stappen

- Geef met behulp van de Azure Portal een virtuele-machine schaalset van Azure beheerde identiteits [toegang tot een andere Azure-resource](howto-assign-access-portal.md).


