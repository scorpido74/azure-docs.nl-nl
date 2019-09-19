---
title: Onverwachte kosten voorkomen en facturering beheren in Azure
description: Meer informatie over het vermijden van onverwachte kosten op uw Azure-factuur. Gebruik functies voor het bijhouden van kosten en beheer voor een Azure-abonnement.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: b64e84c3fff27675029ff35f27972a4aca014ec3
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "68612077"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Onverwachte kosten voorkomen met Azure-facturering en -kostenbeheer

Wanneer u zich aanmeldt voor Azure, zijn er een aantal zaken die u kunt doen om meer inzicht in uw uitgaven te krijgen:

- De [Prijscalculator](https://azure.microsoft.com/pricing/calculator/) biedt een schatting van de kosten voordat u een Azure-resource maakt. 

- [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) biedt u de huidige kostenanalyse en prognose voor uw abonnement. 

- Als u kosten voor verschillende projecten of teams wilt groeperen en begrijpen, bekijkt u [resources taggen](../azure-resource-manager/resource-group-using-tags.md). Als uw organisatie over een rapportensysteem beschikt dat u liever gebruikt, bekijkt u de [facturerings-API's](billing-usage-rate-card-overview.md).

- Als uw abonnement is gemaakt op basis van een Enterprise Agreement (EA), kunt u uw kosten bekijken in Azure Portal. Als uw abonnement via een Cloud Solution Provider (CSP) of Azure Sponsorship loopt, is een aantal van de volgende functies mogelijk niet op u van toepassing. Zie [Aanvullende resources voor EA, CSP en Sponsorship](#other-offers) voor meer informatie.

- Als uw abonnement een gratis proefversie of een [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)-, Azure in Open (AIO)- of BizSpark-abonnement is, dan wordt dit automatisch uitgeschakeld wanneer al uw tegoed is gebruikt. Krijg meer informatie over [bestedingslimieten](#spending-limit) om te voorkomen dat uw abonnement onverwacht wordt uitgeschakeld.

- Als u zich hebt aangemeld voor een [gratis Azure-account](https://azure.microsoft.com/free/), [kunt u een aantal van de populairste Azure-services gedurende 12 maanden gratis gebruiken](billing-create-free-services-included-free-account.md). Naast de aanbevelingen die hieronder worden vermeld, zie [Voorkomen dat u moet betalen voor een gratis account](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Geschatte kosten ontvangen voordat u Azure-services toevoegt

Hier ziet u aanvullende informatie over het schatten van kosten met behulp van de volgende hulpprogramma's:
- Azure-prijscalculator
- Azure Portal
- Bestedingslimiet

Op de afbeeldingen in de volgende secties ziet u voorbeeldprijzen in Amerikaanse dollar.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Online kosten schatten met behulp van de Prijscalculator

Bekijk de [Prijscalculator](https://azure.microsoft.com/pricing/calculator/) voor geschatte maandelijkse kosten van de service waarin u bent geïnteresseerd. U kunt elke eigen Azure-resource toevoegen om geschatte kosten te krijgen. In de Prijscalculator kunt u valutatype wijzigen.

![Schermopname van het menu Prijscalculator](./media/billing-getting-started/pricing-calc.png)

In de Prijscalculator wordt bijvoorbeeld geschat dat de rekenuren voor een A1 virtuele Windows-machine een bepaald bedrag per maand kost als u deze VM de hele tijd blijft uitvoeren:

![Schermopname van de Prijscalculator, met de geschatte kosten voor een A1 Windows-VM](./media/billing-getting-started/pricing-calcvm.png)

Zie de [Veelgestelde vragen over prijzen](https://azure.microsoft.com/pricing/faq/) voor meer informatie over prijzen. Als u met een Azure-verkoopmedewerker wilt spreken, belt u het telefoonnummer bovenaan de pagina Veelgestelde vragen.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Geschatte kosten controleren in Azure Portal

Wanneer u een service in Azure Portal toevoegt, is er doorgaans een weergave beschikbaar met de geschatte kosten per maand in uw gefactureerde valuta. Wanneer u bijvoorbeeld de grootte van uw Windows-VM kiest, ziet u de geschatte maandelijkse kosten voor de rekenuren:

![Voorbeeld: een A1 Windows-VM, met geschatte kosten per maand](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a> Controleer of er een bestedingslimiet is ingeschakeld

Als u een abonnement hebt waarbij u tegoed gebruikt, wordt de bestedingslimiet standaard voor u ingeschakeld. Op deze manier worden geen kosten in rekening gebracht op uw creditcard wanneer u al uw tegoed verbruikt. Zie de [volledige lijst met Azure-aanbiedingen en de beschikbaarheid van de bestedingslimiet](https://azure.microsoft.com/support/legal/offer-details/).

Wanneer u echter uw bestedingslimiet bereikt, worden uw services uitgeschakeld. Dat houdt in dat de toewijzing van uw virtuele machines ongedaan wordt gemaakt. U kunt voorkomen dat de service uitvalt door de bestedingslimiet uit te schakelen. Elke overschrijding van de limiet wordt in rekening gebracht op de voor u geregistreerde creditcard.

Ga naar de [weergave Abonnementen in het accountcentrum](https://account.windowsazure.com/Subscriptions) om te zien of u een bestedingslimiet hebt ingeschakeld. Er wordt een banner weergegeven als uw bestedingslimiet is ingeschakeld, zoals bij het volgende:

![Schermopname met een waarschuwing over een ingeschakelde bestedingslimiet in het accountcentrum](./media/billing-getting-started/spending-limit-banner.png)

Klik op de banner en volg de instructies om de bestedingslimiet te verwijderen. Als u geen creditcardinformatie hebt ingevoerd op het moment dat u zich hebt aangemeld, moet u die gegevens nu invoeren om de bestedingslimiet te verwijderen. Zie [Azure-bestedingslimiet: hoe werkt dit en hoe kan ik dit inschakelen of verwijderen?](https://azure.microsoft.com/pricing/spending-limits/) voor meer informatie.

## <a name="use-budgets-and-cost-alerts"></a>Budgetten en kostenwaarschuwingen gebruiken

U kunt [budgetten](../cost-management/tutorial-acm-create-budgets.md) maken om kosten te beheren en [waarschuwingen](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten.

## <a name="monitor-costs-when-using-azure-services"></a>Kosten bewaken bij het gebruik van Azure-services
U kunt kosten bewaken met de volgende hulpprogramma's:

- Tags
- Kostenanalyse en burn rate
- Kostenanalyse

### <a name="tags"></a> Tags aan resources toevoegen om factureringsgegevens te groeperen

U kunt tags gebruiken om factureringsgegevens voor ondersteunde services te groeperen. Als u bijvoorbeeld een aantal virtuele machines voor verschillende teams uitvoert, dan kunt u tags gebruiken om kosten per kostencentrum te categoriseren (bijvoorbeeld: HR, marketing, financiën, enzovoort) of omgeving (bijvoorbeeld: productie, preproductie, test).

![Schermopname met de instelling van tags in de portal](./media/billing-getting-started/tags.png)

De tags worden in verschillende kostenrapportenweergaven getoond. Ze zijn bijvoorbeeld meteen zichtbaar in uw [kostenanalyseweergave](#costs) en in het gedetailleerde CSV-gebruiksbestand na uw eerste factureringsperiode.

Zie [Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/resource-group-using-tags.md) voor meer informatie.

### <a name="costs"></a> De kostenanalyse en burn rate bewaken

Nadat u uw Azure-services bent gaan uitvoeren, moet u de kosten regelmatig controleren. U kunt de huidige uitgaven en burn rate in Azure Portal zien.

1. Ga naar de [abonnementen in Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) en selecteer een abonnement.

2. Als kostenanalyse en burn rate voor uw abonnement wordt ondersteund, worden die hier weergegeven.

    ![Schermopname van burn rate en analyse in Azure Portal](./media/billing-getting-started/burn-rate.PNG)

3. Klik op [Kostenanalyse](../cost-management/quick-acm-cost-analysis.md) in de lijst om de kostenanalyse per resource weer te geven. Nadat u een service toevoegt, wacht u 24 uur voor de gegevens worden weergegeven.

    ![Schermopname van de kostenanalyseweergave in Azure Portal](./media/billing-getting-started/cost-analysis.png)

4. U kunt filteren op verschillende eigenschappen zoals [tags](#tags), resourcetype, resourcegroep en periode. Klik op **Toepassen** om de filters te bevestigen en klik op **Downloaden** als u de weergave wilt exporteren naar een CSV-bestand met door komma's gescheiden waarden.

5. Daarnaast kunt u op een resource klikken om uw dagelijkse uitgavengeschiedenis weer te geven en om te zien hoeveel de resource per dag kost.

    ![Schermopname van de uitgavengeschiedenisweergave in Azure Portal](./media/billing-getting-started/costhistory.png)

Vergelijk de kosten die u ziet met de geschatte kosten die hebt gezien toen u de services selecteerde. Als de kosten aanzienlijk afwijken van de geschatte kosten, controleert u het prijsplan dat u voor uw resources hebt geselecteerd.

## <a name="optimize-and-reduce-costs"></a>Kosten optimaliseren en verlagen
Als u niet bekend bent met de principes van kostenbeheer, leest u [Uw cloudinvestering optimaliseren met Azure Cost Management](../cost-management/cost-mgt-best-practices.md).

In Azure Portal kunt u ook Azure-kosten optimaliseren en verlagen met behulp van automatisch afsluiten voor virtuele machines en Advisor-aanbevelingen.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Kostenbesparende functies overwegen zoals automatisch afsluiten voor virtuele machines

Afhankelijk van uw scenario kunt u automatisch afsluiten voor uw virtuele machines configureren in Azure Portal. Zie [Automatisch afsluiten voor virtuele machines met behulp van Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) voor meer informatie.

![Schermopname van de optie Automatisch afsluiten in de portal](./media/billing-getting-started/auto-shutdown.png)

Automatisch afsluiten is wat anders dan uitschakelen in de virtuele machine met behulp van aan-uitopties. Met Automatisch afsluiten worden uw virtuele machines gestopt en wordt de toewijzing hiervan ongedaan gemaakt om ervoor te zorgen dat er geen extra kosten meer in rekening worden gebracht. Zie de veelgestelde vragen over prijzen voor [virtuele Linux-machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [virtuele Windows-machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) over VM-statussen voor meer informatie.

Raadpleeg [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) voor meer kostenbesparende functies voor uw ontwikkelings- en testomgevingen.

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Azure Advisor-aanbevelingen inschakelen en beoordelen

[Azure Advisor](../advisor/advisor-overview.md) helpt u kosten te besparen door resources te identificeren die weinig worden gebruikt. Ga naar Advisor in Azure Portal:

![Schermopname van de knop Azure Advisor in Azure Portal](./media/billing-getting-started/advisor-button.png)

U kunt handige aanbevelingen krijgen op het tabblad **Kosten** in het Advisor-dashboard:

![Schermopname van een voorbeeld van de Advisor-aanbevelingen voor kosten](./media/billing-getting-started/advisor-action.png)

Lees de zelfstudie [Kosten optimaliseren op basis van aanbevelingen](../cost-management/tutorial-acm-opt-recommendations.md) voor een begeleide zelfstudie over kostenbesparende Advisor-aanbevelingen.

## <a name="review-costs-against-your-latest-invoice"></a>Kosten controleren op basis van uw meest recente factuur

Aan het einde van de factureringscyclus is uw meest recente factuur is beschikbaar. U kunt ook[facturen en bestanden met gedetailleerde gebruiksgegevens downloaden](billing-download-azure-invoice-daily-usage-date.md) om te controleren of de juiste kosten in rekening zijn gebracht. Zie[Meer informatie over uw factuur voor Microsoft Azure](billing-understand-your-bill.md) voor meer informatie over het vergelijken van uw dagelijkse gebruik met uw factuur.

### <a name="billing-api"></a>Billing-API

Gebruik de Azure Billing-API om programmatisch gebruiksgegevens op te halen. Gebruik zowel de RateCard-API als de gebruik-API om uw gefactureerde gebruik op te halen. Zie [Inzicht verkrijgen in het gebruik van Microsoft Azure-resources](billing-usage-rate-card-overview.md)voor meer informatie.

## <a name="other-offers"></a> Aanvullende resources en speciale gevallen

### <a name="ea-csp-and-sponsorship-customers"></a>EA-, CSP- en Sponsorship-klanten
Ga in gesprek met uw accountbeheerder of Azure-partner om aan de slag te gaan.

| Aanbieding | Resources |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise Agreement (EA) | [EA portal](https://ea.azure.com/), [helpdocumenten](https://ea.azure.com/helpdocs) en [Power BI-rapport](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | In gesprek gaan met uw provider |
| Azure Sponsorship | [Sponsorship-portal](https://www.microsoftazuresponsorships.com/) |

Als u IT voor een grote organisatie beheert, wordt aanbevolen de [zakelijke ondersteuning voor Azure](/azure/architecture/cloud-adoption-guide/subscription-governance) en het [zakelijke technische document voor IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) te lezen (PDF-downloads, alleen in het Engels).

#### <a name="EA"></a> Enterprise Agreement-kostenweergaven in Azure Portal

Zakelijke kostenweergaven zijn momenteel als openbare preview-versie beschikbaar. Aandachtspunten:

- Abonnementskosten zijn gebaseerd op gebruik en zijn exclusief vooruitbetaalde bedragen, overschrijdingen, opgenomen hoeveelheden, aanpassingen en belasting. De daadwerkelijke kosten worden op inschrijvingsniveau berekend.
- De bedragen die in Azure Portal worden weergegeven, kunnen afwijken van de bedragen in de Enterprise-portal. Het kan enkele minuten duren voordat wijzigingen als gevolg van updates in de Enterprise-portal worden weergegeven in Azure Portal.
- Als u geen kosten ziet, komt dit waarschijnlijk door een van de volgende oorzaken:
    - U hebt geen machtigingen op abonnementsniveau. Als u de zakelijke kostenweergaven wilt zien, moet u een lezer met factureringsrechten, een lezer, een inzender of een eigenaar op abonnementsniveau zijn.
    - U bent eigenaar van een account en uw inschrijvingsbeheerder heeft de instelling AO-weergavekosten uitgeschakeld.  Neem contact op met uw inschrijvingsbeheerder voor toegang tot kosten.
    - U bent een afdelingsbeheerder en uw inschrijvingsbeheerder heeft de instelling **DA-weergavekosten** uitgeschakeld.  Neem contact op met uw inschrijvingsbeheerder voor toegang.
    - U hebt Azure via een kanaalpartner aangeschaft en de partner heeft geen prijsgegevens gepubliceerd.  
- Als u instellingen met betrekking tot kostentoegang in de Enterprise-portal bijwerkt, is er een vertraging van enkele minuten voordat de wijzigingen worden weergegeven in Azure Portal.
- Instructies voor de bestedingslimiet en facturen zijn niet van toepassing op EA-abonnementen.

### <a name="check-your-subscription-and-access"></a>Uw abonnement en toegang controleren

Als u kosten wilt weergeven, moet u [toegang op abonnementsniveau hebben tot de factureringsgegevens](billing-manage-access.md). Alleen de accountbeheerder heeft toegang tot het [accountcentrum](https://account.azure.com/Subscriptions) en alleen de accountbeheerder kan factureringsgegevens wijzigen en abonnementen beheren. De accountbeheerder is de persoon die het registratieproces heeft doorlopen. Zie [Azure-beheerderrollen toevoegen of wijzigen waarmee het abonnement of de services worden beheerd](billing-add-change-azure-subscription-administrator.md) voor meer informatie.

Ga naar [Abonnementen in Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) om te zien of u de accountbeheerder bent. Bekijk de lijst met abonnementen en zoek **Mijn rol**. Als *accountbeheerder* de rol is, dan beschikt u over volledige bevoegdheden. Als er iets anders wordt vermeld, zoals *Eigenaar*, dan beschikt u niet over de volledige bevoegdheden.

![Schermopname van uw rol in de weergave Abonnementen in Azure Portal](./media/billing-getting-started/sub-blade-view.PNG)

[Zoek de accountbeheerder](billing-subscription-transfer.md#whoisaa) om abonnementen te beheren en de factureringsgegevens te wijzigen. Vraag de accountbeheerder om de taken te voltooien of om [het abonnement aan u over te dragen](billing-subscription-transfer.md).

Als uw accountbeheerder niet langer in dienst is bij uw organisatie en u de facturering moet beheren, kunt u [contact met ons opnemen](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Een Service Level Agreement-tegoed aanvragen voor een service-incident

In de SLA (Service Level Agreement) worden de toezeggingen van Microsoft voor bedrijfstijd en connectiviteit beschreven. Een service-incident wordt gerapporteerd wanneer er zich een probleem voordoet bij Azure-services die invloed heeft op de bedrijfstijd of connectiviteit, ook wel *uitval* genoemd. Als we de serviceniveaus voor elke service niet kunnen bereiken en onderhouden zoals dat wordt beschreven in de SLA, komt u mogelijk in aanmerking voor tegoed ter hoogte van een deel van uw maandelijkse servicekosten.

U kunt als volgt een tegoed aanvragen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/). Als u meerdere accounts hebt, controleert u of u het account gebruikt die door de uitvaltijd van Azure is beïnvloed. 
2. Maak een nieuwe ondersteuningsaanvraag.
3. Onder **Probleemtype** selecteert u **Facturering**.
4. Onder **Probleemtype** selecteert u **Restitutieaanvraag**.
5. Voeg details toe om op te geven dat u een aanvraag indient voor een SLA-tegoed en noem zowel de datum/tijd/tijdzone als de betrokken services (virtuele machines, websites, enzovoort)
6. Controleer uw contactgegevens en selecteer **Maken** om uw aanvraag in te dienen.

SLA-drempelwaarden variëren per service. De SQL-laag Web heeft bijvoorbeeld een SLA van 99,9%, virtuele machines hebben een SLA van 99,95% en de SQL-laag Standard heeft een SLA van 99,99%.

Voor een aantal services gelden voorwaarden voordat de SLA van toepassing is. Voor virtuele machines moeten bijvoorbeeld twee of meer instanties in dezelfde beschikbaarheidsset zijn geïmplementeerd.

Zie [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/) en de documentatie met de [SLA-samenvatting voor Azure-services](https://azure.microsoft.com/support/legal/sla/summary/) voor meer informatie.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het gebruik van [bestedingslimieten](billing-spending-limit.md) om te voorkomen dat u teveel uitgeeft.
- Begin met het [analyseren van uw Azure-kosten](../cost-management/quick-acm-cost-analysis.md).
