---
title: 'Zelfstudie: kosten van gereserveerde instanties optimaliseren met Cloudyn in Azure| Microsoft Docs'
description: In deze zelfstudie leert u hoe u de kosten van gereserveerde instanties voor Azure en Amazon Web Services (AWS) kunt optimaliseren.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: tutorial
ms.service: cost-management-billing
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: ade3b63116337ae7b4678b459b1a0c388092f409
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229864"
---
<!-- Intent: As a cloud-consuming administrator, I need to ensure that my reserved instances are optimized for cost and usage
-->

# <a name="tutorial-optimize-reserved-instances"></a>Zelfstudie: gereserveerde instanties optimaliseren

In deze zelfstudie leert u hoe u de kosten en het gebruik van gereserveerde instanties voor Azure en Amazon Web Services (AWS) kunt optimaliseren met Cloudyn. Een gereserveerde instantie bij een provider van cloudservices is een verplichting tot een langetermijncontract waarbij u zich vooraf verplicht tot toekomstig gebruik van de virtuele machine. Dit kan mogelijk aanzienlijke besparingen bieden in vergelijking met het prijsmodel voor virtuele machines waarbij u betaalt op basis van gebruik. Mogelijke besparingen worden alleen gerealiseerd wanneer u de capaciteit van uw gereserveerde instanties volledig gebruikt.

In deze zelfstudie wordt uitgelegd hoe gereserveerde instanties van Azure en AWS worden ondersteund door Cloudyn. Ook wordt beschreven hoe u de kosten van gereserveerde instanties kunt optimaliseren. Zorg er in de eerste plaats voor dat uw gereserveerde instanties volledig worden gebruikt. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Inzicht krijgen in kosten van gereserveerde Azure-instanties
> * De voordelen van gereserveerde instanties
> * De kosten van gereserveerde Azure-instanties optimaliseren
> * De kosten van gereserveerde instanties weergeven
> * De kosteneffectiviteit van gereserveerde Azure-instanties beoordelen
> * De kosten van gereserveerde AWS-instanties optimaliseren
> * Aanbevolen gereserveerde instanties aanschaffen
> * Niet-gebruikte gereserveerde instanties aanpassen

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

- U moet een Azure-account hebben.
- U moet een proefregistratie of een betaald abonnement voor Cloudyn hebben.
- U moet gereserveerde instanties in Azure of AWS hebben aangeschaft.

## <a name="understand-azure-ri-costs"></a>Inzicht krijgen in kosten van gereserveerde Azure-instanties

Wanneer u een gereserveerde instantie van een virtuele machine voor Azure koopt, betaalt u vooraf voor toekomstig gebruik. De betaling vooraf dekt de kosten van uw toekomstige gebruik van de virtuele machines:

- van een bepaald type
- in een specifieke regio
- voor een periode van één of drie jaar
- tot een maximaal aantal aangeschafte VM’s.

