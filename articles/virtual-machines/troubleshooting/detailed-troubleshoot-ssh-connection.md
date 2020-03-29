---
title: Gedetailleerde SSH-probleemoplossing voor een Azure-vm | Microsoft Documenten
description: Meer gedetailleerde SSH-probleemoplossingsstappen voor problemen met een azure-virtuele machine
keywords: ssh-verbinding geweigerd, ssh fout, azure ssh, SSH-verbinding mislukt
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ee6d437915f6c87ce9ef5f9c711d90793a96048c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920124"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Gedetailleerde stappen om SSH-problemen op te lossen bij verbindingsproblemen van een Linux-VM in Azure
Er zijn veel mogelijke redenen dat de SSH-client mogelijk niet in staat is om de SSH-service op de VM te bereiken. Als u de meer [algemene ssh-probleemoplossingsstappen](troubleshoot-ssh-connection.md)hebt gevolgd, moet u het verbindingsprobleem verder oplossen. In dit artikel u gedetailleerde stappen voor het oplossen van problemen uitvoeren om te bepalen waar de SSH-verbinding niet werkt en hoe u deze oplossen.

## <a name="take-preliminary-steps"></a>Voorlopige stappen ondernemen
In het volgende diagram ziet u de betrokken onderdelen.

![Diagram met onderdelen van de SSH-service](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Met de volgende stappen u de bron van de fout isoleren en oplossingen of oplossingen vinden.

1. Controleer de status van de VM in de portal.
   Selecteer in de [Azure-portal](https://portal.azure.com)de*VM-naam* **virtuele machines** > .

   In het statusvenster voor de virtuele machine moet **uitvoeren worden**weergegeven. Scroll omlaag om recente activiteit voor rekenkracht, opslag en netwerkbronnen weer te geven.

2. Selecteer **Instellingen** om eindpunten, IP-adressen, netwerkbeveiligingsgroepen en andere instellingen te onderzoeken.

   De VM moet een eindpunt hebben gedefinieerd voor SSH-verkeer dat u weergeven in **Eindpunten** of **[netwerkbeveiligingsgroep](../../virtual-network/security-overview.md)**. Eindpunten in VM's die zijn gemaakt met Behulp van Resourcemanager, worden opgeslagen in een netwerkbeveiligingsgroep. Controleer of de regels zijn toegepast op de netwerkbeveiligingsgroep en waarnaar wordt verwezen in het subnet.

Als u de netwerkconnectiviteit wilt verifiëren, controleert u de geconfigureerde eindpunten en controleert u of u verbinding maken met de VM via een ander protocol, zoals HTTP of een andere service.

Probeer na deze stappen de SSH-verbinding opnieuw.

## <a name="find-the-source-of-the-issue"></a>Zoek de bron van het probleem
De SSH-client op uw computer kan geen verbinding maken met de SSH-service op de Azure VM vanwege problemen of verkeerde configuraties in de volgende gebieden:

* [SSH-clientcomputer](#source-1-ssh-client-computer)
* [Organisatierandapparaat](#source-2-organization-edge-device)
* [Lijst met eindpunten en toegangscontrole voor cloudservices (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Netwerkbeveiligingsgroepen](#source-4-network-security-groups)
* [Op Linux gebaseerde Azure VM](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Bron 1: SSH-clientcomputer
Als u uw computer wilt elimineren als de bron van de storing, controleert u of deze SSH-verbindingen kan maken met een andere on-premises, Linux-gebaseerde computer.

![Diagram met ssh-clientcomputercomponenten](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Als de verbinding mislukt, controleert u op de volgende problemen op uw computer:

* Een lokale firewall-instelling die binnenkomend of uitgaand SSH-verkeer (TCP 22) blokkeert
* Lokaal geïnstalleerde clientproxysoftware die SSH-verbindingen verhindert
* Lokaal geïnstalleerde netwerkbewakingssoftware die SSH-verbindingen verhindert
* Andere soorten beveiligingssoftware die het verkeer controleren of specifieke soorten verkeer toestaan/verbieden

Als een van deze voorwaarden van toepassing is, schakelt u de software tijdelijk uit en probeert u een SSH-verbinding met een on-premises computer om erachter te komen waarom de verbinding op uw computer wordt geblokkeerd. Werk vervolgens samen met uw netwerkbeheerder om de software-instellingen te corrigeren om SSH-verbindingen toe te staan.

Als u certificaatverificatie gebruikt, controleert u of u deze machtigingen hebt voor de .ssh-map in uw thuismap:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (of andere bestanden die uw prive-sleutels opgeslagen in hen)
* Chmod 644 ~/.ssh/known_hosts (bevat hosts waarmee je via SSH bent verbonden)

## <a name="source-2-organization-edge-device"></a>Bron 2: Apparaat voor de rand van de organisatie
Als u uw apparaat als bron van de storing wilt elimineren op uw apparaat als de bron van de storing, controleert u of een computer die rechtstreeks met internet is verbonden, SSH-verbindingen kan maken met uw Azure VM. Als u de VM via een site-to-site VPN of een Azure ExpressRoute-verbinding gebruikt, gaat u naar [Bron 4: Netwerkbeveiligingsgroepen](#nsg).

![Diagram met het randapparaat van de organisatie](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Als u geen computer hebt die rechtstreeks met internet is verbonden, maakt u een nieuwe Azure VM in de eigen resourcegroep of cloudservice en gebruikt u die nieuwe VM. Zie [Een virtuele machine maken met Linux maken in Azure](../linux/quick-create-cli.md)voor meer informatie. Verwijder de brongroep of vm- en cloudservice wanneer u klaar bent met het testen.

Als u een SSH-verbinding maken met een computer die rechtstreeks is verbonden met internet, controleert u het randapparaat van uw organisatie op:

* Een interne firewall die SSH-verkeer blokkeert met het internet
* Een proxyserver die SSH-verbindingen verhindert
* Inbraakdetectie of netwerkbewakingssoftware die wordt uitgevoerd op apparaten in uw edge-netwerk die SSH-verbindingen verhindert

Werk samen met uw netwerkbeheerder om de instellingen van uw gebruikersrandapparaten te corrigeren om SSH-verkeer met internet toe te staan.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Bron 3: Eindpunt van cloudservices en ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Deze bron is alleen van toepassing op VM's die zijn gemaakt met behulp van het klassieke implementatiemodel. Ga naar [bron 4: Netwerkbeveiligingsgroepen](#nsg)voor VM's die zijn gemaakt met Behulp van Resourcebeheer.

Als u het eindpunt van de cloudservice en ACL als bron van de fout wilt elimineren, controleert u of een andere Azure-vm in hetzelfde virtuele netwerk verbinding kan maken met SSH.

![Diagram met hoogtepunten eindpunt voor cloudservice en ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

If you don't have another VM in the same virtual network, you can easily create one. Zie [Een Linux-vm maken op Azure met de CLI](../linux/quick-create-cli.md)voor meer informatie. Verwijder de extra VM wanneer u klaar bent met het testen.

Als u een SSH-verbinding maken met een vm in hetzelfde virtuele netwerk, controleert u de volgende gebieden:

* **De eindpuntconfiguratie voor SSH-verkeer op de doel-VM.** De private TCP-poort van het eindpunt moet overeenkomen met de TCP-poort waarop de SSH-service op de VM luistert. (De standaardpoort is 22). Controleer het SSH TCP-poortnummer in de Azure-portal door*vm-naaminstellingen* > **Settings** > voor **virtuele machines** > **te**selecteren.
* **De ACL voor het SSH-verkeerseindpunt op de beoogde virtuele machine.** Met een ACL u toegestaan of geweigerd binnenkomend verkeer van internet opgeven op basis van het IP-adres van de bron. Verkeerd geconfigureerde ACL's kunnen binnenkomend SSH-verkeer naar het eindpunt voorkomen. Controleer uw ACL's om ervoor te zorgen dat binnenkomend verkeer van de openbare IP-adressen van uw proxy of andere randserver is toegestaan. Zie [Lijsten voor netwerktoegangscontrole (ACL's) voor](../../virtual-network/virtual-networks-acl.md)meer informatie.

Als u het eindpunt als bron van het probleem wilt elimineren, verwijdert u het huidige eindpunt, maakt u een ander eindpunt en geeft u de SSH-naam op (TCP-poort 22 voor het openbare en privépoortnummer). Zie [Eindpunten instellen op een virtuele machine in Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)voor meer informatie.

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Bron 4: Netwerkbeveiligingsgroepen
Met netwerkbeveiligingsgroepen u meer gedetailleerde controle hebben over toegestaan in- en uitgaand verkeer. U regels maken die betrekking hebben op subnetten en cloudservices in een virtueel Azure-netwerk. Controleer de regels van uw netwerkbeveiligingsgroep om ervoor te zorgen dat SSH-verkeer van en naar internet is toegestaan.
Zie [Netwerkbeveiligingsgroepen](../../virtual-network/security-overview.md)voor meer informatie.

U IP Verify ook gebruiken om de NSG-configuratie te valideren. Zie overzicht van [Azure-netwerkbewaking](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)voor meer informatie. 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Bron 5: Op Linux gebaseerde Azure virtuele machine
De laatste bron van mogelijke problemen is de Azure virtuele machine zelf.

![Diagram dat de op Linux gebaseerde Azure-virtuele machine markeert](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Als u dit nog niet hebt gedaan, volgt u de instructies [om een wachtwoord op Linux gebaseerde virtuele machines opnieuw in te stellen.](../linux/reset-password.md)

Probeer opnieuw verbinding te maken vanaf uw computer. Als het nog steeds mislukt, zijn de volgende enkele van de mogelijke problemen:

* De SSH-service wordt niet uitgevoerd op de beoogde virtuele machine.
* De SSH-service luistert niet op TCP-poort 22. Als u wilt testen, installeert u een telnetclient op uw lokale computer en voert u "telnet *cloudServiceName*.cloudapp.net 22" uit. Deze stap bepaalt of de virtuele machine inkomende en uitgaande communicatie naar het SSH-eindpunt toestaat.
* De lokale firewall op de doelvirtuele machine heeft regels die binnenkomend of uitgaand SSH-verkeer voorkomen.
* Inbraakdetectie of netwerkbewakingssoftware die wordt uitgevoerd op de virtuele Azure-machine voorkomt SSH-verbindingen.

## <a name="additional-resources"></a>Aanvullende bronnen
Zie Toegang [tot een toepassing op een virtuele azure-machine oplossen voor](../linux/troubleshoot-app-connection.md) meer informatie over het oplossen van problemen met toepassingen
