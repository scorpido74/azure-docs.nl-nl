---
title: Installeer de chef-client vanuit de Azure Portal
description: Meer informatie over het implementeren en configureren van uw chef-client vanuit de Azure Portal
keywords: Azure, chef, devops, client, installeren, portal
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: 6e46133c598c44b314077f2d020852416d3d2745
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586356"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Installeer de chef-client vanuit de Azure Portal
U kunt de chef-client extensie rechtstreeks toevoegen aan een Linux-of Windows-machine via de Azure Portal. Dit artikel begeleidt u bij het proces met behulp van een nieuwe virtuele Linux-machine.

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.

- **Chef**: als u geen actief chef-account hebt, meldt u zich aan voor een [gratis proef versie van gehoste chef](https://manage.chef.io/signup). Als u de instructies in dit artikel wilt volgen, hebt u de volgende waarden van uw chef-account nodig:
  - organization_validation sleutel
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>De chef-extensie installeren op een nieuwe virtuele Linux-machine
In deze sectie gebruikt u eerst de Azure Portal om een Linux-machine te maken. Tijdens het proces ziet u ook hoe u de chef-extensie installeert op de nieuwe virtuele machine.

1. Blader naar de [Azure-portal](https://portal.azure.com).

1. Selecteer de optie **virtuele machines** in het menu aan de linkerkant. Als de optie **virtuele machines** niet aanwezig is, selecteert u **alle services** en selecteert u vervolgens **virtuele machines**.

1. Selecteer **toevoegen**op het tabblad **virtuele machines** .

    ![Een nieuwe virtuele machine toevoegen in de Azure Portal](./media/chef-extension-portal/add-vm.png)

1. Selecteer op het tabblad **Compute** het gewenste besturings systeem. Voor deze demo is de **Ubuntu-Server** geselecteerd.

1. Op het tabblad **Ubuntu Server** selecteert u **Ubuntu Server 16,04 LTS**.

    ![Wanneer u een virtuele Ubuntu-machine maakt, geeft u de versie op die u nodig hebt](./media/chef-extension-portal/ubuntu-server-version.png)

1. Selecteer **maken**op het tabblad **Ubuntu Server 16,04 LTS** .

    ![Ubuntu biedt aanvullende informatie over het product](./media/chef-extension-portal/create-vm.png)

1. Selecteer op het tabblad **virtuele machine maken** de optie **basis beginselen**.

1. Geef op het tabblad **basis beginselen** de volgende waarden op en selecteer vervolgens **OK**.

   - **Naam** : Voer een naam in voor de nieuwe virtuele machine.
   - **VM-schijf type** : Geef **SSD** of **HDD** voor het type opslag schijf op. Zie het artikel [een schijf type selecteren](../virtual-machines/windows/disks-types.md)voor meer informatie over schijf typen voor virtuele machines op Azure.
   - **Gebruikers naam** : Voer een gebruikers naam in die Administrator bevoegdheden voor de virtuele machine heeft.
   - **Verificatie type** : Selecteer **wacht woord**. U kunt ook een **open bare SSH-sleutel**selecteren en een open bare SSH-sleutel waarde opgeven. Voor doel einden van deze demo (en in de scherm afbeeldingen) is **wacht woord** geselecteerd.
   - **Wacht** woord en wacht **woord bevestigen** : Voer een wacht woord in voor de gebruiker.
   - **Meld u aan met Azure Active Directory** -Select **disabled**.
   - **Abonnement** : Selecteer het gewenste Azure-abonnement als u meer dan één hebt.
   - **Resource groep** : Voer een naam in voor de resource groep.
   - **Locatie**: selecteer **VS - oost**.

     ![Tabblad basis voor het maken van een virtuele machine](./media/chef-extension-portal/add-vm-basics.png)

1. Selecteer op het tabblad **een grootte kiezen** de grootte voor de virtuele machine en selecteer vervolgens **selecteren**.

1. Op het tabblad **instellingen** wordt het meren deel van de waarden voor u ingevuld op basis van de waarden die u hebt geselecteerd in de vorige tabbladen. Selecteer **Extensies**.

     ![Extensies worden toegevoegd aan virtuele machines via het tabblad instellingen](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Selecteer op het tabblad **extensies** de optie **uitbrei ding toevoegen**.

     ![Selecteer extensie toevoegen om een extensie toe te voegen aan een virtuele machine](./media/chef-extension-portal/add-vm-add-extension.png)

1. Selecteer op het tabblad **nieuwe resource** **Linux chef extension (1.2.3)** .

     ![Chef heeft uitbrei dingen voor virtuele Linux-en Windows-machines](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Op het tabblad **Linux chef extension** selecteert u **maken**.

1. Geef op het tabblad **installatie-uitbrei ding** de volgende waarden op en selecteer vervolgens **OK**.

    - **URL** van de chef-server: Voer de chef-server-URL in die de naam van de organisatie bevat, bijvoorbeeld *https://api.chef.io/organization/mycompany* .
    - **Chef-knooppunt naam** : Voer de naam van het chef-knoop punt in.
    - **Lijst uitvoeren** : Voer de chef uit die is toegevoegd aan de machine. Deze waarde mag leeg blijven.
    - **Validatie client naam** : Voer de naam van de chef-validatie client in. Bijvoorbeeld `tarcher-validator`.
    - **Validatie sleutel** : Selecteer een bestand met de validatie sleutel die wordt gebruikt bij het Boots trappen van uw machines.
    - **Client configuratie bestand** : Selecteer een configuratie bestand voor chef-client. Deze waarde mag leeg blijven.
    - **Chef-client versie** : Geef de versie op van de chef-client die u wilt installeren. Deze waarde mag leeg blijven, waardoor de nieuwste versie wordt geïnstalleerd.
    - **SSL-verificatie modus** : Selecteer **geen** of **peer**. *Geen* is geselecteerd voor de demo.
    - **Chef-omgeving** : Voer de chef-omgeving in waarvan dit knoop punt lid moet zijn. Deze waarde mag leeg blijven.
    - **Versleutelde gegevens verzameling Secret** : Selecteer een bestand met het geheim voor de versleutelde gegevens verzameling waartoe deze computer toegang moet hebben. Deze waarde mag leeg blijven.
    - **SSL-certificaat voor chef-server** : Selecteer het SSL-certificaat dat aan uw chef-server is toegewezen. Deze waarde mag leeg blijven.

      ![De chef-server installeren op een virtuele Linux-machine](./media/chef-extension-portal/install-extension.png)

1. Wanneer het tabblad **extensies** wordt weer gegeven, selecteert u **OK**.

1. Wanneer het tabblad **instellingen** wordt weer gegeven, selecteert u **OK**.

1. Wanneer het tabblad **maken** wordt weer gegeven, ziet u een samen vatting van de opties die u hebt geselecteerd en ingevoerd. Controleer de gegevens, evenals de **Gebruiksvoorwaarden**, en selecteer **maken**.

Wanneer het proces van het maken en implementeren van de virtuele machine met de chef-extensie is voltooid, wordt in een melding aangegeven dat de bewerking is geslaagd of mislukt. Daarnaast wordt de resource pagina voor de nieuwe virtuele machine automatisch geopend in het Azure Portal zodra deze is gemaakt.

![De chef-server installeren op een virtuele Linux-machine](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Een virtuele Windows-machine maken in azure met behulp van chef](chef-automation.md)