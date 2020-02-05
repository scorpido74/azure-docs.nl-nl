---
title: Een Azure bastion-host maken | Microsoft Docs
description: In dit artikel leest u hoe u een Azure bastion-host maakt
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f907dcb4427fd07a2c212e5de91ccce5e8198960
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990416"
---
# <a name="create-an-azure-bastion-host"></a>Een Azure bastion-host maken

In dit artikel wordt beschreven hoe u een Azure bastion-host maakt met behulp van de Azure Portal. Zodra u de Azure Bastion-service in uw virtuele netwerk hebt ingericht, is de naadloze RDP/SSH-ervaring beschikbaar voor alle virtuele machines in hetzelfde virtuele netwerk. Azure Bastion-implementatie is per virtueel netwerk, niet per abonnement/account of virtuele machine.

U kunt een nieuwe bastion-host maken in de portal door alle instellingen hand matig op te geven of door de instellingen te gebruiken die overeenkomen met een bestaande virtuele machine. U kunt ook [Azure Power shell](bastion-create-host-powershell.md) gebruiken om een Azure bastion-host te maken.

## <a name="before-you-begin"></a>Voordat u begint

Bastion is beschikbaar in de volgende open bare Azure-regio's:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Een bastion-host maken-instellingen opgeven

In deze sectie kunt u een nieuwe Azure Bastion-resource maken op basis van de Azure Portal.

1. Selecteer in het menu [Azure Portal](https://portal.azure.com) of op de **Start** pagina de optie **een resource maken**.

1. Typ **Bastion**in het veld *Marketplace doorzoeken* op de pagina **Nieuw** en klik vervolgens op **Enter** om naar de zoek resultaten te gaan.

1. Klik in de resultaten op **Bastion**. Zorg ervoor dat de uitgever van *micro soft* is en dat de categorie *netwerk*is.

1. Klik op de pagina **Bastion** op **maken** om de pagina **een bastion maken** te openen.

1. Configureer op de pagina **een bastion maken** een nieuwe Bastion-resource. Geef de configuratie-instellingen voor uw Bastion-resource op.

    ![een bastion maken](./media/bastion-create-host-portal/settings.png)

    * **Abonnement**: het Azure-abonnement dat u wilt gebruiken voor het maken van een nieuwe Bastion-resource.
    * **Resource groep**: de Azure-resource groep waarin de nieuwe Bastion-resource wordt gemaakt. Als u geen bestaande resource groep hebt, kunt u een nieuwe maken.
    * **Naam**: de naam van de nieuwe Bastion-resource
    * **Regio**: de open bare Azure-regio waarin de resource wordt gemaakt.
    * **Virtueel netwerk**: het virtuele netwerk waarin de Bastion-resource wordt gemaakt. U kunt tijdens dit proces een nieuw virtueel netwerk maken in de portal of een bestaand virtueel netwerk gebruiken. Als u een bestaand virtueel netwerk gebruikt, zorg er dan voor dat het bestaande virtuele netwerk voldoende vrije adres ruimte heeft om de vereisten van het Bastion-subnet aan te passen.
    * **Subnet**: het subnet in het virtuele netwerk waarop de nieuwe bastion-host wordt geïmplementeerd. U moet een subnet maken met de naam waarde **AzureBastionSubnet**. Met deze waarde kan Azure weten met welk subnet de Bastion-resources moeten worden geïmplementeerd. Dit wijkt af van een gateway-subnet. U moet een subnet van Mini maal/27 of groter (/27,/26 enzovoort) gebruiken.
    
       Maak de **AzureBastionSubnet** zonder route tabellen of delegaties. Als u netwerk beveiligings groepen op de **AzureBastionSubnet**gebruikt, raadpleegt u het artikel [over het werken met nsg's](bastion-nsg.md) .
    * **Openbaar IP-adres**: de open bare IP van de Bastion-bron waarop RDP/SSH wordt gebruikt (via poort 443). Maak een nieuw openbaar IP-adres of gebruik een bestaande. Het open bare IP-adres moet zich in dezelfde regio bevinden als de Bastion-resource die u maakt.
    * **Naam van openbaar IP-adres**: de naam van de resource voor het open bare IP-adres.
    * **SKU openbaar IP-adres**: deze instelling **is standaard vooraf ingevuld.** Azure Bastion gebruikt/ondersteunt alleen de standaard open bare IP-SKU.
    * **Toewijzing**: deze instelling wordt standaard vooraf ingevuld in **statisch**.

1. Wanneer u klaar bent met het opgeven van de instellingen, klikt u op **controleren + maken**. Hiermee worden de waarden gevalideerd. Zodra de validatie is geslaagd, kunt u beginnen met het aanmaak proces.
1. Klik op de pagina **een bastion maken** op **maken**.
1. Er wordt een bericht weer gegeven met de melding dat uw implementatie zich bevindt. De status wordt op deze pagina weer gegeven wanneer de resources zijn gemaakt. Het duurt ongeveer vijf minuten voordat de Bastion-resource wordt gemaakt en geïmplementeerd.

## <a name="createvmset"></a>Een bastion-host maken-VM-instellingen gebruiken

Als u een bastion-host in de portal maakt met behulp van een bestaande virtuele machine, zullen verschillende instellingen automatisch standaard overeenkomen met de virtuele machines en/of het virtuele netwerk.

1. Open de [Azure Portal](https://portal.azure.com). Ga naar uw virtuele machine en klik vervolgens op **verbinding maken**.

   ![VM-verbinding](./media/bastion-create-host-portal/vmsettings.png)
1. Klik in de rechter zijbalk op **Bastion**en **gebruik vervolgens Bastion**.

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. Vul op de pagina Bastion de volgende instellingen in:

   * **Naam**: de naam van de bastion-host die u wilt maken.
   * **Subnet**: het subnet in het virtuele netwerk waarop de Bastion-resource wordt geïmplementeerd. Het subnet moet worden gemaakt met de naam **AzureBastionSubnet**. Hiermee kan Azure weten met welk subnet de Bastion-resource moet worden geïmplementeerd. Dit wijkt af van een gateway-subnet. U moet een subnet van Mini maal/27 of groter (/27,/26 enzovoort) gebruiken. Maak het subnet zonder netwerk beveiligings groepen, route tabellen of delegaties. Als u later netwerk beveiligings groepen op de **AzureBastionSubnet**wilt gebruiken, raadpleegt u [werken met nsg's](bastion-nsg.md).
   
     Klik op **subnet-configuratie beheren** om de **AzureBastionSubnet**te maken.  Klik op **maken** om het subnet te maken en ga vervolgens verder met de volgende instellingen.
   * **Openbaar IP-adres**: de open bare IP van de Bastion-bron waarop RDP/SSH wordt gebruikt (via poort 443). Maak een nieuw openbaar IP-adres of gebruik een bestaande. Het open bare IP-adres moet zich in dezelfde regio bevinden als de Bastion-resource die u maakt.
   * **Naam van openbaar IP-adres**: de naam van de resource voor het open bare IP-adres.
1. Klik op het scherm validatie op **maken**. Wacht ongeveer 5 minuten tot de Bastion-resource is gemaakt en geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

* Lees de [Veelgestelde vragen over Bastion](bastion-faq.md) voor meer informatie.

* Als u netwerk beveiligings groepen wilt gebruiken met het Azure Bastion-subnet, raadpleegt u [werken met nsg's](bastion-nsg.md).