---
title: Een virtuele SQL Server-machine voor Windows maken in Azure Portal | Microsoft Docs
description: Deze zelfstudie laat zien hoe u een virtuele SQL Server 2017-machine voor Windows in Azure Portal kunt maken.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 801a6fc0602882d1af49c06bafcfd51942e6da2e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75965658"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Snelstartgids: Een virtuele SQL Server 2017-machine voor Windows maken in Azure Portal

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

In deze quickstart gaat u een virtuele SQL Server-machine maken in Azure Portal.


  > [!TIP]
  > - Deze snelstartgids biedt een pad voor het snel inrichten van en verbinding maken met een SQL-VM. Bekijk [Provisioning guide for Windows SQL Server VMs in the Azure portal](virtual-machines-windows-portal-sql-server-provision.md) (Inrichtingshandleiding voor Windows SQL Server-VM's in Azure Portal) voor andere inrichtingskeuzes voor SQL-VM's.
  > - Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](virtual-machines-windows-sql-server-iaas-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a>Een Azure-abonnement aanschaffen

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="select-a-sql-server-vm-image"></a><a id="select"></a> Een installatiekopie selecteren voor uw virtuele SQL Server-machine

1. Meld u met uw account aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Azure SQL** in het linkermenu van de Azure-portal. Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure *SQL* in het zoekvak.
1. Selecteer **+Toevoegen** om de **optiepagina SQL-implementatie selecteren** te openen. U aanvullende informatie weergeven door **Details weergeven** op de tegel **SQL virtuele machines** te selecteren.
1. Selecteer de **afbeelding Gratis SQL Server-licentie: SQL Server 2017-ontwikkelaar op Windows Server 2016** in de vervolgkeuzelijst.

   ![Nieuw zoekvenster](./media/quickstart-sql-vm-create-portal/select-sql-2017-vm-image.png)

1. Selecteer **Maken**.

   ![Nieuw zoekvenster](./media/quickstart-sql-vm-create-portal/create-sql-2017-vm-image.png)

## <a name="provide-basic-details"></a><a id="configure"></a> Algemene gegevens opgeven

Geef op het tabblad **Basisbeginselen** de volgende informatie op:

1. Selecteer in de sectie **Projectdetails** uw Azure-abonnement en selecteer **Nieuw maken** om een nieuwe brongroep te maken. Typ _SQLVM-RG_ voor de naam.

   ![Abonnement](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. Onder **Bijzonderheden van de instantie**:
    1. Typ _SQLVM_ voor de **naam van de virtuele machine**. 
    1. Kies een locatie voor uw **regio.** 
    1. Voor deze snelle start, laat **beschikbaarheid opties** ingesteld op Geen _infrastructuur redundantie vereist_. Zie [Beschikbaarheid](../../windows/availability.md)voor meer informatie over beschikbaarheidsopties. 
    1. Selecteer in de lijst **Met afbeeldingen** de optie Gratis _SQL Server-licentie: SQL Server 2017-ontwikkelaar op Windows Server 2016_. 
    1. Kies voor **Wijziging van de grootte** voor de **grootte** van de virtuele machine en selecteer het **A2 Basic-aanbod.** Zorg ervoor dat u uw resources opschonen zodra u klaar bent met hen om onverwachte kosten te voorkomen. 

   ![Instantiedetails](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. Geef **onder Administrator-account**een gebruikersnaam op, zoals _azureuser_ en een wachtwoord. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Administrator-account](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. Kies onder **Inkomende poortregels**De optie **Geselecteerde poorten toestaan** en selecteer vervolgens **RDP (3389)** in de vervolgkeuzelijst. 

   ![Regels voor binnenkomende poort](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>SQL Server-instellingen

Configureer op het tabblad **SQL Server-instellingen** de volgende opties:

1. Selecteer **onder Security & Networking**de optie Openbaar _(internet)_ voor **SQL-connectiviteit** en wijzig de poort om `1401` te voorkomen dat een bekend poortnummer in het openbare scenario wordt gebruikt. 
1. Selecteer **onder SQL-verificatie**de optie **Inschakelen**. De SQL-aanmeldingsgegevens zijn ingesteld op dezelfde combinatie van gebruikersnaam en wachtwoord als u voor de virtuele machine hebt geconfigureerd. Gebruik de standaardinstelling voor [**Azure Key Vault-integratie**](virtual-machines-windows-ps-sql-keyvault.md). **Opslagconfiguratie** is niet beschikbaar voor de basis-SQL Server VM-afbeelding, maar u meer informatie vinden over beschikbare opties voor andere afbeeldingen bij [opslagconfiguratie.](virtual-machines-windows-sql-server-storage-configuration.md#new-vms)  

   ![SQL-serverbeveiligingsinstellingen](media/quickstart-sql-vm-create-portal/sql-server-settings.png)


1. Wijzig indien nodig andere instellingen en selecteer **Controleren + maken.** 

   ![Controleren + maken](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>De virtuele SQL Server-machine maken

Controleer op het tabblad **Controleren + maken** de samenvatting en selecteer **Maken** om SQL Server, resourcegroep en resources te maken die voor deze vm zijn opgegeven.

U kunt de implementatie bewaken vanuit Azure Portal. Met de knop **Meldingen** boven aan het scherm kunt u de algemene status van de implementatie weergeven. Implementatie kan enkele minuten duren. 

## <a name="connect-to-sql-server"></a>Verbinding maken met SQL Server

1. Zoek in de portal het **openbare IP-adres** van uw SQL Server VM in de sectie **Overzicht** van de eigenschappen van uw virtuele machine.

1. Open SQL Server Management Studio [(SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)op een andere computer die met internet is verbonden.


1. Bewerk in het dialoogvenster **Verbinding maken met server** of **Verbinding maken met Database-engine** de waarde voor **Servernaam**. Typ het openbare IP-adres van de virtuele machine. Typ nu een komma en vervolgens **1401**. Dit is het aangepaste poortnummer dat u hebt opgegeven tijdens de configuratie van de nieuwe virtuele machine. Bijvoorbeeld `11.22.33.444,1401`.

1. Kies in het vak **Verificatie****SQL Server-verificatie**.

1. Typ in het vak **Aanmelden** een geldige SQL-aanmeldingsnaam.

1. Typ in het vak **Wachtwoord** het wachtwoord van de aanmelding.

1. Selecteer **Verbinden**.

    ![ssms verbinden](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a name="log-in-to-the-vm-remotely"></a><a id="remotedesktop"></a> Vanaf een externe locatie aanmelden bij de virtuele machine

Doorloop de volgende stappen om via Extern bureaublad verbinding te maken met de virtuele SQL Server-machine:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Nadat u verbinding hebt gemaakt met de virtuele SQL Server-machine, kunt u SQL Server Management Studio starten en verbinding maken met Windows-verificatie met behulp van de lokale beheerdersreferenties. Als u SQL Server-verificatie inschakelt, kunt u ook verbinding maken met SQL-verificatie. Dit kan met behulp van de SQL-aanmeldingsnaam en het wachtwoord die u hebt geconfigureerd tijdens het inrichten.

Met toegang tot de machine kunt u rechtstreeks de instellingen voor de machine en de SQL Server wijzigen op basis van uw vereisten. U kunt bijvoorbeeld de firewallinstellingen configureren of de SQL Server-configuratie-instellingen wijzigen.

## <a name="clean-up-resources"></a>Resources opschonen

Als het niet nodig is dat de virtuele SQL-machine continu wordt uitgevoerd, kunt u overbodige kosten voorkomen door de virtuele machine te stoppen wanneer deze niet in gebruik is. U kunt ook alle resources die aan de virtuele machine zijn gekoppeld, definitief verwijderen. Dit doet u door in de portal de gekoppelde resourcegroep te verwijderen. Wees voorzichtig met het gebruik van deze opdracht, want hiermee verwijdert u ook de virtuele machine zelf definitief. Raadpleeg [Manage Azure resources through portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md) (Azure-resources via de portal beheren) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een virtuele SQL Server 2017-machine gemaakt in de Azure-portal. Raadpleeg het volgende artikel voor meer informatie over hoe u uw gegevens naar de nieuwe SQL-server migreert.

> [!div class="nextstepaction"]
> [Een database migreren naar een SQL-VM](virtual-machines-windows-migrate-sql.md)
