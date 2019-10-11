---
title: Toegang tot het avere vFXT configuratie scherm-Azure
description: Verbinding maken met het vFXT-cluster en het avere-configuratie scherm op basis van de browser voor het configureren van de avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rohogue
ms.openlocfilehash: 098ed98c1680fa2ea38c377e9e34719ba778b175
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255027"
---
# <a name="access-the-vfxt-cluster"></a>Toegang tot het vFXT-cluster

Als u de instellingen wilt wijzigen en het avere vFXT-cluster wilt controleren, gebruikt u het onderdeel avere van het configuratie scherm. Het configuratie scherm van AVERE is een op een browser gebaseerde grafische interface voor het cluster.

Omdat het vFXT-cluster zich in een particulier virtueel netwerk bevindt, moet u een SSH-tunnel maken of een andere methode gebruiken om het IP-adres van het cluster beheer te bereiken. Er zijn twee basis stappen: 

1. Een verbinding maken tussen uw werk station en het persoonlijke vnet 
1. Het configuratie scherm van het cluster laden in een webbrowser 

> [!NOTE] 
> In dit artikel wordt ervan uitgegaan dat u een openbaar IP-adres op de cluster controller of op een andere VM in het virtuele netwerk van uw cluster hebt ingesteld. In dit artikel wordt beschreven hoe u deze virtuele machine gebruikt als een host voor toegang tot het cluster. Als u een VPN-of ExpressRoute gebruikt voor vnet-toegang, gaat u naar [verbinding maken met het avere configuratie scherm](#connect-to-the-avere-control-panel-in-a-browser).

Voordat u verbinding maakt, moet u ervoor zorgen dat het open bare/persoonlijke SSH-sleutel paar dat u hebt gebruikt bij het maken van de cluster controller, op uw lokale computer is geïnstalleerd. Lees de documentatie van SSH-sleutels voor [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) of voor [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) als u hulp nodig hebt. (Als u een wacht woord hebt gebruikt in plaats van een open bare sleutel, wordt u gevraagd dit in te voeren wanneer u verbinding maakt.) 

## <a name="create-an-ssh-tunnel"></a>Een SSH-tunnel maken 

U kunt een SSH-tunnel maken via de opdracht regel van een Linux-of Windows 10-client systeem. 

Gebruik een SSH-tunnel opdracht met dit formulier: 

ssh-L *local_port*:*cluster_mgmt_ip*: 443 *controller_username*\@*controller_public_IP*

Met deze opdracht wordt verbinding gemaakt met het IP-adres van het cluster beheer via het IP-adres van de cluster controller.

Voorbeeld:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Verificatie wordt automatisch uitgevoerd als u uw open bare SSH-sleutel hebt gebruikt om het cluster te maken en de overeenkomende sleutel is geïnstalleerd op het client systeem. Als u een wacht woord hebt gebruikt, wordt u gevraagd dit in te voeren.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Verbinding maken met het avere-configuratie scherm in een browser

In deze stap maakt gebruik van een webbrowser om verbinding te maken met het configuratie hulpprogramma dat wordt uitgevoerd op het vFXT-cluster.

* Open uw webbrowser voor een SSH-tunnel verbinding en navigeer naar `https://127.0.0.1:8443`. 

  U hebt verbinding gemaakt met het IP-adres van het cluster tijdens het maken van de tunnel. u hoeft alleen het localhost IP-adres te gebruiken in de browser. Als u een andere lokale poort dan 8443 gebruikt, gebruikt u in plaats daarvan het poort nummer.

* Als u een VPN-of ExpressRoute gebruikt om het cluster te bereiken, navigeert u naar het IP-adres van Cluster beheer in uw browser. Voorbeeld: ``https://203.0.113.51``

Afhankelijk van uw browser moet u mogelijk op **Geavanceerd** klikken en controleren of het veilig is om door te gaan naar de pagina.

Voer de gebruikers naam `admin` en het beheerders wachtwoord in dat u hebt opgegeven bij het maken van het cluster.

![Scherm afbeelding van de avere-aanmeldings pagina ingevuld met de gebruikers naam admin en een wacht woord](media/avere-vfxt-gui-login.png)

Klik op **Aanmelden** of druk op ENTER op het toetsen bord.

## <a name="next-steps"></a>Volgende stappen

Nu u toegang hebt tot het cluster, schakelt u [ondersteuning](avere-vfxt-enable-support.md)in.
