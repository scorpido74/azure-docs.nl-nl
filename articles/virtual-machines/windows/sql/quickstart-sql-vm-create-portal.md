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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
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

## <a name="get-an-azure-subscription"></a><a id="subscription"></a>Een Azure-abonnement ontvangen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="select-a-sql-server-vm-image"></a><a id="select"></a> Een installatiekopie selecteren voor uw virtuele SQL Server-machine

1. Meld u met uw account aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Azure SQL** in het linkermenu van de Azure Portal. Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens *Azure SQL* in het zoekvak.
1. Selecteer **+ toevoegen** om de **optie pagina SQL-implementatie selecteren** te openen. U kunt aanvullende informatie weer geven door **Details weer geven** te selecteren op de tegel **virtuele SQL-machines** .
1. Selecteer de **gratis SQL Server licentie: SQL Server 2017 Developer in Windows Server 2016** installatie kopie in de vervolg keuzelijst.

   ![Nieuw zoekvenster](./media/quickstart-sql-vm-create-portal/select-sql-2017-vm-image.png)

1. Selecteer **Maken**.

   ![Nieuw zoekvenster](./media/quickstart-sql-vm-create-portal/create-sql-2017-vm-image.png)

## <a name="provide-basic-details"></a><a id="configure"></a> Algemene gegevens opgeven

Geef op het tabblad **basis beginselen** de volgende informatie op:

1. Selecteer uw Azure-abonnement in de sectie **Project Details** en selecteer vervolgens **nieuwe maken** om een nieuwe resource groep te maken. Typ _SQLVM-RG_ voor de naam.

   ![Abonnement](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. Onder **Details van exemplaar**:
    1. Typ _SQLVM_ voor de **naam van de virtuele machine**. 
    1. Kies een locatie voor uw **regio**. 
    1. Voor het doel van deze Snelstartgids moet u de **beschikbaarheids opties** instellen op _geen infra structuur-redundantie vereist_. Zie [Beschik baarheid](../../windows/availability.md)voor meer informatie over beschikbaarheids opties. 
    1. Selecteer in de lijst met **installatie kopieën** _gratis SQL Server licentie: SQL Server 2017 Developer op Windows Server 2016_. 
    1. Kies voor het wijzigen van de **grootte** voor de **grootte** van de virtuele machine en selecteer de **a2 Basic** -aanbieding. Zorg ervoor dat u uw resources opschoont nadat u klaar bent met deze om te voor komen dat er onverwachte kosten in rekening worden gebracht. 

   ![Exemplaar Details](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. Geef onder **Administrator-account**een gebruikers naam op, zoals _azureuser_ en een wacht woord. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Administrator-account](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. Onder **Binnenkomende poort regels**kiest u **geselecteerde poorten toestaan** en selecteert u vervolgens **RDP (3389)** in de vervolg keuzelijst. 

   ![Regels voor binnenkomende poort](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>SQL Server-instellingen

Configureer op het tabblad **SQL Server instellingen** de volgende opties:

1. Onder **beveiliging & netwerken**selecteert u _openbaar (Internet_) voor **SQL-connectiviteit** en wijzigt u de `1401` poort om te voor komen dat u een bekend poort nummer gebruikt in het open bare scenario. 
1. Selecteer **inschakelen**onder **SQL-verificatie**. De SQL-aanmeldingsgegevens zijn ingesteld op dezelfde combinatie van gebruikersnaam en wachtwoord als u voor de virtuele machine hebt geconfigureerd. Gebruik de standaard instelling voor de [**integratie van Azure Key Vault**](virtual-machines-windows-ps-sql-keyvault.md). De **opslag configuratie** is niet beschikbaar voor de basis installatie kopie van SQL Server VM, maar u kunt meer informatie vinden over de beschik bare opties voor andere installatie kopieën bij de [opslag configuratie](virtual-machines-windows-sql-server-storage-configuration.md#new-vms).  

   ![Beveiligings instellingen van SQL Server](media/quickstart-sql-vm-create-portal/sql-server-settings.png)


1. Wijzig indien nodig de andere instellingen en selecteer vervolgens **controleren + maken**. 

   ![Controleren en maken](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>De virtuele SQL Server-machine maken

Bekijk op het tabblad **controleren en maken** de samen vatting en selecteer **maken** om SQL Server, resource groep en resources te maken die zijn opgegeven voor deze virtuele machine.

U kunt de implementatie bewaken vanuit Azure Portal. Met de knop **Meldingen** boven aan het scherm kunt u de algemene status van de implementatie weergeven. De implementatie kan enkele minuten duren. 

## <a name="connect-to-sql-server"></a>Verbinding maken met SQL Server

1. Zoek in de portal naar het **open bare IP-adres** van uw SQL Server-VM in het gedeelte **overzicht** van de eigenschappen van de virtuele machine.

1. Open [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)op een andere computer die is verbonden met internet.


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

In deze Quick Start hebt u een virtuele machine van SQL Server 2017 gemaakt in de Azure Portal. Raadpleeg het volgende artikel voor meer informatie over hoe u uw gegevens naar de nieuwe SQL-server migreert.

> [!div class="nextstepaction"]
> [Een database migreren naar een SQL-VM](virtual-machines-windows-migrate-sql.md)
