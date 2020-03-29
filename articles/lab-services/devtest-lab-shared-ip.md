---
title: Gedeelde IP-adressen in Azure DevTest Labs begrijpen | Microsoft Documenten
description: Lees hoe Azure DevTest Labs gedeelde IP-adressen gebruikt om de openbare IP-adressen te minimaliseren die nodig zijn om toegang te krijgen tot uw lab-VM's.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2019
ms.author: spelluru
ms.openlocfilehash: f7c9feedddab1aea031cb3a8879e868aae04df00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65236897"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Gedeelde IP-adressen in Azure DevTest Labs begrijpen

Azure DevTest Labs laat lab-VM's hetzelfde openbare IP-adres delen om het aantal openbare IP-adressen te minimaliseren dat nodig is om toegang te krijgen tot uw afzonderlijke laboratorium-VM's.  In dit artikel wordt beschreven hoe gedeelde IP's werken en de bijbehorende configuratieopties.

## <a name="shared-ip-setting"></a>Gedeelde IP-instelling

Wanneer u een lab maakt, wordt het gemaakt in een subnet van een virtueel netwerk.  Standaard wordt dit subnet gemaakt met **Gedeelde IP-ingesteld voor gedeelde openbaar** maken op *Ja*.  Met deze configuratie wordt één openbaar IP-adres voor het gehele subnet gemaakt.  Zie Een virtueel netwerk configureren in Azure [DevTest Labs](devtest-lab-configure-vnet.md)voor meer informatie over het configureren van virtuele netwerken en subnetten.

![Nieuw lab subnet](media/devtest-lab-shared-ip/lab-subnet.png)

Voor bestaande labs u deze optie inschakelen door **Configuratie en beleid > virtuele netwerken te**selecteren. Selecteer vervolgens een virtueel netwerk in de lijst en kies **GEDEELD OPENBAAR IP inschakelen** voor een geselecteerd subnet. U deze optie ook in elk lab uitschakelen als u geen openbaar IP-adres wilt delen in laboratorium-VM's.

Alle VM's die in dit lab zijn gemaakt, gebruiken standaard een gedeeld IP-adres.  Bij het maken van de VM kan deze instelling worden waargenomen op de pagina **Geavanceerde instellingen** onder **IP-adresconfiguratie.**

![Nieuwe VM](media/devtest-lab-shared-ip/new-vm.png)

- **Gedeeld:** Alle VM's die zijn gemaakt **als Gedeeld,** worden in één brongroep (RG) geplaatst. Voor die RG wordt één IP-adres toegewezen en alle VM's in de RG gebruiken dat IP-adres.
- **Publiek:** Elke VM die u maakt heeft zijn eigen IP-adres en wordt gemaakt in een eigen resourcegroep.
- **Privé:** Elke vm die u maakt, gebruikt een privé-IP-adres. U niet rechtstreeks vanaf internet verbinding maken met deze virtuele machine met Extern bureaublad.

Wanneer een VM met gedeelde IP-functionaliteit aan het subnet wordt toegevoegd, voegt DevTest Labs de VM automatisch toe aan een load balancer en wijst een TCP-poortnummer toe op het openbare IP-adres, doorsturen naar de RDP-poort op de VM.  

## <a name="using-the-shared-ip"></a>Het gedeelde IP gebruiken

- **Linux-gebruikers:** SSH naar de VM met behulp van het IP-adres of volledig gekwalificeerde domeinnaam, gevolgd door een dubbele punt, gevolgd door de poort. In de afbeelding hieronder is `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`bijvoorbeeld het RDP-adres om verbinding te maken met de VM .

  ![VM-voorbeeld](media/devtest-lab-shared-ip/vm-info.png)

- **Windows-gebruikers:** Selecteer de knop **Verbinding maken** op de Azure-portal om een vooraf geconfigureerd RDP-bestand te downloaden en toegang te krijgen tot de VM.

## <a name="next-steps"></a>Volgende stappen

* [Labbeleid definiëren in Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Een virtueel netwerk configureren in Azure DevTest Labs](devtest-lab-configure-vnet.md)





