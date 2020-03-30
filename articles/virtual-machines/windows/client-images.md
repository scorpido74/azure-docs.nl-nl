---
title: Windows-clientafbeeldingen gebruiken in Azure
description: Voordelen voor het gebruik van Visual Studio-abonnementen gebruiken om Windows 7, Windows 8 of Windows 10 in Azure te implementeren voor dev/testscenario's
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 812e6d251943d4418666f221ad8b5d2b6e501736
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74039501"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Windows-client in Azure gebruiken voor scenario's voor ontwikkelen/testen
U Windows 7, Windows 8 of Windows 10 Enterprise (x64) in Azure gebruiken voor dev/testscenario's, mits u een geschikt Visual Studio-abonnement (voorheen MSDN) hebt. In dit artikel worden de geschiktheidsvereisten beschreven voor het uitvoeren van Windows 7, Windows 8.1, Windows 10 Enterprise in Azure en het gebruik van de volgende Azure Gallery-afbeeldingen.

![Afbeeldingsgegevens van de Azure-portal](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Raadpleeg Windows 10 Pro- en Windows 10 Pro N-afbeelding in Azure Gallery naar [Hoe u Windows 10 in Azure implementeert met multitenant hostingrechten](windows-desktop-multitenant-hosting-deployment.md)
>![Pro-afbeeldingsgegevens van de Azure-portal](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>In aanmerking komen voor een abonnement
Actieve Visual Studio-abonnees (mensen die een Visual Studio-abonnementslicentie hebben aangeschaft) kunnen Windows-client gebruiken voor ontwikkelings- en testdoeleinden. Windows-client kan worden gebruikt op uw eigen hardware en Azure virtuele machines die worden uitgevoerd in elk type Azure-abonnement. Windows-client mag niet worden geïmplementeerd in of gebruikt op Azure voor normaal productiegebruik of worden gebruikt door mensen die geen actieve Visual Studio-abonnees zijn.

Voor uw gemak zijn bepaalde Windows 10-afbeeldingen beschikbaar in de Azure Gallery binnen [in aanmerking komende dev/testaanbiedingen.](#eligible-offers) Visual Studio-abonnees binnen elk type aanbieding kunnen ook adequaat een 64-bits Windows 7-, Windows 8- of Windows 10-afbeelding [voorbereiden en maken](prepare-for-upload-vhd-image.md) en vervolgens [uploaden naar Azure.](upload-generalized-managed.md) Het gebruik blijft beperkt tot dev/test door actieve Visual Studio-abonnees.

## <a name="eligible-offers"></a>In aanmerking komende aanbiedingen
In de volgende tabel worden de aanbiedings-id's beschreven die in aanmerking komen voor de implementatie van Windows 10 via de Azure Gallery. De Windows 10-afbeeldingen zijn alleen zichtbaar voor de volgende aanbiedingen. Visual Studio-abonnees die Windows-client in een ander aanbiedingstype moeten uitvoeren, vereisen dat u een 64-bits Windows 7-, Windows 8- of Windows 10-afbeelding [adequaat voorbereidt en maakt](prepare-for-upload-vhd-image.md) en vervolgens [uploadt naar Azure.](upload-generalized-managed.md)

| Naam van aanbieding | Aanbiedingsnummer | Beschikbare clientafbeeldingen |
|:--- |:---:|:---:|
| [Dev/Test met betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Visual Studio Enterprise (MPN)-abonnees](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Visual Studio Professional-abonnees](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Visual Studio Test Professional-abonnees](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium met MSDN (voordeel)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Abonnees van Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Visual Studio Enterprise (BizSpark)-abonnees](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Uw Azure-abonnement controleren
Als u uw aanbiedings-id niet kent, u deze op een van de twee manieren verkrijgen via de Azure-portal:  

- Ga als een abonnement naar de andere *kant:*

  ![Id-gegevens aanbieden vanuit de Azure-portal](./media/client-images/offer-id-azure-portal.png) 

- Of klik op **Facturering** en klik vervolgens op uw abonnements-ID. De aanbiedings-id wordt weergegeven in het *venster Facturering.*

U de aanbiedings-id ook bekijken op het [tabblad 'Abonnementen'](https://account.windowsazure.com/Subscriptions) van de Azure-accountportal:

![Id-gegevens aanbieden vanuit de Azure-accountportal](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Volgende stappen
U uw VM's nu implementeren met [PowerShell-sjablonen](quick-create-powershell.md) [voor Resource Beheer](ps-template.md)of Visual [Studio.](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

