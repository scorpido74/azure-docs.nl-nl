---
title: Veelgestelde vragen en bekende problemen met beheerde identiteiten - Azure AD
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
ms.openlocfilehash: f0f0c678f2426d9de58d2ab337c56243394b4d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266531"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Veelgestelde vragen (veelgestelde vragen)

> [!NOTE]
> Beheerde identiteiten voor Azure-resources is de nieuwe naam voor de service die eerder de naam Managed Service Identity (MSI) had.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Werken beheerde identiteiten voor Azure-resources met Azure Cloud Services?

Nee, er zijn geen plannen om beheerde identiteiten voor Azure-bronnen in Azure Cloud Services te ondersteunen.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Werken beheerde identiteiten voor Azure-bronnen met de Active Directory Authentication Library (ADAL) of de Microsoft Authentication Library (MSAL)?

Nee, beheerde identiteiten voor Azure-resources zijn nog niet geïntegreerd met ADAL of MSAL. Zie [Beheerde identiteiten voor Azure-resources gebruiken voor](how-to-use-vm-token.md)meer informatie over het verkrijgen van een token voor beheerde identiteiten voor Azure-resources met behulp van het REST-eindpunt.

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Wat is de beveiligingsgrens van beheerde identiteiten voor Azure-resources?

De beveiligingsgrens van de identiteit is de bron waaraan deze is gekoppeld. De beveiligingsgrens voor een virtuele machine met beheerde identiteiten voor Azure-resources is bijvoorbeeld de virtuele machine. Elke code die op die VM wordt uitgevoerd, kan de beheerde identiteiten voor het eindpunt van Azure-bronnen aanroepen en tokens aanvragen. Het is de vergelijkbare ervaring met andere resources die beheerde identiteiten voor Azure-bronnen ondersteunen.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Welke identiteit wordt IMDS standaard ingesteld als de identiteit in het verzoek niet wordt opgegeven?

- Als beheerde identiteit met systeem toegewezen is ingeschakeld en er geen identiteit is opgegeven in de aanvraag, wordt IMDS standaard ingesteld op de door het systeem toegewezen beheerde identiteit.
- Als beheerde identiteit met systeem toegewezen niet is ingeschakeld en er slechts één door de gebruiker toegewezen beheerde identiteit bestaat, wordt IMDS standaard ingesteld op de door één gebruiker toegewezen beheerde identiteit. 
- Als beheerde identiteit met systeemtoegewezen niet is ingeschakeld en er meerdere door de gebruiker toegewezen beheerde identiteiten bestaan, is het opgeven van een beheerde identiteit in de aanvraag vereist.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Moet ik de beheerde identiteiten gebruiken voor het IMDS-eindpunt van Azure-bronnen of het eindpunt van de VM-extensie?

Wanneer u beheerde identiteiten gebruikt voor Azure-resources met VM's, raden we u aan het IMDS-eindpunt te gebruiken. De Azure Instance Metadata Service is een REST Endpoint dat toegankelijk is voor alle IaaS VM's die zijn gemaakt via Azure Resource Manager. 

Enkele van de voordelen van het gebruik van beheerde identiteiten voor Azure-resources via IMDS zijn:
- Alle door Azure IaaS ondersteunde besturingssystemen kunnen beheerde identiteiten voor Azure-resources via IMDS gebruiken.
- U hoeft niet langer een extensie op uw VM te installeren om beheerde identiteiten voor Azure-resources in te schakelen. 
- De certificaten die worden gebruikt door beheerde identiteiten voor Azure-resources zijn niet langer aanwezig in de VM.
- Het IMDS-eindpunt is een bekend niet-routeerbaar IP-adres, alleen beschikbaar vanuit de VM.
- Aan één vm kunnen 1000 door de gebruiker toegewezen beheerde identiteiten worden toegewezen. 

De beheerde identiteiten voor de VM-extensie voor Azure-bronnen zijn nog steeds beschikbaar. we ontwikkelen er echter geen nieuwe functionaliteit meer op. We raden u aan over te schakelen naar het IMDS-eindpunt. 

Enkele van de beperkingen van het gebruik van het VM-uitbreidingseindpunt zijn:
- Beperkte ondersteuning voor Linux-distributies: CoreOS Stable, CentOS 7.1, Red Hat 7.2, Ubuntu 15.04, Ubuntu 16.04
- Aan de VM kunnen slechts 32 door de gebruiker toegewezen beheerde identiteiten worden toegewezen.


Opmerking: de beheerde identiteiten voor de VM-extensie voor Azure-bronnen zijn in januari 2019 niet meer beschikbaar. 

Zie [IMDS-documentatie](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service) voor meer informatie over Azure Instance Metadata Service

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Worden beheerde identiteiten automatisch opnieuw gemaakt als ik een abonnement naar een andere map verplaats?

Nee. Als u een abonnement naar een andere map verplaatst, moet u deze handmatig opnieuw maken en Azure RBAC-roltoewijzingen opnieuw verlenen.
- Voor beheerde identiteiten met het systeem: uitschakelen en opnieuw inschakelen. 
- Voor door de gebruiker toegewezen beheerde identiteiten: verwijder, maak ze opnieuw en koppel ze opnieuw aan de benodigde resources (bijvoorbeeld virtuele machines)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Kan ik een beheerde identiteit gebruiken om toegang te krijgen tot een bron in een andere map/tenant?

