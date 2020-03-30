---
title: Snelstart - De ansible-oplossingssjabloon voor Azure implementeren in CentOS
description: In deze quickstart leert u hoe u de ansible-oplossingssjabloon implementeert op een virtuele CentOS-machine die op Azure wordt gehost, samen met hulpprogramma's die zijn geconfigureerd om met Azure te werken.
keywords: ansible, azure, devops, solution template, virtual machine, managed identities for azure resources, centos, red hat
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: cc3a6c1f0ef36b51b62e6aa58f317aee13149589
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77193783"
---
# <a name="quickstart-deploy-the-ansible-solution-template-for-azure-to-centos"></a>Snelstart: de ansible-oplossingssjabloon voor Azure implementeren in CentOS

De Ansible-oplossingssjabloon voor Azure is ontworpen voor het configureren van een Ansible-exemplaar op een CentOS virtuele machine, samen met Ansible en een reeks hulpprogramma's die is geconfigureerd om te werken met Azure. Tot de hulpmiddelen behoren:

- **Ansible-modules voor Azure**: de [Ansible-modules voor Azure](./ansible-matrix.md) zijn een reeks modules waarmee u uw infrastructuur in Azure kunt maken en beheren. De meest recente versie van deze modules wordt standaard geïmplementeerd. Tijdens het implementeren van de oplossingssjabloon kunt u echter ook een versienummer opgeven dat past bij uw omgeving.
- **Azure Command-Line Interface (CLI) 2.0**: de [Azure CLI 2.0](/cli/azure/?view=azure-cli-latest) is een platformoverschrijdend opdrachtregelervaring voor het beheren van Azure-resources. 
- **Beheerde identiteiten voor Azure-resources**: de [beheerde identiteiten voor Azure-resources](/azure/active-directory/managed-identities-azure-resources/overview) maken het mogelijk om referenties voor cloudtoepassingen veilig te houden.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="deploy-the-ansible-solution-template"></a>De sjabloon Ansible-oplossing implementeren

1. Blader naar de [Ansible-oplossingssjabloon in Azure Marketplace](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview).

1. Selecteer **Nu ophalen**.

1. Er verschijnt een venster met de gebruiksrechtovereenkomst, het privacybeleid en de gebruiksvoorwaarden voor Azure Marketplace. Selecteer **Doorgaan**.

1. De Azure-portal wordt weergegeven. Hierin ziet u de Ansible-pagina waarop de oplossingssjabloon wordt beschreven. Selecteer **Maken**.

1. Op de pagina **Ansible maken** ziet u meerdere tabbladen. Op het tabblad **Basisprincipes** voert u de vereiste gegevens in:

   - **Naam**: geef de naam op van uw Ansible-exemplaar. Voor deze demo wordt de naam `ansiblehost` gebruikt.
   - **Gebruikersnaam**: geef de gebruikersnaam op waarmee toegang kan worden verkregen tot het Ansible-exemplaar. Voor deze demo wordt de naam `ansibleuser` gebruikt.
   - **Verificatietype:** selecteer **Wachtwoord** of **Openbare SSH-sleutel**. Voor deze demo wordt **Openbare SSH-sleutel** geselecteerd.
   - **Wachtwoord** en **Wachtwoord bevestigen**: als u **Wachtwoord** selecteert als **verificatietype**, voert u uw wachtwoord in.
   - **Openbare SSH-sleutel**: als u **Openbare SSH-sleutel** selecteert als **verificatietype**, voert u uw openbare RSA-sleutel in in de indeling met één regel, beginnend met `ssh-rsa`.
   - **Abonnement**: selecteer uw Azure-abonnement in de vervolgkeuzelijst.
   - **Resourcegroep**: selecteer een bestaande resourcegroep in de vervolgkeuzelijst of selecteer **Nieuwe maken** en geef een naam op voor de nieuwe resourcegroep. Voor deze demo wordt een nieuwe resourcegroep met de naam `ansiblerg` gebruikt.
   - **Locatie**: selecteer de locatie in de vervolgkeuzelijst die geschikt is voor uw scenario.

     ![Azure-portaltabblad voor de Ansible-basisinstellingen](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. Selecteer **OK**.

1. Op het tabblad **Aanvullende instellingen** voert u de vereiste gegevens in:

   - **Grootte**: in de Azure-portal wordt standaard een standaardgrootte gebruikt. Als u een andere grootte wilt opgeven die geschikt is voor uw specifieke scenario, selecteert u de pijl om verschillende grootten weer te geven.
   - **Type VM-schijf**: selecteer **SSD** (Premium Solid-State Drive) of **HDD** (Hard Disk Drive). Voor deze demo wordt **SSD** geselecteerd omwille van de prestatievoordelen. Voor meer informatie over deze typen schijfopslag ziet u de volgende artikelen:
       - [Krachtige Premium Storage en beheerde schijven voor VM's](/azure/virtual-machines/windows/premium-storage)
       - [Standard SSD Managed Disks for Azure Virtual machine workloads](/azure/virtual-machines/windows/disks-standard-ssd) (Standaard beheerde SSD-schijven voor workloads van Azure-VM's)
   - **Openbaar IP-adres**: configureer deze instelling als u van buiten de virtuele machine met de virtuele machine wilt communiceren. De standaardwaarde is een nieuw openbaar IP-adres met de naam `ansible-pip`. Als u een ander IP-adres wilt opgeven, selecteert u de pijl om de kenmerken op te geven, zoals de naam, SKU en toewijzing van dat IP-adres. 
   - **Domeinnaamlabel**: geef de openbare-domeinnaam op van de virtuele machine. De naam moet uniek zijn en voldoen aan de vereisten voor naamgeving. Zie [Naamconventies voor Azure-resources](/azure/architecture/best-practices/resource-naming) voor meer informatie over het opgeven van een naam voor de virtuele machine.
   - **Ansible-versie**: geef een versienummer of de waarde `latest` op om de nieuwste versie te implementeren. Selecteer het informatiepictogram naast de **Ansible-versie** voor meer informatie over de beschikbare versies.

     ![Azure-portaltabblad voor de aanvullende Ansible-instellingen](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. Selecteer **OK**.

1. Op het tabblad **Ansible-integratie-instellingen** geeft u het verificatietype op. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie over het beveiligen van Azure-resources.

    ![Azure-portaltabblad voor de Ansible-integratie-instellingen](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. Selecteer **OK**.

1. De **samenvatting**spagina wordt weergegeven. Hier ziet u het validatieproces en hier worden de opgegeven criteria voor de Ansible-implementatie vermeld. Met een koppeling aan de onderkant van het tabblad kunt u **de sjabloon en parameters downloaden** voor gebruik met ondersteunde Azure-talen en -platforms. 

     ![Azure-portaltabblad met het tabblad Ansible-overzicht](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. Selecteer **OK**.

1. Wanneer het tabblad **Maken** wordt weergegeven, selecteert u **OK** om Ansible te implementeren.

1. Selecteer het pictogram **Meldingen** boven aan de portalpagina om de voortgang van de Ansible-implementatie bij te houden. Nadat de implementatie is voltooid, selecteert u **Ga naar resourcegroep**. 

     ![Azure-portaltabblad met het tabblad Ansible-overzicht](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-complete.png)

1. Op de pagina van de resourcegroep haalt u het IP-adres van uw Ansible-host op en meldt u zich aan voor het beheren van uw Azure-resources met behulp van Ansible.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Snelstart: een virtuele Linux-machine configureren in Azure met Ansible](./ansible-create-vm.md)