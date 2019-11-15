---
title: Azure voorbereiden op herstel na nood geval voor Hyper-V met Azure Site Recovery
description: Meer informatie over het voorbereiden van Azure voor herstel na nood gevallen van on-premises virtuele Hyper-V-machines met behulp van Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ef623b95e104b485c6bfc8b2f489afeca436d81e
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084179"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Azure-resources voorbereiden op herstel na nood geval voor Hyper-V

 [Azure site Recovery](site-recovery-overview.md) helpt bedrijfs continuïteit en herstel na nood gevallen (BCDR) door zakelijke apps uit te voeren tijdens geplande en ongeplande uitval. Site Recovery beheert en orkestreert noodherstel van on-premises machines en virtuele Azure-machines (VM's), met inbegrip van replicatie, failover en herstel.

Deze zelf studie is de eerste in een reeks die beschrijft hoe u herstel na nood gevallen instelt voor on-premises virtuele Hyper-V-machines.

> [!NOTE]
> We ontwerpen zelf studies om het eenvoudigste installatiepad voor een scenario weer te geven. Deze zelf studies gebruiken standaard opties als dat mogelijk is, en worden niet alle mogelijke instellingen en paden weer gegeven. Zie de sectie ' How to ' voor elk van de bijbehorende scenario's voor meer informatie.

Deze zelf studie laat zien hoe u Azure-onderdelen voorbereidt wanneer u on-premises virtuele machines (Hyper-V) wilt repliceren naar Azure. U leert het volgende:

> [!div class="checklist"]
> * Controleren of uw Azure-account replicatiemachtigingen heeft.
> * Maak een Azure Storage-account waarin afbeeldingen van gerepliceerde machines worden opgeslagen.
> * Maak een Recovery Services kluis die meta gegevens en configuratie-informatie voor Vm's en andere replicatie onderdelen opslaat.
> * Stel een Azure-netwerk in. Wanneer Azure Vm's zijn gemaakt na een failover, worden ze toegevoegd aan dit netwerk.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="sign-in"></a>Aanmelden

Meld u aan bij de [Azure Portal](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Accountmachtigingen controleren

Als u zojuist een gratis Azure-account hebt gemaakt, bent u de beheerder van dat abonnement. Als u niet de beheerder bent, kunt u met de beheerder samen werken om de benodigde machtigingen toe te wijzen. Om replicatie in te kunnen schakelen voor een nieuwe virtuele machine, moet u de volgende machtigingen hebben:

- Het maken van een VM in de geselecteerde resourcegroep.
- Het maken van een VM in het geselecteerde virtuele netwerk.
- Schrijven naar het geselecteerde opslagaccount.

Voor het volt ooien van deze taken moet aan uw account de ingebouwde rol Inzender voor de virtuele machine worden toegewezen. Als u Site Recovery bewerkingen in een kluis wilt beheren, moet aan uw account de ingebouwde rol Site Recovery Inzender zijn toegewezen.

## <a name="create-a-storage-account"></a>Maak een opslagaccount

Installatiekopieën van gerepliceerde machines worden bewaard in Azure Storage. Azure VM's worden gemaakt vanuit de opslag wanneer u een failover van on-premises naar Azure uitvoert. Het opslagaccount moet zich in dezelfde regio bevinden als de Recovery Services-kluis.

1. Selecteer in het menu [Azure Portal](https://portal.azure.com) **een resource maken** > **opslag** > **opslag account-blob, bestand, tabel, wachtrij**.
2. Voer in **Opslagaccount maken** een naam voor de account in.  De naam die u kiest, moet uniek zijn in azure, tussen 3 en 24 tekens lang zijn en mag alleen kleine letters en cijfers bevatten. Voor deze zelf studie gebruikt u **contosovmsacct1910171607**.
3. Selecteer bij **implementatiemodel** **Resource Manager**.
4. Selecteer in **rekening soort** **opslag (algemeen-doel v1)** . Selecteer niet blob-opslag.
5. Selecteer bij **Replicatie** de standaardoptie **Geografisch redundante opslag met leestoegang** voor opslagredundantie. Zorg ervoor dat de instelling beveiligde overdracht vereist is uitgeschakeld.
6. Selecteer bij **Prestaties** **Standard**. Selecteer vervolgens in de **laag Access**de standaard optie **Hot**.
7. Kies in **abonnement**het abonnement waarin u het nieuwe opslag account wilt maken.
8. Geef bij **Resourcegroep** een nieuwe resourcegroep op. Een Azure-resource groep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Voor deze zelf studie gebruikt u **ContosoRG**.
9. Kies bij **locatie**de geografische locatie voor uw opslag account. Gebruik **Europa-West**voor deze zelf studie.
10. Selecteer **Maken** om het opslagaccount te maken.

   ![Maak een opslagaccount](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

1. Selecteer in de Azure Portal **+ een resource maken**en zoek vervolgens in azure Marketplace naar Recovery Services.
2. Selecteer **Backup en site Recovery (OMS)** . Selecteer vervolgens op de pagina **back-up en site Recovery** **maken**.
1. Voer in de **Recovery Services-kluis > naam**een beschrijvende naam in om de kluis aan te duiden. Gebruik voor deze zelfstudie **ContosoVMVault**.
2. Selecteer bij **Resourcegroep** een bestaande resourcegroep of maak een nieuwe. Voor deze zelf studie gebruikt u **contosoRG**.
3. Selecteer bij **locatie**de regio waar de kluis zich bevindt. Gebruik **Europa-West**voor deze zelf studie.
4. Voor snelle toegang tot de kluis vanuit het dashboard, selecteert u **Aan dashboard vastmaken** > **Maken**.

![Een nieuwe kluis maken](./media/tutorial-prepare-azure/new-vault-settings.png)

De nieuwe kluis wordt weergegeven in **Dashboard** > **Alle resources** en op de hoofdpagina **Recovery Services-kluizen**.

## <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

Wanneer de Azure VM's zijn gemaakt vanuit de opslag na de failover, worden ze gekoppeld aan dit netwerk.

1. Selecteer in [Azure Portal](https://portal.azure.com) **Een resource maken** > **Netwerken** > **Virtueel netwerk**. Zorg ervoor dat Resource Manager is geselecteerd als het implementatie model.
2. Voer bij **Naam** een netwerknaam in. De naam moet uniek zijn binnen de Azure-resourcegroep. Voor deze zelf studie gebruikt u **ContosoASRnet**.
3. Geef de resource groep op waarin u het netwerk wilt maken. Voor deze zelf studie gebruikt u de bestaande resource groep **contosoRG**.
4. Voer in **adres bereik** **10.0.0.0/24** in als het bereik voor het netwerk. Er is geen subnet voor dit netwerk.
5. Selecteer bij **Abonnement** het abonnement waarin u het netwerk wilt maken.
6. Kies bij **locatie**de optie **Europa-West**. Het netwerk moet zich in dezelfde regio bevinden als de Recovery Services-kluis.
7. Behoud de standaard opties van de Basic DDoS-beveiliging, zonder service-eind punten in het netwerk.
8. Selecteer **Maken**.

![Een virtueel netwerk maken](media/tutorial-prepare-azure/create-network.png)

Het duurt een paar seconden voordat het virtuele netwerk is gemaakt. Nadat de app is gemaakt, wordt deze weer geven in het dash board van Azure Portal.

## <a name="useful-links"></a>Handige koppelingen

Meer informatie over:
- [Azure-netwerken](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De on-premises Hyper-V-infra structuur voorbereiden voor herstel na nood geval naar Azure](hyper-v-prepare-on-premises-tutorial.md)
