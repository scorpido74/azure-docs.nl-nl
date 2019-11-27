---
title: 'Zelfstudie: Azure verlagen met aanbevelingen voor optimalisatie | Microsoft Docs'
description: Deze zelfstudie helpt u om Azure-kosten verlagen wanneer u actie op optimalisatie aanbevelingen ondernemen.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a9dbb121cab49024aaf0dc65bbac938764d9f8b2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229837"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Zelfstudie: Kosten van aanbevelingen optimaliseren

Azure Cost Management werkt met Azure Advisor voor aanbevelingen om kosten te optimaliseren. Azure Advisor helpt u om de efficiëntie te optimaliseren en te verbeteren door inactieve en onderbenutte resources te identificeren. Deze zelfstudie leert u een voorbeeld waarin u weinig gebruikte Azure-resources identificeren en vervolgens het ondernemen van actie om kosten te verlagen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Optimalisatie van aanbevelingen om weer te geven inefficiënt mogelijke gebruik weergeven
> * Reageren op een aanbeveling klikken om het formaat van een virtuele machine naar een meer voordelige optie zijn
> * Controleer of de actie om ervoor te zorgen dat de virtuele machine met succes is gewijzigd

## <a name="prerequisites"></a>Vereisten
Er zijn aanbevelingen beschikbaar voor diverse bereiken en Azure-account typen. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](understand-cost-mgt-data.md). U moet minimaal leestoegang hebben tot een of meer van de volgende bereiken om kostengegevens te kunnen bekijken. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

- Abonnement
- Resourcegroep

U moet actieve virtuele machines met ten minste 14 dagen van de activiteit hebben.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Optimalisatie aanbevelingen weergeven

Als u aanbevelingen voor kosten optimalisatie voor een abonnement wilt weer geven, opent u het gewenste bereik in de Azure Portal en selecteert u **Advisor-aanbevelingen**.

Als u aanbevelingen voor een beheer groep wilt weer geven, opent u het gewenste bereik in de Azure Portal en selecteert u **kosten analyse** in het menu. Gebruik het **bereik** Pill om over te scha kelen naar een ander bereik, zoals een beheer groep. Selecteer **aanbevelingen voor Advisor** in het menu. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

![Aanbevelingen voor kosten Management Advisor wordt weergegeven in de Azure-portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

De lijst met aanbevelingen inefficiënt gebruik Hiermee wordt aangegeven of aanschafaanbevelingen die u kunnen helpen extra geld besparen. De totale mogelijke kosten per **jaar** worden weer gegeven in het totale aantal dat u kunt opslaan als u alle vm's die voldoen aan de aanbevelings regels, sluit of de toewijzing ervan ongedaan hebt. Als u niet dat ze uitschakelen wilt, kunt u overwegen ze naar een minder dure VM-SKU vergroten of verkleinen.

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

Azure Advisor bewaakt het gebruik van de virtuele machine gedurende zeven dagen en identificeert vervolgens gevirtualiseerde virtuele machines. Virtuele machines waarvan CPU-gebruik is vijf procent of minder en netwerkgebruik is 7 MB of minder voor vier of meer dagen worden beschouwd als laag gebruik virtuele machines.

De 5% of minder CPU-gebruik instelling is de standaardinstelling, maar u kunt de instellingen aanpassen. Zie voor meer informatie over het aanpassen van de instelling de [aanbeveling de gemiddelde CPU-gebruiks regel configureren of de aanbevelingen voor de virtuele machine met weinig gebruik](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Hoewel sommige scenario's in laag gebruik standaard resulteren kunnen, kunt u vaak geld besparen door de grootte van uw virtuele machines te wijzigen naar minder dure grootten. De daadwerkelijke besparingen kunnen variëren als u ervoor een actie formaat wijzigen kiest. We nemen een voorbeeld van het formaat van een virtuele machine.

Klik in de lijst met aanbevelingen op de aanbeveling over het **juiste formaat of het afsluiten van ondergebruikte virtuele machines** . Kies in de lijst met kandidaten voor virtuele machine, een virtuele machine vergroten of verkleinen en klik vervolgens op de virtuele machine. Details van de virtuele machine worden weergegeven zodat u kunt controleren of de metrische gegevens over het Resourcegebruik. De **mogelijke jaarlijkse besparings** waarde is wat u kunt besparen als u de virtuele machine afsluit of verwijdert. Formaat van een virtuele machine waarschijnlijk bespaart u geld, maar u het volledige bedrag van de mogelijke jaarlijkse besparingen worden niet opgeslagen.

![Voorbeeld van details van de aanbeveling](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Controleer in de virtuele machine, het gebruik van de virtuele machine om te bevestigen dat het een kandidaat een geschikte grootte.

![Voorbeeld van de virtuele machine details historische gebruik weergeven](./media/tutorial-acm-opt-recommendations/vm-details.png)

Houd er rekening mee grootte van de huidige virtuele machine. Nadat u hebt gecontroleerd dat de virtuele machine moet worden gewijzigd, sluit u de details van de virtuele machine, zodat u de lijst met virtuele machines.

Selecteer in de lijst met kandidaten die moeten worden afgesloten, de optie * * formaat *&lt;FromVirtualMachineSKU&gt;* aan *&lt;ToVirtualMachineSKU&gt;* * *.
![voor beeld van aanbeveling met de optie om de grootte van de virtuele machine te wijzigen](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Vervolgens krijgt u een lijst met beschikbare formaat opties. Kies de methode die de beste prestaties en kosteneffectiviteit voor uw scenario krijgt. In het volgende voor beeld wordt de gekozen optie grootte gewijzigd van **Standard_D8s_v3** in **Standard_D2s_v3**.

![Voorbeeld van de lijst met beschikbare VM-grootten, waar u kunt een grootte kiezen](./media/tutorial-acm-opt-recommendations/choose-size.png)

Nadat u een geschikte grootte hebt gekozen, klikt u op **formaat wijzigen** om de actie formaat wijzigen te starten.

Formaat wijzigen, moet een actief actieve virtuele machine opnieuw op te starten. Als de virtuele machine zich in een productieomgeving, wordt u aangeraden de bewerking formaat wijzigen na kantooruren uit te voeren. Het opstarten van de planning inkorten onderbrekingen veroorzaakt door tijdelijk niet beschikbaar zijn.

## <a name="verify-the-action"></a>Controleer of de actie

Wanneer de VM-grootte voltooid is, ziet u een melding van Azure.

![Melding van de virtuele machine van de geslaagde gewijzigd](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Optimalisatie van aanbevelingen om weer te geven inefficiënt mogelijke gebruik weergeven
> * Reageren op een aanbeveling klikken om het formaat van een virtuele machine naar een meer voordelige optie zijn
> * Controleer of de actie om ervoor te zorgen dat de virtuele machine met succes is gewijzigd

Als u de aanbevolen procedures voor kostenbeheer al nog niet hebt gelezen en biedt uitgebreide hulp en principes rekening houden met het beheer van kosten.

> [!div class="nextstepaction"]
> [Aanbevolen procedures Cost Management](cost-mgt-best-practices.md)
