---
title: Azure Automation gebruiken om uit te vinden welke software is geïnstalleerd op uw computers | Microsoft Docs
description: Inventaris gebruiken om uit te vinden welke software op de computers in uw omgeving is geïnstalleerd.
services: automation
keywords: inventaris, automation, wijziging, bijhouden
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: 136521799dbc928a03c339ecc1cef6fdd3d029b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239663"
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Uitvinden welke software is geïnstalleerd op uw Azure- en niet-Azure-computers

In deze zelfstudie leert u hoe u kunt uitvinden welke software er is geïnstalleerd in uw omgeving. U kunt inventarisgegevens verzamelen en weergeven voor software, bestanden, Linux-daemons, Windows-services en Windows-registersleutels op uw computers. Door de configuraties van uw computer bij te houden, is het voor u gemakkelijker vast te stellen waar in uw omgeving zich operationele problemen voordoen, en kunt u beter beoordelen wat de status van uw computers is.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De oplossing inschakelen
> * Onboarding van een Azure-VM uitvoeren
> * Onboarding van een niet-Azure VM
> * Geïnstalleerde software weergeven
> * Inventarislogboeken doorzoeken op geïnstalleerde software

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Een Azure-abonnement. Als je er nog geen hebt, kun je [je MSDN-abonneevoordeel activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of je aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Een [Automation-account](automation-offering-get-started.md) voor het opslaan van de watcher- en actie-runbooks en de Watcher-taak.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md) voor de onboarding.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

Voor deze zelfstudie moet u eerst Wijzigingen bijhouden en Inventaris inschakelen. Als u de **Wijzigingen bijhouden**-oplossing in al eerder hebt ingeschakeld, is deze stap niet nodig.

Navigeer naar uw Automation-account en selecteer **Inventaris** onder **CONFIGURATIEBEHEER**.

Kies de werkruimte Log Analytics en het automatiseringsaccount en klik op **Inschakelen** om de oplossing in te schakelen. Het duurt maximaal 15 minuten om de oplossing in te schakelen.

![Banner voor configuratie van onboarden Inventaris](./media/automation-tutorial-installed-software/enableinventory.png)

Als u de oplossing wilt inschakelen, configureert u de locatie, de werkruimte Logboekanalyse en het automatiseringsaccount om te gebruiken en klikt u op **Inschakelen**. Als de velden niet beschikbaar zijn, betekent dit dat er een andere automatiseringsoplossing is ingeschakeld voor de VM en dat dezelfde werkruimte en hetzelfde Automation-account moeten worden gebruikt.

