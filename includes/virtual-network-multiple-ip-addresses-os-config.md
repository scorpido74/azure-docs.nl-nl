---
title: bestand opnemen
description: bestand opnemen
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 05/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: a9473f69d600a86ff71da69c7efe0dea3f2b0a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76159594"
---
## <a name="add-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>IP-adressen toevoegen aan een VM-besturingssysteem

Maak verbinding en meld u aan bij een vm die u hebt gemaakt met meerdere privé-IP-adressen. U moet alle privé-IP-adressen (met inbegrip van het primaire) die u aan de virtuele machine hebt toegevoegd, handmatig toevoegen. Voer de volgende stappen uit voor uw VM-besturingssysteem.

### <a name="windows"></a>Windows

1. Typ vanaf een opdrachtprompt *ipconfig /all*.  U ziet alleen het *primaire* privé-IP-adres (via DHCP).
2. Typ *ncpa.cpl* in het opdrachtpromptvenster om het venster **Netwerkverbindingen** te openen.
3. Open de eigenschappen van de geschikte adapter: **LAN-verbinding**.
4. Dubbelklik op Internet Protocol versie 4 (IPv4).
5. Selecteer **Het volgende IP-adres gebruiken** en voer de volgende waarden in:

    * **IP-adres**: voer het *primaire* privé-IP-adres in
    * **Subnetmasker**: stel dit in op basis van uw subnet. Als het subnet bijvoorbeeld een /24 subnet is, is het subnetmasker 255.255.255.0.
    * **Standaardgateway**: het eerste IP-adres in het subnet. Als uw subnet 10.0.0.0/24 is, is het IP-adres van de gateway 10.0.0.1.
    * Selecteer **De volgende DNS-serveradressen gebruiken** en voer de volgende waarden in:
        * **DNS-voorkeursserver**: als u niet uw eigen DNS-server gebruikt, voert u 168.63.129.16 in.  Als u uw eigen DNS-server gebruikt, voert u het IP-adres voor de server in.
    * Selecteer de knop **Geavanceerd** en voeg extra IP-adressen toe. Voeg elk van de secundaire privé-IP-adressen, die u in een vorige stap aan de Azure-netwerkinterface hebt toegevoegd, toe aan de Windows-netwerkinterface waaraan het primaire IP-adres is toegewezen dat is toegewezen aan de Azure-netwerkinterface.

        U mag nooit handmatig het openbare IP-adres toewijzen dat is toegewezen aan een virtuele Azure-machine in het besturingssysteem van de virtuele machine. Wanneer u het IP-adres handmatig instelt in het besturingssysteem, moet u ervoor zorgen dat het hetzelfde adres is als het privé-IP-adres dat is toegewezen aan de [Azure-netwerkinterface,](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings)of u de verbinding met de virtuele machine verliezen. Meer informatie over [instellingen voor privé-IP-adres.](../articles/virtual-network/virtual-network-network-interface-addresses.md#private) U moet nooit een openbaar IP-adres van Azure toewijzen in het besturingssysteem.

    * Klik op **OK** om de TCP/IP-instellingen te sluiten en vervolgens nogmaals op **OK** om de instellingen van de netwerkadapter te sluiten. Uw RDP-verbinding wordt opnieuw tot stand gebracht.

6. Typ vanaf een opdrachtprompt *ipconfig /all*. Alle IP-adressen die u hebt toegevoegd, worden weergegeven en DHCP is uitgeschakeld.
7. Configureer Windows om het privé-IP-adres van de primaire IP-configuratie in Azure te gebruiken als het primaire IP-adres voor Windows. Zie [Geen internettoegang van Azure Windows VM met meerdere IP-adressen](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse) voor meer informatie. 

### <a name="validation-windows"></a>Validatie (Windows)

Als u wilt controleren of u via uw secundaire IP-configuratie verbinding kunt maken met internet via de openbare IP die eraan is gekoppeld, gebruikt u de volgende opdracht nadat u de bovenstaande stappen hebt gevolgd:

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>Voor secundaire IP-configuraties u alleen pingen naar internet als er een openbaar IP-adres aan is gekoppeld. Voor primaire IP-configuraties is een openbaar IP-adres niet vereist om te pingen naar het internet.

### <a name="linux-ubuntu-1416"></a>Linux (Ubuntu 14/16)

We raden u aan de nieuwste documentatie voor uw Linux-distributie te bekijken. 

1. Open een terminalvenster.
2. Controleer of u de hoofdgebruiker bent. Voer de volgende opdracht in als u niet de hoofdgebruiker bent:

   ```bash
   sudo -i
   ```

3. Werk het configuratiebestand van de netwerkinterface bij (uitgaande van 'eth0').

   * Houd het bestaande regelitem voor dhcp. Het primaire IP-adres blijft geconfigureerd als voorheen.
   * Voeg een configuratie toe voor een extra statisch IP-adres met de volgende opdrachten:

     ```bash
     cd /etc/network/interfaces.d/
     ls
     ```

     U moet een .CFG-bestand zien.
4. Open het bestand. U moet de volgende regels aan het einde van het bestand zien:

   ```bash
   auto eth0
   iface eth0 inet dhcp
   ```

5. Voeg de volgende regels toe na de regels die zijn opgenomen in dit bestand:

   ```bash
   iface eth0 inet static
   address <your private IP address here>
   netmask <your subnet mask>
   ```

6. Sla het bestand op met de volgende opdracht:

   ```bash
   :wq
   ```

7. Stel de netwerkinterface opnieuw in met de volgende opdracht:

   ```bash
   sudo ifdown eth0 && sudo ifup eth0
   ```

   > [!IMPORTANT]
   > Voer zowel ifdown als ifup op dezelfde regel uit als u een externe verbinding gebruikt.
   >

8. Controleer of het IP-adres is toegevoegd aan de netwerkinterface met de volgende opdracht:

   ```bash
   ip addr list eth0
   ```

   Het IP-adres dat u hebt toegevoegd, moet nu in de lijst staan.

### <a name="linux-ubuntu-1804"></a>Linux (Ubuntu 18.04+)

Ubuntu 18.04 en hoger `netplan` zijn gewijzigd in voor OS-netwerkbeheer. We raden u aan de nieuwste documentatie voor uw Linux-distributie te bekijken. 

1. Open een terminalvenster.
2. Controleer of u de hoofdgebruiker bent. Voer de volgende opdracht in als u niet de hoofdgebruiker bent:

    ```bash
    sudo -i
    ```

3. Maak een bestand voor de tweede interface en open het in een teksteditor:

    ```bash
    vi /etc/netplan/60-static.yaml
    ```

4. Voeg de volgende regels toe `10.0.0.6/24` aan het bestand en vervang uw IP/netmask:

    ```bash
    network:
        version: 2
        ethernets:
            eth0:
                addresses:
                    - 10.0.0.6/24
    ```

5. Sla het bestand op met de volgende opdracht:

    ```bash
    :wq
    ```

6. Test de wijzigingen met [netplan probeer](http://manpages.ubuntu.com/manpages/cosmic/man8/netplan-try.8.html) syntaxis te bevestigen:

    ```bash
    netplan try
    ```

> [!NOTE]
> `netplan try`zal de wijzigingen tijdelijk toepassen en de wijzigingen na 120 seconden terugdraaien. Als er een verlies van connectiviteit is, wacht dan 120 seconden en maak vervolgens opnieuw verbinding. Op dat moment zijn de wijzigingen teruggedraaid.

7. Als u `netplan try`geen problemen met , de configuratiewijzigingen toepassen:

    ```bash
    netplan apply
    ```

8. Controleer of het IP-adres is toegevoegd aan de netwerkinterface met de volgende opdracht:

    ```bash
    ip addr list eth0
    ```

    Het IP-adres dat u hebt toegevoegd, moet nu in de lijst staan. Voorbeeld:

    ```bash
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 00:0d:3a:8c:14:a5 brd ff:ff:ff:ff:ff:ff
        inet 10.0.0.6/24 brd 10.0.0.255 scope global eth0
        valid_lft forever preferred_lft forever
        inet 10.0.0.4/24 brd 10.0.0.255 scope global secondary eth0
        valid_lft forever preferred_lft forever
        inet6 fe80::20d:3aff:fe8c:14a5/64 scope link
        valid_lft forever preferred_lft forever
    ```
    
### <a name="linux-red-hat-centos-and-others"></a>Linux (Red Hat, CentOS, en anderen)

1. Open een terminalvenster.
2. Controleer of u de hoofdgebruiker bent. Voer de volgende opdracht in als u niet de hoofdgebruiker bent:

    ```bash
    sudo -i
    ```

3. Voer uw wachtwoord in en volg de instructies. Wanneer u de hoofdgebruiker bent, navigeert u met de volgende opdracht naar de map met netwerkscripts:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Geef een lijst weer met de gerelateerde ifcfg-bestanden met de volgende opdracht:

    ```bash
    ls ifcfg-*
    ```

    *ifcfg eth0* moet als een van de bestanden worden weergegeven.

5. Als u een IP-adres wilt toevoegen, maakt u er een configuratiebestand voor zoals hieronder wordt weergegeven. Houd er rekening mee dat er voor elke IP-configuratie één bestand moet worden gemaakt.

    ```bash
    touch ifcfg-eth0:0
    ```

6. Open het bestand *ifcfg-eth0:0* met de volgende opdracht:

    ```bash
    vi ifcfg-eth0:0
    ```

7. Voeg inhoud toe aan het bestand, in dit geval *eth0:0*, met de volgende opdracht. Zorg ervoor dat u de gegevens bijwerkt op basis van uw IP-adres.

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. Sla het bestand op met de volgende opdracht:

    ```bash
    :wq
    ```

9. Start de netwerkservices opnieuw op en controleer of de wijzigingen zijn toegepast door de volgende opdrachten uit te voeren:

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    Het IP-adres dat u hebt toegevoegd, *eth0:0*, moet nu in de lijst staan die wordt opgehaald.

### <a name="validation-linux"></a>Validatie (Linux)

Als u wilt controleren of u via uw secundaire IP-configuratie verbinding kunt maken met internet via de openbare IP die eraan is gekoppeld, gebruikt u de volgende opdracht:

```bash
ping -I 10.0.0.5 hotmail.com
```
>[!NOTE]
>Voor secundaire IP-configuraties u alleen pingen naar internet als er een openbaar IP-adres aan is gekoppeld. Voor primaire IP-configuraties is een openbaar IP-adres niet vereist om te pingen naar het internet.

Voor virtuele Linux-machines moet u mogelijk geschikte routes toevoegen wanneer u probeert uitgaande verbindingen te valideren vanaf een secundaire NIC. Er zijn meerdere manieren om dit te doen. Zie de relevante documentatie voor uw Linux-distributie. Hieronder staat één van de mogelijke manieren:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Vervang de volgende zaken:
    - **10.0.0.5** door het privé-IP-adres waaraan een openbaar IP-adres is gekoppeld
    - **10.0.0.1** door uw standaardgateway
    - **eth2** door de naam van uw secundaire NIC
