---
title: Dynamische DNS gebruiken om hostnamen in Azure te registreren | Microsoft Documenten
description: Meer informatie over het instellen van dynamische DNS om hostnamen in uw eigen DNS-servers te registreren.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: c2ef842fd62ef060f06536d66387c3facd0627b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60640375"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Dynamische DNS gebruiken om hostnamen te registreren op uw eigen DNS-server

[Azure biedt naamresolutie](virtual-networks-name-resolution-for-vms-and-role-instances.md) voor virtuele machines (VM) en rolexemplaren. Wanneer uw naamomzetting smeer nodig heeft dan de mogelijkheden van de standaard DNS van Azure, u uw eigen DNS-servers opgeven. Het gebruik van uw eigen DNS-servers geeft u de mogelijkheid om uw DNS-oplossing aan te passen aan uw eigen specifieke behoeften. Het kan bijvoorbeeld nodig zijn om on-premises bronnen te openen via uw Active Directory-domeincontroller.

Wanneer uw aangepaste DNS-servers worden gehost als Azure VM's, u hostnamequery's voor hetzelfde virtuele netwerk doorsturen naar Azure om hostnamen op te lossen. Als u deze optie niet wilt gebruiken, u uw VM-hostnamen registreren in uw DNS-server met behulp van dynamische DNS (DDNS). Azure heeft niet de referenties om direct records te maken in uw DNS-servers, dus er zijn vaak alternatieve regelingen nodig. Enkele veelvoorkomende scenario's, met alternatieven volgen:

## <a name="windows-clients"></a>Windows-clients
Niet-domein-verbonden Windows-clients proberen onbeveiligde DDNS-updates uit te voeren wanneer ze worden opgestart of wanneer hun IP-adres verandert. De DNS-naam is de hostnaam plus het primaire DNS-achtervoegsel. Azure laat het primaire DNS-achtervoegsel leeg, maar u het achtervoegsel in de VM instellen via de [gebruikersinterface](https://technet.microsoft.com/library/cc794784.aspx) of [PowerShell.](/powershell/module/dnsclient/set-dnsclient)

Domein-verbonden Windows-clients registreren hun IP-adressen bij de domeincontroller met behulp van beveiligde DDNS. Het domein-joinproces stelt het primaire DNS-achtervoegsel op de client in en maakt en onderhoudt de vertrouwensrelatie.

## <a name="linux-clients"></a>Linux clients
Linux-clients registreren zich over het algemeen niet bij de DNS-server bij het opstarten, ze gaan ervan uit dat de DHCP-server dit doet. De DHCP-servers van Azure hebben niet de referenties om records op uw DNS-server te registreren. U een `nsupdate`tool genaamd, die is opgenomen in het Bind-pakket, gebruiken om DDNS-updates te verzenden. Omdat het DDNS-protocol gestandaardiseerd `nsupdate` is, u zelfs gebruiken wanneer u Binding niet gebruikt op de DNS-server.

U de haken die door de DHCP-client worden geleverd, gebruiken om de hostnamevermelding in de DNS-server te maken en te behouden. Tijdens de DHCP-cyclus voert de client de scripts uit in */etc/dhcp/dhclient-exit-hooks.d/*. U de haken gebruiken om `nsupdate`het nieuwe IP-adres te registreren met behulp van. Bijvoorbeeld:

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

U de `nsupdate` opdracht ook gebruiken om beveiligde DDNS-updates uit te voeren. Wanneer u bijvoorbeeld een Bind DNS-server gebruikt, wordt een sleutelpaar met een openbare privésleutel [gegenereerd.](http://linux.yyz.us/nsupdate/) De DNS-server is [geconfigureerd](http://linux.yyz.us/dns/ddns-server.html) met het openbare gedeelte van de sleutel, zodat deze de handtekening op de aanvraag kan verifiëren. Als u het sleutelpaar wilt verstrekken om `nsupdate`, gebruik de `-k` optie, om het ddns-updateverzoek te ondertekenen.

Wanneer u een Windows DNS-server gebruikt, u `-g` Kerberos-verificatie gebruiken met de parameter `nsupdate` `nsupdate`in, maar deze is niet beschikbaar in de Windows-versie van. Als u Kerberos `kinit` wilt gebruiken, gebruikt u de referenties om de referenties te laden. U bijvoorbeeld referenties laden vanuit een [tabbestand](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)en vervolgens `nsupdate -g` de referenties ophalen uit de cache.

Indien nodig u een DNS-zoekachtervoegsel toevoegen aan uw VM's. Het DNS-achtervoegsel is opgegeven in het bestand */etc/resolv.conf.* De meeste Linux distro's beheren automatisch de inhoud van dit bestand, dus meestal kun je het niet bewerken. U het achtervoegsel echter overschrijven `supersede` met de opdracht van de DHCP-client. Als u het achtervoegsel wilt overschrijven, voegt u de volgende regel toe aan het bestand */etc/dhcp/dhclient.conf:*

```
supersede domain-name <required-dns-suffix>;
```
