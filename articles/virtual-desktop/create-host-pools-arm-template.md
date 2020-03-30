---
title: Windows Virtual Desktop-hostgroep Azure Resource Manager - Azure
description: Een hostgroep maken in Windows Virtual Desktop met een Azure Resource Manager-sjabloon.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ce6440989cbf962c474de2a6c90db4c485bf4a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292333"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Een hostpool maken met een Azure Resource Manager-sjabloon

Hostpools zijn een verzameling van een of meer identieke virtuele machines binnen Windows Virtual Desktop-tenantomgevingen. Elke hostgroep kan een appgroep bevatten waarmee gebruikers kunnen communiceren zoals op een fysiek bureaublad.

Volg de instructies van deze sectie om een hostgroep te maken voor een Windows Virtual Desktop-tenant met een Azure Resource Manager-sjabloon van Microsoft. In dit artikel wordt uitgelegd hoe u een hostgroep maakt in Windows Virtual Desktop, een brongroep maakt met VM's in een Azure-abonnement, deze VM's aansluiten bij het AD-domein en de VM's registreren met Windows Virtual Desktop.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Wat u nodig hebt om de sjabloon Azure Resource Manager uit te voeren

Zorg ervoor dat u de volgende dingen weet voordat u de sjabloon Azure Resource Manager uitvoert:

- Waar de bron van de afbeelding die u wilt gebruiken is. Is het van Azure Gallery of is het aangepast?
- Uw domein join referenties.
- Uw Windows Virtual Desktop-referenties.

Wanneer u een Windows Virtual Desktop-hostgroep maakt met de sjabloon Azure Resource Manager, u een virtuele machine maken vanuit de Azure-galerie, een beheerde afbeelding of een niet-beheerde afbeelding. Zie [Een Windows VHD of VHDX voorbereiden om te uploaden naar Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) en Een [beheerde afbeelding van een gegeneraliseerde VM in Azure](../virtual-machines/windows/capture-image-resource.md)voor meer informatie over het maken van VM-afbeeldingen.

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>De sjabloon Azure Resource Manager uitvoeren voor het inrichten van een nieuwe hostgroep

Om te beginnen gaat u naar [deze GitHub-URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>De sjabloon implementeren in Azure

Als u een Enterprise-abonnement implementeert, schuift u omlaag en selecteert **u Implementeren naar Azure,** en vult u de parameters in op basis van uw afbeeldingsbron.

Als u een abonnement in een Cloud Solution Provider-abonnement implementeert, voert u de volgende stappen uit om te implementeren in Azure:

1. Schuif omlaag en klik met de rechtermuisknop op **Implementeren naar Azure**en selecteer Vervolgens **Koppelingslocatie kopiëren**.
2. Open een teksteditor zoals Kladblok en plak de koppeling daar.
3. Direct nahttps://portal.azure.com/" " en voordat de hashtag (#) een bij teken (@) invoert, gevolgd door de tenantdomeinnaam. Hier is een voorbeeld van het `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`formaat dat u moet gebruiken: .
4. Meld u aan bij de Azure-portal als gebruiker met machtigingen voor beheerders/inzenderen bij het abonnement van de Cloud Solution Provider.
5. Plak de koppeling die u naar de teksteditor hebt gekopieerd in de adresbalk.

Zie het Windows Virtual Desktop [Readme-bestand](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)voor richtlijnen over welke parameters u moet invoeren voor uw scenario. De Readme wordt altijd bijgewerkt met de laatste wijzigingen.

## <a name="assign-users-to-the-desktop-application-group"></a>Gebruikers toewijzen aan de bureaubladtoepassingsgroep

Nadat de sjabloon GitHub Azure Resource Manager is voltooid, wijst u gebruikerstoegang toe voordat u de volledige sessiedesktops op uw virtuele machines gaat testen.

Download en importeer eerst [de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) om te gebruiken in uw PowerShell-sessie als u dat nog niet hebt gedaan.

Als u gebruikers wilt toewijzen aan de bureaubladtoepassingsgroep, opent u een PowerShell-venster en voert u deze cmdlet uit om u aan te melden bij de Windows Virtual Desktop-omgeving:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Voeg daarna gebruikers toe aan de bureaubladtoepassingsgroep met deze cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Het UPN van de gebruiker moet overeenkomen met de identiteit user1@contoso.comvan de gebruiker in Azure Active Directory (bijvoorbeeld ). Als u meerdere gebruikers wilt toevoegen, moet u deze cmdlet voor elke gebruiker uitvoeren.

Nadat u deze stappen hebt voltooid, kunnen gebruikers die zijn toegevoegd aan de bureaubladtoepassingsgroep zich aanmelden bij Windows Virtual Desktop met ondersteunde Extern bureaublad-clients en een bron voor een sessiebureaublad zien.

>[!IMPORTANT]
>Om uw Windows Virtual Desktop-omgeving in Azure te beveiligen, raden we u aan de binnenkomende poort 3389 niet op uw VM's te openen. Voor Windows Virtual Desktop is geen open inkomende poort 3389 vereist voor gebruikers om toegang te krijgen tot de VM's van de hostgroep. Als u poort 3389 moet openen voor probleemoplossingsdoeleinden, raden we u aan [just-in-time VM-toegang te](../security-center/security-center-just-in-time.md)gebruiken.