---
title: Hoofdroltoewijzing voor Windows Virtual Desktop-service - Azure
description: Serviceprincipals maken en rollen toewijzen met PowerShell in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61b5017609d99f2f0074c67d3838cf351ea38bea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79365420"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Zelfstudie: Serviceprincipals en roltoewijzingen maken met PowerShell

Serviceprincipals zijn identiteiten die u maken in Azure Active Directory om rollen en machtigingen toe te wijzen voor een specifiek doel. In Windows Virtual Desktop u een serviceprincipal maken voor:

- Automatiseer specifieke Windows Virtual Desktop-beheertaken.
- Gebruik als referenties in plaats van voor MFA vereiste gebruikers wanneer u een Azure Resource Manager-sjabloon voor Windows Virtual Desktop uitvoert.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een serviceprincipal in Azure Active Directory.
> * Maak een roltoewijzing in Windows Virtual Desktop.
> * Meld u aan bij Windows Virtual Desktop met behulp van de serviceprincipal.

## <a name="prerequisites"></a>Vereisten

Voordat u serviceprincipals en roltoewijzingen maken, moet u drie dingen doen:

1. Installeer de AzureAD-module. Als u de module wilt installeren, voert u PowerShell uit als beheerder en voert u de volgende cmdlet uit:

    ```powershell
    Install-Module AzureAD
    ```

2. [Download en importeer de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/).

3. Volg alle instructies in dit artikel in dezelfde PowerShell-sessie. Het proces werkt mogelijk niet als u uw PowerShell-sessie onderbreekt door het venster te sluiten en het later opnieuw te openen.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Een service-principal maken in Azure Active Directory

Nadat u aan de vereisten in uw PowerShell-sessie hebt voldaan, voert u de volgende PowerShell-cmdlets uit om een multitenant-serviceprincipal in Azure te maken.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Uw referenties weergeven in PowerShell

Voordat u de roltoewijzing voor uw serviceprincipal maakt, bekijkt u uw referenties en schrijft u deze op voor toekomstige referentie. Het wachtwoord is vooral belangrijk omdat u het niet meer ophalen nadat u deze PowerShell-sessie hebt gesloten.

Hier zijn de drie referenties die u moet opschrijven en de cmdlets die u moet uitvoeren om ze te krijgen:

- Wachtwoord:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Tenant-id:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Toepassings-id:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Een roltoewijzing maken in Windows Virtual Desktop Preview

Vervolgens moet u een roltoewijzing maken, zodat de serviceprincipal zich kan aanmelden bij Windows Virtual Desktop. Zorg ervoor dat u zich aanmeldt met een account met machtigingen voor het maken van roltoewijzingen.

Download en importeer eerst [de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) om te gebruiken in uw PowerShell-sessie als u dat nog niet hebt gedaan.

Voer de volgende PowerShell-cmdlets uit om verbinding te maken met Windows Virtual Desktop en uw tenants weer te geven.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Wanneer u de tenantnaam vindt voor de tenant waarvoor u een roltoewijzing wilt maken, gebruikt u die naam in de volgende cmdlet:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Inloggen bij de serviceprincipal

Nadat u een roltoewijzing voor de serviceprincipal hebt gemaakt, controleert u of de serviceprincipal zich kan aanmelden bij Windows Virtual Desktop door de volgende cmdlet uit te voeren:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Nadat u zich hebt aangemeld, controleert u of alles werkt door een paar Windows Virtual Desktop PowerShell-cmdlets te testen met de serviceprincipal.

## <a name="next-steps"></a>Volgende stappen

Nadat u de serviceprincipal hebt gemaakt en deze een rol hebt toegewezen in uw Windows Virtual Desktop-tenant, u deze gebruiken om een hostgroep te maken. Ga voor meer informatie over hostpools verder naar de zelfstudie voor het maken van een hostpool in Windows Virtual Desktop.

 > [!div class="nextstepaction"]
 > [Een hostpool maken met Azure Marketplace](./create-host-pools-azure-marketplace.md)
