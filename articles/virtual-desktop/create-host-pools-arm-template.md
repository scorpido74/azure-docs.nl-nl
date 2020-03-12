---
title: Windows-hostgroep voor virtuele Bureau bladen Azure Resource Manager-Azure
description: Het maken van een hostgroep in virtueel bureau blad van Windows met een Azure Resource Manager sjabloon.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: f61319a1d2a5d5599a4745979f3c5abc0a713ac7
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128135"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Een hostpool maken met een Azure Resource Manager-sjabloon

Hostgroepen zijn een verzameling van een of meer identieke virtuele machines in Windows-Tenant omgevingen voor virtueel bureau blad. Elke hostgroep kan een app-groep bevatten waarmee gebruikers kunnen communiceren, op dezelfde manier als op een fysiek bureau blad.

Volg de instructies in deze sectie voor het maken van een hostgroep voor een Windows Virtual Desktop-Tenant met een Azure Resource Manager-sjabloon die door micro soft wordt verschaft. In dit artikel leest u hoe u een hostgroep maakt in virtueel bureau blad van Windows, een resource groep maakt met virtuele machines in een Azure-abonnement, deze Vm's toevoegt aan het AD-domein en de Vm's registreert met behulp van virtueel bureau blad van Windows.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Wat u nodig hebt om de Azure Resource Manager-sjabloon uit te voeren

Zorg ervoor dat u de volgende zaken kent voordat u de Azure Resource Manager sjabloon uitvoert:

- Als de bron van de afbeelding die u wilt gebruiken, is. Is het van Azure Gallery of is het aangepast?
- Referenties voor domein deelname.
- Uw Windows-referenties voor virtueel bureau blad.

Wanneer u een virtuele Windows-bureau blad-hostgroep maakt met de Azure Resource Manager sjabloon, kunt u een virtuele machine maken vanuit de Azure-galerie, een beheerde installatie kopie of een niet-beheerde installatie kopie. Voor meer informatie over het maken van VM-installatie kopieën raadpleegt u [een Windows-VHD of VHDX voorbereiden om te uploaden naar Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) en [een beheerde installatie kopie te maken van een gegeneraliseerde vm in azure](../virtual-machines/windows/capture-image-resource.md).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>De Azure Resource Manager-sjabloon uitvoeren voor het inrichten van een nieuwe hostgroep

Ga naar [deze github-URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)om te starten.

### <a name="deploy-the-template-to-azure"></a>De sjabloon implementeren in azure

Als u in een bedrijfs abonnement implementeert, schuift u naar beneden en selecteert u **implementeren naar Azure**. Ga vervolgens verder met het invullen van de para meters op basis van uw installatie kopie bron.

Als u in een Cloud Solution Provider-abonnement implementeert, voert u de volgende stappen uit om te implementeren in Azure:

1. Schuif omlaag en klik met de rechter muisknop op **implementeren naar Azure**, en selecteer vervolgens **koppelings locatie kopiëren**.
2. Open een tekst editor zoals Klad blok en plak de koppeling daar.
3. Direct na 'https://portal.azure.com/' en vóór de hashtag (#) voert u een apen staartje (@) in, gevolgd door de domein naam van de Tenant. Hier volgt een voor beeld van de indeling die u moet gebruiken: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Meld u aan bij de Azure Portal als gebruiker met beheerders-en Inzender machtigingen voor het Cloud Solution Provider-abonnement.
5. Plak de koppeling die u naar de tekst editor hebt gekopieerd naar de adres balk.

Zie het [Leesmij-bestand](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)van het Windows-virtueel bureau blad voor meer informatie over welke para meters u moet invoeren voor uw scenario. Het Leesmij-bestand wordt altijd bijgewerkt met de meest recente wijzigingen.

## <a name="assign-users-to-the-desktop-application-group"></a>Gebruikers toewijzen aan de bureaublad toepassings groep

Nadat de GitHub-Azure Resource Manager sjabloon is voltooid, moet u gebruikers toegang toewijzen voordat u begint met het testen van de volledige sessie Desk tops op uw virtuele machines.

[Down load en Importeer eerst de Windows Virtual Desktop Power shell-module](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw Power shell-sessie als u dat nog niet hebt gedaan.

Als u gebruikers wilt toewijzen aan de groep bureau blad-toepassing, opent u een Power shell-venster en voert u deze cmdlet uit om u aan te melden bij de virtuele Windows-bureaublad omgeving:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Daarna voegt u gebruikers toe aan de groep bureau blad-toepassing met deze cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

De UPN van de gebruiker moet overeenkomen met de identiteit van de gebruiker in Azure Active Directory (bijvoorbeeld user1@contoso.com). Als u meerdere gebruikers wilt toevoegen, moet u deze cmdlet voor elke gebruiker uitvoeren.

Nadat u deze stappen hebt voltooid, kunnen gebruikers die zijn toegevoegd aan de groep bureau blad, zich aanmelden bij een virtueel bureau blad van Windows met ondersteunde Extern bureaublad clients en een resource voor een sessie bureau blad bekijken.

>[!IMPORTANT]
>Voor het beveiligen van uw virtuele bureau blad-omgeving in azure, raden we u aan om de binnenkomende poort 3389 niet te openen op uw virtuele machines. Voor het virtuele bureau blad van Windows is geen open bare poort 3389 voor gebruikers nodig om toegang te krijgen tot de virtuele machines van de hostgroep. Als u poort 3389 moet openen voor het oplossen van problemen, raden we u aan [just-in-time-VM-toegang](../security-center/security-center-just-in-time.md)te gebruiken.