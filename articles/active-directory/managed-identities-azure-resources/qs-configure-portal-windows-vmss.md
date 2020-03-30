---
title: Beheerde identiteiten configureren op Azure VMSS - Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een virtuele machineschaalset met behulp van de Azure-portal.
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
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32d8944b55794412eb322697469933030e2fbb56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184038"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Beheerde identiteiten configureren voor Azure-resources op een virtuele machineschaalset met behulp van de Azure-portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben. 

In dit artikel leert u met PowerShell hoe u de volgende beheerde identiteiten uitvoert voor Azure-resourcesbewerkingen op een virtuele machineschaalset:

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md).
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de beheerbewerkingen in dit artikel wilt uitvoeren, heeft uw account de volgende op Azure-rollen gebaseerde toegangsbeheertoewijzingen nodig:

    > [!NOTE]
    > Er zijn geen extra Azure AD-maproltoewijzingen vereist.

    - [Virtuele machinebijdrager](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) om beheerde identiteit met het systeem in te schakelen en te verwijderen uit een virtuele machineschaalset.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u de door het systeem toegewezen beheerde identiteit in- en uitschakelen met behulp van de Azure-portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Beheerde identiteit met systeemtoegewezen status inschakelen tijdens het maken van een virtuele machineschaalset

Momenteel biedt de Azure-portal geen ondersteuning voor het inschakelen van door het systeem toegewezen beheerde identiteit tijdens het maken van een virtuele machineschaalset. In plaats daarvan verwijst u naar het volgende artikel voor het maken van een snelle start-artikel voor virtuele machineom eerst een virtuele machineschaalset te maken en vervolgens naar de volgende sectie te gaan voor meer informatie over het inschakelen van beheerde identiteit met systeemtoegewezen op een virtuele machineschaalset:

- [Een virtuele machineschaalset maken in de Azure-portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Beheerde identiteit met systeem toegewezen op een bestaande virtuele machineschaalset inschakelen

Ga als lid van het systeem om de beheerde identiteit in te schakelen op een virtuele machineschaalset die oorspronkelijk zonder deze is ingericht:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de grootteset voor virtuele machines bevat.

2. Navigeer naar de gewenste virtuele machineschaalset.

3. Selecteer **Onder Systeem toegewezen**, **Status**, selecteer **Aan** en klik vervolgens op **Opslaan:**

   ![Schermafbeelding van configuratiepagina](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Beheerde identiteit met systeemtoegewezen identiteit verwijderen uit een schaalset van virtuele machine

Als u een virtuele machineschaalset hebt die geen beheerde identiteit met systeemtoegewezen identiteit meer nodig heeft:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de grootteset voor virtuele machines bevat. Zorg er ook voor dat uw account deel uitmaakt van een rol die u schrijfmachtigingen geeft op de virtuele machineschaalset.

2. Navigeer naar de gewenste virtuele machineschaalset.

3. Selecteer **Onder Systeem toegewezen**, **Status**, selecteer **Uit** en klik vervolgens op **Opslaan:**

   ![Schermafbeelding van configuratiepagina](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit toevoegen en verwijderen uit een schaalset van virtuele machines met behulp van de Azure-portal.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen tijdens het maken van een virtuele machineschaalset

Momenteel biedt de Azure-portal geen ondersteuning voor het toewijzen van een door de gebruiker toegewezen beheerde identiteit tijdens het maken van een virtuele machineschaalset. In plaats daarvan verwijst u naar het volgende artikel voor het maken van een snelle start-artikel voor virtuele machineom eerst een virtuele machineschaalset te maken en vervolgens naar de volgende sectie te gaan voor meer informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit:

- [Een virtuele machineschaalset maken in de Azure-portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande virtuele machineschaalset

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de grootteset voor virtuele machines bevat.
2. Navigeer naar de gewenste virtuele machineschaalset en klik op **Identiteit**, **Toegewezen gebruiker** en voeg ** \+vervolgens toe**.

   ![Door gebruikers toegewezen identiteit toevoegen aan VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Klik op de door de gebruiker toegewezen identiteit die u wilt toevoegen aan de virtuele machineschaalset en klik vervolgens op **Toevoegen**.
   
   ![Door gebruikers toegewezen identiteit toevoegen aan VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een schaalset van een virtuele machine

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat.
2. Navigeer naar de gewenste virtuele machineschaalset en klik op **Identiteit**, **Toegewezen gebruiker**, de naam van de door de gebruiker toegewezen beheerde identiteit die u wilt verwijderen en klik vervolgens op **Verwijderen** (klik op **Ja** in het bevestigingsvenster).

   ![Identiteit van de gebruiker verwijderen uit een VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Volgende stappen

- Geef met de Azure-portal een Azure-modelschaalset beheerde [identiteitstoegang tot een andere Azure-bron.](howto-assign-access-portal.md)


