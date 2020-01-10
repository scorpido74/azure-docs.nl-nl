---
title: Implementeer uw eigen Azure-hosts met behulp van de Azure Portal
description: Implementeer Vm's op toegewezen hosts met behulp van de Azure Portal.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c8e2ac929b3285b0ba122928485b423e34dc8f4f
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75835135"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Vm's implementeren op toegewezen hosts met behulp van de portal

Dit artikel begeleidt u bij het maken van een toegewezen Azure- [host](dedicated-hosts.md) voor het hosten van uw virtuele machines (vm's). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Een VM maken

1. Kies in de linkerbovenhoek van de Azure-portal **Een resource maken**.
1. Zoek via het zoekvak boven de lijst met Azure Marketplace-resources naar **Ubuntu Server 16.04 LTS by Canonical**, selecteer deze en kies **Maken**.
1. Controleer op het tabblad **basis beginselen** onder **Project Details**of het juiste abonnement is geselecteerd en selecteer vervolgens *myDedicatedHostsRG* als de **resource groep**. 
1. Typ onder **Exemplaardetails***myVM* als **Naam van de virtuele machine** en kies *US - oost* als de **Locatie**.
1. Selecteer in **beschikbaarheids opties** **beschikbaarheids zone**selecteren, selecteer *1* in de vervolg keuzelijst.
1. Voor de grootte selecteert u **grootte wijzigen**. Kies in de lijst met beschik bare grootten een van de Esv3-reeksen, zoals **Standard E2s v3**. Mogelijk moet u het filter wissen om alle beschik bare grootten weer te geven.
1. Selecteer onder **Administrator-account** **Openbare SSH-sleutel**, typ uw gebruikersnaam en plak uw openbare sleutel in het tekstvak. Verwijder eventuele voorloop- en volgspaties van uw openbare sleutel.

    ![Administrator-account](./media/quick-create-portal/administrator-account.png)

1. Onder **Binnenkomende poort regels** > **open bare binnenkomende poorten**kiest u **geselecteerde poorten toestaan** en selecteert u **SSH (22)** in de vervolg keuzelijst. 
1. Selecteer boven aan de pagina het tabblad **Geavanceerd** en selecteer in de sectie **host** *MyHostGroup* voor **hostgroep** en *myHost* voor de **host**. 
    hostgroep en host ![selecteren](./media/dedicated-hosts-portal/advanced.png)
1. Laat de resterende standaardwaarden staan ​​en selecteer vervolgens de knop **Beoordelen en maken** aan de onderkant van de pagina.
1. Wanneer u het bericht ziet dat de validatie is voltooid, selecteert u **maken**.

Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie het overzicht [gespecialiseerde hosts](dedicated-hosts.md) .

- [Hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)vindt u een voor beeld van een sjabloon, die zowel zones als fout domeinen gebruikt voor maximale tolerantie in een regio.

- U kunt ook een speciale host implementeren met behulp van de [Azure cli](dedicated-hosts-cli.md).



