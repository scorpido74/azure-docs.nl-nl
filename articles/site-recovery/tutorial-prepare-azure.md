---
title: Azure voorbereiden op on-premises herstel na noodgevallen met Azure Site Recovery
description: Informatie over het voorbereiden van Azure op herstel na noodgevallen van on-premises machines met Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ba5ecc29edbcd69324500e87add846e4395ce0a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067567"
---
# <a name="prepare-azure-for-on-premises-disaster-recovery-to-azure"></a>Azure voorbereiden op on-premises noodherstel naar Azure

In dit artikel wordt beschreven hoe u Azure-resources en -componenten voorbereiden, zodat u met behulp van de [Azure Site Recovery-service](site-recovery-overview.md) on-premises Vm's vMware-vm's, HyperV-VM's of Windows/Linux-servers instellen op Azure.

Dit artikel is de eerste zelfstudie in een reeks waarin u leest hoe u noodherstel kunt instellen voor on-premises VM’s. 


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Controleer of het Azure-account replicatiemachtigingen heeft.
> * Maak een Recovery Services-kluis. Een kluis bevat metagegevens en configuratiegegevens voor VM’s, en andere replicatieonderdelen.
> * Een Virtueel Azure-netwerk (VNet) instellen. Wanneer Azure VM's worden gemaakt na failover, worden ze samengevoegd met dit netwerk.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Bekijk het artikel in de sectie Hoe naar de inhoudsopgave van de site herstel voor gedetailleerde instructies.

## <a name="before-you-start"></a>Voordat u begint

- Bekijk de architectuur voor [VMware,](vmware-azure-architecture.md) [Hyper-V](hyper-v-azure-architecture.md)en [fysieke server](physical-azure-architecture.md) disaster recovery.
- Lees veelgestelde vragen voor [VMware](vmware-azure-common-questions.md) en [Hyper-V](hyper-v-azure-common-questions.md)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) voordat u begint. Meld u dan aan bij de [Azure-portal.](https://portal.azure.com)


## <a name="verify-account-permissions"></a>Accountmachtigingen controleren

Als u zojuist uw gratis Azure-account hebt gemaakt, bent u de beheerder van uw abonnement en hebt u de machtigingen die u nodig hebt. Als u niet de abonnementsbeheerder bent, neemt u contact op met de beheerder om de machtigingen te krijgen die u nodig hebt. Om replicatie in te kunnen schakelen voor een nieuwe virtuele machine, moet u de volgende machtigingen hebben:

- Het maken van een VM in de geselecteerde resourcegroep.
- Het maken van een VM in het geselecteerde virtuele netwerk.
- Schrijf naar een Azure-opslagaccount.
- Schrijf naar een door Azure beheerde schijf.

U kunt deze taken alleen uitvoeren als aan uw account de ingebouwde rol van Inzender voor virtuele machines is toegewezen. Om siteherstelbewerkingen in een kluis te beheren, moet bovendien aan uw account de ingebouwde rol siteherstelmedewerker worden toegewezen.


## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

1. Selecteer in het menu azure portal de optie **Een resource maken**en zoek in de Marketplace voor **herstel**.
2. Selecteer **Back-up en siteherstel** in de zoekresultaten en klik op de pagina Back-up en siteherstel op **Maken**. 
3. Selecteer op de **kluispagina Herstelservices maken** de **optie Abonnement**. We gebruiken **Contoso-abonnement.**
4. Selecteer bij **Resourcegroep** een bestaande resourcegroep of maak een nieuwe. Voor deze zelfstudie gebruiken we **contosoRG**.
5. Voer in **Vault-naam**een vriendelijke naam in om de kluis te identificeren. Voor deze reeks zelfstudies gebruiken we **ContosoVMVault**.
6. Selecteer **in Regio**het gebied waarin de kluis zich moet bevinden. gebruiken we **Europa - west**.
7. Selecteer **Controleren + maken**.

   ![Een nieuwe kluis maken](./media/tutorial-prepare-azure/new-vault-settings.png)

   De nieuwe kluis wordt nu weergegeven in **Dashboard** > **Alle bronnen**en op de pagina Met de belangrijkste **vaults van Recovery Services.**

## <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

On-premises machines worden gerepliceerd naar door Azure beheerde schijven. Wanneer er een failerstoring optreedt, worden Azure VM's gemaakt van deze beheerde schijven en zijn ze verbonden met het Azure-netwerk dat u in deze procedure opgeeft.

1. Selecteer in [Azure Portal](https://portal.azure.com) achtereenvolgens **Een resource maken** > **Netwerken** > **Virtueel netwerk**.
2. **Resourcebeheer** geselecteerd als implementatiemodel.
3. Voer bij **Naam** een netwerknaam in. De naam moet uniek zijn binnen de Azure-resourcegroep. In deze zelfstudie gebruiken we **ContosoASRnet**.
4. Voer **in adresruimte**het adresbereik van het virtuele netwerk in CDR-notatie in. We gebruiken **10.1.0.0/24.**
5. Selecteer bij **Abonnement** het abonnement waarin u het netwerk wilt maken.
6. Geef de **resourcegroep** op waarin het netwerk wordt gemaakt. We gebruiken de bestaande resourcegroep **contosoRG**.
7. Selecteer **in Locatie**dezelfde regio als die waarin de kluis Herstelservices is gemaakt. In onze tutorial is het **West-Europa**. Het netwerk moet zich in dezelfde regio bevinden als de kluis.
8. Voer **in adresbereik**het bereik voor het netwerk in. We gebruiken **10.1.0.0/24**en gebruiken geen subnet.
9. We laten de standaardopties van basisDDoS-beveiliging, zonder serviceeindpunt of firewall op het netwerk.
9. Selecteer **Maken**.

   ![Een virtueel netwerk maken](media/tutorial-prepare-azure/create-network.png)

Het duurt een paar seconden voordat het virtuele netwerk is gemaakt. Nadat het is gemaakt, ziet u het in het Azure-portaldashboard.




## <a name="next-steps"></a>Volgende stappen

- Voor VMware disaster [recovery, de voorbereiding van de on-premises VMware-infrastructuur](tutorial-prepare-on-premises-vmware.md).
- Voor hyper-v-noodherstel [bereidt u de on-premises Hyper-V-servers voor.](hyper-v-prepare-on-premises-tutorial.md)
- Voor herstel van fysieke servers noodherstel stelt [u de configuratieserver en de bronomgeving in](physical-azure-disaster-recovery.md)
- [Meer informatie](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) over Azure-netwerken.
- [Meer informatie over](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) beheerde schijven.
