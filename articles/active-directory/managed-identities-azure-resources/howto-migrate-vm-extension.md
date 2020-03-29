---
title: Stoppen met het gebruik van vm-extensie beheerde identiteit - Azure AD
description: Stapsgewijze instructies om te stoppen met het gebruik van de VM-extensie en de IMDS (Azure Instance Metadata Service) te gebruiken voor verificatie.
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
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 01b8e1dbc290bed86ccfc3c7016e8bd9168e427a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049068"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>De extensie voor beheerde identiteiten van de virtuele machine stoppen en de Azure Instance Metadata Service gaan gebruiken

## <a name="virtual-machine-extension-for-managed-identities"></a>Extensie virtuele machine voor beheerde identiteiten

De virtuele machine-extensie voor beheerde identiteiten wordt gebruikt om tokens voor een beheerde identiteit binnen de virtuele machine aan te vragen. De werkstroom bestaat uit de volgende stappen:

1. Ten eerste roept de werkbelasting binnen `http://localhost/oauth2/token` de resource het lokale eindpunt op om een toegangstoken aan te vragen.
2. De extensie voor virtuele machines gebruikt vervolgens de referenties voor de beheerde identiteit om een toegangstoken van Azure AD aan te vragen. 
3. Het toegangstoken wordt teruggestuurd naar de beller en kan worden gebruikt om te verifiëren voor services die Azure AD-verificatie ondersteunen, zoals Azure Key Vault of Azure Storage.

Vanwege verschillende beperkingen die in de volgende sectie zijn beschreven, is de VM-extensie voor beheerde identiteit afgeschaft ten gunste van het gebruik van het gelijkwaardige eindpunt in de IMDS (Azure Instance Metadata Service)

### <a name="provision-the-extension"></a>Bepaling van de uitbreiding 

Wanneer u een virtuele machine of een virtuele machineschaal configureert om een beheerde identiteit te hebben, u er optioneel voor kiezen om de beheerde identiteiten voor Azure-resources VM-extensie in te richten met behulp van de `-Type` parameter op de cmdlet [Set-AzVMExtension.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) U `ManagedIdentityExtensionForWindows` een `ManagedIdentityExtensionForLinux`van beide of, afhankelijk van het `-Name` type virtuele machine, en de naam met behulp van de parameter. De `-Settings` parameter geeft de poort op die wordt gebruikt door het Eindpunt van het OAuth-token voor tokenacquisitie:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

U de implementatiesjabloon Azure Resource Manager ook gebruiken om de VM-extensie in te richten door de volgende JSON aan de `resources` sectie toe te voegen aan de sjabloon (gebruik `ManagedIdentityExtensionForLinux` voor de naam- en tekstelementen voor de Linux-versie).

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
Als u met virtuele machineschaalsets werkt, u de beheerde identiteiten voor de extensie voor virtuele machineschaal van Azure-resources ook inrichten met de cmdlet [Add-AzVmssExtension.](/powershell/module/az.compute/add-azvmssextension) U `ManagedIdentityExtensionForWindows` een `ManagedIdentityExtensionForLinux`van beide of, afhankelijk van het type `-Name` virtuele machine schaal set, en de naam met behulp van de parameter. De `-Settings` parameter geeft de poort op die wordt gebruikt door het Eindpunt van het OAuth-token voor tokenacquisitie:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Als u de extensie voor de virtuele machineschaalset wilt inrichten `extensionpProfile` met de implementatiesjabloon `ManagedIdentityExtensionForLinux` Azure Resource Manager, voegt u de volgende JSON toe aan de sectie aan de sjabloon (gebruik voor de naam- en tekstelementen voor de Linux-versie).

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

De inrichting van de extensie van de virtuele machine kan mislukken als gevolg van DNS-opzoekfouten. Als dit gebeurt, start u de virtuele machine opnieuw op en probeert u het opnieuw. 

