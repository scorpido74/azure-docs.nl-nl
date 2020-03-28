---
title: Ga terug naar een primaire regio met de Azure Site Recovery-service.
description: Beschrijft hoe u Azure VM's naar het primaire gebied terugwerken met de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c27b7bf29e5f124fdcfb886b658fd8e9d4cc48fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74091347"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Een Azure VM tussen Azure-regio's niet meer teruglaten

De [Azure Site Recovery-service](site-recovery-overview.md) draagt bij aan uw strategie voor noodherstel door replicatie, failover en failback van on-premises machines en Virtuele Azure-machines (VM's) te beheren en te orkestreren.

In deze zelfstudie wordt beschreven hoe u één Azure VM teruglaten mislukken. Nadat u hebt gefaald, moet u niet meer naar de primaire regio gaan wanneer deze beschikbaar is. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * De VM in het secundaire gebied teruglaten mislukken.
> * Bescherm de primaire vm terug naar het secundaire gebied.
> 
> [!NOTE]
> 
> Met deze zelfstudie u een paar VM's naar een doelregio en terug naar het brongebied met minimale aanpassingen. Bekijk de [handleidingen voor Azure VM's voor](https://docs.microsoft.com/azure/virtual-machines/windows/)meer diepgaande instructies.

## <a name="before-you-start"></a>Voordat u begint

* Zorg ervoor dat de status van de VM **failover is vastgelegd.**
* Controleer of de primaire regio beschikbaar is en of u er nieuwe bronnen in maken en openen.
* Zorg ervoor dat herbeveiliging is ingeschakeld.

## <a name="fail-back-to-the-primary-region"></a>Een failback naar de primaire regio uitvoeren

Nadat VM's opnieuw zijn beveiligd, u zo nodig niet meer naar de primaire regio.

1. Selecteer in de kluis **Gerepliceerde items**en selecteer vervolgens de vm die opnieuw is beveiligd.

    ![Failback naar primaire](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. Selecteer **in gerepliceerde items**de VM en selecteer Vervolgens **Failover**.
3. Selecteer **in Failover**een herstelpunt om niet over te gaan:
    - **Laatste (standaard)**: Verwerkt alle gegevens in de Site Recovery-service en biedt de laagste doelstelling voor herstelpunten (RPO).
    - **Laatste verwerkt**: Hiermee wordt de VM teruggezet naar het laatste herstelpunt dat is verwerkt door Site Recovery.
    - **Aangepast:** mislukt naar een bepaald herstelpunt. Deze optie is handig voor het uitvoeren van een testfailover.
4. Selecteer De machine afsluiten voordat u **failover begint** als u wilt dat Siteherstel een uitschakeling van VM's in dr-regio probeert voordat de failover wordt geactiveerd. De failover gaat door, zelfs als de shutdown mislukt. 
5. Volg de voortgang van de failover op de pagina **Taken**.
6. Nadat de failover is voltooid, valideert u de VM door u aan te melden. U het herstelpunt indien nodig wijzigen.
7. Nadat u de failover hebt geverifieerd, selecteert u **De failover vastleggen**. Het plegen verwijdert alle beschikbare herstelpunten. De optie wijzigingsherstelpunt is niet meer beschikbaar.
8. De VM moet worden weergegeven als mislukt over en mislukt terug.

    ![VM in primaire en secundaire regio's](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Voor machines met de Site Recovery-extensieversie 9.28.x.x verder [Update rollup 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) Site Recovery reinigt machines in het secundaire noodherstelgebied, nadat failback is voltooid en VM's opnieuw zijn beveiligd. Het is niet nodig om VM's en NIC's handmatig te verwijderen in het secundaire gebied. Als u replicatie volledig uitschakelt nadat u niet meer bent teruggegaan, ruimt Site Recovery de schijven op in het gebied voor noodherstel, naast de VM's en NIC's.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) over de reprotectiestroom.
