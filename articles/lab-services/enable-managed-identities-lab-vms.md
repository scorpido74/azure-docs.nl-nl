---
title: Beheerde identiteiten inschakelen op uw labVM's in Azure DevTest Labs
description: In dit artikel ziet u hoe een labeigenaar door gebruikers toegewezen beheerde identiteiten op uw virtuele labmachines kan inschakelen.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692665"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Beheerde identiteiten van gebruikers inschakelen op virtuele laboratoriummachines in Azure DevTest Labs
Als eigenaar van een lab u door de gebruiker toegewezen beheerde identiteiten inschakelen op uw virtuele labmachines (VM's) in Azure DevTest Labs.

Een beheerde identiteit kan worden gebruikt om te verifiëren voor elke service die Azure Active Directory (AD)-verificatie ondersteunt, inclusief Key Vault, zonder referenties in de code door te geven. Zie [Wat zijn beheerde identiteiten voor Azure-bronnen voor](../active-directory/managed-identities-azure-resources/overview.md)meer informatie over beheerde identiteiten.

Met deze functie kunnen labgebruikers Azure-bronnen zoals Azure SQL Database delen in de context van het lab. De verificatie naar de resource wordt verzorgd door de identiteit zelf. Eenmaal geconfigureerd, zal elke bestaande / nieuw gecreëerde lab VM worden ingeschakeld met deze identiteit. Labgebruikers hebben toegang tot bronnen zodra ze zijn aangemeld bij hun machines.

> [!NOTE]
> U meerdere door de gebruiker toegewezen beheerde identiteiten toevoegen die moeten worden ingeschakeld op uw vm's in het lab.

## <a name="use-azure-portal"></a>Azure Portal gebruiken
Voer de volgende stappen uit om een door een gebruiker toegewezen beheerde identiteit toe te voegen voor laboratorium-VM's:

1. [Een door de gebruiker toegewezen beheerde identiteit maken in uw abonnement](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Navigeer naar de pagina **Configuratie en beleid** voor uw lab.
2. Selecteer **Identiteit (voorbeeld)** in het linkermenu.
3. Selecteer het tabblad **Virtuele machine.**
4. Selecteer **Toevoegen** om een bestaande identiteit te selecteren in een vooraf ingevulde vervolgkeuzelijst. 

    ![Knop Identiteit toevoegen](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. Selecteer een bestaande **door de gebruiker beheerde identiteit** in de lijst met gedropte gegevens en selecteer **OK**. 

    ![Identiteit toevoegen](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>API gebruiken

1.  Noteer na het maken van een identiteit de bron-id van de identiteit. Het moet er uitzien als de volgende steekproef: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Voer een PUT HTTPS-methode uit om een nieuwe **ServiceRunner-bron** aan het lab toe te voegen, zoals in het volgende voorbeeld wordt weergegeven. 

    Service runner resource is een proxy resource voor het beheren en beheren van beheerde identiteiten in DevTest Labs. De naam van de serviceagent kan een geldige naam zijn, maar we raden u aan de naam van de beheerde identiteitsbron te gebruiken.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Volgende stappen
Zie [Wat zijn beheerde identiteiten voor Azure-bronnen voor](../active-directory/managed-identities-azure-resources/overview.md)meer informatie over beheerde identiteiten?







