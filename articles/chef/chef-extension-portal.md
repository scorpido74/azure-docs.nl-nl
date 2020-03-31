---
title: De Chef-client installeren vanuit de Azure-portal
description: Meer informatie over het implementeren en configureren van uw Chef-client vanuit de Azure-portal
keywords: azure, chef-kok, devops, client, installeren, portal
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: 6e46133c598c44b314077f2d020852416d3d2745
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586356"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>De Chef-client installeren vanuit de Azure-portal
U de clientextensie Chef rechtstreeks toevoegen aan een Linux- of Windows-machine vanuit de Azure-portal. Dit artikel leidt u door het proces met behulp van een nieuwe Linux virtuele machine.

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.

- **Chef:** Als u geen actief Chef-account hebt, meld u dan aan voor een [gratis proefversie van Hosted Chef.](https://manage.chef.io/signup) Om de instructies in dit artikel te volgen, heb je de volgende waarden uit je Chef-account nodig:
  - organization_validation sleutel
  - Rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Installeer de Chef-extensie op een nieuwe Linux virtuele machine
In deze sectie gebruikt u eerst de Azure-portal om een Linux-machine te maken. Tijdens het proces ziet u ook hoe u de Chef-extensie op de nieuwe virtuele machine installeren.

1. Blader naar [Azure Portal](https://portal.azure.com).

1. Selecteer in het menu aan de linkerkant de optie **Virtuele machines.** Als de optie **Virtuele machines** niet aanwezig is, selecteert u **Alle services** en selecteert u Virtuele **machines**.

1. Selecteer op het tabblad **Virtuele machines** de optie **Toevoegen**.

    ![Een nieuwe virtuele machine toevoegen aan de Azure-portal](./media/chef-extension-portal/add-vm.png)

1. Selecteer **op** het tabblad Compute het gewenste besturingssysteem. Voor deze demo is **Ubuntu Server** geselecteerd.

1. Selecteer op het tabblad **Ubuntu Server** de optie **Ubuntu Server 16.04 LTS**.

    ![Geef bij het maken van een Virtuele Ubuntu-machine de versie op die u nodig hebt](./media/chef-extension-portal/ubuntu-server-version.png)

1. Selecteer op het tabblad **Ubuntu Server 16.04 LTS** de optie **Maken**.

    ![Ubuntu geeft aanvullende informatie over hun product](./media/chef-extension-portal/create-vm.png)

1. Selecteer op het tabblad **Virtuele machine maken** de optie **Basisbeginselen**.

1. Geef op het tabblad **Basisdeatoets** de volgende waarden op en selecteer **OK**.

   - **Naam** - Voer een naam in voor de nieuwe virtuele machine.
   - **VM-schijftype** - Geef **SSD** of **HDD** op voor het type opslagschijf. Zie het artikel [Een schijftype selecteren](../virtual-machines/windows/disks-types.md)voor meer informatie over schijftypen voor virtuele machines op Azure.
   - **Gebruikersnaam** - Voer een gebruikersnaam in die beheerdersbevoegdheden op de virtuele machine krijgt.
   - **Verificatietype** - **Wachtwoord selecteren**. U ook **ssh-openbare sleutel**selecteren en een SSH-waarde voor openbare sleutels leveren. Voor deze demo (en in de screenshots) wordt **Wachtwoord** geselecteerd.
   - **Wachtwoord** en **Wachtwoord bevestigen** - Voer een wachtwoord in voor de gebruiker.
   - **Meld u aan met Azure Active Directory** - Selecteer **Uitgeschakeld**.
   - **Abonnement** - Selecteer het gewenste Azure-abonnement als u er meer dan één hebt.
   - **Resourcegroep** - Voer een naam in voor uw resourcegroep.
   - **Locatie**: selecteer **VS - oost**.

     ![Tabblad Basisvoor het maken van een virtuele machine](./media/chef-extension-portal/add-vm-basics.png)

1. Selecteer **op** het tabblad Een grootte kiezen de grootte voor de virtuele machine en selecteer **Selecteer .**

1. Op het tabblad **Instellingen** worden de meeste waarden voor u ingevuld op basis van de waarden die u in de vorige tabbladen hebt geselecteerd. Selecteer **Extensies**.

     ![Extensies worden toegevoegd aan virtuele machines via het tabblad Instellingen](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Selecteer op het tabblad **Extensies** de optie **Extensie toevoegen**.

     ![Selecteer Extensie toevoegen om een extensie toe te voegen aan een virtuele machine](./media/chef-extension-portal/add-vm-add-extension.png)

1. Selecteer op het tabblad **Nieuwe bron** de optie Linux Chef **Extension (1.2.3)**.

     ![Chef heeft extensies voor Linux en Windows virtuele machines](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Selecteer op het tabblad **Linux-chef-extensie** de optie **Maken**.

1. Geef op het tabblad **Extensie installeren** de volgende waarden op en selecteer **OK**.

    - **Chef Server URL** - Voer bijvoorbeeld de URL van *https://api.chef.io/organization/mycompany*chef-server in die de naam van de organisatie bevat.
    - **Chef-kok Node naam** - Voer de Chef Node naam.
    - **Lijst uitvoeren** - Voer de lijst met chef-koks uit die aan de machine is toegevoegd. Deze waarde kan leeg blijven.
    - **Clientnaam validatie** - Voer de clientnaam van chef-kokvalidatie in. bijvoorbeeld . `tarcher-validator`
    - **Validatiesleutel** : selecteer een bestand met de validatiesleutel die wordt gebruikt bij het ophangen van uw machines.
    - **Clientconfiguratiebestand** - Selecteer een configuratiebestand voor chef-client. Deze waarde kan leeg blijven.
    - **Chef Client versie** - Voer de versie van de chef-kok klant te installeren. Deze waarde kan leeg worden gelaten, die de nieuwste versie installeert.
    - **SSL-verificatiemodus** - Selecteer **Geen** of **Peer**. *Geen* werd geselecteerd voor de demo.
    - **Chef Milieu** - Voer de chef-kok milieu dit knooppunt moet een lid van. Deze waarde kan leeg blijven.
    - **Versleutelde data bag geheim** - Selecteer een bestand met het geheim voor de versleutelde data bag deze machine nodig heeft om toegang te krijgen. Deze waarde kan leeg blijven.
    - **Chef Server SSL-certificaat** - Selecteer het SSL-certificaat dat is toegewezen aan uw Chef Server. Deze waarde kan leeg blijven.

      ![Het installeren van de Chef Server op een Linux virtuele machine](./media/chef-extension-portal/install-extension.png)

1. Wanneer het tabblad **Extensies** wordt weergegeven, selecteert u **OK**.

1. Wanneer het tabblad **Instellingen** wordt weergegeven, selecteert u **OK**.

1. Wanneer het tabblad **Maken** wordt weergegeven, ziet u een overzicht van de opties die u hebt geselecteerd en ingevoerd. Controleer de gegevens en de **gebruiksvoorwaarden**en selecteer **Maken**.

Wanneer het proces van het maken en implementeren van de virtuele machine met de Chef Extension is voltooid, geeft een melding het succes of falen van de bewerking aan. Bovendien wordt de resourcepagina voor de nieuwe virtuele machine automatisch geopend in de Azure-portal zodra deze is gemaakt.

![Het installeren van de Chef Server op een Linux virtuele machine](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Een virtuele Windows-machine maken op Azure met Chef](chef-automation.md)