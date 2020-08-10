---
title: Voer failback van virtuele Azure-machines naar een primaire regio uit met de Azure Site Recovery-service.
description: Hierin wordt beschreven hoe u failback van virtuele Azure-machines naar de primaire regio met de Azure Site Recovery-service kunt uitvoeren.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 432c92bcfa8a2e0df26adf1516f5bdc9ee73d267
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502372"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Failback uitvoeren van een virtuele Azure-machine uit tussen Azure-regio's

De [Azure Site Recovery](site-recovery-overview.md)-service draagt bij aan uw strategie voor herstel na noodgeval, door de replicatie, failover en failback van on-premises machines en Azure-VM’s (virtuele machines) te beheren en in te delen.

In deze zelfstudie wordt beschreven hoe u een failback van één virtuele Azure-machine kunt uitvoeren. Nadat u een failover hebt uitgevoerd, moet u een failback naar de primaire regio uitvoeren wanneer deze beschikbaar is. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * Een failback uitvoeren van de virtuele machine in de secundaire regio.
> * De primaire virtuele machine opnieuw beveiligen naar de secundaire regio.
> 
> [!NOTE]
> 
> In deze zelfstudie wordt uitgelegd hoe u met minimale aanpassingen een failover van een aantal virtuele machines kunt uitvoeren naar een doelregio en terug naar de bronregio. Zie de [instructiegidsen over virtuele Azure-machines](../virtual-machines/windows/index.yml) voor meer gedetailleerde instructies.

## <a name="before-you-start"></a>Voordat u begint

* Zorg ervoor dat de status van de virtuele machine **Failover is doorgevoerd** is.
* Controleer of de primaire regio beschikbaar is en of u er nieuwe resources in kunt maken en er toegang toe hebt.
* Zorg ervoor dat de beveiliging is ingeschakeld.

## <a name="fail-back-to-the-primary-region"></a>Een failback naar de primaire regio uitvoeren

Nadat de virtuele machines opnieuw zijn beveiligd, kunt u desgewenst een failback uitvoeren naar de primaire regio.

1. Selecteer in de kluis **Gerepliceerde items**en selecteer vervolgens de VM die opnieuw is beveiligd.

    ![Schermopname met failback naar de primaire regio in de Azure-portal.](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. Selecteer in **Gerepliceerde items** de virtuele machine en selecteer vervolgens **Failover**.
3. Selecteer in **Failover** een herstelpunt waarnaar u de failover wilt uitvoeren:
    - **Meest recent (standaardwaarde)** : hiermee worden alle gegevens in de Site Recovery-service verwerkt en het laagste Recovery Point Objective (RPO) geboden.
    - **Laatst verwerkt**: herstelt de VM naar het laatste herstelpunt dat met Site Recovery is verwerkt.
    - **Aangepast**: voert een failover uit naar een bepaald herstelpunt. Deze optie is handig voor het uitvoeren van een testfailover.
4. Selecteer **Sluit de computer af voordat de failover wordt gestart** als u wilt dat Site Recovery VM's in de regio voor noodherstel gaat afsluiten voordat de failover wordt geactiveerd. Failover wordt voortgezet, ook als het afsluiten mislukt. 
5. Volg de voortgang van de failover op de pagina **Taken**.
6. Valideer de VM na de failover door u aan te melden bij de VM. U kunt het herstelpunt zo nodig wijzigen.
7. Nadat u de failover hebt geverifieerd, selecteert u **Failover doorvoeren**. Na het doorvoeren worden alle beschikbare herstelpunten verwijderd. De optie om het herstelpunt te wijzigen is niet meer beschikbaar.
8. Voor de VM moet zijn weergegeven dat failover en failback zijn uitgevoerd.

    ![Schermopname van VM in primaire en secundaire regio's.](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Voor machines die beheerde schijven gebruiken en de Site Recovery-extensie vanaf versie 9.28.x.x [update-rollup 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) uitvoeren, schoont Site Recovery machines in de secundaire regio voor noodherstel op nadat de failback is voltooid en de VM's opnieuw zijn beveiligd. Het is niet nodig om VM's en NIC's handmatig uit de secundaire regio te verwijderen. VM’s met niet-beheerde schijven worden niet opgeschoond. Als u de replicatie volledig uitschakelt nadat u een failback hebt uitgevoerd, worden, naast de VM's en NIC's, de schijven in de regio voor noodherstel door Site Recovery opgeschoond.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) over de stroom voor opnieuw beveiligen.
