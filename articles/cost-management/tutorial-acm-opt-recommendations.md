---
title: 'Zelf studie: Azure-kosten reduceren met aanbevelingen voor optimalisatie | Microsoft Docs'
description: Deze zelf studie helpt u bij het reduceren van de kosten van Azure wanneer u de aanbevelingen voor optimalisatie uitvoert.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 603de4d9bed936ecb91f130b0e30f6d1383a9092
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935847"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Zelf studie: kosten optimaliseren op basis van aanbevelingen

Azure Cost Management werkt met Azure Advisor voor aanbevelingen om kosten te optimaliseren. Azure Advisor helpt u om de efficiëntie te optimaliseren en te verbeteren door inactieve en onderbenutte resources te identificeren. In deze zelf studie leert u een voor beeld waarin u onderbenutte Azure-resources identificeert en vervolgens actie onderneemt om de kosten te verlagen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Aanbevelingen voor kosten optimalisatie weer geven om potentiële gebruiks inefficiënties weer te geven
> * Reageren op een aanbeveling om het formaat van een virtuele machine te wijzigen in een rendabelere optie
> * Controleer de actie om te controleren of de grootte van de virtuele machine is gewijzigd

## <a name="prerequisites"></a>Vereisten
Er zijn aanbevelingen beschikbaar voor diverse bereiken en Azure-account typen. Zie [Gegevens van kostenbeheer begrijpen](understand-cost-mgt-data.md) voor de volledige lijst met ondersteunde accounttypen. U moet minimaal leestoegang hebben tot een of meer van de volgende bereiken om kostengegevens te kunnen bekijken. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

- Abonnement
- Resourcegroep

U moet actieve virtuele machines hebben met ten minste 14 dagen aan activiteit.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Aanbevelingen voor kosten optimalisatie weer geven

Als u aanbevelingen voor kosten optimalisatie voor een abonnement wilt weer geven, opent u het gewenste bereik in de Azure Portal en selecteert u **Advisor-aanbevelingen**.

Als u aanbevelingen voor een beheer groep wilt weer geven, opent u het gewenste bereik in de Azure Portal en selecteert u **kosten analyse** in het menu. Gebruik het **bereik** Pill om over te scha kelen naar een ander bereik, zoals een beheer groep. Selecteer **aanbevelingen voor Advisor** in het menu. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

