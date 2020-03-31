---
title: Azure Bastion oplossen | Microsoft Documenten
description: In dit artikel leest u hoe u Azure Bastion oplost.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: cherylmc
ms.openlocfilehash: de112ff441bb53a0b3bc7f4ffa4456f1c241682c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512949"
---
# <a name="troubleshoot-azure-bastion"></a>Azure Bastion oplossen

In dit artikel ziet u hoe u Azure Bastion oplossen.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Kan geen NSG maken op AzureBastionSubnet

**V:** Wanneer ik een NSG probeer te maken op het Subnet Azure Bastion, krijg ik de volgende fout: * <NSG name> 'Netwerkbeveiligingsgroep heeft geen noodzakelijke regels voor Azure Bastion Subnet AzureBastionSubnet'.*

**A:** Als u een NSG maakt en toepast op *AzureBastionSubnet,* controleert u of u de volgende regels in uw NSG hebt toegevoegd. Als u deze regels niet toevoegt, mislukt de NSG-creatie/-update.

1. Vliegtuigconnectiviteit beheren – Binnenkomend op 443 vanuit GatewayManager
2. Logboekregistratie voor diagnostische gegevens en anderen – Uitgaand op 443 naar AzureCloud (regionale tags binnen deze servicetag worden nog niet ondersteund.)
3. Target VM – Uitgaand voor 3389 en 22 naar VirtualNetwork

Een voorbeeld van de NSG-regels is beschikbaar voor referentie in de [sjabloon snelstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion).
Zie [NSG-richtlijnen voor Azure Bastion voor](bastion-nsg.md)meer informatie.

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Kan mijn SSH-sleutel niet gebruiken met Azure Bastion

**V:** Wanneer ik mijn SSH-sleutelbestand probeer te bladeren, krijg ik de volgende foutmelding: *'SSH Private key must start with -----BEGIN RSA PRIVATE KEY----- and ends with -----END RSA PRIVATE KEY-----'*.

**A:** Azure Bastion ondersteunt op dit moment alleen RSA SSH-sleutels. Zorg ervoor dat u door een sleutelbestand bladert dat een PRIVÉsleutel van RSA voor SSH is, met openbare sleutel die is ingericht op de doel-VM. 

U bijvoorbeeld de volgende opdracht gebruiken om een nieuwe RSA SSH-toets te maken:

**ssh-keygen -t rsa -b 4096 -C "email@domain.com**

Uitvoer:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Kan zich niet aanmelden bij mijn virtuele computer met Windows-domein

**V:** Ik kan geen verbinding maken met mijn virtuele Windows-machine die is aangesloten op het domein.

**A:** Azure Bastion ondersteunt alleen aanmelden voor vm-aanmeldingen met domeinvoor gebruikersnaam en wachtwoord. Wanneer u de domeinreferenties in de Azure-portalusername@domainopgeeft, gebruikt u de UPN -indeling ( ) in plaats van *domein\gebruikersnaamindeling* om u aan te melden. Dit wordt ondersteund voor virtuele machines met domeinverbonden of hybride verbonden (zowel met domeinen verbonden als voor Azure AD-verbonden) virtuele machines. Het wordt niet ondersteund voor virtuele machines die alleen voor Azure AD zijn aangesloten.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Problemen met bestandsoverdracht

**V:** Wordt bestandsoverdracht ondersteund met Azure Bastion?

**A:** Bestandsoverdracht wordt op dit moment niet ondersteund. We werken aan het toevoegen van ondersteuning.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Zwart scherm in de Azure-portal

**V:** Wanneer ik verbinding probeer te maken via Azure Bastion, krijg ik een zwart scherm in de Azure-portal.

**A:** Dit gebeurt wanneer er een probleem is met de netwerkverbinding tussen uw webbrowser en Azure Bastion (uw clientinternetfirewall blokkeert mogelijk WebSockets-verkeer of iets dergelijks) of tussen het Azure Bastion en uw doel-vm. De meeste gevallen omvatten een NSG toegepast op AzureBastionSubnet, of op uw doel VM subnet dat het RDP / SSH verkeer blokkeert in uw virtuele netwerk. Sta WebSockets-verkeer toe op de internetfirewall van uw client en controleer de NSG's op uw doel-VM-subnet.

## <a name="next-steps"></a>Volgende stappen

Zie de [Veelgestelde vragen van het Bastion voor](bastion-faq.md)meer informatie.