Een [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-werkruimte wordt gebruikt om gegevens te verzamelen die worden gegenereerd door functies en services zoals Inventaris.
De werkruimte biedt één locatie om gegevens uit meerdere bronnen te bekijken en te analyseren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Het inschakelen van de oplossing kan maximaal 15 minuten duren. Gedurende deze tijd mag u het browservenster niet sluiten.
Nadat de oplossing is ingeschakeld, wordt informatie over geïnstalleerde software en wijzigingen aan de VM-stromen naar Azure Monitor-logboeken verzonden.
Het duurt tussen 30 minuten en 6 uur voordat de gegevens beschikbaar zijn voor analyse.

## <a name="onboard-a-vm"></a>Onboarding van een VM

Navigeer in uw Automation-account naar **Inventaris** onder **CONFIGURATIEBEHEER**.

Selecteer **+ Azure-VM toevoegen**. Hierdoor wordt de pagina **Virtuele machines** geopend en kunt u een bestaande VM uit de lijst selecteren. Selecteer de VM die u wilt vrijgeven. Klik op de pagina die wordt geopend op **Inschakelen** om de oplossing op de VM in te schakelen. De Microsoft Management Agent wordt geïmplementeerd op de VM en de agent wordt geconfigureerd voor communicatie met de Log Analytics-werkruimte die u hebt geconfigureerd bij het inschakelen van de oplossing. Het voltooien van deze stap van de onboarding kan een paar minuten duren. Op dit punt kunt u een nieuwe VM in de lijst selecteren en een andere VM vrijgeven.

## <a name="onboard-a-non-azure-machine"></a>Onboarding van een niet-Azure-machine

Als u niet-Azure-machines wilt toevoegen, installeert u de agent voor [Windows](../azure-monitor/platform/agent-windows.md) of [ Linux](automation-linux-hrw-install.md), afhankelijk van uw besturingssysteem. Nadat de agent is geïnstalleerd, navigeert u naar uw Automation-account en gaat u naar **Inventaris** onder **CONFIGURATIEBEHEER**. Wanneer u op **Machines beheren** klikt, ziet u een lijst van de computers die rapporteren aan uw Log Analytics-werkruimte en waarvoor de oplossing niet is ingeschakeld. Selecteer de gewenste optie voor uw omgeving.

* **Inschakelen op alle beschikbare machines** - met deze optie schakelt u de oplossing in op alle computers die momenteel aan uw Log Analytics-werkruimte rapporteren.
* **Inschakelen op alle beschikbare machines en toekomstige machines** -met deze optie schakelt u de oplossing in op alle computers die momenteel aan uw Log Analytics-werkruimte rapporteren en op alle toekomstige computers die aan de werkruimte worden toegevoegd.
* **Inschakelen op de geselecteerde machines** - met deze optie schakelt u de oplossing alleen in op de machines die u hebt geselecteerd.

![Machines beheren](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Geïnstalleerde software weergeven

Zodra de oplossing Wijzigingen bijhouden en Inventaris is ingeschakeld, kunt u de resultaten op de pagina **Inventaris** bekijken.

Kies vanuit uw Automation-account **Inventaris** onder ** CONFIGURATIEBEHEER**.

Op de pagina **Inventaris** klikt u op het tabblad **Software**.

Op het tabblad **Software** bevindt zich een tabel met de gevonden software. De software is gegroepeerd op de naam en versie van de software.

De algemene details voor elke softwarerecord worden in de tabel weergegeven. Deze gegevens omvatten de naam van de software, de versie, uitgever, tijd laatst vernieuwd (het meest recente tijdstip waarop is vernieuwd, gemeld door een computer in de groep) en de computers (het aantal computers met die software).

![Software-inventaris](./media/automation-tutorial-installed-software/inventory-software.png)

Klik op een rij om de eigenschappen van de softwarerecord te bekijken evenals de namen van de computers met die software.

Als u wilt zoeken naar specifieke software of de softwaregroep, kunt u dat doen via het tekstvak direct boven de lijst met software.
Het filter kunt u gebruiken om te zoeken op basis van de naam van de software, de versie of de uitgever.

Als u bijvoorbeeld zoekt naar 'Contoso', wordt alle software met 'Contoso' in de naam, uitgever of versie geretourneerd.

## <a name="search-inventory-logs-for-installed-software"></a>Inventarislogboeken doorzoeken op geïnstalleerde software

Inventaris genereert logboekgegevens die naar Azure Monitor-logboeken worden verzonden. Als u de logboeken wilt doorzoeken door query's uit te voeren, selecteert u **Log Analytics** boven in het venster **Inventaris**.

Inventarisgegevens worden opgeslagen onder het type **ConfigurationData**.
De volgende voorbeeldquery voor Log Analytics retourneert de inventarisatieresultaten waar de uitgever gelijk is aan "Microsoft Corporation".

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Zie [Azure Monitor-logboeken](../azure-monitor/log-query/log-query-overview.md) voor meer informatie over het uitvoeren en doorzoeken van logboekbestanden in Azure Monitor-logboeken.

### <a name="single-machine-inventory"></a>Inventaris van één computer

Als u een overzicht van de software voor één computer wilt zien, kunt u Inventaris openen vanuit de pagina met VM-resources in Azure, of kunt u met behulp van de filterfunctie in Azure Monitor-logboeken naar de overeenkomstige computer gaan.
De volgende voorbeeldquery van Log Analytics retourneert de lijst met software voor een computer met de naam ContosoVM.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u software-inventaris kunt weergeven om het volgende te kunnen doen:

> [!div class="checklist"]
> * De oplossing inschakelen
> * Onboarding van een Azure-VM uitvoeren
> * Onboarding van een niet-Azure VM
> * Geïnstalleerde software weergeven
> * Inventarislogboeken doorzoeken op geïnstalleerde software

Ga verder naar het overzicht van de oplossing Wijzigingen bijhouden en Inventaris als u er meer over wilt weten.

> [!div class="nextstepaction"]
> [De oplossing Wijzigingen bijhouden en Inventaris](automation-change-tracking.md)
