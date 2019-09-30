---
title: Windows Virtual Desktop-Tenant en hostgroep maken-Azure
description: Problemen met Tenant-en hostgroepen oplossen tijdens de installatie van een Windows Virtual Desktop-Tenant omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: helohr
ms.openlocfilehash: 811fdd0b177e3a255ca91145e39005de1d2328ad
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676458"
---
# <a name="tenant-and-host-pool-creation"></a>Tenants en hostpools maken

In dit artikel komen problemen aan bod tijdens de eerste installatie van de virtuele bureau blad-Tenant van Windows en de gerelateerde infra structuur van de sessie-hostgroep.

## <a name="provide-feedback"></a>Feedback geven

Ga naar de [technische community van Windows virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) voor het bespreken van de Windows Virtual Desktop-service met het product team en de actieve leden van de community.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>De afbeelding voor meerdere sessies van Windows 10 Enter prise ophalen

Als u de Windows 10 Enter prise-installatie kopie voor meerdere sessies wilt gebruiken, gaat u naar Azure Marketplace en selecteert u aan de **slag** > **micro soft Windows 10** > en [Windows 10 Enter prise voor virtuele Bureau bladen versie 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Een scherm opname van het selecteren van Windows 10 Enter prise voor virtuele Bureau bladen, versie 1809.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Tenant voor Windows virtueel bureau blad maken

In deze sectie worden mogelijke problemen beschreven bij het maken van de virtuele bureau blad-Tenant van Windows.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Fout: De gebruiker is niet gemachtigd om een query uit te zoeken op de beheer service

![Scherm opname van het Power shell-venster waarin een gebruiker niet is gemachtigd om een query uit te zoeken op de beheer service.](media/UserNotAuthorizedNewTenant.png)

Voor beeld van onbewerkte fout:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Wordt** De gebruiker die zich heeft aangemeld, is niet toegewezen aan de TenantCreator-rol in hun Azure Active Directory.

**Fix:** Volg de instructies in [De toepassingsrol van de TenantCreator toewijzen aan een gebruiker in uw Azure Active Directory-Tenant](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role). Nadat u de instructies hebt gevolgd, hebt u een gebruiker toegewezen aan de TenantCreator-rol.

![Scherm opname van TenantCreator rol toegewezen.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Vm's voor virtuele Windows-bureau blad-sessies maken

Vm's voor sessie-hosts kunnen op verschillende manieren worden gemaakt, maar Extern bureaublad-services/bureau blad-teams ondersteunen alleen VM-inrichtings problemen die betrekking hebben op de Azure Resource Manager sjabloon. De Azure Resource Manager-sjabloon is beschikbaar in [Azure Marketplace](https://azuremarketplace.microsoft.com/) en [github](https://github.com/).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemen met het gebruik van Windows virtueel bureau blad – een hostgroep aanbieden voor Azure Marketplace

Het virtuele bureau blad van Windows – inrichten van een sjabloon voor een hostgroep is beschikbaar via de Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Fout: Wanneer u de koppeling van GitHub gebruikt, wordt het bericht ' een gratis account maken ' weer gegeven

![Scherm opname voor het maken van een gratis account.](media/be615904ace9832754f0669de28abd94.png)

**Oorzaak 1:** Er zijn geen actieve abonnementen in het account dat wordt gebruikt om u aan te melden bij Azure, of het account dat wordt gebruikt, heeft geen machtigingen om de abonnementen weer te geven.

**Oplossing 1:** Meld u aan met een account met Inzender toegang (ten minste) voor het abonnement waar de Vm's van de host worden geïmplementeerd.

**Oorzaak 2:** Het abonnement dat wordt gebruikt, maakt deel uit van een CSP-Tenant (Microsoft Cloud service provider).

**Fix 2:** Ga naar de locatie van de GitHub voor het **maken en inrichten van nieuwe Windows Virtual Desktop** -hostgroep en volg deze instructies:

1. Klik met de rechter muisknop op **implementeren naar Azure** en selecteer **koppelings adres kopiëren**.
2. Open **Klad blok** en plak de koppeling.
3. Voeg vóór het teken # de Tenant naam van de CSP-eind gebruiker in.
4. Open de nieuwe koppeling in een browser en de Azure Portal zal de sjabloon laden.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager sjabloon en DSC-fouten (desired state Configuration) van Power shell

Volg deze instructies voor het oplossen van problemen met mislukte implementaties van Azure Resource Manager sjablonen en Power shell DSC.

1. Bekijk fouten in de implementatie met behulp [van de weer gave-implementatie bewerkingen met Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. Als er geen fouten in de implementatie zijn, controleert u de fouten in het activiteiten logboek met behulp van [activiteiten logboeken weer geven om acties op resources te controleren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. Wanneer de fout is geïdentificeerd, gebruikt u het fout bericht en de resources in [problemen met veelvoorkomende Azure-implementatie fouten oplossen met Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) om het probleem op te lossen.
4. Verwijder alle resources die tijdens de vorige implementatie zijn gemaakt en probeer de sjabloon opnieuw te implementeren.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Fout: De implementatie is mislukt...\<. hostnaam >/JoinDomain

![De scherm afbeelding van de implementatie is mislukt.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Voor beeld van onbewerkte fout:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Oorzaak 1:** De referenties die zijn ingevoerd voor het samen voegen van Vm's aan het domein, zijn onjuist.

**Oplossing 1:** Zie de fout ' onjuiste referenties ' voor virtuele machines die geen deel uitmaken van het domein in de [host-VM-configuratie](troubleshoot-vm-configuration.md).

**Oorzaak 2:** De domein naam wordt niet omgezet.

**Fix 2:** Zie de fout ' de domein naam wordt niet opgelost ' voor virtuele machines die geen deel uitmaken van het domein in de [host-VM-configuratie](troubleshoot-vm-configuration.md).


### <a name="error-your-deployment-failedunauthorized"></a>Fout: De implementatie is mislukt. ..\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Wordt** Het abonnement dat u gebruikt, is een type dat geen toegang heeft tot de vereiste onderdelen in de regio waarin de klant probeert te implementeren. Bijvoorbeeld: MSDN-, Free-of Education-abonnementen kunnen deze fout weer geven.

**Fix:** Wijzig het abonnements type of de regio in een die toegang heeft tot de vereiste onderdelen.

### <a name="error-vmextensionprovisioningerror"></a>Fout: VMExtensionProvisioningError

![Scherm opname van de implementatie is mislukt met de status van de Terminal-inrichting is mislukt.](media/7aaf15615309c18a984673be73ac969a.png)

**Oorzaak 1:** Tijdelijke fout met de virtueel-bureaublad omgeving van Windows.

**Oorzaak 2:** Tijdelijke fout met de verbinding.

**Fix:** Bevestig dat Windows Virtual Desktop Environment in orde is door u aan te melden met Power shell. Voltooi de VM-registratie hand matig in [een hostgroep maken met Power shell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Fout: De opgegeven gebruikers naam voor de beheerder is niet toegestaan

![Scherm opname van de implementatie is mislukt, omdat een opgegeven beheerder niet is toegestaan.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Voor beeld van onbewerkte fout:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Wordt** Het opgegeven wacht woord bevat verboden subtekenreeksen (admin, Administrator, root).

**Fix:** Werk de gebruikers naam bij of gebruik andere gebruikers.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Fout: De virtuele machine heeft een fout gerapporteerd bij het verwerken van de extensie

![Scherm opname van de bron bewerking die is voltooid met een Terminal-inrichtings status in uw implementatie is mislukt.](media/49c4a1836a55d91cd65125cf227f411f.png)

Voor beeld van onbewerkte fout:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Wordt** De Power shell DSC-uitbrei ding kan geen beheerders toegang krijgen op de virtuele machine.

**Fix:** Bevestig dat de gebruikers naam en het wacht woord beheerders toegang hebben op de virtuele machine en voer de Azure Resource Manager sjabloon opnieuw uit.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Fout: Heeft-Power shell DSC-configuratie FirstSessionHost voltooid met fout (en)

![Scherm opname van implementatie mislukt met Power shell DSC-configuratie FirstSessionHost voltooid met fout (en).](media/64870370bcbe1286906f34cf0a8646ab.png)

Voor beeld van onbewerkte fout:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Wordt** De Power shell DSC-uitbrei ding kan geen beheerders toegang krijgen op de virtuele machine.

**Fix:** Bevestig dat de opgegeven gebruikers naam en het wacht woord beheerders toegang hebben op de virtuele machine en voer de Azure Resource Manager sjabloon opnieuw uit.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fout: Heeft – InvalidResourceReference

Voor beeld van onbewerkte fout:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Wordt** Een deel van de naam van de resource groep wordt gebruikt voor bepaalde resources die worden gemaakt door de sjabloon. Omdat de naam overeenkomt met bestaande resources, kan de sjabloon een bestaande resource uit een andere groep selecteren.

**Fix:** Wanneer u de Azure Resource Manager sjabloon uitvoert om Vm's voor de host te implementeren, moet u de eerste twee tekens uniek maken voor de naam van de resource groep voor uw abonnement.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fout: Heeft – InvalidResourceReference

Voor beeld van onbewerkte fout:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Wordt** Deze fout is omdat de NIC die is gemaakt met de Azure Resource Manager sjabloon dezelfde naam heeft als een andere NIC die al in het VNET is opgenomen.

**Fix:** Gebruik een ander host-voor voegsel.

### <a name="error-deploymentfailed--error-downloading"></a>Fout: Heeft: fout bij het downloaden

Voor beeld van onbewerkte fout:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Wordt** Deze fout wordt veroorzaakt door een statische route, een firewall regel of een NSG die het downloaden van het zip-bestand dat is gekoppeld aan de Azure Resource Manager-sjabloon blokkeert.

**Fix:** Verwijder de blokkerende statische route, firewall regel of NSG. U kunt eventueel het JSON-bestand van de Azure Resource Manager sjabloon openen in een tekst editor, de koppeling naar een zip-bestand maken en de resource downloaden naar een toegestane locatie.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Fout: De gebruiker is niet gemachtigd om een query uit te zoeken op de beheer service

Voor beeld van onbewerkte fout:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Wordt** De opgegeven Tenant beheerder voor het virtuele bureau blad van Windows heeft geen geldige roltoewijzing.

**Fix:** De gebruiker die de virtuele bureau blad-Tenant van Windows heeft gemaakt, moet zich aanmelden bij Windows virtueel bureau blad Power shell en de poging om een roltoewijzing toe te wijzen aan de gebruiker. Als u de para meters voor GitHub Azure Resource Manager-sjabloon uitvoert, volgt u deze instructies met Power shell-opdrachten:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Fout: Gebruiker vereist Azure multi-factor Authentication (MFA)

![Scherm opname van de implementatie is mislukt vanwege een gebrek aan multi-factor Authentication (MFA)](media/MFARequiredError.png)

Voor beeld van onbewerkte fout:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Wordt** Voor de opgegeven Tenant beheerder van het Windows-bureau blad is Azure multi-factor Authentication (MFA) vereist om u aan te melden.

**Fix:** Maak een Service-Principal en wijs hieraan een rol toe voor uw Windows Virtual Desktop-Tenant door de [stappen in de volgende zelf studie uit te voeren: Service-principals en roltoewijzingen maken met Power](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)shell. Nadat u hebt gecontroleerd of u zich kunt aanmelden bij het virtuele bureau blad van Windows met de Service-Principal, voert u de Azure Marketplace-aanbieding of de GitHub Azure Resource Manager-sjabloon uit, afhankelijk van de methode die u gebruikt. Volg de onderstaande instructies om de juiste para meters voor uw methode op te geven.

Als u de Azure Marketplace-aanbieding uitvoert, geeft u waarden op voor de volgende para meters voor het correct verifiëren van het virtuele bureau blad van Windows:

- RDS-eigenaar van Windows Virtual Desktop Tenant: Service-principal
- Toepassings-ID: De toepassings-id van de nieuwe service-principal die u hebt gemaakt
- Wacht woord/wacht woord bevestigen: Het wachtwoord geheim dat u hebt gegenereerd voor de Service-Principal
- Tenant-ID voor Azure AD: De Azure AD-Tenant-ID van de service-principal die u hebt gemaakt

Als u de GitHub-Azure Resource Manager sjabloon uitvoert, geeft u waarden op voor de volgende para meters voor een juiste verificatie van Windows virtueel bureau blad:

- Tenant beheerder user principal name (UPN) of toepassings-ID: De toepassings-id van de nieuwe service-principal die u hebt gemaakt
- Wacht woord voor Tenant beheerder: Het wachtwoord geheim dat u hebt gegenereerd voor de Service-Principal
- IsServicePrincipal: **true**
- AadTenantId: De Azure AD-Tenant-ID van de service-principal die u hebt gemaakt

## <a name="next-steps"></a>Volgende stappen

- Zie [probleemoplossings overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van het oplossen van problemen met het virtuele bureau blad van Windows en de escalatie trajecten.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie [extern bureaublad-client verbindingen](troubleshoot-client-connection.md)voor het oplossen van problemen met Windows-client verbindingen met virtueel bureau blad.
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [Windows Virtual Desktop Environment](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)(Engelstalig) voor meer informatie over de service.
- Zie [zelf studie voor het oplossen van problemen met de zelf studie: Problemen oplossen met implementaties](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)van Resource Manager-sjablonen.
- Zie [bewerkingen controleren met Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)voor meer informatie over controle acties.
- Zie [implementatie bewerkingen weer geven](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)voor meer informatie over acties om de fouten te bepalen tijdens de implementatie.
