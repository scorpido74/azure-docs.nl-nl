---
title: Veelgestelde vragen en bekende problemen met beheerde identiteiten-Azure AD
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
ms.custom: has-adal-ref
ms.openlocfilehash: 971ec1fcda87a9db61147133604dd0e28cc4d102
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976163"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Veelgestelde vragen (FAQ)

> [!NOTE]
> Beheerde identiteiten voor Azure-resources is de nieuwe naam voor de service die eerder de naam Managed Service Identity (MSI) had.


### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Hoe kunt u resources vinden die een beheerde identiteit hebben?

U kunt de lijst met resources die een door het systeem toegewezen beheerde identiteit hebben, vinden met behulp van de volgende Azure CLI-opdracht: 

`az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table`




### <a name="do-managed-identities-have-a-backing-app-object"></a>Hebben beheerde identiteiten een object voor een back-uptoepassing?

Nee. Beheerde identiteiten en Azure AD-app registraties zijn niet hetzelfde als in de Directory. 

App-registraties hebben twee onderdelen: een toepassings object + een Service-Principal-object. Beheerde identiteiten voor Azure-resources hebben slechts een van deze onderdelen: een Service-Principal-object. 

Beheerde identiteiten hebben geen toepassings object in de map, wat vaak wordt gebruikt om app-machtigingen voor MS Graph te verlenen. In plaats daarvan moeten de MS Graph-machtigingen voor beheerde identiteiten rechtstreeks aan de service-principal worden verleend.  

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



### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Worden beheerde identiteiten automatisch opnieuw gemaakt als ik een abonnement naar een andere map Verplaats?

Nee. Als u een abonnement naar een andere map verplaatst, moet u deze hand matig opnieuw maken en de toewijzingen van Azure RBAC-rollen opnieuw verlenen.
- Voor door het systeem toegewezen beheerde identiteiten: uitschakelen en opnieuw inschakelen. 
- Voor door de gebruiker toegewezen beheerde identiteiten: verwijderen, opnieuw maken en opnieuw koppelen aan de benodigde resources (bijvoorbeeld virtuele machines)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Kan ik een beheerde identiteit gebruiken om toegang te krijgen tot een resource in een andere Directory/Tenant?

Nee. Beheerde identiteiten bieden momenteel geen ondersteuning voor scenario's met meerdere mappen. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Welke Azure RBAC-machtigingen zijn vereist voor de beheerde identiteit van een resource? 

- Door het systeem toegewezen beheerde identiteit: u hebt schrijf machtigingen nodig voor de resource. Voor virtuele machines hebt u bijvoorbeeld Microsoft. Compute/virtualMachines/write nodig. Deze actie is opgenomen in resource-specifieke ingebouwde rollen als [Inzender voor virtuele machines](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).
- Door de gebruiker toegewezen beheerde identiteit: u hebt schrijf machtigingen nodig voor de resource. Voor virtuele machines hebt u bijvoorbeeld Microsoft. Compute/virtualMachines/write nodig. Naast de roltoewijzing voor [beheerde identiteits operatoren](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) via de beheerde identiteit.



## <a name="known-issues"></a>Bekende problemen

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Automatiserings script mislukt bij het proberen van schema export voor beheerde identiteiten voor Azure-bronnen uitbreiding

Wanneer beheerde identiteiten voor Azure-resources zijn ingeschakeld op een virtuele machine, wordt de volgende fout weer gegeven wanneer u probeert de functie Automation script te gebruiken voor de virtuele machine of de bijbehorende resource groep:

![Export fout van beheerde identiteiten voor het automatiserings script van Azure-resources](./media/msi-known-issues/automation-script-export-error.png)

De beheerde identiteiten voor de VM-extensie van Azure resources (gepland voor afschaffing in januari 2019) biedt momenteel geen ondersteuning voor de mogelijkheid om het schema te exporteren naar een sjabloon voor een resource groep. Als gevolg hiervan worden door de gegenereerde sjabloon geen configuratie parameters weer gegeven voor het inschakelen van beheerde identiteiten voor Azure-resources op de resource. Deze secties kunnen hand matig worden toegevoegd met behulp van de voor beelden in [beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met een sjabloon](qs-configure-template-windows-vm.md).

Wanneer de functie voor het exporteren van het schema beschikbaar wordt voor de VM-extensie Managed Identities voor Azure resources (gepland voor afschaffing in januari 2019), wordt deze weer gegeven bij het [exporteren van resource groepen die VM-extensies bevatten](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>De virtuele machine kan niet worden gestart na het verplaatsen van de resource groep of het abonnement

Als u een virtuele machine met de status actief verplaatst, blijft deze tijdens de verplaatsing actief. Als de virtuele machine eenmaal is gestopt en opnieuw wordt gestart, kan deze echter niet worden gestart. Dit probleem doet zich voor omdat de virtuele machine de verwijzing naar de beheerde identiteiten voor de Azure-resources-identiteit niet bijwerkt en blijft verwijzen naar deze in de oude resource groep.

**Enkele** 
 
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



### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Een abonnement verplaatsen tussen Azure AD-directory's

Beheerde identiteiten worden niet bijgewerkt wanneer een abonnement wordt verplaatst of overgedragen naar een andere map. Als gevolg hiervan worden bestaande door het systeem toegewezen of door de gebruiker toegewezen beheerde identiteiten verbroken. 

Tijdelijke oplossing voor beheerde identiteiten in een abonnement dat is verplaatst naar een andere map:

 - Voor door het systeem toegewezen beheerde identiteiten: uitschakelen en opnieuw inschakelen. 
 - Voor door de gebruiker toegewezen beheerde identiteiten: verwijderen, opnieuw maken en opnieuw koppelen aan de benodigde resources (bijvoorbeeld virtuele machines)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Een door de gebruiker toegewezen beheerde identiteit verplaatsen naar een andere resource groep of een ander abonnement

Het verplaatsen van een door de gebruiker toegewezen beheerde identiteit naar een andere resource groep wordt niet ondersteund.