Nee. Beheerde identiteiten ondersteunen momenteel geen cross-directory scenario's. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Welke Azure RBAC-machtigingen zijn vereist om de identiteit op een resource te beheren? 

- Beheerde identiteit met systeemtoegewezen: u hebt schrijfmachtigingen nodig voor de resource. Voor virtuele machines hebt u bijvoorbeeld Microsoft. Compute/virtualMachines/write nodig. Deze actie is opgenomen in resourcespecifieke ingebouwde rollen zoals [Virtuele machinebijdrager.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)
- Door de gebruiker toegewezen beheerde identiteit: u hebt schrijfmachtigingen nodig voor de bron. Voor virtuele machines hebt u bijvoorbeeld Microsoft. Compute/virtualMachines/write nodig. Naast [Managed Identity Operator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) roltoewijzing over de beheerde identiteit.

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Hoe start u de beheerde identiteiten opnieuw voor azure resources extensie?
Op Windows en bepaalde versies van Linux, als de extensie stopt, kan de volgende cmdlet worden gebruikt om handmatig opnieuw op te starten:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Waar: 
- Extensienaam en -type voor Windows is: ManagedIdentityExtensionForWindows
- Uitbreiding naam en type voor Linux is: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Bekende problemen

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>'Automatiseringsscript' mislukt bij het uitvoeren van schema's voor beheerde identiteiten voor Azure-resources-extensie

Wanneer beheerde identiteiten voor Azure-resources zijn ingeschakeld op een vm, wordt de volgende fout weergegeven wanneer u probeert de functie 'Automatiseringsscript' te gebruiken voor de VM of de brongroep:

![Fout beheerde identiteiten voor azure-resources-automatiseringsscript-exportfout](./media/msi-known-issues/automation-script-export-error.png)

De beheerde identiteiten voor de VM-extensie voor Azure-resources (gepland voor afschaffing in januari 2019) bieden momenteel geen ondersteuning voor de mogelijkheid om het schema te exporteren naar een sjabloon voor resourcegroepen. Als gevolg hiervan worden in de gegenereerde sjabloon geen configuratieparameters weergegeven om beheerde identiteiten voor Azure-resources op de bron in te schakelen. Deze secties kunnen handmatig worden toegevoegd door de voorbeelden te volgen in [Beheerde identiteiten configureren voor Azure-resources op een Azure VM met behulp van een sjablonen.](qs-configure-template-windows-vm.md)

Wanneer de functie voor het exporteren van schema's beschikbaar komt voor de beheerde identiteiten voor de VM-extensie voor Azure-resources (gepland voor afschaffing in januari 2019), wordt deze weergegeven in [Resourcegroepen exporteren die VM-extensies bevatten.](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>VM kan niet worden gestart nadat deze is verplaatst van resourcegroep of -abonnement

Als u een virtuele machine in de loopstatus verplaatst, wordt deze tijdens de verplaatsing voortgezet. Echter, na de verhuizing, als de VM is gestopt en opnieuw opgestart, zal het niet starten. Dit probleem treedt op omdat de VM de verwijzing naar de beheerde identiteit van Azure-bronnen niet bijwerkt en blijft verwijzen naar de oude brongroep.

**Oplossing** 
 
Activeer een update op de VM, zodat deze de juiste waarden kan krijgen voor de beheerde identiteiten voor Azure-resources. U een wijziging van de VM-eigenschap uitvoeren om de verwijzing naar de beheerde identiteit van azure-bronnen bij te werken. U bijvoorbeeld een nieuwe tagwaarde op de VM instellen met de volgende opdracht:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Met deze opdracht wordt een nieuwe tag "fixVM" ingesteld met een waarde van 1 op de VM. 
 
Door deze eigenschap in te stellen, wordt de VM bijgewerkt met de juiste beheerde identiteiten voor Azure-bronURI en moet u de VM kunnen starten. 
 
Zodra de vm is gestart, kan de tag worden verwijderd met behulp van de volgende opdracht:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>VM-extensiebepaling mislukt

De inrichting van de VM-extensie kan mislukken als gevolg van DNS-opzoekfouten. Start de VM opnieuw op en probeer het opnieuw.
 
> [!NOTE]
> De VM-uitbreiding is gepland voor januari 2019. We raden u aan het IMDS-eindpunt te gebruiken.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Een abonnement overbrengen tussen Azure AD-mappen

Beheerde identiteiten worden niet bijgewerkt wanneer een abonnement wordt verplaatst/overgedragen naar een andere directory. Als gevolg hiervan worden bestaande door het systeem toegewezen of door de gebruiker toegewezen beheerde identiteiten verbroken. 

Tijdelijke oplossing voor beheerde identiteiten in een abonnement dat is verplaatst naar een andere map:

 - Voor beheerde identiteiten met het systeem: uitschakelen en opnieuw inschakelen. 
 - Voor door de gebruiker toegewezen beheerde identiteiten: verwijder, maak ze opnieuw en koppel ze opnieuw aan de benodigde resources (bijvoorbeeld virtuele machines)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Een door de gebruiker toegewezen beheerde identiteit verplaatsen naar een andere resourcegroep/-abonnement

Als u een door de gebruiker toegewezen beheerde identiteit verplaatst naar een andere resourcegroep, wordt de identiteit afgebroken. Als gevolg hiervan kunnen resources (bijvoorbeeld VM) die die identiteit gebruiken, er geen tokens voor aanvragen. 
