---
title: Windows Virtual Desktop Service Principal Role Assignment-Azure
description: Service-principals maken en rollen toewijzen met behulp van Power shell in Windows virtueel bureau blad.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: helohr
ms.openlocfilehash: 1141731697c9f649a4a8d4052cd550605049b52e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606941"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Zelf studie: Service-principals en roltoewijzingen maken met behulp van Power shell

Service-principals zijn identiteiten die u in Azure Active Directory kunt maken om rollen en machtigingen voor een bepaald doel toe te wijzen. In Windows Virtual Desktop kunt u een service-principal maken voor het volgende:

- Specifieke Windows-beheer taken voor virtueel bureau blad automatiseren.
- Als referenties gebruiken in plaats van MFA-vereiste gebruikers bij het uitvoeren van een Azure Resource Manager sjabloon voor Windows virtueel bureau blad.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een Service-Principal in Azure Active Directory.
> * Een roltoewijzing maken in virtuele Windows-bureau blad.
> * Meld u aan bij Windows Virtual Desktop met behulp van de Service-Principal.

## <a name="prerequisites"></a>Vereisten

Voordat u service-principals en roltoewijzingen kunt maken, moet u drie dingen doen:

1. Installeer de AzureAD-module. Als u de module wilt installeren, voert u Power shell uit als beheerder en voert u de volgende cmdlet uit:

    ```powershell
    Install-Module AzureAD
    ```

2. [Down load en importeer de Windows-module virtueel bureau blad Power shell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview).

3. Volg alle instructies in dit artikel in dezelfde Power shell-sessie. Het proces werkt mogelijk niet als u de Power shell-sessie onderbreekt door het venster te sluiten en later opnieuw te openen.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Een service-principal maken in Azure Active Directory

Nadat u de vereisten in uw Power shell-sessie hebt vervuld, voert u de volgende Power shell-cmdlets uit om een service-principal voor meerdere tenants in azure te maken.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Uw referenties weer geven in Power shell

Voordat u de roltoewijzing voor uw Service-Principal maakt, bekijkt u uw referenties en noteert u deze voor toekomstige referentie. Het wacht woord is vooral belang rijk omdat u het niet kunt ophalen nadat u deze Power shell-sessie hebt gesloten.

Hier volgen de drie referenties die u moet noteren en de cmdlets die u moet uitvoeren om ze te verkrijgen:

- Wacht woord

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Tenant-ID:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Toepassings-ID:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Een roltoewijzing maken in Windows virtueel bureau blad preview

Vervolgens moet u een roltoewijzing maken zodat de Service-Principal zich kan aanmelden bij Windows virtueel bureau blad. Zorg ervoor dat u zich aanmeldt met een account dat machtigingen heeft om roltoewijzingen te maken.

[Down load en Importeer eerst de Windows Virtual Desktop Power shell-module](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) voor gebruik in uw Power shell-sessie als u dat nog niet hebt gedaan.

Voer de volgende Power shell-cmdlets uit om verbinding te maken met het virtuele bureau blad van Windows en uw tenants weer te geven.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Wanneer u de Tenant naam voor de Tenant vindt waarvoor u een roltoewijzing wilt maken, gebruikt u die naam in de volgende cmdlet:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Meld u aan met de Service-Principal

Nadat u een roltoewijzing voor de Service-Principal hebt gemaakt, moet u ervoor zorgen dat de Service-Principal zich kan aanmelden bij Windows virtueel bureau blad door de volgende cmdlet uit te voeren:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Nadat u zich hebt aangemeld, controleert u of alles werkt door enkele virtuele Windows Power shell-cmdlets te testen met behulp van de Service-Principal.

## <a name="next-steps"></a>Volgende stappen

Nadat u de Service-Principal hebt gemaakt en hieraan een rol hebt toegewezen in uw Windows Virtual Desktop-Tenant, kunt u deze gebruiken voor het maken van een hostgroep. Voor meer informatie over hostgroepen gaat u verder met de zelf studie voor het maken van een hostgroep in het virtuele bureau blad van Windows.

 > [!div class="nextstepaction"]
 > [Zelf studie voor hosten in Windows Virtual Desktop](./create-host-pools-azure-marketplace.md)
