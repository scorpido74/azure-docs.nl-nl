---
title: 'Een Azure Bastion-host maken: portal'
description: In dit artikel vindt u meer informatie over het maken van een Azure Bastion-host met behulp van de portal
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 14a596d78fb1f560c62013e7e439ed60d3a29b8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366140"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Een Azure Bastion-host maken met behulp van de portal

In dit artikel ziet u hoe u een Azure Bastion-host maakt met behulp van de Azure-portal. Zodra u de Azure Bastion-service in uw virtuele netwerk indient, is de naadloze RDP/SSH-ervaring beschikbaar voor alle VM's in hetzelfde virtuele netwerk. Azure Bastion-implementatie is per virtueel netwerk, niet per abonnement/account of virtuele machine.

U een nieuwe bastionhostbron in de portal maken door alle instellingen handmatig op te geven of door de instellingen te gebruiken die overeenkomen met een bestaande vm. Zie het [snelstartartikel](quickstart-host-portal.md) om een bastionhost te maken met vm-instellingen. Optioneel u [Azure PowerShell](bastion-create-host-powershell.md) gebruiken om een Azure Bastion-host te maken.

## <a name="before-you-begin"></a>Voordat u begint

Bastion is beschikbaar in de volgende openbare Azure-regio's:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Een Bastion-host maken

Met deze sectie u een nieuwe Azure Bastion-bron maken vanuit de Azure-portal.

1. Selecteer in het menu [azure portal](https://portal.azure.com) of op **de** startpagina de optie Een **bron maken**.

1. Typ **Bastion**op de pagina **Nieuw** in het veld *Marktplaats* zoeken en klik vervolgens op **Enter** om naar de zoekresultaten te gaan.

1. Klik in de resultaten op **Bastion**. Zorg ervoor dat de uitgever *Microsoft* is en de categorie *Netwerken*.

1. Klik op de pagina **Bastion** op **Maken** om de pagina **Een bastion maken** te openen.

1. Configureer op de pagina **Een bastion maken** een nieuwe Bastion-bron. Geef de configuratie-instellingen voor uw Bastion-bron op.

    ![een bastion maken](./media/bastion-create-host-portal/settings.png)

    * **Abonnement:** het Azure-abonnement dat u wilt gebruiken om een nieuwe Bastion-bron te maken.
    * **Resourcegroep:** de Azure-brongroep waarin de nieuwe Bastion-bron wordt gemaakt. Als u geen bestaande resourcegroep hebt, u een nieuwe groep maken.
    * **Naam**: De naam van de nieuwe Bastion-bron
    * **Regio**: de openbare azure-regio waarin de bron wordt gemaakt.
    * **Virtueel netwerk**: het virtuele netwerk waarin de Bastion-bron wordt gemaakt. U tijdens dit proces een nieuw virtueel netwerk in de portal maken of een bestaand virtueel netwerk gebruiken. Als u een bestaand virtueel netwerk gebruikt, moet u ervoor zorgen dat het bestaande virtuele netwerk voldoende vrije adresruimte heeft om aan de bastionsubnetvereisten te voldoen.
    * **Subnet**: Het subnet in uw virtuele netwerk waarop de nieuwe Bastion-hostbron wordt geïmplementeerd. U moet een subnet maken met de naamwaarde **AzureBastionSubnet**. Met deze waarde weet Azure naar welk subnet de Bastion-resources moeten worden geïmplementeerd. Dit is anders dan een Gateway-subnet. U moet een subnet van ten minste /27 of groter gebruiken (/27, /26, enzovoort).
    
       Maak het **AzureBastionSubnet** zonder routetabellen of delegaties. Als u netwerkbeveiligingsgroepen op het **AzureBastionSubnet**gebruikt, raadpleegt u het artikel [Werken met NSGs.](bastion-nsg.md)
    * **Openbaar IP-adres**: Het openbare IP-adres van de Bastion-bron waarop RDP/SSH zal worden geopend (via poort 443). Maak een nieuw openbaar IP-adres of gebruik een bestaand IP-adres. Het openbare IP-adres moet zich in dezelfde regio bevinden als de Bastion-bron die u maakt.
    * **Naam van het openbare IP-adres**: de naam van de bron van het openbare IP-adres.
    * **Openbaar IP-adres SKU**: Deze instelling is standaard vooraf ingevuld op **Standaard**. Azure Bastion gebruikt/ondersteunt alleen de Standard Public IP SKU.
    * **Toewijzing:** deze instelling wordt standaard vooraf ingevuld in **Statisch**.

1. Als u klaar bent met het opgeven van de instellingen, klikt u op **Controleren + Maken**. Hiermee worden de waarden gevalideerd. Zodra de validatie is voorbij, u het creatieproces starten.
1. Klik **op de** pagina Een bastion maken op **Maken**.
1. U ziet een bericht waarin staat dat uw implementatie aan de gang is. Status wordt weergegeven op deze pagina als de bronnen worden gemaakt. Het duurt ongeveer 5 minuten voordat de Bastion-bron is gemaakt en geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

* Lees de [Veelgestelde vragen over bastion](bastion-faq.md) voor meer informatie.

* Zie [Werken met NSGs](bastion-nsg.md)als u netwerkbeveiligingsgroepen wilt gebruiken met het subnet Azure Bastion.