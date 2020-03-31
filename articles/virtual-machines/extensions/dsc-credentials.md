---
title: Referenties doorgeven aan Azure met gewenste statusconfiguratie
description: Meer informatie over het veilig doorgeven van referenties aan virtuele Azure-machines met Behulp van De gewenste configuratie van PowerShell (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: Dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: f7edbd0fd8791829a2d9ffaa4e7c0ee0e561cc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73748973"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Referenties doorgeven aan de Azure DSCExtension-handler

Dit artikel gaat over de DSC-extensie (Desired State Configuration) voor Azure. Zie [Inleiding tot de azure desired state configuration extension handler voor](dsc-overview.md)een overzicht van de DSC-extensiehandler .

 

## <a name="pass-in-credentials"></a>Referenties doorgeven

Als onderdeel van het configuratieproces moet u mogelijk gebruikersaccounts instellen, services openen of een programma installeren in een gebruikerscontext. Om deze dingen te doen, moet u referenties verstrekken.

U DSC gebruiken om geparameteriseerde configuraties in te stellen. In een geparameteriseerde configuratie worden referenties doorgegeven in de configuratie en veilig opgeslagen in .mof-bestanden. De Azure-extensiehandler vereenvoudigt het referentiebeheer door automatisch beheer van certificaten te bieden.

Met het volgende DSC-configuratiescript wordt een lokaal gebruikersaccount gemaakt met het opgegeven wachtwoord:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Het is belangrijk om **knooppuntlocalhost** op te nemen als onderdeel van de configuratie. De extensiehandler zoekt specifiek naar de **knooppuntlocalhost-instructie.** Als deze instructie ontbreekt, werken de volgende stappen niet. Het is ook belangrijk om de typecast **[PsCredential]** op te nemen. Dit specifieke type activeert de extensie om de referentie te versleutelen.

Ga als volgt te werk om dit script te publiceren naar Azure Blob-opslag:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Ga als het gaat om het instellen van de Azure DSC-extensie en het verstrekken van de referenties:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Hoe een referentie is beveiligd

Het uitvoeren van deze code vraagt om een referentie. Nadat de referentie is verstrekt, wordt deze kort opgeslagen in het geheugen. Wanneer de referentie wordt gepubliceerd met de cmdlet **Set-AzVMDscExtension,** wordt de referentie via HTTPS naar de VM verzonden. In de VM slaat Azure de op schijf versleutelde referenties op met behulp van het lokale VM-certificaat. De referentie wordt kort gedecodeerd in het geheugen, en dan is het opnieuw versleuteld om het door te geven aan DSC.

Dit proces is anders dan [het gebruik van beveiligde configuraties zonder de extensiehandler.](/powershell/scripting/dsc/pull-server/securemof) De Azure-omgeving biedt u een manier om configuratiegegevens veilig via certificaten te verzenden. Wanneer u de DSC-extensiehandler gebruikt, hoeft u geen **$CertificatePath** of een **$CertificateID**/ **$Thumbprint** vermelding in **ConfigurationData**op te geven.

## <a name="next-steps"></a>Volgende stappen

- Een [inleiding tot azure DSC-extensiehandler](dsc-overview.md).
- Onderzoek de [sjabloon Azure Resource Manager voor de DSC-extensie](dsc-template.md).
- Ga voor meer informatie over PowerShell DSC naar het [PowerShell-documentatiecentrum.](/powershell/scripting/dsc/overview/overview)
- Blader door de [PowerShell-galerie](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)voor meer functionaliteit die u beheren met PowerShell DSC en voor meer DSC-bronnen.