---
title: 'Zelfstudie: Azure-kosten verlagen met behulp van aanbevelingen'
description: Deze zelfstudie helpt u bij het verlagen van de kosten van Azure door aanbevelingen voor optimalisatie uit te voeren.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 04b3802ae17440fe7cb96b424727600ab53aefce
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168769"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Zelfstudie: Kosten optimaliseren op basis van aanbevelingen

Azure Cost Management werkt met Azure Advisor voor aanbevelingen om kosten te optimaliseren. Azure Advisor helpt u om de efficiëntie te optimaliseren en te verbeteren door inactieve en onderbenutte resources te identificeren. In deze zelfstudie identificeert u onderbenutte Azure-resources waarna u actie onderneemt om de bijbehorende kosten te verlagen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Optimalisatieaanbevelingen voor kosten bekijken om mogelijk inefficiënt gebruik te zien
> * Actie ondernemen op een aanbeveling om de grootte van een virtuele machine te wijzigen in een meer rendabele optie
> * De genomen actie controleren om er zeker van te zijn dat de grootte van de virtuele machine is gewijzigd

## <a name="prerequisites"></a>Vereisten
Er zijn aanbevelingen beschikbaar voor diverse bereiken en typen Azure-accounts. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](understand-cost-mgt-data.md). U moet minimaal leestoegang hebben tot een of meer van de volgende bereiken om kostengegevens te kunnen bekijken. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken.

- Abonnement
- Resourcegroep

U moet actieve virtuele machines hebben met ten minste 14 dagen aan activiteit.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Aanbevelingen voor kostenoptimalisatie bekijken

Als u aanbevelingen voor kostenoptimalisatie voor een abonnement wilt bekijken, opent u het gewenste bereik in de Azure-portal en selecteert u **Advisor-aanbevelingen**.

Als u aanbevelingen voor een beheergroep wilt bekijken, opent u het gewenste bereik in de Azure-portal en selecteert u **Kostenanalyse** in het menu. Gebruik het besturingselement **Bereik** om over te schakelen naar een ander bereik, zoals een beheergroep. Selecteer **Advisor-aanbevelingen** in het menu. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken.

