---
title: Beheer door de gebruiker toegewezen beheerde identiteit - Azure CLI - Azure AD
description: Stap voor stap instructies voor het maken, aanbieden en verwijderen van een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure CLI.
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
ms.date: 04/17/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: be5defb85547e8750dea9ceaa481217aa40a004e
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639764"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Een door de gebruiker toegewezen beheerde identiteit maken, aanbieden of verwijderen met behulp van de Azure CLI


Beheerde identiteiten voor Azure-resources bieden Azure-services een beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor services die Azure AD-verificatie ondersteunen, zonder dat u referenties in uw code nodig hebt. 

In dit artikel leert u hoe u een door de gebruiker toegewezen beheerde identiteit maakt, vermeldt en verwijdert met Azure CLI.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de voorbeelden van CLI-scripten wilt uitvoeren, hebt u drie opties:
    - [Azure Cloud Shell gebruiken](../../cloud-shell/overview.md) vanuit de Azure-portal (zie volgende sectie).
    - Gebruik de ingesloten Azure Cloud Shell via de knop 'Probeer het' in de rechterbovenhoek van elk codeblok.
    - [Installeer de nieuwste versie van de Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 of hoger) als u liever een lokale CLI-console gebruikt. Meld u aan `az login`bij Azure met behulp van een account dat is gekoppeld aan het Azure-abonnement waaronder u de door de gebruiker toegewezen beheerde identiteit wilt implementeren.


> [!NOTE]
> Als u gebruikersmachtigingen wilt wijzigen wanneer u een hoofdser van de app servivce gebruikt met CLI, moet u de serviceprincipal extra machtigingen in azure AD Graph API opgeven, omdat delen van CLI GET-aanvragen uitvoeren tegen de Graph API. Anders u uiteindelijk een bericht 'Onvoldoende bevoegdheden om de bewerking te voltooien' ontvangen. Om dit te doen, moet u naar de app-registratie in Azure Active Directory gaan, uw app selecteren, op API-machtigingen klikken, omlaag scrollen en Azure Active Directory Graph selecteren. Selecteer van daaruit Toepassingsmachtigingen en voeg vervolgens de juiste machtigingen toe. 



[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken 

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, heeft uw account de roltoewijzing [Voor beheerde identiteitsbijdrager](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) nodig.

Gebruik de opdracht [AZ-identiteit maken](/cli/azure/identity#az-identity-create) om een door de gebruiker toegewezen beheerde identiteit te maken. De `-g` parameter geeft de resourcegroep op waar de door `-n` de gebruiker toegewezen beheerde identiteit moet worden gemaakt en de parameter geeft de naam aan. Vervang `<RESOURCE GROUP>` de `<USER ASSIGNED IDENTITY NAME>` parameterwaarden en parameterwaarden door uw eigen waarden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Door de gebruiker toegewezen beheerde identiteiten weergeven

Als u een door de gebruiker toegewezen beheerde identiteit wilt aanbieden/lezen, heeft uw account de taaktoewijzing [Managed Identity Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) of Managed Identity [Contributor](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) nodig.

Als u door de gebruiker toegewezen beheerde identiteiten wilt weergeven, gebruikt u de opdracht [AZ-identiteitslijst.](/cli/azure/identity#az-identity-list) Vervang `<RESOURCE GROUP>` de met uw eigen waarde:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
In het json-antwoord hebben `"Microsoft.ManagedIdentity/userAssignedIdentities"` door de gebruiker toegewezen `type`beheerde identiteiten waarde geretourneerd voor sleutel, .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, heeft uw account de roltoewijzing [Voor beheerde identiteitsbijdrager](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) nodig.

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, gebruikt u de opdracht [voor het verwijderen van az-identiteit.](/cli/azure/identity#az-identity-delete)  De parameter -n geeft de naam aan en de parameter -g geeft de resourcegroep op waar de door de gebruiker toegewezen beheerde identiteit is gemaakt. Vervang `<USER ASSIGNED IDENTITY NAME>` de `<RESOURCE GROUP>` waarden en parameters door uw eigen waarden:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Als u een door de gebruiker toegewezen beheerde identiteit verwijdert, wordt de verwijzing niet verwijderd uit een resource waaraan de verwijzing is toegewezen. Verwijder deze uit VM/VMSS `az vm/vmss identity remove` met de opdracht

## <a name="next-steps"></a>Volgende stappen

Zie [az-identiteit](/cli/azure/identity)voor een volledige lijst met Azure CLI-identiteitsopdrachten.

Voor informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit aan een Azure VM [zie, Beheerde identiteiten configureren voor Azure-resources op een Azure VM met Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
