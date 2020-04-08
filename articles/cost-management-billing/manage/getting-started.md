---
title: Onverwachte kosten voorkomen en analyseren met Azure kostenbeheer en facturering
description: Meer informatie over het voorkomen van onverwachte kosten op uw Azure-factuur en het gebruik van functies voor het bijhouden en beheren van kosten voor uw Azure-account.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 3/30/2020
ms.author: banders
ms.openlocfilehash: 79af6f78e8e9bf93c49deafe79f6a421cbb77d1a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475255"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Onverwachte kosten voorkomen met Azure-facturering en -kostenbeheer

Wanneer u zich aanmeldt voor Azure, zijn er een aantal zaken die u kunt doen om meer inzicht in uw uitgaven te krijgen:

- Ontvang geschatte kosten voordat u services toevoegt met behulp van de [prijscalculator](https://azure.microsoft.com/pricing/calculator/), het prijzenoverzicht voor Azure of bij het toevoegen van services in de Azure-portal.
- Bewaak kosten met [budgetten](../costs/tutorial-acm-create-budgets.md), [meldingen](../costs/cost-mgt-alerts-monitor-usage-spending.md) en [kostenanalyses](../costs/quick-acm-cost-analysis.md).
- Controleer de kosten op uw factuur door deze te vergelijken met [gedetailleerde gebruiksgegevensbestanden](download-azure-invoice-daily-usage-date.md).
- Integreer facturerings- en kostengegevens met uw eigen rapportagesysteem met behulp van de [Billing](https://docs.microsoft.com/rest/api/billing/)- en [Consumption](https://docs.microsoft.com/rest/api/consumption/)-API's.
- Gebruik aanvullende bronnen en hulpprogramma's voor Enterprise Agreement- (EA), Cloud Solution Provider- (CSP) en Azure Sponsorship-klanten.
- Maak [gratis gebruik van een aantal populaire Azure-services gedurende 12 maanden](create-free-services.md) die beschikbaar zijn bij het [gratis Azure-account](https://azure.microsoft.com/free/). Naast de aanbevelingen die hieronder worden vermeld, zie [Voorkomen dat u moet betalen voor een gratis account](avoid-charges-free-account.md).

Raadpleeg [Uw Azure-abonnement annuleren](cancel-azure-subscription.md) als u uw Azure-abonnement wilt annuleren.

## <a name="get-estimated-costs-before-adding-azure-services"></a>Geschatte kosten ontvangen voordat u Azure-services toevoegt

Gebruik een van de volgende hulpprogramma's om de verbruikskosten van een Azure-service te schatten:
- Azure-prijscalculator
- Prijzenoverzicht voor Azure
- Azure Portal

Op de afbeeldingen in de volgende secties ziet u voorbeeldprijzen in Amerikaanse dollar.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Online kosten schatten met behulp van de Prijscalculator

Bekijk de [Prijscalculator](https://azure.microsoft.com/pricing/calculator/) voor geschatte maandelijkse kosten van de service die u wilt toevoegen. U kunt de valuta wijzigen om de schatting in uw lokale valuta weer te geven.

![Schermopname van het menu Prijscalculator](./media/getting-started/pricing-calc.png)

U kunt de geschatte kosten weergeven voor elke eigen Azure-service. In de onderstaande schermopname wordt bijvoorbeeld geschat dat de rekenuren voor een A1 virtuele Windows-machine $ 66,96 per maand kost als u deze VM de hele tijd blijft uitvoeren:

![Schermopname van de Prijscalculator, met de geschatte kosten voor een A1 Windows-VM](./media/getting-started/pricing-calcvm.png)

Zie de [Veelgestelde vragen over prijzen](https://azure.microsoft.com/pricing/faq/) voor meer informatie over prijzen. Als u met een Azure-verkoopmedewerker wilt spreken, belt u het telefoonnummer bovenaan de pagina Veelgestelde vragen.

### <a name="view-and-download-azure-price-sheet"></a>Prijzenoverzicht voor Azure weergeven en downloaden

Als u toegang hebt tot Azure via een Enterprise Agreement (EA) of een Microsoft-klantovereenkomst (MCA), kunt u het prijzenoverzicht voor uw Azure-account bekijken en downloaden. Het prijzenoverzicht is een Excel-bestand met prijzen voor alle Azure-Services. Zie [Uw prijzen voor Azure weergeven en downloaden](ea-pricing.md) voor meer informatie.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Geschatte kosten controleren in Azure Portal

U kunt de geschatte kosten per maand bekijken bij het toevoegen van een service in de Azure-portal. Wanneer u bijvoorbeeld de grootte van uw Windows-VM kiest, ziet u de geschatte maandelijkse kosten voor de rekenuren:

![Voorbeeld: een A1 Windows-VM, met geschatte kosten per maand](./media/getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Kosten bewaken bij het gebruik van Azure-services
U kunt kosten bewaken met de volgende hulpprogramma's:

- Budget- en kostenmeldingen
- Kostenanalyse

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Kosten bijhouden met budget- en kostenmeldingen

Maak [budgetten](../costs/tutorial-acm-create-budgets.md) om kosten te beheren maak en [meldingen](../costs/cost-mgt-alerts-monitor-usage-spending.md) waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende en te hoge uitgaven.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> Kosten verkennen en analyseren met Kostenanalyse

Nadat u uw Azure-services bent gaan uitvoeren, moet u de kosten regelmatig controleren om uw Azure-uitgaven bij te houden. U kunt Kostenanalyse gebruiken om inzicht te krijgen in de kosten van uw Azure-gebruik.

1. Ga naar [Kostenbeheer en facturering in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Klik op **Kostenanalyse** aan de linkerkant van het scherm om de huidige kosten weer te geven, onderverdeeld naar verschillende draaipunten, zoals de service, locatie en het abonnement. Als u een service toevoegt of een aankoop doet, moet u 24 uur wachten voordat de gegevens worden weergegeven. Standaard worden in Kostenanalyse de kosten voor uw huidige bereik weergegeven. In de onderstaande schermopname worden bijvoorbeeld kosten voor de factureringsrekening van Contoso weergegeven. Gebruik Bereik om over te schakelen naar een ander bereik in Kostenanalyse. Zie [Understand and work with scopes](../costs/understand-work-scopes.md#scopes) (Engelstalig) voor meer informatie over bereiken

    ![Schermopname van de kostenanalyseweergave in Azure Portal](./media/getting-started/cost-analysis.png)

4. U kunt de weergave filteren op verschillende eigenschappen, zoals labels, resourcetype en tijdsbestek. Klik op **Filter toevoegen** om het filter voor een eigenschap toe te voegen en selecteer de gewenste filterwaarden. Selecteer **Exporteren** als u de weergave wilt exporteren als bestand met door komma's gescheiden waarden (CSV).

5. Daarnaast kunt u op de labels van het diagram klikken om de dagelijkse uitgavengeschiedenis voor dat label weer te geven. Bijvoorbeeld: In de onderstaande schermopname worden de dagelijkse kosten van het uitvoeren van uw VM's weergegeven door op virtuele machines te klikken.

    ![Schermopname van de uitgavengeschiedenisweergave in Azure Portal](./media/getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Kosten optimaliseren en verlagen
Als u niet bekend bent met de principes van kostenbeheer, leest u [Uw cloudinvestering optimaliseren met Azure Cost Management](../costs/cost-mgt-best-practices.md).

In Azure Portal kunt u ook Azure-kosten optimaliseren en verlagen met behulp van automatisch afsluiten voor virtuele machines en Advisor-aanbevelingen.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Kostenbesparende functies overwegen zoals automatisch afsluiten voor virtuele machines

Afhankelijk van uw scenario kunt u automatisch afsluiten voor uw virtuele machines configureren in Azure Portal. Zie [Automatisch afsluiten voor virtuele machines met behulp van Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) voor meer informatie.

![Schermopname van de optie Automatisch afsluiten in de portal](./media/getting-started/auto-shutdown.png)

Automatisch afsluiten is wat anders dan uitschakelen in de virtuele machine met behulp van aan-uitopties. Met Automatisch afsluiten worden uw virtuele machines gestopt en wordt de toewijzing hiervan ongedaan gemaakt om ervoor te zorgen dat er geen extra kosten meer in rekening worden gebracht. Zie de veelgestelde vragen over prijzen voor [virtuele Linux-machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [virtuele Windows-machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) over VM-statussen voor meer informatie.

Raadpleeg [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) voor meer kostenbesparende functies voor uw ontwikkelings- en testomgevingen.

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Azure Advisor-aanbevelingen inschakelen en beoordelen

[Azure Advisor](../../advisor/advisor-overview.md) helpt u kosten te besparen door resources te identificeren die weinig worden gebruikt. Zoek naar **Advisor** in de Azure-portal:

![Schermopname van de knop Azure Advisor in Azure Portal](./media/getting-started/advisor-button.png)

Selecteer **Kosten** aan de linkerkant. Op het tabblad **Kosten** ziet u aanbevelingen voor acties:

![Schermopname van een voorbeeld van de Advisor-aanbevelingen voor kosten](./media/getting-started/advisor-action.png)

Lees de zelfstudie [Kosten optimaliseren op basis van aanbevelingen](../costs/tutorial-acm-opt-recommendations.md) voor een begeleide zelfstudie over kostenbesparende Advisor-aanbevelingen.


## <a name="integrate-with-billing-and-consumption-apis"></a>Integreren met de API's voor facturerings- en verbruiksgegevens

Gebruik de Azure [Billing](https://docs.microsoft.com/rest/api/billing/)- en [Consumption](https://docs.microsoft.com/rest/api/consumption/)-API's om programmatisch facturerings-en kostengegevens op te halen. Gebruik zowel de RateCard-API als de gebruik-API om uw gefactureerde gebruik op te halen. Zie [Inzicht verkrijgen in het gebruik van Microsoft Azure-resources](usage-rate-card-overview.md)voor meer informatie.

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> Aanvullende resources en speciale gevallen

### <a name="ea-csp-and-sponsorship-customers"></a>EA-, CSP- en Sponsorship-klanten
Ga in gesprek met uw accountbeheerder of Azure-partner om aan de slag te gaan.

| Aanbieding | Resources |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise Agreement (EA) | [EA portal](https://ea.azure.com/), [helpdocumenten](https://ea.azure.com/helpdocs) en [Power BI-rapport](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | In gesprek gaan met uw provider |
| Azure Sponsorship | [Sponsorship-portal](https://www.microsoftazuresponsorships.com/) |

Als u IT voor een grote organisatie beheert, wordt aanbevolen de [zakelijke ondersteuning voor Azure](/azure/architecture/cloud-adoption-guide/subscription-governance) en het [zakelijke technische document voor IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) te lezen (PDF-downloads, alleen in het Engels).

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Enterprise Agreement-kostenweergaven in Azure Portal

Zakelijke kostenweergaven zijn momenteel als openbare preview-versie beschikbaar. Aandachtspunten:

- Abonnementskosten zijn gebaseerd op gebruik en zijn exclusief vooruitbetaalde bedragen, overschrijdingen, opgenomen hoeveelheden, aanpassingen en belasting. De daadwerkelijke kosten worden op inschrijvingsniveau berekend.
- De bedragen die in Azure Portal worden weergegeven, kunnen afwijken van de bedragen in de Enterprise-portal. Het kan enkele minuten duren voordat wijzigingen als gevolg van updates in de Enterprise-portal worden weergegeven in Azure Portal.
- Als u geen kosten ziet, komt dit waarschijnlijk door een van de volgende oorzaken:
    - U hebt geen machtigingen op abonnementsniveau. Als u de zakelijke kostenweergaven wilt zien, moet u een lezer met factureringsrechten, een lezer, een inzender of een eigenaar op abonnementsniveau zijn.
    - U bent eigenaar van een account en uw inschrijvingsbeheerder heeft de instelling AO-weergavekosten uitgeschakeld.  Neem contact op met uw inschrijvingsbeheerder voor toegang tot kosten.
    - U bent een afdelingsbeheerder en uw inschrijvingsbeheerder heeft de instelling **DA-weergavekosten** uitgeschakeld.  Neem contact op met uw inschrijvingsbeheerder voor toegang.
    - U hebt Azure via een kanaalpartner aangeschaft en de partner heeft geen prijsgegevens gepubliceerd.  
- Als u instellingen met betrekking tot kostentoegang in de Enterprise-portal bijwerkt, is er een vertraging van enkele minuten voordat de wijzigingen worden weergegeven in de Azure-portal.
- Instructies voor de bestedingslimiet en facturen zijn niet van toepassing op EA-abonnementen.

### <a name="check-your-subscription-and-access"></a>Uw abonnement en toegang controleren

Als u kosten wilt weergeven, hebt u toegang tot kosten- of factureringsgegevens nodig op rekening- of abonnementsniveau. De toegang is afhankelijk van het type factureringsrekening. Zie[Uw factureringsrekeningen weergeven in de Azure-portal](view-all-accounts.md) voor meer informatie over factureringsrekeningen en het controleren van uw type factureringsrekening.

Als u toegang hebt tot Azure via een factureringsrekening van een Microsoft Online-serviceprogramma (MOSP), raadpleegt u [Toegang tot factureringsgegevens beheren voor Azure](manage-billing-access.md).

Als u toegang hebt tot Azure via een factureringsrekening van een Enterprise Agreement (EA), raadpleegt u [Informatie over beheerdersrollen voor Azure Enterprise Agreements in Azure ](understand-ea-roles.md).

Als u toegang hebt tot Azure via een factureringsrekening van een Microsoft-klantovereenkomst (MCA), raadpleegt u [Informatie over beheerdersrollen voor Microsoft-klantovereenkomsten in Azure ](understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Een Service Level Agreement-tegoed aanvragen voor een service-incident

In de SLA (Service Level Agreement) worden de toezeggingen van Microsoft voor bedrijfstijd en connectiviteit beschreven. Een service-incident wordt gerapporteerd wanneer er zich een probleem voordoet bij Azure-services die invloed heeft op de bedrijfstijd of connectiviteit, ook wel *uitval* genoemd. Als we de serviceniveaus voor elke service niet kunnen bereiken en onderhouden zoals dat wordt beschreven in de SLA, komt u mogelijk in aanmerking voor tegoed ter hoogte van een deel van uw maandelijkse servicekosten.

U kunt als volgt een tegoed aanvragen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/). Als u meerdere accounts hebt, controleert u of u het account gebruikt die door de uitvaltijd van Azure is beïnvloed.
2. Maak een nieuwe ondersteuningsaanvraag.
3. Onder **Probleemtype** selecteert u **Facturering**.
4. Onder **Probleemtype** selecteert u **Restitutieaanvraag**.
5. Voeg details toe om op te geven dat u een aanvraag indient voor een SLA-tegoed, en noem zowel de datum/tijd/tijdzone als de betrokken services (virtuele machines, websites, enzovoort)
6. Controleer uw contactgegevens en selecteer **Maken** om uw aanvraag in te dienen.

SLA-drempelwaarden variëren per service. De SQL-laag Web heeft bijvoorbeeld een SLA van 99,9%, virtuele machines hebben een SLA van 99,95% en de SQL-laag Standard heeft een SLA van 99,99%.

Voor een aantal services gelden voorwaarden voordat de SLA van toepassing is. Voor virtuele machines moeten bijvoorbeeld twee of meer instanties in dezelfde beschikbaarheidsset zijn geïmplementeerd.

Zie [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/) en de documentatie met de [SLA-samenvatting voor Azure-services](https://azure.microsoft.com/support/legal/sla/summary/) voor meer informatie.

## <a name="analyze-unexpected-charges"></a>Onverwachte kosten analyseren

De infrastructuur van de cloudresource die u voor uw organisatie hebt gemaakt, is waarschijnlijk ingewikkeld. Veel Azure-resourcetypen kunnen verschillende soorten kosten hebben. Azure-resources zijn mogelijk eigendom van verschillende teams in uw organisatie en zij kunnen verschillende typen factureringsmodellen hebben die van toepassing zijn op verschillende resources. Als u meer inzicht wilt krijgen in de kosten, kunt u om te beginnen deze analyseren met behulp van een of meer van de strategieën in de volgende secties.

### <a name="review-your-invoice-and-identify-the-resource-that-is-responsible-for-the-charge"></a>Controleer uw factuur en identificeer de resource die verantwoordelijk is voor de kosten

Hoe u uw Azure-services aanschaft, helpt u te bepalen welke methodologie en hulpprogramma's er voor u beschikbaar zijn wanneer u de resource identificeert die voor de kosten verantwoordelijk is. Als u wilt bepalen welke methodologie op u van toepassing is, [ moet u eerst vaststellen welk type Azure-aanbieding u hebt](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Bepaal vervolgens uw klantcategorie in de lijst met [ondersteunde Azure-aanbiedingen](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

De volgende artikelen bevatten gedetailleerde stappen waarin wordt uitgelegd hoe u uw factuur kunt controleren op basis van uw klanttype. In elk artikel vindt u instructies voor het downloaden van een CSV-bestand met daarin gegevens over het gebruik en de kosten over een bepaalde factureringsperiode.

- [Controleproces voor factuur voor Betalen per gebruik](../understand/review-individual-bill.md#compare-invoiced-charges-with-usage-file)
- [Controleproces voor factuur voor Enterprise Agreement](../understand/review-enterprise-agreement-bill.md)
- [Controleproces voor factuur voor Microsoft-klantovereenkomst](../understand/review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Controleproces voor Microsoft Partner-overeenkomst](../understand/review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Op uw Azure-factuur worden alle kosten voor die maand per _meter_ geaggregeerd. Meters worden gebruikt om het gebruik van een resource gedurende een bepaalde periode bij te houden, en worden gebruikt om uw factuur te berekenen. Wanneer u één Azure-resource maakt, zoals een virtuele machine, worden er een of meer meterexemplaren voor die resource gemaakt.

Filter het CSV-bestand met het gebruik op de _MeterName_ zoals die wordt weergegeven op de factuur die u wilt analyseren, zodat u alle regelitems ziet die van toepassing zijn op de meter. De _InstanceID_ voor het regelitem komt overeen met de werkelijke Azure-resource die de kosten heeft gegenereerd.

Wanneer u de desbetreffende resource hebt geïdentificeerd, kunt u Kostenanalyse in Azure Cost Management gebruiken om de kosten verder te analyseren die zijn gerelateerd aan de resource. Zie [Beginnen met analyseren van kosten](../costs/quick-acm-cost-analysis.md) voor meer informatie over het gebruik van Kostenanalyse.

### <a name="identify-spikes-in-cost-over-time"></a>Pieken in de kosten in de loop van de tijd identificeren

Soms weet u mogelijk niet welke recente kosten de wijzigingen in uw gefactureerde kosten hebben veroorzaakt. Als u wilt weten wat er is gewijzigd, kunt u Kostenanalyse gebruiken om [een uitsplitsing per dag of per maand te zien van kosten die in de loop van de tijd zijn gemaakt](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). Nadat u de weergave hebt gemaakt, kunt u de kosten groeperen op **service** of **resource** om de wijzigingen te identificeren. U kunt uw weergave ook wijzigen in een **lijndiagram** voor een betere visualisatie van de gegevens.

![Voorbeeld van de kosten in de loop van de tijd in Kostenanalyse](./media/getting-started/costs-over-time.png)

### <a name="determine-resource-pricing-and-understand-its-billing-model"></a>Resourceprijzen bepalen en het factureringsmodel begrijpen

Eén enkele resource kan zorgen voor een toename van de kosten voor meerdere Azure-producten en -services. Bekijk de pagina [Azure-prijzen per product](https://azure.microsoft.com/pricing/#product-pricing) voor meer informatie over de prijzen voor elke Azure-service. Een virtuele machine (VM) die in Azure is gemaakt, kan bijvoorbeeld de volgende meters hebben om het gebruik bij te houden. Elk van deze kan verschillende prijzen hebben.

- Rekenuren
- Uren IP-adres
- Inkomende gegevensoverdracht
- Uitgaande gegevensoverdracht
- Standard - Beheerde schijf
- Standard - Beheerde schijfbewerkingen
- Standard - IO - Schijf
- Standard - IO - Lezen blok-blob
- Standard - IO - Schrijven blok-blob
- Standard - IO - Verwijderen blok-blob

Wanneer de virtuele machine wordt gemaakt, begint elke meter gebruiksrecords te verzenden. Het gebruik en de metertarieven worden bijgehouden in het Azure-metersysteem. In het CSV-gebruiksbestand kunt u de meters zien die zijn gebruikt voor het berekenen van uw factuur.

### <a name="find-the-people-responsible-for-the-resource-and-engage-them"></a>Stel vast welke personen verantwoordelijk zijn voor de resource en spreek ze erop aan

Vaak weet het team dat verantwoordelijk is voor een bepaalde resource, dat er iets is veranderd met betrekking tot een resource. Het is nuttig om het team erbij te betrekken als u wilt vaststellen waarom er bepaalde kosten zijn gemaakt. Het verantwoordelijke team kan de resource bijvoorbeeld onlangs hebben gemaakt, de SKU ervan hebben bijgewerkt (waardoor de resourcetarieven zijn gewijzigd) of de workload voor de resource hebben verhoogd vanwege wijzigingen in de code. Lees ook de volgende secties voor meer technieken om te bepalen wie eigenaar is van een resource.

#### <a name="analyze-the-audit-logs-for-the-resource"></a>De auditlogboeken voor de resource analyseren

Als u gemachtigd bent om een resource weer te geven, zou u ook toegang moeten hebben tot de auditlogboeken. Raadpleeg de logboeken om de gebruiker te vinden die verantwoordelijk is voor de meest recente wijzigingen aan een resource. Zie [Gebeurtenissen in het Azure-activiteitenlogboek weergeven en ophalen](../../azure-monitor/platform/activity-log-view.md) voor meer informatie.

#### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Gebruikersmachtigingen voor het bovenliggende bereik van de resource analyseren

Personen met schrijftoegang tot een abonnement of resourcegroep beschikken meestal over informatie over de resources die zijn gemaakt. Ze zouden het doel van een resource moeten kunnen verklaren of u kunnen verwijzen naar de persoon die het wel weet. Zie [Roltoewijzingen weergeven](../../role-based-access-control/check-access.md#view-role-assignments) als u wilt weten hoe u kunt vaststellen welke mensen er machtigingen voor een abonnementsbereik hebben. U kunt een vergelijkbaar proces voor resourcegroepen gebruiken.

### <a name="get-help-to-identify-charges"></a>Hulp krijgen bij het identificeren van kosten

Als u de voorgaande strategieën hebt gebruikt en u nog steeds niet weet waar bepaalde kosten vandaan komen, of als u hulp nodig hebt bij factureringsproblemen, kunt u [een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het gebruik van [bestedingslimieten](spending-limit.md) om te voorkomen dat u teveel uitgeeft.
- Begin met het [analyseren van uw Azure-kosten](../costs/quick-acm-cost-analysis.md).