### <a name="remove-the-extension"></a>De extensie verwijderen 
Als u de `-n ManagedIdentityExtensionForWindows` extensie `-n ManagedIdentityExtensionForLinux` wilt verwijderen, gebruikt of schakelt u (afhankelijk van het type virtuele machine) met het verwijderen `Remove-AzVMExtension` van [az vm-extensie](https://docs.microsoft.com/cli/azure/vm/)of het verwijderen van az [vmss-extensie](https://docs.microsoft.com/cli/azure/vmss) voor virtuele machineschaalsets met Azure CLI of voor Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Een token aanschaffen met behulp van de extensie virtuele machine

Het volgende is een voorbeeldaanvraag met de beheerde identiteiten voor het VM Extension Endpoint van Azure-bronnen:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Beschrijving |
| ------- | ----------- |
| `GET` | Het HTTP-werkwoord, dat aangeeft dat u gegevens uit het eindpunt wilt ophalen. In dit geval een OAuth-toegangstoken. | 
| `http://localhost:50342/oauth2/token` | Het beheerde identiteiten voor het eindpunt van Azure-resources, waarbij 50342 de standaardpoort is en configureerbaar is. |
| `resource` | Een parameter querytekenreeks die de URI van de app-id van de doelbron aangeeft. Het verschijnt ook `aud` in de (publieks)claim van het uitgegeven token. In dit voorbeeld wordt een token gevraagd om toegang `https://management.azure.com/`te krijgen tot Azure Resource Manager, met een URI voor app-id's van . |
| `Metadata` | Een http-aanvraagkopveld, vereist door beheerde identiteiten voor Azure-resources als beperking tegen De Server Side Request Forgery (SSRF) aanval. Deze waarde moet worden ingesteld op "true", in alle kleine letters.|
| `object_id` | (Optioneel) Een querytekenreeksparameter die aangeeft hoe object_id van de beheerde identiteit waarvoor u het token wilt hebben. Vereist als uw VM meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|
| `client_id` | (Optioneel) Een querytekenreeksparameter die de client_id aangeeft van de beheerde identiteit waarvoor u het token wilt hebben. Vereist als uw VM meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|


Voorbeeldrespons:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Beschrijving |
| ------- | ----------- |
| `access_token` | Het gevraagde toegangstoken. Wanneer u een beveiligde REST-API aanroept, wordt het token ingesloten in het veld van de `Authorization` aanvraagkoptekst als een 'drager'-token, waardoor de API de beller kan verifiëren. | 
| `refresh_token` | Niet gebruikt door beheerde identiteiten voor Azure-resources. |
| `expires_in` | Het aantal seconden dat het toegangstoken geldig blijft, voordat het afloopt, vanaf het moment van uitgifte. Tijd van uitgifte is te vinden `iat` in de claim van het token. |
| `expires_on` | De tijdspanne wanneer het toegangstoken verloopt. De datum wordt weergegeven als het aantal seconden van "1970-01-01T0:0:0Z UTC" `exp` (komt overeen met de claim van het token). |
| `not_before` | De tijdspanne waarin het toegangstoken van kracht wordt en kan worden geaccepteerd. De datum wordt weergegeven als het aantal seconden van "1970-01-01T0:0:0Z UTC" `nbf` (komt overeen met de claim van het token). |
| `resource` | De bron waarvoor het toegangstoken is `resource` aangevraagd, komt overeen met de parameter querytekenreeks van de aanvraag. |
| `token_type` | Het type token, dat een toegangstoken "Drager" is, wat betekent dat de bron toegang kan geven tot de drager van dit token. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Problemen met de extensie van de virtuele machine oplossen 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Start de extensie van de virtuele machine opnieuw na een storing

Op Windows en bepaalde versies van Linux, als de extensie stopt, kan de volgende cmdlet worden gebruikt om handmatig opnieuw op te starten:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Waar: 
- De naam en het type extensie voor Windows zijn:`ManagedIdentityExtensionForWindows`
- Uitbreiding naam en type voor Linux is:`ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>'Automatiseringsscript' mislukt bij het uitvoeren van schema's voor beheerde identiteiten voor Azure-resources-extensie

Wanneer beheerde identiteiten voor Azure-resources zijn ingeschakeld op een virtuele machine, wordt de volgende fout weergegeven wanneer u probeert de functie 'Automatiseringsscript' te gebruiken voor de virtuele machine of de brongroep:

![Fout beheerde identiteiten voor azure-resources-automatiseringsscript-exportfout](./media/howto-migrate-vm-extension/automation-script-export-error.png)

De beheerde identiteiten voor azure-resources met virtuele machine-extensie bieden momenteel geen ondersteuning voor de mogelijkheid om het schema te exporteren naar een sjabloon voor resources. Als gevolg hiervan worden in de gegenereerde sjabloon geen configuratieparameters weergegeven om beheerde identiteiten voor Azure-resources op de bron in te schakelen. Deze secties kunnen handmatig worden toegevoegd door de voorbeelden te volgen in [Beheerde identiteiten configureren voor Azure-resources op een virtuele Azure-machine met behulp van een sjablonen.](qs-configure-template-windows-vm.md)

Wanneer de functie voor het exporteren van schema's beschikbaar komt voor de beheerde identiteiten voor de extensie virtuele machine van Azure-resources (gepland voor afschaffing in januari 2019), wordt deze weergegeven in [Resourcegroepen exporteren die VM-extensies bevatten.](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)

## <a name="limitations-of-the-virtual-machine-extension"></a>Beperkingen van de extensie van de virtuele machine 

Er zijn verschillende belangrijke beperkingen aan het gebruik van de virtuele machine extensie. 

 * De ernstigste beperking is het feit dat de referenties die worden gebruikt om tokens aan te vragen, worden opgeslagen op de virtuele machine. Een aanvaller die met succes de virtuele machine schendt, kan de referenties exfiltreren. 
 * Bovendien wordt de extensie van de virtuele machine nog steeds niet ondersteund door verschillende Linux-distributies, met een enorme ontwikkelingskosten om de extensie op elk van deze distributies te wijzigen, te bouwen en te testen. Momenteel worden alleen de volgende Linux-distributies ondersteund: 
    * CoreOS Stabiel
    * CentOS 7.1 
    * Rode Hoed 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Er is een impact op de prestaties van het implementeren van virtuele machines met beheerde identiteiten, omdat de extensie van de virtuele machine ook moet worden ingericht. 
 * Ten slotte kan de extensie van de virtuele machine alleen ondersteuning bieden voor het hebben van 32 door de gebruiker toegewezen beheerde identiteiten per virtuele machine. 

## <a name="azure-instance-metadata-service"></a>Azure-instantiemetagegevensservice

De [ImdS (Azure Instance Metadata Service)](/azure/virtual-machines/windows/instance-metadata-service) is een REST-eindpunt dat informatie biedt over het uitvoeren van virtuele machine-exemplaren die kunnen worden gebruikt om uw virtuele machines te beheren en te configureren. Het eindpunt is beschikbaar op een bekend niet-routable IP-adres (`169.254.169.254`) dat alleen toegankelijk is vanuit de virtuele machine.

Er zijn verschillende voordelen aan het gebruik van Azure IMDS om tokens aan te vragen. 

1. De service is extern aan de virtuele machine, daarom zijn de referenties die worden gebruikt door beheerde identiteiten niet langer aanwezig op de virtuele machine. In plaats daarvan worden ze gehost en beveiligd op de hostmachine van de virtuele Azure-machine.   
2. Alle Windows- en Linux-besturingssystemen die worden ondersteund op Azure IaaS kunnen beheerde identiteiten gebruiken.
3. Implementatie gaat sneller en eenvoudiger, omdat de VM-extensie niet langer hoeft te worden ingericht.
4. Met het IMDS-eindpunt kunnen maximaal 1000 door de gebruiker toegewezen beheerde identiteiten aan één virtuele machine worden toegewezen.
5. Er is geen significante wijziging in de aanvragen met behulp van IMDS in tegenstelling tot degenen die gebruik maken van de virtuele machine extensie, dus het is vrij eenvoudig om de poort over bestaande implementaties die momenteel gebruik maken van de virtuele machine extensie.

Om deze redenen is de Azure IMDS-service de feitelijke manier om tokens aan te vragen, zodra de extensie voor virtuele machines is afgeschaft. 


## <a name="next-steps"></a>Volgende stappen

* [Beheerde identiteiten voor Azure-resources gebruiken op een virtuele Azure-machine om een toegangstoken te verkrijgen](how-to-use-vm-token.md)
* [Azure-instantiemetagegevensservice](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