![Aanbevelingen voor Kostenbeheer in de Azure-portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

In de lijst met aanbevelingen worden gebruiksinefficiënties vermeld of ziet u aankoopaanbevelingen die u kunnen helpen om extra geld te besparen. Het bedrag bij **Potentiële jaarlijkse besparingen** is het totale bedrag dat u kunt besparen als u alle VM's afsluit die voldoen aan de aanbevelingsregels. U kunt er ook voor kiezen om de toewijzing van deze VM's ongedaan te maken. Als u deze VM's niet wilt afsluiten, is het raadzaam om de grootte te wijzigen in een goedkopere VM-SKU.

Aan de hand van de categorie **Impact**, in combinatie met de waarde voor **Potentiële jaarlijkse besparingen**, kunt u snel zien met welke aanbevelingen u potentieel zoveel mogelijk kunt besparen.

Enkele voorbeelden van aanbevelingen met een hoge impact:
- [Gereserveerde instanties van virtuele machines kopen om kosten te besparen ten opzichte van betalen per gebruik](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Uitgaven aan virtuele machines optimaliseren door de grootte ervan te wijzigen of door onderbenutte instanties af te sluiten](../../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Standard Storage gebruiken voor het opslaan van Managed Disks-momentopnamen](../../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Voorbeelden van aanbevelingen met een gemiddelde impact:
- [Azure Data Factory-pijplijnen verwijderen die niet werken](../../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Kosten verlagen door niet-ingerichte ExpressRoute-circuits te verwijderen](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Kosten verlagen door niet-actieve virtuele netwerkgateways te verwijderen of opnieuw te configureren](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Actie ondernemen op een aanbeveling

Azure Advisor controleert het gebruik van virtuele machines gedurende zeven dagen en identificeert vervolgens onderbenutte virtuele machines. Virtuele machines waarvan het CPU-gebruik vijf procent of minder is en het netwerkgebruik zeven MB of minder is gedurende vier of meer dagen, worden als virtuele machines met weinig gebruik beschouwd.

De instelling voor het CPU-gebruik van 5% of minder is de standaardwaarde, maar u kunt de instelling aanpassen. Zie [Aanbeveling voor weinig gebruik van VM configureren](../../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation) voor meer informatie over het aanpassen van de instelling.

Hoewel bepaalde scenario’s met opzet een laag gebruik tot gevolg hebben, kunt u vaak geld besparen door de grootte van uw virtuele machines te wijzigen in goedkopere formaten. De werkelijke besparingen kunnen variëren wanneer u een andere grootte kiest. Laten we een voorbeeld bekijken van het wijzigen van de grootte van een virtuele machine.

Klik in de lijst met aanbevelingen op de aanbeveling **Beperkt gebruikte virtuele machine afsluiten of de grootte ervan wijzigen**. Kies in de lijst met kandidaten van virtuele machines een virtuele machine waarvan u de grootte wilt wijzigen en klik vervolgens op de virtuele machine. De details van de virtuele machine worden weergegeven, zodat u de metrische gegevens over het gebruik kunt controleren. De waarde voor **Potentiële jaarlijkse besparingen** geeft aan wat u kunt besparen als u de VM afsluit of verwijdert. Als u de grootte van een VM machine wijzigt, bespaart u waarschijnlijk geld, maar niet het volledige bedrag van de potentiële jaarlijkse besparingen.

![Voorbeeld van details van aanbeveling](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Controleer in de VM-details het gebruik van de VM om te bevestigen dat het een geschikte kandidaat is voor een kleinere grootte.

![Voorbeeld van VM-details met historisch gebruik](./media/tutorial-acm-opt-recommendations/vm-details.png)

Kijk naar de huidige grootte van de virtuele machine. Nadat u hebt vastgesteld dat de grootte van de virtuele machine moet worden gewijzigd, sluit u de VM-details zodat u de lijst met virtuele machines ziet.

Selecteer **Formaat wijzigen _&lt;SKUVanVirtueleMachine&gt;_ naar _&lt;SKUNaarVirtueleMachine&gt;_** in de lijst met VM's die in aanmerking komen voor afsluiten of een kleinere grootte.
![Voorbeeld van aanbeveling met de optie om de grootte van de virtuele machine te wijzigen](./media/tutorial-acm-opt-recommendations/resize-vm.png)

U ziet nu een lijst met beschikbare opties voor het wijzigen van de grootte. Kies de grootte met het gewenste compromis tussen prestaties en kosten voor uw scenario. In het volgende voorbeeld wordt de huidige grootte gewijzigd van **Standard_D8s_v3** in **Standard_D2s_v3**.

![Voorbeeld van een lijst met beschikbare VM-grootten waarin u een grootte kunt kiezen](./media/tutorial-acm-opt-recommendations/choose-size.png)

Nadat u een geschikte grootte hebt gekozen, klikt u op **Formaat wijzigen** om de wijziging door te voeren.

Voor het aanpassen van de grootte moet er een virtuele machine actief zijn die opnieuw kan worden opgestart. Als de virtuele machine zich in een productieomgeving bevindt, is het raadzaam om de bewerking van het wijzigen van de grootte na kantooruren uit te voeren. Het plannen van het opnieuw opstarten van de VM kan namelijk leiden tot onderbrekingen als gevolg van tijdelijke niet-beschikbaarheid.

## <a name="verify-the-action"></a>De actie controleren

Wanneer de grootte van de VM is gewijzigd, wordt er een melding van Azure weergegeven.

![Melding dat de grootte van de virtuele machine is gewijzigd](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Optimalisatieaanbevelingen voor kosten bekijken om mogelijk inefficiënt gebruik te zien
> * Actie ondernemen op een aanbeveling om de grootte van een virtuele machine te wijzigen in een meer rendabele optie
> * De genomen actie controleren om er zeker van te zijn dat de grootte van de virtuele machine is gewijzigd

Als u het artikel met best practices voor Cost Management nog niet hebt gelezen, is dat zeker een aanrader als u meer wilt weten over algemene richtlijnen en principes voor kostenbeheersing.

> [!div class="nextstepaction"]
> [Best practices voor Cost Management](cost-mgt-best-practices.md)
