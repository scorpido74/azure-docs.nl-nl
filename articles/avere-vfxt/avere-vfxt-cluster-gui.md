---
title: Toegang tot het configuratiescherm van Avere vFXT - Azure
description: Verbinding maken met het vFXT-cluster en het op de browser gebaseerde Avere-configuratiescherm om de Avere vFXT te configureren
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: fe2fc062f690498f3d1f588887279aa33d2434b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75416151"
---
# <a name="access-the-vfxt-cluster"></a>Access the vFXT cluster (Toegang tot het vFXT-cluster)

Als u de clusterinstellingen wilt aanpassen en het cluster wilt bewaken, gebruikt u het Configuratiescherm van Avere. Avere Configuratiescherm is een browser-gebaseerde grafische interface naar het cluster.

Omdat het vFXT-cluster zich in een virtueel privénetwerk bevindt, moet u een SSH-tunnel maken of een andere methode gebruiken om het ip-adres van het clusterbeheer te bereiken.

Er zijn twee basisstappen:

1. Maak een verbinding tussen uw werkstation en het virtuele privénetwerk
1. Het configuratiescherm van het cluster laden in een webbrowser

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat u een openbaar IP-adres hebt ingesteld op de clustercontroller of op een andere vm in het virtuele netwerk van uw cluster. In dit artikel wordt beschreven hoe u die VM als host gebruikt om toegang te krijgen tot het cluster. Als u een VPN of ExpressRoute gebruikt voor virtuele netwerktoegang, gaat u naar [Verbinding maken met het Configuratiescherm van Avere.](#connect-to-the-avere-control-panel-in-a-browser)

Controleer voordat u verbinding maakt het ssh-sleutelpaar voor de openbare/private sleutel die u hebt gebruikt bij het maken van de clustercontroller, op uw lokale machine is geïnstalleerd. Lees de Documentatie van De sleutels van SSH voor [Vensters](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) of voor [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) als u hulp nodig hebt. Als u een wachtwoord hebt gebruikt in plaats van een openbare sleutel, wordt u gevraagd dit in te voeren wanneer u verbinding maakt.

## <a name="create-an-ssh-tunnel"></a>Een SSH-tunnel maken

U een SSH-tunnel maken vanaf de opdrachtregel van een Linux- of Windows 10-clientsysteem.

Gebruik een SSH-tunnelopdracht met dit formulier:

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Deze opdracht maakt verbinding met het beheer-IP-adres van het cluster via het IP-adres van de clustercontroller.

Voorbeeld:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Verificatie is automatisch als u uw SSH-openbare sleutel hebt gebruikt om het cluster te maken en de overeenkomende sleutel op het clientsysteem is geïnstalleerd. Als u een wachtwoord hebt gebruikt, vraagt het systeem u om het in te voeren.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Verbinding maken met het Configuratiescherm van Avere in een browser

Deze stap maakt gebruik van een webbrowser om verbinding te maken met het configuratiehulpprogramma op het vFXT-cluster.

* Open uw webbrowser voor een SSH-tunnelverbinding en navigeer naar `https://127.0.0.1:8443`.

  U hebt verbinding gemaakt met het IP-adres van het cluster toen u de tunnel maakte, dus u hoeft alleen maar het IP-adres van localhost in de browser te gebruiken. Als u een andere lokale poort dan 8443 hebt gebruikt, gebruikt u in plaats daarvan uw poortnummer.

* Als u een VPN of ExpressRoute gebruikt om het cluster te bereiken, navigeert u naar het IP-adres voor clusterbeheer in uw browser. Voorbeeld: ``https://203.0.113.51``

Afhankelijk van uw browser moet u mogelijk op **Geavanceerd** klikken en controleren of het veilig is om naar de pagina te gaan.

Voer de `admin` gebruikersnaam en het beheerderswachtwoord in dat u hebt opgegeven bij het maken van het cluster.

![Schermafbeelding van de aanmeldingspagina van Avere gevuld met de gebruikersnaam 'beheerder' en een wachtwoord](media/avere-vfxt-gui-login.png)

Klik **op Inloggen** of druk op enter op het toetsenbord.

## <a name="next-steps"></a>Volgende stappen

Nadat u bent ingelogd bij het configuratiescherm van het cluster, schakelt u [ondersteuning](avere-vfxt-enable-support.md)in.
