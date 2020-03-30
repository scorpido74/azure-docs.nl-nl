---
title: Windows Virtual Desktop tenant host pool creatie - Azure
description: Problemen met tenant- en hostgroepen oplossen en oplossen tijdens het instellen van een Windows Virtual Desktop-tenantomgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36b15b41279edc60d337a7ba70abe2ca64d4bc7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371593"
---
# <a name="tenant-and-host-pool-creation"></a>Tenants en hostpools maken

In dit artikel worden problemen opgelost tijdens de eerste installatie van de Windows Virtual Desktop-tenant en de bijbehorende sessiehostpoolinfrastructuur.

## <a name="provide-feedback"></a>Feedback geven

Ga naar de [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) om de Windows Virtual Desktop-service te bespreken met het productteam en actieve communityleden.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>De afbeelding voor meerdere sessies van Windows 10 Enterprise aanschaffen

Als u de afbeelding met meerdere sessies van Windows 10 Enterprise wilt gebruiken, gaat u naar Azure Marketplace en selecteert **U Microsoft** > **Windows 10** > en [Windows 10 Enterprise voor virtuele bureaubladen, versie 1809.](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)

![Een screenshot van het selecteren van Windows 10 Enterprise voor virtuele desktops, versie 1809.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Windows Virtual Desktop-tenant maken

In deze sectie worden mogelijke problemen bij het maken van de Windows Virtual Desktop-tenant.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Fout: de gebruiker is niet bevoegd om de beheerservice op te vragen

![Schermafbeelding van het PowerShell-venster waarin een gebruiker niet bevoegd is om de beheerservice op te vragen.](media/UserNotAuthorizedNewTenant.png)

Voorbeeld van ruwe fouten:

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

**Oorzaak:** De gebruiker die is aangemeld, heeft de rol TenantCreator niet toegewezen gekregen in zijn Azure Active Directory.

**Oplossing:** Volg de instructies in [De toepassingsrol TenantCreator toewijzen aan een gebruiker in uw Azure Active Directory-tenant.](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role) Nadat u de instructies hebt gevolgd, wordt een gebruiker toegewezen aan de rol TenantCreator.

![Schermafbeelding van de toegewezen rol TenantCreator.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Vm's voor Windows Virtual Desktop-sessiehost maken

Vm's voor sessiehost kunnen op verschillende manieren worden gemaakt, maar het Windows Virtual Desktop-team ondersteunt alleen VM-inrichtingsproblemen in verband met het [Azure Marketplace-aanbod.](https://azuremarketplace.microsoft.com/) Zie [Problemen met Windows Virtual Desktop - Een Azure Marketplace-aanbieding voor hostpool inrichten](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering)voor meer informatie.

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemen met Windows Virtual Desktop : een Azure Marketplace-aanbieding voor hostpool en inrichten

De sjabloon Windows Virtual Desktop : Een hostpoolsjabloon inrichten is beschikbaar op de Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Fout: Wanneer u de koppeling van GitHub gebruikt, wordt het bericht 'Een gratis account maken' weergegeven

![Schermafbeelding om een gratis account aan te maken.](media/be615904ace9832754f0669de28abd94.png)

**Oorzaak 1:** Er zijn geen actieve abonnementen in het account dat wordt gebruikt om u aan te melden bij Azure of het gebruikte account heeft geen machtigingen om de abonnementen weer te geven.

**Fix 1:** Meld u aan met een account dat (minimaal) toegang heeft tot het abonnement waarbij vm's voor sessiehost worden geïmplementeerd.

**Oorzaak 2:** Het gebruik van het abonnement is onderdeel van een CSP-tenant (Microsoft Cloud Service Provider).

**Fix 2:** Ga naar de GitHub-locatie voor **Het maken en inrichten van nieuwe Windows Virtual Desktop-hostgroep** en volg de volgende instructies:

1. Klik met de rechtermuisknop op **Implementeren naar Azure** en selecteer **Koppelingsadres kopiëren**.
2. Open **Kladblok** en plak de koppeling.
3. Voeg vóór het teken # de naam van de CSP-eindklanttenant in.
4. Open de nieuwe koppeling in een browser en de Azure-portal laadt de sjabloon.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Fout: U ontvangt fout 'sjabloonimplementatie is niet geldig'

![Schermafbeelding van "template deployment ... is niet geldig" fout](media/troubleshooting-marketplace-validation-error-generic.png)

Voordat u specifieke actie onderneemt, moet u het activiteitenlogboek controleren om de gedetailleerde fout voor de mislukte implementatievalidatie te zien.

Ga als bedoeld als u de fout in het activiteitenlogboek weerziet:

1. Sluit de huidige Azure Marketplace-implementatieaanbieding af.
2. Zoek en selecteer **activiteitslogboek**in de bovenste zoekbalk .
3. Zoek een activiteit met de naam **Implementatie valideren** met de status **Mislukt** en selecteer de activiteit.
   ![Schermafbeelding van afzonderlijke **Implementatie** activiteit valideren met een status **Mislukt**](media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Selecteer JSON en schuif naar beneden naar de onderkant van het scherm totdat u het veld 'statusMessage' ziet.
   ![Schermafbeelding van mislukte activiteit, met een rood vak rond de eigenschap statusMessage van de JSON-tekst.](media/troubleshooting-marketplace-validation-error-json-boxed.png)

Als uw bewerkingssjabloon de quotumlimiet overschrijdt, u een van de volgende dingen uitvoeren om deze te herstellen:

 - Voer de Azure Marketplace uit met de parameters die u de eerste keer hebt gebruikt, maar gebruik deze keer minder VM's en VM-kernen.
 - Open de koppeling die u ziet in het veld **statusMessage** in een browser om een aanvraag in te dienen om het quotum voor uw Azure-abonnement voor de opgegeven VM SKU te verhogen.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager-sjabloon en DSC-fouten (Desired State Configuration)

Volg deze instructies om mislukte implementaties van Azure Resource Manager-sjablonen en PowerShell DSC op te lossen.

1. Fouten in de implementatie controleren met [behulp van Implementatiebewerkingen weergeven met Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-operations.md).
2. Als er geen fouten in de implementatie zijn, controleert u fouten in het activiteitenlogboek met [activiteitslogboeken weergeven om acties op resources te controleren.](../azure-resource-manager/resource-group-audit.md)
3. Zodra de fout is geïdentificeerd, gebruikt u het foutbericht en de resources in [het oplossen van veelvoorkomende Azure-implementatiefouten met Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md) om het probleem op te lossen.
4. Verwijder alle resources die tijdens de vorige implementatie zijn gemaakt en probeer de sjabloon opnieuw te implementeren.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Fout: uw implementatie\<is mislukt.... hostname>/joindomain

![Schermafbeelding van uw implementatie mislukt.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Voorbeeld van ruwe fouten:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Oorzaak 1:** Referenties voor het samenvoegen van VM's aan het domein zijn onjuist.

**Fix 1:** Zie de fout 'Onjuiste referenties' voor VM's zijn niet verbonden met het domein in [de VM-configuratie van sessiehost.](troubleshoot-vm-configuration.md)

**Oorzaak 2:** Domeinnaam wordt niet opgelost.

**Fix 2:** Zie [Fout: domeinnaam wordt niet opgelost](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) in de [VM-configuratie van sessiehost.](troubleshoot-vm-configuration.md)

**Oorzaak 3:** Uw DNS-configuratie (Virtual Network) is ingesteld op **Standaard**.

Ga als volgt te werk om dit op te lossen:

1. Open de Azure Portal en ga naar het tabblad **Virtuele netwerken.**
2. Zoek uw VNET en selecteer **DNS-servers.**
3. Het menu DNS-servers moet aan de rechterkant van het scherm worden weergegeven. Selecteer in dat menu **Aangepast**.
4. Controleer of de DNS-servers die onder Aangepast staan overeenkomen met uw domeincontroller of Active Directory-domein. Als u uw DNS-server niet ziet, u deze toevoegen door de waarde ervan in te voeren in het veld **DNS-server toevoegen.**

### <a name="error-your-deployment-failedunauthorized"></a>Fout: Uw implementatie is mislukt...\Ongeautoriseerd

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Oorzaak:** Het abonnement dat u gebruikt, is een type dat geen toegang heeft tot vereiste functies in de regio waar de klant probeert te implementeren. MsDN-, Gratis- of Onderwijsabonnementen kunnen deze fout bijvoorbeeld weergeven.

**Oplossing:** Wijzig uw abonnementstype of regio in een abonnement die toegang heeft tot de vereiste functies.

### <a name="error-vmextensionprovisioningerror"></a>Fout: VMExtensionProvisioningError

![Schermafbeelding van uw implementatie is mislukt omdat de status van terminalinrichting is mislukt.](media/7aaf15615309c18a984673be73ac969a.png)

**Oorzaak 1:** Tijdelijke fout met de Windows Virtual Desktop-omgeving.

**Oorzaak 2:** Tijdelijke fout met verbinding.

**Oplossing:** Controleer of de Windows Virtual Desktop-omgeving in orde is door u aan te melden met PowerShell. Voltooi de VM-registratie handmatig in [Een hostgroep maken met PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Fout: de opgegeven gebruikersnaam beheerder is niet toegestaan

![Schermafbeelding van uw implementatie is mislukt, waarbij een opgegeven beheerder niet is toegestaan.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Voorbeeld van ruwe fouten:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Oorzaak:** Het opgegeven wachtwoord bevat verboden subtekenreeksen (beheerder, beheerder, root).

**Oplossing:** Werk gebruikersnaam bij of gebruik verschillende gebruikers.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Fout: VM heeft een fout gemeld bij het verwerken van extensie

![Schermafbeelding van de resourcebewerking die is voltooid met de status van terminalinrichting in Uw implementatie is mislukt.](media/49c4a1836a55d91cd65125cf227f411f.png)

Voorbeeld van ruwe fouten:

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

**Oorzaak:** PowerShell DSC extensie was niet in staat om admin toegang te krijgen op de VM.

**Oplossing:** Controleer of gebruikersnaam en wachtwoord beheerderstoegang hebben op de virtuele machine en voer de azure resource manager-sjabloon opnieuw uit.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Fout: DeploymentFailed – PowerShell DSC Configuration 'FirstSessionHost' voltooid met fout(en)

![Schermafbeelding van implementatie mislukt met PowerShell DSC Configuration 'FirstSessionHost' voltooid met Error(s).](media/64870370bcbe1286906f34cf0a8646ab.png)

Voorbeeld van ruwe fouten:

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

**Oorzaak:** PowerShell DSC extensie was niet in staat om admin toegang te krijgen op de VM.

**Oplossing:** Controleer of gebruikersnaam en wachtwoord zijn opgegeven, hebben beheerderstoegang op de virtuele machine en voer de azure resource manager-sjabloon opnieuw uit.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fout: Deployment Failed – InvalidResourceReference

Voorbeeld van ruwe fouten:

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

**Oorzaak:** Een deel van de naam van de resourcegroep wordt gebruikt voor bepaalde bronnen die door de sjabloon worden gemaakt. Vanwege de naam die overeenkomt met bestaande bronnen, kan de sjabloon een bestaande resource uit een andere groep selecteren.

**Oplossing:** Wanneer u de sjabloon Azure Resource Manager uitvoert om vm's voor sessiehost te implementeren, maakt u de eerste twee tekens uniek voor de naam van uw abonnementsbrongroep.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fout: Deployment Failed – InvalidResourceReference

Voorbeeld van ruwe fouten:

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

**Oorzaak:** Deze fout is omdat de NIC die is gemaakt met de sjabloon Azure Resource Manager dezelfde naam heeft als een andere NIC die al in het VNET is.

**Oplossing:** Gebruik een ander hostvoorvoegsel.

### <a name="error-deploymentfailed--error-downloading"></a>Fout: Deployment Failed – Fout downloaden

Voorbeeld van ruwe fouten:

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

**Oorzaak:** Deze fout is te wijten aan een statische route, firewallregel of NSG die het downloaden van het zip-bestand blokkeert dat is gekoppeld aan de sjabloon Azure Resource Manager.

**Oplossing:** Verwijder het blokkeren van statische route, firewallregel of NSG. Open optioneel het Json-bestand azure resource manager in een teksteditor, neem de koppeling naar zip-bestand en download de bron naar een toegestane locatie.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Fout: de gebruiker is niet bevoegd om de beheerservice op te vragen

Voorbeeld van ruwe fouten:

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

**Oorzaak:** De opgegeven Windows Virtual Desktop-tenantbeheerder heeft geen geldige roltoewijzing.

**Oplossing:** De gebruiker die de Windows Virtual Desktop-tenant heeft gemaakt, moet zich aanmelden bij Windows Virtual Desktop PowerShell en de geprobeerde gebruiker een roltoewijzing toewijzen. Als u de sjabloonparameters van GitHub Azure Resource Manager uitvoert, volgt u de volgende instructies met PowerShell-opdrachten:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Fout: Gebruiker vereist Azure Multi-Factor Authentication (MFA)

![Schermafbeelding van uw implementatie is mislukt vanwege het ontbreken van multi-factorauthenticatie (MFA)](media/MFARequiredError.png)

Voorbeeld van ruwe fouten:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Oorzaak:** Voor de opgegeven Windows Virtual Desktop-tenantbeheerder moet Azure Multi-Factor Authentication (MFA) worden aangemeld.

**Oplossing:** Maak een serviceprincipal en wijs deze een rol toe voor uw Windows Virtual Desktop-tenant door de stappen in [Zelfstudie: Serviceprincipals en roltoewijzingen maken met PowerShell](create-service-principal-role-powershell.md). Nadat u hebt gecontroleerd of u zich aanmelden bij Windows Virtual Desktop met de serviceprincipal, voert u het Azure Marketplace-aanbod of de GitHub Azure Resource Manager-sjabloon opnieuw uit, afhankelijk van de methode die u gebruikt. Volg de onderstaande instructies om de juiste parameters voor uw methode in te voeren.

Als u het Azure Marketplace-aanbod uitvoert, geeft u waarden op voor de volgende parameters om de volgende parameters correct te verifiëren voor Windows Virtual Desktop:

- Windows Virtual Desktop tenant RDS Owner: Service principal
- Toepassings-id: de toepassingsidentificatie van de nieuwe serviceprincipal die u hebt gemaakt
- Wachtwoord/wachtwoord bevestigen: het wachtwoordgeheim dat u hebt gegenereerd voor de serviceprincipal
- Azure AD-tenant-id: de Azure AD-tenant-id van de serviceprincipal die u hebt gemaakt

Als u de sjabloon GitHub Azure Resource Manager uitvoert, geeft u waarden op voor de volgende parameters om de volgende parameters correct te verifiëren voor Windows Virtual Desktop:

- Hoofdnaam van de tenantbeheerder (UPN) of toepassings-id: de toepassingsidentificatie van de nieuwe serviceprincipal die u hebt gemaakt
- Wachtwoord tenantbeheerder: het wachtwoordgeheim dat u hebt gegenereerd voor de serviceprincipal
- IsServicePrincipal: **waar**
- AadTenantId: de Azure AD-tenant-id van de serviceprincipal die u hebt gemaakt

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van probleemoplossing voor het oplossen van problemen met Windows Virtual Desktop en de escalatietracks.
- Zie Configuratie van de [virtuele machine sessiehost](troubleshoot-vm-configuration.md)voor het oplossen van problemen tijdens het configureren van een virtuele machine (VM) in Windows Virtual Desktop.
- Zie [Windows Virtual Desktop-serviceverbindingen](troubleshoot-service-connection.md)voor het oplossen van problemen met Windows Virtual Desktop-clientverbindingen.
- Zie Problemen met [de Extern bureaublad-client oplossen](troubleshoot-client.md) als u problemen met Extern bureaublad-clients wilt oplossen
- Zie [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)voor het oplossen van problemen bij het gebruik van PowerShell met Windows Virtual Desktop.
- Zie De Windows Virtual [Desktop-omgeving](environment-setup.md)voor meer informatie over de service.
- Zie [Zelfstudie: Problemen met resourcebeheersjabloonimplementaties](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)oplossen.
- Zie [Controlebewerkingen met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over controleacties.
- Zie [Implementatiebewerkingen weergeven](../azure-resource-manager/templates/deployment-history.md)voor meer informatie over acties om de fouten tijdens de implementatie te bepalen.
