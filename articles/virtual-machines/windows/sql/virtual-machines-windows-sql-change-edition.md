---
title: Een in-place upgrade van een SQL Server-editie op een virtuele Azure-machine uitvoeren | Microsoft Docs
description: Meer informatie over het wijzigen van de editie van uw SQL Server-VM in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: eec2e588b1c2b03e9880dad0848b8213bf5fa449
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100512"
---
# <a name="perform-an-in-place-upgrade-of-a-sql-server-edition-on-an-azure-vm"></a>Een in-place upgrade van een SQL Server-editie op een virtuele Azure-machine uitvoeren

In dit artikel wordt beschreven hoe u de editie van SQL Server kunt wijzigen op een virtuele Windows-machine in Azure. 

De versie van SQL Server wordt bepaald door de product code en wordt opgegeven met het installatie proces. De versie bepaalt welke [functies](/sql/sql-server/editions-and-components-of-sql-server-2017) beschikbaar zijn in het SQL Server product. U kunt de SQL Server Edition met de installatie media wijzigen en een downgrade uitvoeren om de kosten te verlagen of een upgrade uit te voeren om meer functies in te scha kelen.

Als u de versie van SQL Server hebt bijgewerkt met behulp van het installatie medium nadat u zich hebt geregistreerd bij de resource provider van de SQL-VM, moet u de eigenschap SQL Server Edition van de SQL-VM-resource als volgt instellen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
1. Ga naar uw SQL Server virtuele machine bron. 
1. Selecteer **configureren**onder **instellingen**. Selecteer vervolgens de gewenste versie van SQL Server in de vervolg keuzelijst onder **editie**. 

   ![Meta gegevens van de editie wijzigen](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Bekijk de waarschuwing met de melding dat u eerst de SQL Server Edition moet wijzigen en dat de editie-eigenschap moet overeenkomen met de SQL Server Edition. 
1. Selecteer **Toep assen** om de wijzigingen in de meta gegevens van de editie toe te passen. 


## <a name="prerequisites"></a>Vereisten

Als u een in-place wijziging van de editie van SQL Server wilt uitvoeren, hebt u het volgende nodig: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een [SQL Server VM in Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) die is geregistreerd bij de [resource provider](virtual-machines-windows-sql-register-with-resource-provider.md)van de SQL-VM.
- Stel media in met de gewenste versie van SQL Server. Klanten met [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kunnen hun installatie media verkrijgen via het [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klanten die geen Software Assurance hebben, kunnen het installatie medium van een Azure Marketplace gebruiken SQL Server VM-installatie kopie met de gewenste versie.


## <a name="upgrade-an-edition"></a>Een editie bijwerken

> [!WARNING]
> Als u de versie van SQL Server bijwerkt, wordt de service voor SQL Server opnieuw gestart, samen met de bijbehorende services, zoals Analysis Services en R-Services. 

Als u SQL Server wilt bijwerken, moet u de SQL Server Setup-media verkrijgen voor de gewenste versie van SQL Server en vervolgens het volgende doen:

1. Open Setup. exe vanaf de SQL Server-installatie media. 
1. Ga naar **onderhoud** en kies de optie voor **editie-upgrades** . 

   ![Selectie voor het bijwerken van de versie van SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Selecteer **volgende** totdat u de pagina **gereed voor de upgrade-editie** hebt bereikt en selecteer vervolgens **upgrade**. Het installatie venster reageert mogelijk enkele minuten niet meer wanneer de wijziging wordt doorgevoerd. Op een **volledige** pagina wordt bevestigd dat uw editie-upgrade is voltooid. 

Nadat de SQL Server-editie is bijgewerkt, wijzigt u de eigenschap Edition van de virtuele machine SQL Server in de Azure Portal zoals eerder weer gegeven. Hiermee worden de meta gegevens en de facturering bijgewerkt die aan deze virtuele machine zijn gekoppeld.

## <a name="downgrade-an-edition"></a>Een editie downgradeen

Als u de editie van SQL Server wilt downgradeen, moet u SQL Server volledig verwijderen en opnieuw installeren met de gewenste installatie media van de editie.

> [!WARNING]
> Het verwijderen van SQL Server kan extra downtime opleveren. 

U kunt de editie van SQL Server downgrade door de volgende stappen uit te voeren:

1. Maak een back-up van alle data bases, met inbegrip van de systeem databases. 
1. Verplaats systeem databases (Master, model en msdb) naar een nieuwe locatie. 
1. SQL Server en alle gekoppelde services volledig verwijderen. 
1. Start de virtuele machine opnieuw op. 
1. Installeer SQL Server met behulp van de media met de gewenste versie van SQL Server.
1. Installeer de nieuwste service packs en cumulatieve updates.  
1. Vervang de nieuwe systeem databases die tijdens de installatie zijn gemaakt met de systeem databases die u eerder naar een andere locatie hebt verplaatst. 

Nadat de SQL Server-editie is gedowngraded, wijzigt u de eigenschap Edition van de virtuele machine SQL Server in de Azure Portal zoals eerder weer gegeven. Hiermee worden de meta gegevens en de facturering bijgewerkt die aan deze virtuele machine zijn gekoppeld.

## <a name="remarks"></a>Opmerkingen

- De editie-eigenschap voor de SQL Server virtuele machine moet overeenkomen met de editie van het SQL Server exemplaar dat is geïnstalleerd voor alle SQL Server virtual machines, inclusief betalen per gebruik en uw eigen licentie typen licenties.
- Als u uw SQL Server VM-resource weghaalt, gaat u terug naar de in code vastgelegde editie van de installatie kopie.
- De mogelijkheid om de editie te wijzigen, is een functie van de resource provider van de SQL-VM. Als u een installatie kopie van Azure Marketplace implementeert via de Azure Portal, wordt automatisch een SQL Server VM geregistreerd bij de resource provider. Klanten die zelf een SQL Server installeren, moeten echter [hun SQL Server virtuele machine](virtual-machines-windows-sql-register-with-resource-provider.md)hand matig registreren.
- Voor het toevoegen van een SQL Server-VM aan een beschikbaarheidsset moet de virtuele machine opnieuw worden gemaakt. Alle Vm's die zijn toegevoegd aan een beschikbaarheidsset, worden teruggestuurd naar de standaard editie en de editie moet opnieuw worden gewijzigd.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Prijs informatie voor SQL Server op een Windows-VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


