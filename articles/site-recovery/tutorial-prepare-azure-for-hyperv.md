---
title: Azure voorbereiden op herstel van hyperv-nood met Azure Site Recovery
description: Meer informatie over het voorbereiden van Azure op noodherstel van on-premises Hyper-V Vm's met Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ef623b95e104b485c6bfc8b2f489afeca436d81e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084179"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Azure-bronnen voorbereiden op herstel na spoedherstel van Hyper V

 [Azure Site Recovery](site-recovery-overview.md) helpt bedrijfscontinuïteit en disaster recovery (BCDR) door zakelijke apps actief te houden tijdens geplande en ongeplande uitval. Site Recovery beheert en orkestreert noodherstel van on-premises machines en virtuele Azure-machines (VM's), met inbegrip van replicatie, failover en herstel.

Deze zelfstudie is de eerste in een reeks waarin wordt beschreven hoe u noodherstel voor on-premises Hyper-VMs instellen.

> [!NOTE]
> We ontwerpen zelfstudies om het eenvoudigste implementatiepad voor een scenario weer te geven. Deze zelfstudies maken gebruik van standaardopties waar mogelijk en tonen niet alle mogelijke instellingen en paden. Zie de sectie 'Hoe to' voor elk corresponderend scenario voor meer informatie.

In deze zelfstudie ziet u hoe u Azure-componenten voorbereiden wanneer u on-premises VM's (Hyper-V) wilt repliceren naar Azure. U leert het volgende:

> [!div class="checklist"]
> * Controleren of uw Azure-account replicatiemachtigingen heeft.
> * Maak een Azure-opslagaccount, dat afbeeldingen van gerepliceerde machines opslaat.
> * Maak een vault van Recovery Services, waarin metagegevens en configuratiegegevens voor VM's en andere replicatiecomponenten worden opgeslagen.
> * Stel een Azure-netwerk in. Wanneer Azure VM's worden gemaakt na failover, worden ze samengevoegd met dit netwerk.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) voordat u begint.

## <a name="sign-in"></a>Aanmelden

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Accountmachtigingen controleren

Als u zojuist een gratis Azure-account hebt gemaakt, bent u de beheerder van dat abonnement. Als u niet de beheerder bent, werkt u samen met de beheerder om de machtigingen toe te wijzen die u nodig hebt. Om replicatie in te kunnen schakelen voor een nieuwe virtuele machine, moet u de volgende machtigingen hebben:

- Het maken van een VM in de geselecteerde resourcegroep.
- Het maken van een VM in het geselecteerde virtuele netwerk.
- Schrijven naar het geselecteerde opslagaccount.

Als u deze taken wilt voltooien, moet aan uw account de ingebouwde rol Virtuele machineinzender worden toegewezen. Als u siteherstelbewerkingen in een kluis wilt beheren, moet aan uw account de ingebouwde rol Siterecovery-bijdrager worden toegewezen.

## <a name="create-a-storage-account"></a>Een opslagaccount maken

Installatiekopieën van gerepliceerde machines worden bewaard in Azure Storage. Azure VM's worden gemaakt vanuit de opslag wanneer u een failover van on-premises naar Azure uitvoert. Het opslagaccount moet zich in dezelfde regio bevinden als de Recovery Services-kluis.

1. Selecteer in het menu [Azure-portal](https://portal.azure.com) de optie **Een** > **bronopslagopslagaccount** > maken **- blob, bestand, tabel, wachtrij**.
2. Voer in **Opslagaccount maken** een naam voor de account in.  De naam die u kiest, moet uniek zijn binnen Azure, 3 tot 24 tekens lang zijn en alleen kleine letters en cijfers gebruiken. Gebruik voor deze zelfstudie **contosovmsacct1910171607**.
3. Selecteer bij **implementatiemodel****Resource Manager**.
4. Selecteer **Opslag (v1 voor algemeen gebruik)** in **accountsoort**. Selecteer niet blob-opslag.
5. Selecteer bij **Replicatie** de standaardoptie **Geografisch redundante opslag met leestoegang** voor opslagredundantie. Laat de vereiste instelling Secure transfer als Uitgeschakeld.
6. Selecteer bij **Prestaties****Standard**. Selecteer vervolgens in **de categorie Access**de standaardoptie **Hot**.
7. Kies **in Abonnement**het abonnement waarin u het nieuwe opslagaccount wilt maken.
8. Geef bij **Resourcegroep** een nieuwe resourcegroep op. Een Azure-brongroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Gebruik **ContosoRG**voor deze zelfstudie.
9. Kies **in Locatie**de geografische locatie voor uw opslagaccount. Gebruik **West-Europa**voor deze zelfstudie.
10. Selecteer **Maken** om het opslagaccount te maken.

   ![Een opslagaccount maken](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

1. Selecteer in de Azure-portal **+Een resource maken**en zoek vervolgens in de Azure Marketplace voor Herstelservices.
2. Selecteer **Back-up en siteherstel (OMS)**. Selecteer vervolgens op de pagina **Back-up en siteherstel** de optie **Maken**.
1. Voer in **Recovery services vault > Name**een vriendelijke naam in om de kluis te identificeren. Gebruik voor deze zelfstudie **ContosoVMVault**.
2. Selecteer bij **Resourcegroep** een bestaande resourcegroep of maak een nieuwe. Gebruik voor deze zelfstudie **contosoRG.**
3. Selecteer **in Locatie**het gebied waar de kluis zich moet bevinden. Gebruik **West-Europa**voor deze zelfstudie.
4. Als u snel toegang wilt krijgen tot de kluis vanuit het dashboard, selecteert u **Vastmaken aan dashboard** > **Maken**.

![Een nieuwe kluis maken](./media/tutorial-prepare-azure/new-vault-settings.png)

De nieuwe kluis wordt weergegeven op **Dashboard** > **Alle resources**en op de pagina **Overherstelservices.**

## <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

Wanneer de Azure VM's zijn gemaakt vanuit de opslag na de failover, worden ze gekoppeld aan dit netwerk.

1. Selecteer in [Azure Portal](https://portal.azure.com) achtereenvolgens **Een resource maken** > **Netwerken** > **Virtueel netwerk**. Laat Resource Manager geselecteerd als het implementatiemodel.
2. Voer bij **Naam** een netwerknaam in. De naam moet uniek zijn binnen de Azure-resourcegroep. Gebruik **ContosoASRnet**voor deze zelfstudie .
3. Geef de resourcegroep op waarin het netwerk moet worden gemaakt. Gebruik voor deze zelfstudie de bestaande resourcegroep **contosoRG**.
4. Voer **in adresbereik** **10.0.0.0/24** in als het bereik voor het netwerk. Er is geen subnet voor dit netwerk.
5. Selecteer bij **Abonnement** het abonnement waarin u het netwerk wilt maken.
6. Kies **in Locatie** **west-Europa.** Het netwerk moet zich in dezelfde regio bevinden als de Recovery Services-kluis.
7. Laat de standaardopties van basisDDoS-beveiliging, zonder serviceeindpunt in het netwerk.
8. Selecteer **Maken**.

![Een virtueel netwerk maken](media/tutorial-prepare-azure/create-network.png)

Het duurt een paar seconden voordat het virtuele netwerk is gemaakt. Nadat het is gemaakt, ziet u het in het Azure-portaldashboard.

## <a name="useful-links"></a>Handige koppelingen

Meer informatie over:
- [Azure-netwerken](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De on-premises Hyper-V-infrastructuur voorbereiden op noodherstel naar Azure](hyper-v-prepare-on-premises-tutorial.md)
