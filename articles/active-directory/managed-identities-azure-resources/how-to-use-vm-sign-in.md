---
title: Beheerde identiteiten gebruiken op een Azure VM voor aanmelden - Azure AD
description: Stapsgewijze instructies en voorbeelden voor het gebruik van een Azure VM-beheerde identiteiten voor Azure resources service principal voor het aanmelden van scriptclients en brontoegang.
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
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34f4dc749c0254b5aa4e9ff018d2a869832de3f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547392"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Beheerde identiteiten voor Azure-resources gebruiken op een Azure VM voor aanmelden 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
In dit artikel worden PowerShell- en CLI-scriptvoorbeelden weergegeven voor aanmelding met beheerde identiteiten voor de principal azure resources service en richtlijnen voor belangrijke onderwerpen zoals foutafhandeling.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Als u van plan bent de azure PowerShell- of Azure CLI-voorbeelden in dit artikel te gebruiken, moet u de nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps) of [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)installeren. 

> [!IMPORTANT]
> - In alle voorbeeldscripts in dit artikel wordt ervan uitgegaan dat de opdrachtregelclient wordt uitgevoerd op een vm met beheerde identiteiten voor Azure-resources ingeschakeld. Gebruik de functie VM 'Verbinden' in de Azure-portal om op afstand verbinding te maken met uw vm. Zie [Beheerde identiteiten voor Azure-resources op een VM configureren met behulp van de Azure-portal](qs-configure-portal-windows-vm.md)of een van de varianten (met PowerShell, CLI, een sjabloon of een Azure SDK) voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources op een vm. 
> - Om fouten tijdens brontoegang te voorkomen, moet de beheerde identiteit van de VM ten minste 'Reader'-toegang krijgen op het juiste bereik (de VM of hoger) om Azure Resource Manager-bewerkingen op de VM toe te staan. Zie [Beheerde identiteiten toewijzen voor Azure-bronnen toegang tot een bron met behulp van de Azure-portal](howto-assign-access-portal.md) voor meer informatie.

## <a name="overview"></a>Overzicht

Beheerde identiteiten voor Azure-resources bieden een [serviceprincipalobject,](../develop/developer-glossary.md#service-principal-object) dat wordt [gemaakt bij het inschakelen van beheerde identiteiten voor Azure-resources](overview.md#how-does-the-managed-identities-for-azure-resources-work) op de VM. De serviceprincipal kan toegang krijgen tot Azure-bronnen en worden gebruikt als een identiteit door script-/opdrachtregelclients voor aanmelding en brontoegang. Traditioneel, om toegang te krijgen tot beveiligde bronnen onder zijn eigen identiteit, zou een scriptclient:  

   - worden geregistreerd en goedgekeurd met Azure AD als een vertrouwelijke/webclienttoepassing
   - aanmelden onder de serviceprincipal, met behulp van de referenties van de app (die waarschijnlijk zijn ingebed in het script)

Met beheerde identiteiten voor Azure-resources hoeft uw scriptclient dit ook niet meer te doen, omdat deze zich kan aanmelden onder de beheerde identiteiten voor de principal van azure resources service. 

## <a name="azure-cli"></a>Azure-CLI

In het volgende script wordt uitgelegd hoe u:

1. Meld u aan bij Azure AD onder de beheerde identiteit van de VM voor de principal Azure Resources Service  
2. Bel Azure Resource Manager en verkrijg de serviceprincipal-id van de VM. CLI zorgt voor het automatisch beheren van token acquisitie/gebruik voor u. Zorg ervoor dat u uw `<VM-NAME>`virtuele machine naam te vervangen door .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

In het volgende script wordt uitgelegd hoe u:

1. Meld u aan bij Azure AD onder de beheerde identiteit van de VM voor de principal Azure Resources Service  
2. Bel een Azure Resource Manager-cmdlet om informatie over de VM op te vragen. PowerShell zorgt voor het automatisch beheren van tokengebruik.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Bron-iD's voor Azure-services

Zie [Azure-services die Azure AD-verificatie ondersteunen](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) voor een lijst met bronnen die Azure AD ondersteunen en zijn getest met beheerde identiteiten voor Azure-resources en hun respectieve bron-id's.

## <a name="error-handling-guidance"></a>Richtlijnen voor foutafhandeling 

Antwoorden zoals de volgende kunnen aangeven dat de beheerde identiteit van de VM voor Azure-resources niet correct is geconfigureerd:

- PowerShell: *Invoke-WebRequest: Kan geen verbinding maken met de externe server*
- CLI: *MSI: Kan geen `http://localhost:50342/oauth2/token` token ophalen met een foutmelding 'HTTPConnectionPool(host='localhost', port=50342)* 

Als u een van deze fouten ontvangt, gaat u terug naar de Azure VM in de [Azure-portal](https://portal.azure.com) en:

- Ga naar de **pagina Identiteit** en zorg ervoor dat **Systeem toegewezen** is ingesteld op 'Ja'.
- Ga naar de pagina **Extensies** en zorg ervoor dat de beheerde identiteiten voor Azure-resources-extensie **(gepland voor afschaffing in januari 2019)** met succes zijn geïmplementeerd.

Als een van beide onjuist is, moet u mogelijk de beheerde identiteiten voor Azure-resources op uw bron opnieuw implementeren of de implementatiefout oplossen. Zie [Beheerde identiteiten configureren voor Azure-resources op een VM met behulp van de Azure-portal](qs-configure-portal-windows-vm.md) als u hulp nodig hebt bij vm-configuratie.

## <a name="next-steps"></a>Volgende stappen

- Zie [Beheerde identiteiten voor Azure-resources configureren voor Azure-resources op een Azure VM met PowerShell](qs-configure-powershell-windows-vm.md)of [Beheerde identiteiten configureren voor Azure-resources op een Azure VM met Azure CLI](qs-configure-cli-windows-vm.md)






