---
title: Problemen met Azure Bastion oplossen | Microsoft Docs
description: In dit artikel vindt u informatie over het oplossen van problemen met Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 749d7125c013f419197ef8243d2475e612dc81b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619173"
---
# <a name="troubleshoot-azure-bastion"></a>Problemen met Azure Bastion oplossen

In dit artikel leest u hoe u problemen met Azure Bastion kunt oplossen.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Kan geen NSG maken op AzureBastionSubnet

**V:** Wanneer ik een NSG op het Azure Bastion-subnet probeer te maken, krijg ik de volgende fout melding: *' de <NSG name> netwerk beveiligings groep heeft niet de vereiste regels voor het Azure Bastion-subnet AzureBastionSubnet '*.

**A:** Als u een NSG maakt en toepast op *AzureBastionSubnet*, moet u ervoor zorgen dat u de volgende regels in uw NSG hebt toegevoegd. Als u deze regels niet toevoegt, mislukt het maken/bijwerken van de NSG.

1. Besturings vlak connectiviteit: inkomend op 443 van GatewayManager
2. Logboek registratie van diagnostische gegevens en anderen: uitgaand op 443 tot Cloud (regionale Tags in deze servicetag worden nog niet ondersteund.)
3. Doel-VM: uitgaand voor 3389 en 22 tot VirtualNetwork

Een voor beeld van de NSG-regels is beschikbaar als referentie in de Quick Start- [sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion).
Zie [NSG-richt lijnen voor Azure Bastion](bastion-nsg.md)voor meer informatie.

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Mijn SSH-sleutel kan niet worden gebruikt met Azure Bastion

**V:** Wanneer ik mijn SSH-sleutel bestand probeer te bladeren, wordt het volgende fout bericht weer geven: *' de persoonlijke SSH-sleutel moet beginnen met-----begin RSA persoonlijke sleutel-----en eindigt met-----end RSA-sleutel-----'*.

**A:** Azure Bastion ondersteunt op dit moment alleen RSA SSH-sleutels. Zorg ervoor dat u een sleutel bestand met een persoonlijke RSA-sleutel voor SSH bladert met een open bare sleutel die is ingericht op de doel-VM. 

U kunt bijvoorbeeld de volgende opdracht gebruiken om een nieuwe RSA SSH-sleutel te maken:

**ssh-keygen-t rsa-b 4096-C "email@domain.com"**

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

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Kan niet aanmelden bij een virtuele machine die lid is van een Windows-domein

**V:** Ik kan geen verbinding maken met mijn virtuele Windows-machine die lid is van een domein.

**A:** Azure Bastion ondersteunt alleen aanmelden bij een domein in een gebruikers naam-wacht woord op basis van een domein. Wanneer u de domein referenties opgeeft in de Azure Portal, gebruikt u deusername@domainindeling UPN () in plaats van *domein\gebruikersnaam* om u aan te melden. Dit wordt ondersteund voor virtuele machines die lid zijn van een domein of een hybride lid (zowel een domein als een Azure AD-lid). Het wordt niet ondersteund voor virtuele machines die lid zijn van Azure AD.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Problemen met bestands overdracht

**V:** Wordt bestands overdracht ondersteund met Azure Bastion?

**A:** Bestands overdracht wordt op dit moment niet ondersteund. We werken aan het toevoegen van ondersteuning.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Zwart scherm in de Azure Portal

**V:** Wanneer ik probeer verbinding te maken met behulp van Azure Bastion, wordt er een zwart scherm weer gegeven in de Azure Portal.

**A:** Dit gebeurt wanneer er een probleem is met de netwerk verbinding tussen uw webbrowser en Azure Bastion (uw firewall voor Internet-verbindingen van de client blokkeert mogelijk websockets of vergelijk bare) of tussen de Azure-Bastion en de doel-VM. De meeste gevallen zijn een NSG toegepast op AzureBastionSubnet of op uw doel-VM-subnet dat het RDP/SSH-verkeer in het virtuele netwerk blokkeert. Sta websockets toe op de firewall van Internet en controleer de Nsg's op uw doel-VM-subnet.

## <a name="next-steps"></a>Volgende stappen

Zie de [Veelgestelde vragen over Bastion](bastion-faq.md)voor meer informatie.