---
title: Azure-dedicated hosts implementeren met behulp van de Azure-portal
description: Implementeer VM's voor speciale hosts via de Azure-portal.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 195a19ef881f235ad8e42f23b53da9e667ef88d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086749"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>VM's implementeren voor speciale hosts via de portal

In dit artikel u een [azure-dedicated host](dedicated-hosts.md) maken om uw virtuele machines (VM's) te hosten. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Een virtuele machine maken

1. Kies in de linkerbovenhoek van de Azure-portal **Een resource maken**.
1. Zoek via het zoekvak boven de lijst met Azure Marketplace-resources naar **Ubuntu Server 16.04 LTS by Canonical**, selecteer deze en kies **Maken**.
1. Controleer op het tabblad **Basisbeginselen** onder **Projectdetails**of het juiste abonnement is geselecteerd en selecteer *vervolgens myDedicatedHostsRG* als **de groep Resource**. 
1. Typ onder **Exemplaardetails***myVM* als **Naam van de virtuele machine** en kies *VS - oost* als de **Locatie**.
1. Selecteer **in Beschikbaarheidsopties** **beschikbaarheidszone**1 *in* de vervolgkeuzelijst.
1. Selecteer **Grootte wijzigen**voor de grootte . Kies in de lijst met beschikbare maten een uit de Esv3-serie, zoals **Standard E2s v3.** Het kan nodig zijn om het filter te wissen om alle beschikbare maten te zien.
1. Selecteer onder **Administrator-account****Openbare SSH-sleutel**, typ uw gebruikersnaam en plak uw openbare sleutel in het tekstvak. Verwijder eventuele voorloop- en volgspaties van uw openbare sleutel.

    ![Administrator-account](./media/quick-create-portal/administrator-account.png)

1. Kies onder **Inkomende poortregels** > **Openbare binnenkomende poorten**de optie Geselecteerde poorten **toestaan** en selecteer **vervolgens SSH (22)** in de vervolgkeuzelijst. 
1. Selecteer boven aan de pagina het tabblad **Geavanceerd** en selecteer in de sectie **Host** *myHostGroup* voor **hostgroep** en *myHost* voor de **hostgroep**. 
    ![Hostgroep en host selecteren](./media/dedicated-hosts-portal/advanced.png)
1. Laat de resterende standaardwaarden staan ​​en selecteer vervolgens de knop **Beoordelen en maken** aan de onderkant van de pagina.
1. Wanneer u het bericht ziet dat de validatie is verstreken, selecteert u **Maken**.

Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd.

## <a name="add-an-existing-vm"></a>Een bestaande VM toevoegen 

U een afsluitende VM toevoegen aan een speciale host, maar de VM moet eerst Stop\Deallocated zijn. Voordat u een vm naar een speciale host verplaatst, moet u ervoor zorgen dat de VM-configuratie wordt ondersteund:

- De VM-grootte moet in dezelfde grootte-familie zijn als de toegewezen host. Als uw dedicated host bijvoorbeeld DSv3 is, kan de VM-grootte worden Standard_D4s_v3, maar kan het geen Standard_A4_v2 zijn. 
- De VM moet zich in dezelfde regio bevinden als de speciale host.
- De VM kan geen deel uitmaken van een groep voor plaatsing in de nabijheid. Verwijder de VM uit de plaatsingsgroep in de nabijheid voordat u deze verplaatst naar een speciale host. Zie [Een virtuele machine uit een plaatsingsgroep voor de nabijheid verplaatsen uit een groep nabijheidsplaatsingen](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- De VM kan niet in een beschikbaarheidsset staan.
- Als de VM zich in een beschikbaarheidszone bevindt, moet deze dezelfde beschikbaarheidszone hebben als de hostgroep. De instellingen van de beschikbaarheidszone voor de VM en de hostgroep moeten overeenkomen.

Verplaats de VM naar een speciale host via de [portal.](https://portal.azure.com)

1. Open de pagina voor de VM.
1. Selecteer **Stoppen** om te stoppen\de toewijzing van de VM.
1. Selecteer **Configuratie** in het linkermenu.
1. Selecteer een hostgroep en een host in de vervolgkeuzemenu's.
1. Wanneer u klaar bent, selecteert **u Opslaan** boven aan de pagina.
1. Nadat de vm aan de host is toegevoegd, selecteert u **Overzicht** in het linkermenu.
1. Selecteer boven aan de pagina **Start** om de VM opnieuw te starten.

## <a name="next-steps"></a>Volgende stappen

- Zie het overzicht [van speciale hosts](dedicated-hosts.md) voor meer informatie.

- Er is voorbeeld sjabloon, [hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)gevonden, dat zowel zones en fout domeinen gebruikt voor maximale tolerantie in een regio.

- U ook een dedicated host implementeren met de [Azure CLI.](dedicated-hosts-cli.md)