U kunt uw aangeschafte gereserveerde instanties van een virtuele machine voor Azure weergeven in Azure Portal in [Reserveringen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

De term _gereserveerde VM-instantie voor Azure_ is alleen van toepassing op een prijsmodel. Dit verandert helemaal niets aan uw actieve virtuele machines. De term is specifiek voor Azure en wordt meer in het algemeen aangeduid als _gereserveerde instantie_ of _reservering_. Gereserveerde instanties die u hebt aangeschaft, zijn niet van toepassing op specifieke virtuele machines - ze gelden voor elke overeenkomende virtuele machine. Bijvoorbeeld een reservering voor een VM-type dat wordt uitgevoerd in een regio die u hebt gekozen voor uw aangeschafte reservering.

Aangeschafte gereserveerde instanties gelden alleen voor de basishardware. Ze hebben geen betrekking op softwarelicenties van een virtuele machine. U kunt bijvoorbeeld een instantie reserveren en u hebt een overeenkomende VM waarop Windows wordt uitgevoerd. De gereserveerde instantie dekt alleen de basiskosten van de virtuele machine. In dit voorbeeld betaalt u de volledige prijs voor alle vereiste Windows-licenties. Voor korting op het besturingssysteem of andere software die op uw virtuele machines worden uitgevoerd, kunt u het gebruik van [Azure Hybrid Benefits](https://azure.microsoft.com/pricing/hybrid-benefit) overwegen. Hybrid Benefits biedt u een vergelijkbaar type korting voor uw softwarelicenties als de gereserveerde instanties doen voor de basis-VM's.

Het gebruik van gereserveerde instanties beïnvloedt de kosten niet rechtstreeks. Met andere woorden, het uitvoeren van een virtuele machine met 100% CPU-gebruik of 0% CPU-gebruik heeft hetzelfde effect: u betaalt vooraf voor de VM-toewijzing, niet voor het werkelijke gebruik.

Laten we eens kijken hoe de kosten van standaard on-demandgebruik van virtuele machines zich verhouden tot de kosten van gereserveerde instanties. Dit is op de volgende afbeelding te zien:

![Kosten van on-demand versus kosten van gereserveerde instanties](./media/tutorial-optimize-reserved-instances/azure01.png)



De rode balken geven de totale kosten weer van de aangeschafte gereserveerde instantie. U betaalt alleen de eenmalige kosten. Het gebruik van de VM is gratis. De blauwe balken geven de totale kosten van dezelfde virtuele machine die wordt gebruikt met het prijsmodel voor betalen op basis van gebruik of on-demand. Ergens tussen de zevende en achtste maand van VM-gebruik is er een *break-evenpoint*. In dit voorbeeld begint u vanaf de achtste maand geld te besparen.

## <a name="benefits-of-ris"></a>Voordelen van gereserveerde instanties

Elke aankoop van een gereserveerde instantie is van toepassing op een virtuele machine van een specifieke grootte en op een specifieke locatie. Bijvoorbeeld: D2s\_v3 die wordt uitgevoerd op de locatie US - west, zoals wordt weergegeven op de volgende afbeelding:

![Details van gereserveerde instanties voor Azure](./media/tutorial-optimize-reserved-instances/azure02.png)

De aanschaf van een gereserveerde instantie wordt voordelig wanneer een virtuele machine voldoende uren wordt uitgevoerd en het break-evenpoint bereikt. De virtuele machine moet overeenkomen met de grootte en de locatie van uw gereserveerde instantie. In de voorgaande grafiek vindt het break-evenpoint plaats na circa zeven en een halve maand. De aanschaf is dus voordelig wanneer de virtuele machine die overeenkomt met de reservering ten minste 7,5 maanden gedurende \* 30 dagen \* 24 uur = 5400 uur wordt uitgevoerd. Als de overeenkomende virtuele machine minder dan 5400 uur wordt uitgevoerd, is de reservering duurder dan betaling op basis van gebruik.

Het break-evenpoint kan voor elke VM-grootte en voor elke locatie verschillend zijn. Het is ook afhankelijk van de prijs die u hebt onderhandeld voor betaling op basis van gebruik. Voordat u tot aanschaf overgaat, moet u het break-evenpoint controleren dat van toepassing is op uw situatie.

Een ander punt waarmee u rekening moet houden wanneer u de reservering aanschaft, is het bereik van de gereserveerde instantie. Het bereik bepaalt of het voordeel van de reservering wordt gedeeld of van toepassing is op een specifiek abonnement. Gedeelde gereserveerde instanties worden willekeurig toegepast in al uw abonnementen op de eerste gevonden overeenkomende VM's.

Een aanschaf waarbij het bereik wordt gedeeld, is het meest flexibel en wordt indien mogelijk altijd aanbevolen. De kans dat alle gereserveerde instanties worden gebruikt, is aanzienlijk hoger met een gedeeld bereik. Wanneer de eigenaar van een abonnement voor de gereserveerde instantie heeft betaald, heeft deze mogelijk geen andere keuze dan te kopen met het bereik van één abonnement.

## <a name="optimize-azure-ri-costs"></a>De kosten van gereserveerde Azure-instanties optimaliseren

Gereserveerde instanties en Hybrid Benefits worden als volgt door Cloudyn ondersteund:

- De kosten van prijsmodellen weergeven
- Gebruik van gereserveerde instanties bijhouden
- De impact van gereserveerde instanties evalueren
- De kosten van gereserveerde instanties toewijzen volgens uw beleid

De eerste actie die u moet ondernemen voordat u een gereserveerde instantie aanschaft, is het beoordelen van de impact van de aangeschafte gereserveerde instantie:

- Hoeveel kost het u?
- Hoeveel bespaart u?
- Wat is het break-evenpoint?

Het rapport over de impact van aanschaf van gereserveerde instanties kan u helpen deze vragen te beantwoorden.

## <a name="assess-azure-ri-cost-effectiveness"></a>De kosteneffectiviteit van gereserveerde Azure-instanties beoordelen

Navigeer in de Cloudyn-portal naar **Optimizer** > **RI Comparison** en selecteer **Reserved Instance Purchase Impact**.

Selecteer in het rapport Reserved Instance Purchase Impact een VM-grootte (instantietype), locatie (regio), reserveringstermijn, aantal en de verwachte runtime. Vervolgens kunt u beoordelen of u met uw aankoop geld bespaart.

Als u bijvoorbeeld een reservering koopt voor een virtuele machine van het type DS1\_v2 in US - oost en deze een heel jaar 24 x 7 uitvoert, kunt u jaarlijks $ 369,48 besparen. Het break-evenpoint is met vijf maanden. Zie de volgende afbeelding:

![Break-evenpoint van gereserveerde instantie voor Azure](./media/tutorial-optimize-reserved-instances/azure03.png)

Als deze slechts 50% van de tijd wordt uitgevoerd, is het break-evenpoint met 10 maanden en bespaart u jaarlijks slechts $ 49,74. Mogelijk levert de aanschaf van de reservering voor dit instantietype u in dit voorbeeld geen voordeel op. Zie de volgende afbeelding:

![Voorbeeld van het break-evenpoint voor Azure-VM's](./media/tutorial-optimize-reserved-instances/azure04.png)

## <a name="view-ri-costs"></a>De kosten van gereserveerde instanties weergeven

Wanneer u een reservering koopt, verricht u een eenmalige betaling. Er zijn twee manieren om de betaling in Cloudyn weer te geven:

- Werkelijke kosten
- Afgeschreven kosten

### <a name="actual-reserved-instance-cost"></a>Werkelijke kosten gereserveerde instanties

In de rapporten met de werkelijke kostenanalyse en analyse gedurende een periode wordt het volledige bedrag weergegeven dat u hebt betaald voor de reservering, met ingang van de maand van aankoop. Zo kunt u uw werkelijke uitgaven gedurende een periode bekijken.

Navigeer naar **Costs** > **Cost Analysis** > in de Cloudyn-portal en selecteer vervolgens **Actual Cost Analysis** of **Actual Cost Over Time**. Stel daarna de filters in. U kunt bijvoorbeeld alleen filteren op Azure/VM-service en groeperen op resourcetype en prijsmodel. Zie de volgende afbeelding:

![Voorbeeld van de werkelijke kosten van gereserveerde instanties](./media/tutorial-optimize-reserved-instances/azure05.png)

U kunt filteren op een service, **Azure/VM** in dit voorbeeld, en groeperen op **prijsmodel** en **resourcetype** zoals weergegeven op de volgende afbeelding:

![Voorbeeld van rapportgroepen en filters voor werkelijke kosten per prijsmodel en resourcetype](./media/tutorial-optimize-reserved-instances/azure06.png)

U kunt ook het soort betalingen dat u hebt verricht, zoals eenmalige kosten, gebruikskosten en licentiekosten, analyseren.

### <a name="amortized-reserved-instance-cost"></a>Afgeschreven kosten gereserveerde instanties

U betaalt kosten vooraf die zichtbaar zijn in de maand van aanschaf wanneer u een gereserveerde instantie koopt. Deze zijn niet zichtbaar op volgende facturen. Het kan dus misleidend zijn om te kijken naar uw maandelijkse gebruik. De maand kost u in het echt het maandelijkse gebruik plus het evenredig (afgeschreven) deel van de eerder gemaakte eenmalige kosten. Het rapport met afgeschreven kosten kan u helpen om een reëel beeld te krijgen.

De afgeschreven kosten van de gereserveerde instantie worden berekend door de eenmalige kosten van de reservering te nemen en deze gedurende de reserveringstermijn af te schrijven. In het rapport met werkelijke kosten zijn eenmalige kosten zichtbaar in de maand van de aanschaf van de reservering. Dagelijkse en maandelijkse uitgaven worden niet weergegeven in de werkelijke kosten van de implementatie. Het rapport met afgeschreven kosten toont de werkelijke kosten van de implementatie gedurende een bepaalde periode.  Het rapport met afgeschreven kosten is de enige manier om uw werkelijke kostenontwikkelingen te zien. Het is ook de enige manier om uw toekomstige uitgaven te plannen.

In het rapport met werkelijke kosten hebt u een piek voor een aanschaf van een gereserveerde instantie gezien van $ 747 op 16 november. In het rapport met afgeschreven kosten (zie de volgende afbeelding), zijn er kosten voor een deel van de dag op 16 november. Vanaf 17 november ziet u de afgeschreven kosten voor de gereserveerde instantie van $ 747/365 = $ 2,05. U ziet ook dat de aangeschafte reservering incidenteel niet wordt gebruikt, zodat u deze kunt optimaliseren door over te schakelen naar een andere VM-grootte.

Om deze te bekijken, gaat u naar **Costs** > **Cost Analysis** > en selecteert u vervolgens **Amortized Cost Analysis** of **Amortized Cost Over Time**.

![Voorbeeldrapport van afgeschreven kosten per gereserveerde instantie](./media/tutorial-optimize-reserved-instances/azure07.png)

## <a name="optimize-aws-ri-costs"></a>De kosten van gereserveerde AWS-instanties optimaliseren

Gereserveerde instanties zijn een lopende verplichting. Ze zijn nuttig wanneer u virtuele machines doorlopend gebruikt omdat gereserveerde instanties minder duur zijn dan on-demand instanties. Ze moeten echter wel voldoende worden gebruikt. De verplichting bestaat uit het gebruik van resources gedurende een gedefinieerde periode - één of drie jaar. Wanneer u overgaat tot aankoop, betaalt u vooraf voor de resources met een reservering. Mogelijk maakt u echter niet altijd volledig gebruik van waartoe u zich in de reservering hebt verplicht.

U kunt bijvoorbeeld uw omgeving beoordelen en bepalen dat u vorig jaar 20 D2-standaardinstanties had die voortdurend actief waren. U kunt hiervoor een reservering aanschaffen en mogelijk veel geld besparen. In een ander voorbeeld hebt u zich mogelijk verplicht tot het gebruik van tien MA4-instanties voor het jaar. Maar misschien hebt u er tot nu toe nog maar vijf gebruikt. Beide voorbeelden laten inefficiënt gebruik van gereserveerde instanties zien. Er zijn twee manieren om kosten voor gereserveerde instanties te besparen met Cloudyn-optimalisatierapporten:

- Aanbevelingen voor uw mogelijke aanschaf evalueren op basis van uw historische gebruik
- Niet-gebruikte gereserveerde instanties aanpassen

Met het rapport _EC2 RI Buying Recommendations_ en het rapport _EC2 Currently Unused Reservations_ kunt u uw gebruik en de kosten van gereserveerde instanties verbeteren.

## <a name="buy-recommended-ris"></a>Aanbevolen gereserveerde instanties aanschaffen

Cloudyn vergelijkt het gebruik van on-demand instanties en vergelijkt dit met het gebruik van potentieel gereserveerde instanties. Als er mogelijke besparingen worden gevonden, worden aanbevelingen weergegeven in het rapport EC2 Buying Recommendations.

Klik op het rapportmenu bovenaan in de portal en klik op **Optimizer** > **Pricing Optimization** > **EC2 RI Buying Recommendations**.

Op de volgende afbeelding worden aanschafaanbevelingen uit het rapport weergegeven.

![Voorbeeld van aanschafaanbevelingen in het rapport EC2 Buying Recommendations](./media/tutorial-optimize-reserved-instances/aws01.png)

In dit voorbeeld heeft de Cloudyn\_A-account de aanbeveling voor het kopen van 32 gereserveerde instanties. Als u de alle aanbevelingen volgt, kunt u jaarlijks mogelijk $ 137.770 jaarlijks besparen. Houd er rekening mee dat er bij de aanschafaanbevelingen die door Cloudyn worden gedaan, ervan wordt uitgegaan dat het gebruik voor uw actieve workloads consistent blijft.

Klik op het plusteken ( **+** ) onder **Justifications** om details te bekijken met uitleg waarom elke aanschaf wordt aanbevolen. Hier volgt een voorbeeld van de eerste aanbeveling in de lijst.

![Voorbeeld van details van rechtvaardigingen voor aanschaf](./media/tutorial-optimize-reserved-instances/aws02.png)

In het voorgaande voorbeeld is te zien dat het uitvoeren van de workload on-demand $ 90.456 per jaar zou kosten. Als u de reservering echter vooraf aanschaft, kost dezelfde workload $ 56.592 en bespaart u jaarlijks $ 33.864.

Klik op het plusteken naast **EC2 RI Purchase Impact** om uw break-evenpoint te bekijken gedurende een periode van een jaar om ongeveer te zien wanneer uw aankoopinvestering rendement oplevert. In het volgende voorbeeld beginnen na ongeveer acht maanden na de aanschaf de totale kosten voor on-demand de totale kosten voor gereserveerde instanties te overschrijden:

![Voorbeeld van details van effect van aanschaf](./media/tutorial-optimize-reserved-instances/aws03.png)

Vanaf dat moment gaat u geld besparen.

U kunt **Instances over Time** bekijken om de nauwkeurigheid van de voorgestelde aanschafaanbeveling te verifiëren. In dit voorbeeld kunt u zien dat er gedurende de afgelopen periode van 30 dagen gemiddeld zes instanties werden gebruikt voor de workload.

![Voorbeeld van historisch gebruik van instanties in een periode](./media/tutorial-optimize-reserved-instances/aws04.png)

## <a name="modify-unused-reservations"></a>Niet-gebruikte gereserveerde instanties aanpassen

Ongebruikte reserveringen komen veel voor in cloudresourceomgevingen van consumenten. Door ervoor te zorgen dat ongebruikte reserveringen volledig worden gebruikt, kunt u geld besparen wanneer u de reserveringen aanpast zodat deze voldoen aan uw huidige behoeften. Mogelijk hebt u bijvoorbeeld een abonnement met D3-standaardinstanties die worden uitgevoerd op Linux. Als u de reservering niet volledig gebruikt, kunt u het type instantie wijzigen. Of u kunt de niet-gebruikte resources verplaatsen naar een andere reservering of een ander account.

AWS verkoopt gereserveerde instanties voor specifieke beschikbaarheidszones en regio's. Als u gereserveerde instanties voor een specifieke beschikbaarheidszone hebt gekocht, kunt u de reserveringen niet tussen zones verplaatsen. U kunt regionale gereserveerde instanties echter eenvoudig verplaatsen tussen zones met behulp van het rapport **EC2 Currently Unused Reservations**. Of u kunt deze aanpassen zodat ze een regionaal bereik hebben. Vervolgens worden deze overeenkomende instanties toegepast in alle beschikbaarheidszones.

Klik in het rapportmenu bovenaan de portal op **Optimizer** > **Inefficiencies** > **EC2 Currently Unused Reservations**.

Op de volgende afbeeldingen wordt het rapport met niet-gebruikte gereserveerde instanties weergegeven.

![Voorbeeld van samengevatte informatie over niet-gebruikte reserveringen](./media/tutorial-optimize-reserved-instances/unused-ri01.png)

Klik op het plusteken onder **Details** om reserveringsdetails weer te geven voor een specifieke reservering.

![Voorbeeld van details van niet-gebruikte reserveringen](./media/tutorial-optimize-reserved-instances/unused-ri02.png)

In het voorgaande voorbeeld zijn er in totaal 77 niet-gebruikte reserveringen in verschillende beschikbaarheidszones. De eerste reservering heeft 51 ongebruikte instanties. Als u lager in de lijst kijkt, zijn er mogelijke aanpassingen van gereserveerde instanties die u kunt doen met behulp van het instantietype **m3.2xlarge** in de beschikbaarheidszone **us-east-1c**.

Klik op **Modify** voor de eerste reservering in de lijst om de pagina **Modify RI** te openen, waar gegevens over de reservering staan.

![Voorbeeld van reserveringen die u kunt wijzigen](./media/tutorial-optimize-reserved-instances/unused-ri03.png)

Gereserveerde instanties die u kunt aanpassen, worden weergegeven. Op de afbeelding met het volgende voorbeeld zijn er 51 niet-gebruikte reserveringen die u kunt wijzigen, maar zijn er 54 nodig tussen de twee reserveringen. Als u uw niet-gebruikte reserveringen wijzigt voor gebruik van alle, blijven vier instanties on-demand worden uitgevoerd. Voor dit voorbeeld splitst u uw niet-gebruikte reservering, waarbij de eerste reservering er 30 gebruikt en de tweede reservering er 21 gebruikt.

Klik op het plusteken voor het eerste reserveringsitem in de reservering en stel **Reservation quantity** in op **30**. Stel voor het tweede item de reserveringshoeveelheid in op **21** en klik vervolgens op **Apply**.

![Voorbeeld van wijzigingen in de reserveringshoeveelheid](./media/tutorial-optimize-reserved-instances/unused-ri04.png)

Al uw niet-gebruikte instanties voor de reservering worden volledig gebruikt en 51 instanties worden niet meer on-demand uitgevoerd. In dit voorbeeld bespaart u uw organisatie geld door het gebruik van on-demand aanzienlijk te verminderen en gebruik te maken van reserveringen waarvoor al is betaald.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de volgende taken uitgevoerd:

> [!div class="checklist"]
> * Inzichten opgedaan over de kosten van gereserveerde Azure-instanties
> * Meer geleerd over de voordelen van gereserveerde instanties
> * De kosten van gereserveerde Azure-instanties geoptimaliseerd
> * Kosten van gereserveerde instanties bekeken
> * De kosteneffectiviteit van gereserveerde Azure-instanties beoordeeld
> * De kosten van gereserveerde AWS-instanties geoptimaliseerd
> * Aanbevolen gereserveerde instanties aangeschaft
> * Niet-gebruikte gereserveerde instanties aangepast


In de volgende zelfstudie leert u hoe u toegang tot gegevens beheert.

> [!div class="nextstepaction"]
> [Toegang tot gegevens beheren](tutorial-user-access.md)
