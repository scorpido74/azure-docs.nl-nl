---
title: Service-principals en roltoewijzingen voor virtuele Windows-Bureau bladen maken met Power shell-Azure
description: Service-principals maken en rollen toewijzen met behulp van Power shell in Windows virtueel bureau blad preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 3e9ee3f5dd04ef838f78b9731885b7ea48e6c99d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811320"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Zelfstudie: Service-principals en roltoewijzingen maken met behulp van PowerShell

Service-principals zijn identiteiten die u in Azure Active Directory kunt maken om rollen en machtigingen voor een bepaald doel toe te wijzen. In Windows virtueel bureau blad preview kunt u een service-principal maken voor het volgende:

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

2. [De Power shell-module voor virtueel bureau blad van Windows downloaden en importeren](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)

3. Volg alle instructies in dit artikel in dezelfde Power shell-sessie. Het werkt mogelijk niet als u het venster sluit en later terugkeert.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Een service-principal maken in Azure Active Directory

Nadat u de vereisten in uw Power shell-sessie hebt vervuld, voert u de volgende Power shell-cmdlets uit om een service-principal voor meerdere tenants in azure te maken.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="view-your-credentials-in-powershell"></a>Uw referenties weer geven in Power shell

Voordat u uw Power shell-sessie beëindigt, bekijkt u uw referenties en schrijft u deze naar beneden zodat u ze later kunt raadplegen. Het wacht woord is vooral belang rijk omdat u het niet kunt ophalen nadat u deze Power shell-sessie hebt gesloten.

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

Vervolgens maakt u een functie toewijzing voor RDS in het virtuele bureau blad van Windows voor de Service-Principal, waarmee de Service-Principal zich kan aanmelden bij Windows virtueel bureau blad. Zorg ervoor dat u een account gebruikt dat machtigingen heeft voor het maken van RDS-roltoewijzingen.

Voer de volgende Power shell-cmdlets uit om verbinding te maken met het virtuele bureau blad van Windows en uw RDS-tenants weer te geven.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant | FL
```

Gebruik de Tenantnaam voor de juiste Tenant en voer de volgende Power shell-cmdlets uit om een roltoewijzing voor de Service-Principal in de opgegeven Tenant te maken.

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName "<my-rds-tenantname>"
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
