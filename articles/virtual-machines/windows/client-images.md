---
title: Windows-client installatie kopieën in azure gebruiken
description: De voor delen van Visual Studio-abonnementen gebruiken om Windows 7, Windows 8 of Windows 10 in azure te implementeren voor dev/test-scenario's
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: e0853b08ced0b9ca328d5e7f506ca54299aebeee
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028189"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Windows-client in Azure gebruiken voor scenario's voor ontwikkelen/testen
U kunt Windows 7, Windows 8 of Windows 10 Enter prise (x64) gebruiken in azure voor ontwikkel-en test scenario's met de juiste Visual Studio (voorheen MSDN)-abonnement. In dit artikel vindt u een overzicht van de vereisten voor het uitvoeren van Windows 7, Windows 8,1, Windows 10 Enter prise in Azure en het gebruik van de volgende Azure Gallery-installatie kopieën.

![Details van de afbeelding van de Azure Portal](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Zie voor een installatie kopie van Windows 10 Pro en Windows 10 Pro N in azure Gallery [hoe u Windows 10 op Azure implementeert met multi tenant-hosting rechten](windows-desktop-multitenant-hosting-deployment.md) 
> ![ Pro Image Details van de Azure Portal](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Abonnements geschiktheid
Actieve Visual Studio-abonnees (personen die een licentie voor een Visual Studio-abonnement hebben aangeschaft) kunnen Windows-clients gebruiken voor ontwikkelings-en test doeleinden. Windows-client kan worden gebruikt op uw eigen hardware en virtuele Azure-machines die worden uitgevoerd in elk type Azure-abonnement. Windows-client kan niet worden geïmplementeerd op of gebruikt in azure voor normaal productie gebruik of worden gebruikt door mensen die geen actieve Visual Studio-abonnees zijn.

Voor uw gemak zijn bepaalde Windows 10-installatie kopieën beschikbaar vanuit de Azure Gallery binnen [in aanmerking komende ontwikkel-en test aanbiedingen](#eligible-offers). Visual Studio-abonnees binnen elk type aanbieding kunnen ook adequaat een 64-bits Windows 7-, Windows 8-of Windows 10-installatie kopie [voorbereiden en maken](prepare-for-upload-vhd-image.md) en vervolgens [uploaden naar Azure](upload-generalized-managed.md). Het gebruik blijft beperkt tot dev/test door actieve Visual Studio-abonnees.

## <a name="eligible-offers"></a>Aanbiedingen in aanmerking komend
De volgende tabel bevat informatie over de aanbieding-Id's die in aanmerking komen voor het implementeren van Windows 10 via de Azure Gallery. De Windows 10-installatie kopieën zijn alleen zichtbaar voor de volgende aanbiedingen. Voor Visual Studio-abonnees die Windows-client in een ander type aanbieding moeten uitvoeren, moet u een 64-bits Windows 7-, Windows 8-of Windows 10-installatie kopie op een [juiste wijze voorbereiden en maken](prepare-for-upload-vhd-image.md) en [vervolgens uploaden naar Azure](upload-generalized-managed.md).

| Naam van aanbieding | Aanbiedings nummer | Beschik bare client installatie kopieën |
|:--- |:---:|:---:|
| [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Visual Studio Enterprise (MPN)-abonnees](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Visual Studio Professional-abonnees](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Visual Studio Test Professional-abonnees](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium met MSDN (voor deel)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Abonnees van Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Visual Studio Enterprise (BizSpark)-abonnees](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Uw Azure-abonnement controleren
Als u uw aanbiedings-ID niet weet, kunt u deze op een van de volgende twee manieren verkrijgen via de Azure Portal:  

- In het venster *abonnementen* :

  ![Details van de aanbiedings-ID van de Azure Portal](./media/client-images/offer-id-azure-portal.png) 

- U kunt ook op **facturering** klikken en vervolgens op uw abonnements-id klikken. De aanbiedings-ID wordt weer gegeven in het *facturerings* venster.

U kunt ook de aanbiedings-ID weer geven op het [tabblad abonnementen](https://account.windowsazure.com/Subscriptions) van de Azure-account portal:

![Details van aanbiedings-ID vanuit de Azure-account Portal](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Volgende stappen
U kunt nu uw Vm's implementeren met behulp van [Power shell](quick-create-powershell.md), [Resource Manager-sjablonen](ps-template.md)of [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
