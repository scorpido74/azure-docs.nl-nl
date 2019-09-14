---
title: Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources
description: Bekende problemen met beheerde identiteiten voor Azure-resources.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d882b34bc4f057035a16b7916249cfe8f0b8d0b
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983436"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Veelgestelde vragen

> [!NOTE]
> Beheerde identiteiten voor Azure-resources is de nieuwe naam voor de service die eerder de naam Managed Service Identity (MSI) had.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Werken beheerde identiteiten voor Azure-resources met Azure Cloud Services?

Nee, er zijn geen plannen voor het ondersteunen van beheerde identiteiten voor Azure-resources in azure Cloud Services.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Werken beheerde identiteiten voor Azure-resources met de Active Directory Authentication Library (ADAL) of de micro soft Authentication Library (MSAL)?

Nee, beheerde identiteiten voor Azure-resources zijn nog niet geïntegreerd met ADAL of MSAL. Ga voor meer informatie over het verkrijgen van een token voor beheerde identiteiten voor Azure-resources met het REST-eind punt naar [Managed Identities voor Azure resources op een Azure VM gebruiken om een toegangs token te verkrijgen](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Wat is de beveiligings grens van beheerde identiteiten voor Azure-resources?

De beveiligings grens van de identiteit is de resource waaraan deze is gekoppeld. Zo is de beveiligings grens voor een virtuele machine met beheerde identiteiten voor Azure-resources ingeschakeld, de virtuele machine. Alle code die op die virtuele machine wordt uitgevoerd, kan de beheerde identiteiten voor het eind punt van Azure-resources aanroepen en tokens aanvragen. Het is een vergelijk bare ervaring met andere resources die beheerde identiteiten voor Azure-resources ondersteunen.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Met welke identiteit wordt de IMDS standaard ingesteld als de identiteit in de aanvraag niet wordt opgegeven?

- Als door het systeem toegewezen beheerde identiteit is ingeschakeld en er geen identiteit is opgegeven in de aanvraag, wordt IMDS standaard ingesteld op de door het systeem toegewezen beheerde identiteit.
- Als door het systeem toegewezen beheerde identiteit niet is ingeschakeld en er slechts één door de gebruiker toegewezen beheerde identiteit bestaat, wordt IMDS standaard ingesteld op de door de gebruiker toegewezen beheerde identiteit. 
- Als door het systeem toegewezen beheerde identiteit niet is ingeschakeld en er meerdere door de gebruiker toegewezen beheerde identiteiten bestaan, is het opgeven van een beheerde identiteit in de aanvraag vereist.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Moet ik de beheerde identiteiten voor Azure resources IMDS-eind punt of het eind punt van de VM-extensie gebruiken?

Wanneer u beheerde identiteiten voor Azure-resources met Vm's gebruikt, raden we u aan het IMDS-eind punt te gebruiken. De Azure-Instance Metadata Service is een REST-eind punt dat toegankelijk is voor alle virtuele IaaS-machines die via de Azure Resource Manager zijn gemaakt. 

Enkele van de voor delen van het gebruik van beheerde identiteiten voor Azure-bronnen via IMDS zijn:
- Alle door Azure IaaS ondersteunde besturings systemen kunnen beheerde identiteiten gebruiken voor Azure-resources via IMDS.
- U hoeft geen uitbrei ding meer op uw virtuele machine te installeren om beheerde identiteiten voor Azure-resources in te scha kelen. 
- De certificaten die worden gebruikt door beheerde identiteiten voor Azure-resources, zijn niet meer aanwezig in de VM.
- Het IMDS-eind punt is een bekend, niet-routeerbaar IP-adres dat alleen beschikbaar is vanuit de virtuele machine.
- 1000 door de gebruiker toegewezen beheerde identiteiten kunnen worden toegewezen aan één virtuele machine. 

De beheerde identiteiten voor de VM-extensie van Azure-resources zijn nog steeds beschikbaar. We ontwikkelen echter geen nieuwe functionaliteit meer. U kunt het beste overschakelen op het gebruik van het IMDS-eind punt. 

Enkele van de beperkingen van het gebruik van het eind punt van de VM-extensie zijn:
- Beperkte ondersteuning voor Linux-distributies: CoreOS stabiel, CentOS 7,1, Red Hat 7,2, Ubuntu 15,04, Ubuntu 16,04
- Er kunnen slechts 32 door de gebruiker toegewezen beheerde identiteiten aan de virtuele machine worden toegewezen.


Opmerking: De beheerde identiteiten voor de VM-extensie van Azure-resources zijn in januari 2019 niet meer in aanmerking komen. 

Zie [IMDS-documentatie](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service) voor meer informatie over Azure instance metadata service

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Worden beheerde identiteiten automatisch opnieuw gemaakt als ik een abonnement naar een andere map Verplaats?

Nee. Als u een abonnement naar een andere map verplaatst, moet u deze hand matig opnieuw maken en de toewijzingen van Azure RBAC-rollen opnieuw verlenen.
- Voor door het systeem toegewezen beheerde identiteiten: uitschakelen en opnieuw inschakelen. 
- Voor door de gebruiker toegewezen beheerde identiteiten: verwijderen, opnieuw maken en opnieuw koppelen aan de benodigde resources (bijvoorbeeld virtuele machines)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Kan ik een beheerde identiteit gebruiken om toegang te krijgen tot een resource in een andere Directory/Tenant?

Nee. Beheerde identiteiten bieden momenteel geen ondersteuning voor scenario's met meerdere mappen. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Welke Azure RBAC-machtigingen zijn vereist voor de beheerde identiteit van een resource? 

- Door het systeem toegewezen beheerde identiteit: U hebt schrijf machtigingen nodig voor de resource. Voor virtuele machines hebt u bijvoorbeeld micro soft. Compute/informatie/write nodig. Deze actie is opgenomen in resource-specifieke ingebouwde rollen als [Inzender voor virtuele machines](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).
- Door de gebruiker toegewezen beheerde identiteit: U hebt schrijf machtigingen nodig voor de resource. Voor virtuele machines hebt u bijvoorbeeld micro soft. Compute/informatie/write nodig. Naast de roltoewijzing voor [beheerde identiteits operatoren](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) via de beheerde identiteit.

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Hoe start u de beheerde identiteiten voor Azure resources extension opnieuw?
In Windows en bepaalde versies van Linux, als de extensie stopt, kan de volgende cmdlet worden gebruikt om deze hand matig opnieuw te starten:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Waar: 
- De naam en het type van de extensie voor Windows is: ManagedIdentityExtensionForWindows
- De naam en het type van de extensie voor Linux is: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Bekende problemen

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Automatiserings script mislukt bij het proberen van schema export voor beheerde identiteiten voor Azure-bronnen uitbreiding

Wanneer beheerde identiteiten voor Azure-resources zijn ingeschakeld op een virtuele machine, wordt de volgende fout weer gegeven wanneer u probeert de functie Automation script te gebruiken voor de virtuele machine of de bijbehorende resource groep:

![Export fout van beheerde identiteiten voor het automatiserings script van Azure-resources](./media/msi-known-issues/automation-script-export-error.png)

De beheerde identiteiten voor de VM-extensie van Azure resources (gepland voor afschaffing in januari 2019) biedt momenteel geen ondersteuning voor de mogelijkheid om het schema te exporteren naar een sjabloon voor een resource groep. Als gevolg hiervan worden door de gegenereerde sjabloon geen configuratie parameters weer gegeven voor het inschakelen van beheerde identiteiten voor Azure-resources op de resource. Deze secties kunnen hand matig worden toegevoegd met behulp van de voor beelden in [beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met een sjabloon](qs-configure-template-windows-vm.md).

Wanneer de functie voor het exporteren van het schema beschikbaar wordt voor de VM-extensie Managed Identities voor Azure resources (gepland voor afschaffing in januari 2019), wordt deze weer gegeven bij het [exporteren van resource groepen die VM-extensies bevatten](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>De virtuele machine kan niet worden gestart na het verplaatsen van de resource groep of het abonnement

Als u een virtuele machine met de status actief verplaatst, blijft deze tijdens de verplaatsing actief. Als de virtuele machine eenmaal is gestopt en opnieuw wordt gestart, kan deze echter niet worden gestart. Dit probleem doet zich voor omdat de virtuele machine de verwijzing naar de beheerde identiteiten voor de Azure-resources-identiteit niet bijwerkt en blijft verwijzen naar deze in de oude resource groep.

**Tijdelijke oplossing** 
 
Activeer een update op de VM zodat deze de juiste waarden kan ophalen voor de beheerde identiteiten voor Azure-resources. U kunt een wijziging van de VM-eigenschap uitvoeren om de verwijzing naar de beheerde identiteiten voor de Azure-resources-identiteit bij te werken. U kunt bijvoorbeeld een nieuwe label waarde op de VM instellen met de volgende opdracht:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Met deze opdracht wordt een nieuwe tag ' fixVM ' ingesteld met de waarde 1 in de virtuele machine. 
 
Door deze eigenschap in te stellen, wordt de VM bijgewerkt met de juiste beheerde identiteiten voor de resource-URI van Azure resources. vervolgens moet u de virtuele machine kunnen starten. 
 
Zodra de VM is gestart, kan de tag worden verwijderd met behulp van de volgende opdracht:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Het inrichten van de VM-extensie mislukt

Het inrichten van de VM-extensie kan mislukken vanwege fouten in de DNS-zoek actie. Start de virtuele machine opnieuw op en probeer het opnieuw.
 
> [!NOTE]
> De VM-extensie wordt gepland voor afschaffing op 2019 januari. U wordt aangeraden over te stappen naar het IMDS-eind punt te gebruiken.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Een abonnement verplaatsen tussen Azure AD-directory's

Beheerde identiteiten worden niet bijgewerkt wanneer een abonnement wordt verplaatst of overgedragen naar een andere map. Als gevolg hiervan worden bestaande door het systeem toegewezen of door de gebruiker toegewezen beheerde identiteiten verbroken. 

Tijdelijke oplossing voor beheerde identiteiten in een abonnement dat is verplaatst naar een andere map:

 - Voor door het systeem toegewezen beheerde identiteiten: uitschakelen en opnieuw inschakelen. 
 - Voor door de gebruiker toegewezen beheerde identiteiten: verwijderen, opnieuw maken en opnieuw koppelen aan de benodigde resources (bijvoorbeeld virtuele machines)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Een door de gebruiker toegewezen beheerde identiteit verplaatsen naar een andere resource groep of een ander abonnement

Als een door de gebruiker toegewezen beheerde identiteit wordt verplaatst naar een andere resource groep, wordt de identiteit verbroken. Als gevolg hiervan kunnen bronnen (bijvoorbeeld virtuele machines) die met die identiteit worden gebruikt, geen tokens meer aanvragen. 
