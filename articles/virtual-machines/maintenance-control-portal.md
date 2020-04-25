---
title: Onderhouds beheer voor virtuele Azure-machines met behulp van de Azure Portal
description: Meer informatie over hoe u kunt bepalen wanneer onderhoud wordt toegepast op uw Azure-Vm's met behulp van onderhouds beheer en de Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: c0cb4800bdabe5eb500422fca55b3060b6422e8e
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82139236"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>Updates beheren met onderhouds beheer en de Azure Portal

Met de onderhouds controle kunt u bepalen wanneer u updates wilt Toep assen op uw geïsoleerde Vm's en voor Azure toegewezen hosts. In dit onderwerp worden de Azure Portal opties voor onderhouds beheer beschreven. Zie [platform updates beheren met onderhouds beheer](maintenance-control.md)voor meer informatie over de voor delen van het gebruik van onderhouds beheer, de beperkingen en andere beheer opties.

## <a name="create-a-maintenance-configuration"></a>Een onderhouds configuratie maken

1. Meld u aan bij Azure Portal.

1. Zoeken naar **onderhouds configuraties**.

   ![Scherm afbeelding die laat zien hoe onderhouds configuraties worden geopend](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. Klik op **Add**.

   ![Scherm afbeelding die laat zien hoe u een onderhouds configuratie toevoegt](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. Kies een abonnement en een resource groep, geef een naam op voor de configuratie en kies een regio. Klik op **Volgende**.

   ![Scherm opname van basis informatie over de onderhouds configuratie](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. Tags en waarden toevoegen. Klik op **Volgende**.

   ![Scherm afbeelding die laat zien hoe Tags worden toegevoegd aan een onderhouds configuratie](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. Bekijk de samenvatting. Klik op **maken**.

   ![Scherm afbeelding die laat zien hoe u een onderhouds configuratie maakt](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. Wanneer de implementatie is voltooid, klikt **u op Ga naar resource**.

   ![Scherm opname van implementatie van onderhouds configuratie voltooid](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>De configuratie toewijzen

Klik op de pagina Details van de onderhouds configuratie op toewijzingen en klik vervolgens op **resource toewijzen**. 

![Scherm afbeelding die laat zien hoe een resource wordt toegewezen](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Selecteer de resources waaraan u de onderhouds configuratie wilt toewijzen en klik op **OK**. De kolom **type** geeft aan of de resource een geïsoleerde virtuele machine of een door Azure toegewezen host is. De virtuele machine moet worden uitgevoerd om de configuratie toe te wijzen. Er treedt een fout op als u een configuratie probeert toe te wijzen aan een virtuele machine die wordt gestopt. 

<!---Shantanu to add details about the error case--->

![Scherm afbeelding die laat zien hoe u een resource selecteert](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>Configuratie controleren

U kunt controleren of de configuratie correct is toegepast of een onderhouds configuratie bekijken die momenteel is toegewezen met behulp van **onderhouds configuraties**. De kolom **type** geeft aan of de configuratie is toegewezen aan een geïsoleerde virtuele machine of een speciaal voor Azure toegewezen host. 

![Scherm afbeelding die laat zien hoe u een onderhouds configuratie controleert](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

U kunt ook de configuratie van een specifieke virtuele machine op de eigenschappen pagina controleren. Klik op **onderhoud** om de configuratie weer te geven die aan deze virtuele machine is toegewezen.

![Scherm afbeelding die laat zien hoe onderhoud voor een host wordt gecontroleerd](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>Controleren op updates die nog niet zijn uitgevoerd

Er zijn ook twee manieren om te controleren of updates in behandeling zijn voor een onderhouds configuratie. In **onderhouds configuraties**klikt u op de details van de configuratie op **toewijzingen** en controleert u de **onderhouds status**.

![Scherm afbeelding die laat zien hoe updates in behandeling worden gecontroleerd](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

U kunt ook een specifieke host controleren met behulp van **virtual machines** of eigenschappen van de toegewezen host. 

![Scherm afbeelding die laat zien hoe onderhoud voor een host wordt gecontroleerd](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>Updates toepassen

U kunt openstaande updates op aanvraag Toep assen met behulp van **virtual machines**. Klik in de VM-Details op **onderhoud** en klik op **nu onderhoud Toep assen**.

![Scherm afbeelding die laat zien hoe updates moeten worden toegepast](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>De status van het Toep assen van updates controleren 

U kunt de voortgang van de updates controleren voor een configuratie in **onderhouds configuraties** of met behulp van **virtual machines**. Klik op **onderhoud**onder Details van de virtuele machine. In het volgende voor beeld ziet u in de **onderhouds status** dat een update **in behandeling**is.

![Scherm afbeelding die laat zien hoe de status van updates in behandeling moet worden gecontroleerd](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>Een onderhouds configuratie verwijderen

Als u een configuratie wilt verwijderen, opent u de configuratie gegevens en klikt u op **verwijderen**.

![Scherm afbeelding die laat zien hoe onderhoud voor een host wordt gecontroleerd](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>Volgende stappen

Zie [onderhoud en updates](maintenance-and-updates.md)voor meer informatie.