![Aanbevelingen voor Cost Management Advisor weer gegeven in de Azure Portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

De lijst met aanbevelingen identificeert gebruiks inefficiëntie of toont aankoop aanbevelingen die u kunnen helpen om extra geld te besparen. De totale mogelijke kosten per **jaar** worden weer gegeven in het totale aantal dat u kunt opslaan als u alle vm's die voldoen aan de aanbevelings regels, sluit of de toewijzing ervan ongedaan hebt. Als u deze niet wilt afsluiten, kunt u overwegen om het formaat ervan te wijzigen in een goedkopere VM-SKU.

De categorie **impact** , samen met de **mogelijke jaarlijkse besparing**, is ontworpen om aanbevelingen te helpen identificeren die zoveel mogelijk kunnen besparen.

Aanbevelingen voor hoge impact zijn:
- [Gereserveerde instanties van virtuele machines kopen om geld te besparen op kosten voor betalen per gebruik](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimaliseer de uitgaven van de virtuele machine door het formaat ervan te wijzigen of door ondergebruikte instanties te afsluiten](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Standaard opslag gebruiken om Managed Disks moment opnamen op te slaan](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Aanbevelingen voor normale impact zijn:
- [Azure Data Factory pijp lijnen verwijderen die niet werken](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Kosten verlagen door oningerichte ExpressRoute-circuits te elimineren](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Kosten verlagen door niet-actieve virtuele netwerk gateways te verwijderen of opnieuw te configureren](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Reageren op een aanbeveling

Azure Advisor bewaakt het gebruik van de virtuele machine gedurende zeven dagen en identificeert vervolgens gevirtualiseerde virtuele machines. Virtuele machines waarvan het CPU-gebruik vijf% of minder is en het netwerk gebruik zeven MB of minder is voor vier of meer dagen, worden als virtuele machines met weinig gebruik beschouwd.

De instelling voor het CPU-gebruik van 5% of minder is de standaard waarde, maar u kunt de instellingen aanpassen. Zie voor meer informatie over het aanpassen van de instelling de [aanbeveling de gemiddelde CPU-gebruiks regel configureren of de aanbevelingen voor de virtuele machine met weinig gebruik](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Hoewel sommige scenario's kunnen leiden tot een laag gebruik door ontwerp, kunt u vaak geld besparen door de grootte van uw virtuele machines te wijzigen in minder dure grootten. De werkelijke besparingen kunnen variëren wanneer u een formaat wijziging kiest. Laten we een voor beeld zien van het wijzigen van de grootte van een virtuele machine.

Klik in de lijst met aanbevelingen op de aanbeveling over het **juiste formaat of het afsluiten van ondergebruikte virtuele machines** . Kies in de lijst met kandidaten voor virtuele machines een virtuele machine waarvan u de grootte wilt wijzigen en klik vervolgens op de virtuele machine. De details van de virtuele machine worden weer gegeven, zodat u de metrische gegevens over het gebruik kunt controleren. De **mogelijke jaarlijkse besparings** waarde is wat u kunt besparen als u de virtuele machine afsluit of verwijdert. Als u het formaat van een virtuele machine wijzigt, bespaart u waarschijnlijk geld, maar bespaart u het volledige bedrag van de mogelijke jaarlijkse besparingen niet.

![Voor beeld van Aanbevelings Details](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Controleer in de VM-Details het gebruik van de virtuele machine om te bevestigen dat het een geschikte grootte van de Candi date is.

![Voor beeld van VM-Details met historisch gebruik](./media/tutorial-acm-opt-recommendations/vm-details.png)

Let op de grootte van de huidige virtuele machine. Nadat u hebt gecontroleerd of de grootte van de virtuele machine moet worden gewijzigd, sluit u de VM-Details zodat u de lijst met virtuele machines ziet.

Selecteer in de lijst met kandidaten die moeten worden afgesloten, de optie * * formaat *&lt;FromVirtualMachineSKU&gt;* aan *&lt;ToVirtualMachineSKU&gt;* * *.
![voor beeld van aanbeveling met de optie om de grootte van de virtuele machine te wijzigen](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Vervolgens wordt er een lijst met beschik bare opties voor formaat wijziging weer gegeven. Kies het abonnement dat de beste prestaties en kosten effectiviteit biedt voor uw scenario. In het volgende voor beeld wordt de gekozen optie grootte gewijzigd van **Standard_D8s_v3** in **Standard_D2s_v3**.

![Een voor beeld van een lijst met beschik bare VM-grootten waarin u een grootte kunt kiezen](./media/tutorial-acm-opt-recommendations/choose-size.png)

Nadat u een geschikte grootte hebt gekozen, klikt u op **formaat wijzigen** om de actie formaat wijzigen te starten.

Voor het verg Roten/verkleinen moet een actieve virtuele machine opnieuw worden opgestart. Als de virtuele machine zich in een productie omgeving bevindt, wordt u aangeraden de bewerking voor het wijzigen van de grootte na kantoor uren uit te voeren. Het plannen van het opnieuw opstarten kan leiden tot onderbrekingen die zijn veroorzaakt door tijdelijk niet beschik baarheid.

## <a name="verify-the-action"></a>De actie controleren

Wanneer de grootte van de VM is gewijzigd, wordt een Azure-melding weer gegeven.

![De melding van de virtuele machine is gewijzigd](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Aanbevelingen voor kosten optimalisatie weer geven om potentiële gebruiks inefficiënties weer te geven
> * Reageren op een aanbeveling om het formaat van een virtuele machine te wijzigen in een rendabelere optie
> * Controleer de actie om te controleren of de grootte van de virtuele machine is gewijzigd

Als u het artikel Cost Management Best practices nog niet hebt gelezen, beschikt u over hoogwaardige hulp en principes waarmee u rekening moet houden met het beheer van de kosten.

> [!div class="nextstepaction"]
> [Aanbevolen procedures Cost Management](cost-mgt-best-practices.md)
