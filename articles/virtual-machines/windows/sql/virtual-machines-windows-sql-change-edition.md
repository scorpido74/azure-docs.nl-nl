---
title: In-place wijziging van SQL Server-editie
description: Meer informatie over het wijzigen van de editie van uw virtuele SQL Server-machine in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605456"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>In-place wijziging van SQL Server-editie op Azure VM

In dit artikel wordt beschreven hoe u de editie van SQL Server op een virtuele Windows-machine in Azure wijzigen. 

De editie van SQL Server wordt bepaald door de productcode en wordt tijdens het installatieproces opgegeven met behulp van de installatiemedia. De editie bepaalt welke [functies](/sql/sql-server/editions-and-components-of-sql-server-2017) beschikbaar zijn in het SQL Server-product. U de SQL Server-editie wijzigen met de installatiemedia en downgraden om de kosten te verlagen of te upgraden om meer functies in te schakelen.

Zodra de editie van SQL Server intern is gewijzigd in de SQL Server VM, moet u de eigenschap edition van SQL Server in de Azure-portal bijwerken voor factureringsdoeleinden. 

## <a name="prerequisites"></a>Vereisten

Als u een in-place wijziging van de editie van SQL Server wilt doen, hebt u het volgende nodig: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een [SQL Server VM op Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) die is geregistreerd bij de SQL [VM-resourceprovider.](virtual-machines-windows-sql-register-with-resource-provider.md)
- Installatiemedia met de **gewenste editie** van SQL Server. Klanten die [Software Assurance hebben,](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kunnen hun installatiemedia verkrijgen bij het [Volume Licensing Center.](https://www.microsoft.com/Licensing/servicecenter/default.aspx) Klanten die geen Software Assurance hebben, kunnen de installatiemedia gebruiken van een Azure Marketplace SQL `C:\SQLServerFull`Server VM-afbeelding met de gewenste editie (meestal gevestigd in ). 


## <a name="upgrade-an-edition"></a>Een editie upgraden

> [!WARNING]
> Als u de editie van SQL Server upgradet, wordt de service voor SQL Server opnieuw opgestart, samen met bijbehorende services, zoals Analysis Services en R Services. 

Als u de editie van SQL Server wilt upgraden, verkrijgt u de SQL Server-installatiemedia voor de gewenste editie van SQL Server en gaat u als volgt te werk:

1. Open Setup.exe vanaf de SQL Server-installatiemedia. 
1. Ga naar **Onderhoud** en kies de optie **Upgrade van editie.** 

   ![Selectie voor het upgraden van de editie van SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Selecteer **Volgende** totdat u de pagina **Klaar voor een upgrade-editie** bereikt en selecteer **Vervolgens Upgrade**. Het installatievenster reageert mogelijk enkele minuten niet meer terwijl de wijziging van kracht wordt. Een **pagina Voltooien** bevestigt dat de upgrade van uw editie is voltooid. 

Nadat de SQL Server-editie is bijgewerkt, wijzigt u de eigenschap edition van de virtuele SQL Server-machine in de Azure-portal. Hiermee worden de metagegevens en facturering die aan deze VM zijn gekoppeld, bijgewerkt.

## <a name="downgrade-an-edition"></a>Een editie downgraden

Als u de editie van SQL Server wilt downgraden, moet u SQL Server volledig verwijderen en opnieuw installeren met de gewenste installatiemedia van de editie. 

> [!WARNING]
> Als u SQL Server installeert, kan dit leiden tot extra downtime. 

U de editie van SQL Server downgraden door de volgende stappen te volgen:

1. Een back-up maken van alle databases, inclusief de systeemdatabases. 
1. Systeemdatabases (master, model en msdb) verplaatsen naar een nieuwe locatie. 
1. Verwijder SQL Server en alle bijbehorende services volledig. 
1. Start de virtuele machine opnieuw op. 
1. Installeer SQL Server met behulp van de media met de gewenste editie van SQL Server.
1. Installeer de nieuwste servicepacks en cumulatieve updates.  
1. Vervang de nieuwe systeemdatabases die tijdens de installatie zijn gemaakt door de systeemdatabases die u eerder naar een andere locatie hebt verplaatst. 

Nadat de SQL Server-editie is gedegradeerd, wijzigt u de eigenschap edition van de virtuele SQL Server-machine in de Azure-portal. Hiermee worden de metagegevens en facturering die aan deze VM zijn gekoppeld, bijgewerkt.

## <a name="change-edition-in-portal"></a>Editie wijzigen in portal 

Zodra u de editie van SQL Server hebt gewijzigd met behulp van de installatiemedia en u uw SQL Server VM hebt geregistreerd bij de [SQL VM-bronprovider,](virtual-machines-windows-sql-register-with-resource-provider.md)u vervolgens de Azure-portal gebruiken om de eigenschap Edition van de SQL Server VM te wijzigen voor factureringsdoeleinden. Hiervoor volgt u de volgende stappen: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
1. Ga naar uw SQL Server virtuele machinebron. 
1. Selecteer **Onder Instellingen**de optie **Configureren**. Selecteer vervolgens de gewenste versie van SQL Server in de vervolgkeuzelijst onder **Edition**. 

   ![Metagegevens van edities wijzigen](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Controleer de waarschuwing waarin staat dat u eerst de SQL Server-editie moet wijzigen en dat de eigenschap-Edition moet overeenkomen met de SQL Server-editie. 
1. Selecteer **Toepassen** om wijzigingen in de metagegevens van uw editie toe te passen. 


## <a name="remarks"></a>Opmerkingen

- De eigenschap edition voor de SQL Server VM moet overeenkomen met de editie van het SQL Server-exemplaar dat is geïnstalleerd voor alle virtuele SQL Server-machines, inclusief zowel pay-as-you-go- als bring-your-own-license-typen licenties.
- Als u uw SQL Server VM-bron laat vallen, gaat u terug naar de hard-coded editieinstelling van de afbeelding.
- De mogelijkheid om de editie te wijzigen is een functie van de SQL VM-resourceprovider. Als u een Azure Marketplace-afbeelding implementeert via de Azure-portal, wordt automatisch een SQL Server VM bij de resourceprovider geregistreerd. Klanten die SQL Server zelf installeren, moeten hun SQL Server VM echter handmatig [registreren.](virtual-machines-windows-sql-register-with-resource-provider.md)
- Als u een SQL Server VM toevoegt aan een beschikbaarheidsset, moet de VM opnieuw worden gemaakt. Alle VM's die aan een beschikbaarheidsset zijn toegevoegd, gaan terug naar de standaardeditie en de editie moet opnieuw worden gewijzigd.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-vm](virtual-machines-windows-sql-server-iaas-faq.md)
* [Richtlijnen voor prijzen voor SQL Server op een Windows-vm](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notities voor SQL Server vrijgeven op een Windows-vm](virtual-machines-windows-sql-server-iaas-release-notes.md)


