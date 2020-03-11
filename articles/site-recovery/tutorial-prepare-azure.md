---
title: Azure voorbereiden voor on-premises nood herstel met Azure Site Recovery
description: Informatie over het voorbereiden van Azure op herstel na noodgevallen van on-premises machines met Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1ec668fac087773001ca401eefb5ca8bc10ea2b8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367087"
---
# <a name="prepare-azure-for-on-premises-disaster-recovery-to-azure"></a>Azure voorbereiden voor on-premises herstel na nood gevallen naar Azure

In dit artikel wordt beschreven hoe u Azure-resources en-onderdelen voorbereidt, zodat u herstel na nood gevallen van on-premises VMware-Vm's, virtuele Hyper-V-machines of fysieke Windows/Linux-servers naar Azure kunt instellen met behulp van de [Azure site Recovery](site-recovery-overview.md) -service.

Dit artikel is de eerste zelfstudie in een reeks waarin u leest hoe u noodherstel kunt instellen voor on-premises VM’s. 


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Controleer of het Azure-account replicatie machtigingen heeft.
> * Maak een Recovery Services-kluis. Een kluis bevat metagegevens en configuratiegegevens voor VM’s, en andere replicatieonderdelen.
> * Stel een Azure Virtual Network (VNet) in. Wanneer Azure Vm's zijn gemaakt na een failover, worden ze toegevoegd aan dit netwerk.

> [!NOTE]
> Zelf studies laten u de eenvoudigste implementatie traject voor een scenario zien. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Raadpleeg het artikel in het gedeelte How to van de inhouds opgave van Site Recovery voor gedetailleerde instructies.

## <a name="before-you-start"></a>Voordat u begint

- Bekijk de architectuur voor [VMware](vmware-azure-architecture.md), [Hyper-V](hyper-v-azure-architecture.md)en [fysieke server](physical-azure-architecture.md) herstel na nood gevallen.
- Lees veelgestelde vragen voor [VMware](vmware-azure-common-questions.md) en [Hyper-V](hyper-v-azure-common-questions.md)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint. Meld u vervolgens aan bij de [Azure Portal](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Accountmachtigingen controleren

Als u zojuist een gratis Azure-account hebt gemaakt, bent u de beheerder van uw abonnement en hebt u de benodigde machtigingen. Als u niet de abonnementsbeheerder bent, neemt u contact op met de beheerder om de machtigingen te krijgen die u nodig hebt. Om replicatie in te kunnen schakelen voor een nieuwe virtuele machine, moet u de volgende machtigingen hebben:

- Het maken van een VM in de geselecteerde resourcegroep.
- Het maken van een VM in het geselecteerde virtuele netwerk.
- Schrijven naar een Azure Storage-account.
- Schrijven naar een door Azure beheerde schijf.

U kunt deze taken alleen uitvoeren als aan uw account de ingebouwde rol van Inzender voor virtuele machines is toegewezen. Daarnaast moet aan uw account de ingebouwde rol Site Recovery Inzender zijn toegewezen om Site Recovery bewerkingen in een kluis te beheren.


## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

1. Selecteer in het menu Azure Portal de optie **een resource maken**en zoek op de Marketplace naar **herstel**.
2. Selecteer **back-up en site Recovery** in de zoek resultaten en klik op de pagina back-up en site Recovery op **maken**. 
3. Selecteer op de pagina **Recovery Services kluis maken** het **abonnement**. We gebruiken het **Contoso-abonnement**.
4. Selecteer bij **Resourcegroep** een bestaande resourcegroep of maak een nieuwe. Voor deze zelfstudie gebruiken we **contosoRG**.
5. Voer in **kluis naam**een beschrijvende naam in om de kluis aan te duiden. Voor deze reeks zelfstudies gebruiken we **ContosoVMVault**.
6. Selecteer in **regio**de regio waarin de kluis zich bevindt. gebruiken we **Europa - west**.
7. Selecteer **Controleren + maken**.

   ![Een nieuwe kluis maken](./media/tutorial-prepare-azure/new-vault-settings.png)

   De nieuwe kluis wordt nu weer gegeven in het **dash board** > **alle resources**en op de hoofd pagina van **Recovery Services kluizen** .

## <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

On-premises machines worden gerepliceerd naar Azure Managed disks. Als er een failover optreedt, worden virtuele Azure-machines gemaakt op basis van deze beheerde schijven en gekoppeld aan het Azure-netwerk dat u in deze procedure opgeeft.

1. Selecteer in [Azure Portal](https://portal.azure.com)**Een resource maken** > **Netwerken** > **Virtueel netwerk**.
2. Zorg ervoor dat **Resource Manager** is geselecteerd als het implementatie model.
3. Voer bij **Naam** een netwerknaam in. De naam moet uniek zijn binnen de Azure-resourcegroep. In deze zelfstudie gebruiken we **ContosoASRnet**.
4. Voer in **adres ruimte**het adres bereik van het virtuele netwerk in de notatie CDR in. We gebruiken **10.1.0.0/24**.
5. Selecteer bij **Abonnement** het abonnement waarin u het netwerk wilt maken.
6. Geef de **resource groep** op waarin het netwerk wordt gemaakt. We gebruiken de bestaande resourcegroep **contosoRG**.
7. Selecteer bij **locatie**de regio waarin de Recovery Services kluis is gemaakt. In onze zelf studie is deze **Europa-West**. Het netwerk moet zich in dezelfde regio bevinden als de kluis.
8. In **adres bereik**voert u het bereik voor het netwerk in. We gebruiken **10.1.0.0/24**en maken geen gebruik van een subnet.
9. We hebben de standaard opties van Basic DDoS Protection, zonder service-eind punt of firewall op het netwerk, verlaten.
9. Selecteer **Maken**.

   ![Een virtueel netwerk maken](media/tutorial-prepare-azure/create-network.png)

Het duurt een paar seconden voordat het virtuele netwerk is gemaakt. Nadat de app is gemaakt, wordt deze weer geven in het dash board van Azure Portal.




## <a name="next-steps"></a>Volgende stappen

- Voor de nood herstel van VMware moet u [de on-premises VMware-infra structuur voorbereiden](tutorial-prepare-on-premises-vmware.md).
- [Bereid de on-premises hyper-v-servers voor op](hyper-v-prepare-on-premises-tutorial.md)herstel na nood gevallen voor hyper-v.
- [De configuratie server en de bron omgeving instellen](physical-azure-disaster-recovery.md) voor herstel na nood gevallen voor fysieke servers
- [Meer informatie](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) over Azure-netwerken.
- [Meer informatie over](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) Managed disks.
