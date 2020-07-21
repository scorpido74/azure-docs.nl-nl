---
title: Geïmplementeerde Azure-hosts met behulp van de portal
description: Implementeer Vm's op toegewezen hosts met behulp van de portal.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 444f7cd7094a688c43d9aedce67d293e3c32e273
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86508556"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Vm's implementeren op toegewezen hosts met behulp van de portal

Dit artikel begeleidt u bij het maken van een toegewezen Azure- [host](dedicated-hosts.md) voor het hosten van uw virtuele machines (vm's). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Een virtuele machine maken

1. Kies in de linkerbovenhoek van Azure Portal **Een resource maken**.
1. Selecteer op de pagina **Nieuw** onder **populair** **Windows Server 2016 Data Center**.
1. Controleer op het tabblad **basis beginselen** onder **Project Details**of het juiste abonnement is geselecteerd en selecteer vervolgens *myDedicatedHostsRG* als de **resource groep**. 
1. Typ onder **Exemplaardetails***myVM* als **Naam van de virtuele machine** en kies *VS - oost* als de **Locatie**.
1. Selecteer in **beschikbaarheids opties** **beschikbaarheids zone**selecteren, selecteer *1* in de vervolg keuzelijst.
1. Voor de grootte selecteert u **grootte wijzigen**. Kies in de lijst met beschik bare grootten een van de Esv3-reeksen, zoals **Standard E2s v3**. Mogelijk moet u het filter wissen om alle beschik bare grootten weer te geven.
1. Geef onder **Administrator-account**een gebruikers naam op, zoals *azureuser* en een wacht woord. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. Kies onder **Regels voor binnenkomende poort** de optie **​​Geselecteerde poorten toestaan** en selecteer **RDP (3389)** in de vervolgkeuzelijst.
1. Selecteer boven aan de pagina het tabblad **Geavanceerd** en selecteer in de sectie **host** *MyHostGroup* voor **hostgroep** en *myHost* voor de **host**. 
    ![Hostgroep en host selecteren](./media/dedicated-hosts-portal/advanced.png)
1. Laat de resterende standaardwaarden staan ​​en selecteer vervolgens de knop **Beoordelen en maken** aan de onderkant van de pagina.
1. Wanneer u het bericht ziet dat de validatie is voltooid, selecteert u **maken**.

## <a name="add-an-existing-vm"></a>Een bestaande VM toevoegen 

U kunt een bestaande virtuele machine toevoegen aan een specifieke host, maar de virtuele machine moet eerst worden Stop\Deallocated. Voordat u een virtuele machine naar een toegewezen host verplaatst, moet u ervoor zorgen dat de VM-configuratie wordt ondersteund:

- De VM-grootte moet zich in dezelfde grootte familie bestaan als de toegewezen host. Als uw toegewezen host bijvoorbeeld DSv3 is, kan de grootte van de virtuele machine worden Standard_D4s_v3, maar kan deze niet Standard_A4_v2. 
- De virtuele machine moet zich in dezelfde regio bevinden als de toegewezen host.
- De virtuele machine kan geen deel uitmaken van een proximity-plaatsings groep. Verwijder de virtuele machine uit de plaatsings groep voor nabijheid voordat u deze naar een specifieke host verplaatst. Zie [een virtuele machine uit een proximity-plaatsings groep verplaatsen](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group) voor meer informatie
- De virtuele machine kan zich niet in een beschikbaarheidsset bevinden.
- Als de virtuele machine zich in een beschikbaarheids zone bevindt, moet deze dezelfde beschikbaarheids zone zijn als de hostgroep. De instellingen voor de beschikbaarheids zone voor de virtuele machine en de hostgroep moeten overeenkomen.

Verplaats de virtuele machine naar een speciale host met behulp van de [Portal](https://portal.azure.com).

1. Open de pagina voor de virtuele machine.
1. Selecteer **stoppen** om de virtuele machine te stop\deallocate.
1. Selecteer **configuratie** in het menu links.
1. Selecteer een hostgroep en een host in de vervolg keuzelijst.
1. Wanneer u klaar bent, selecteert u **Opslaan** boven aan de pagina.
1. Nadat de virtuele machine is toegevoegd aan de host, selecteert u **overzicht** in het menu links.
1. Selecteer boven aan de pagina **Start** om de virtuele machine opnieuw op te starten.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie het overzicht [gespecialiseerde hosts](dedicated-hosts.md) . 

- [Hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)vindt u een voor beeld van een sjabloon, die zowel zones als fout domeinen gebruikt voor maximale tolerantie in een regio.

- U kunt ook een toegewezen host implementeren met behulp van [Azure PowerShell](dedicated-hosts-powershell.md).
