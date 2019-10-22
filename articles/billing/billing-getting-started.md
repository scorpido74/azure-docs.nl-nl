---
title: Onverwachte kosten voorkomen en facturering beheren in Azure
description: Meer informatie over het vermijden van onverwachte kosten op uw Azure-factuur. Gebruik functies voor het bijhouden van kosten en beheer voor uw Azure-account.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 6f7a29adfbd145be11f9f6c91e8e66dd229fed62
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375681"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Onverwachte kosten voorkomen met Azure-facturering en -kostenbeheer

Wanneer u zich aanmeldt voor Azure, zijn er een aantal zaken die u kunt doen om meer inzicht in uw uitgaven te krijgen:

- Ontvang geschatte kosten voordat u services toevoegt met behulp van de [prijscalculator](https://azure.microsoft.com/pricing/calculator/), het prijzenoverzicht voor Azure of bij het toevoegen van services in de Azure-portal.
- Bewaak kosten met [budgetten](../cost-management/tutorial-acm-create-budgets.md), [meldingen](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) en [kostenanalyses](../cost-management/quick-acm-cost-analysis.md).
- Controleer de kosten op uw factuur door deze te vergelijken met [gedetailleerde gebruiksgegevensbestanden](billing-download-azure-invoice-daily-usage-date.md).
- Integreer facturerings- en kostengegevens met uw eigen rapportagesysteem met behulp van de [Billing](https://docs.microsoft.com/rest/api/billing/)- en [Consumption](https://docs.microsoft.com/rest/api/consumption/)-API's.
- Gebruik aanvullende bronnen en hulpprogramma's voor Enterprise Agreement- (EA), Cloud Solution Provider- (CSP) en Azure Sponsorship-klanten.
- Maak [gratis gebruik van een aantal populaire Azure-services gedurende 12 maanden](billing-create-free-services-included-free-account.md) die beschikbaar zijn bij het [gratis Azure-account](https://azure.microsoft.com/free/). Naast de aanbevelingen die hieronder worden vermeld, zie [Voorkomen dat u moet betalen voor een gratis account](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Geschatte kosten ontvangen voordat u Azure-services toevoegt

Gebruik een van de volgende hulpprogramma's om de verbruikskosten van een Azure-service te schatten:
- Azure-prijscalculator
- Prijzenoverzicht voor Azure
- Azure Portal

Op de afbeeldingen in de volgende secties ziet u voorbeeldprijzen in Amerikaanse dollar.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Online kosten schatten met behulp van de Prijscalculator

Bekijk de [Prijscalculator](https://azure.microsoft.com/pricing/calculator/) voor geschatte maandelijkse kosten van de service die u wilt toevoegen. U kunt de valuta wijzigen om de schatting in uw lokale valuta weer te geven.

![Schermopname van het menu Prijscalculator](./media/billing-getting-started/pricing-calc.png)

U kunt de geschatte kosten weergeven voor elke eigen Azure-service. In de onderstaande schermopname wordt bijvoorbeeld geschat dat de rekenuren voor een A1 virtuele Windows-machine $ 66,96 per maand kost als u deze VM de hele tijd blijft uitvoeren:

![Schermopname van de Prijscalculator, met de geschatte kosten voor een A1 Windows-VM](./media/billing-getting-started/pricing-calcvm.png)

Zie de [Veelgestelde vragen over prijzen](https://azure.microsoft.com/pricing/faq/) voor meer informatie over prijzen. Als u met een Azure-verkoopmedewerker wilt spreken, belt u het telefoonnummer bovenaan de pagina Veelgestelde vragen.

### <a name="view-and-download-azure-price-sheet"></a>Prijzenoverzicht voor Azure weergeven en downloaden

Als u toegang hebt tot Azure via een Enterprise Agreement (EA) of een Microsoft-klantovereenkomst (MCA), kunt u het prijzenoverzicht voor uw Azure-account bekijken en downloaden. Het prijzenoverzicht is een Excel-bestand met prijzen voor alle Azure-Services. Zie [Uw prijzen voor Azure weergeven en downloaden](billing-ea-pricing.md) voor meer informatie.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Geschatte kosten controleren in Azure Portal

U kunt de geschatte kosten per maand bekijken bij het toevoegen van een service in de Azure-portal. Wanneer u bijvoorbeeld de grootte van uw Windows-VM kiest, ziet u de geschatte maandelijkse kosten voor de rekenuren:

![Voorbeeld: een A1 Windows-VM, met geschatte kosten per maand](./media/billing-getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Kosten bewaken bij het gebruik van Azure-services
U kunt kosten bewaken met de volgende hulpprogramma's:

- Budget- en kostenmeldingen
- Kostenanalyse

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Kosten bijhouden met budget- en kostenmeldingen

Maak [budgetten](../cost-management/tutorial-acm-create-budgets.md) om kosten te beheren maak en [meldingen](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende en te hoge uitgaven. 

### <a name="costs"></a> Kosten verkennen en analyseren met Kostenanalyse

Nadat u uw Azure-services bent gaan uitvoeren, moet u de kosten regelmatig controleren om uw Azure-uitgaven bij te houden. U kunt Kostenanalyse gebruiken om inzicht te krijgen in de kosten van uw Azure-gebruik. 

1. Ga naar [Kostenbeheer en facturering in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Klik op **Kostenanalyse** aan de linkerkant van het scherm om de huidige kosten weer te geven, onderverdeeld naar verschillende draaipunten, zoals de service, locatie en het abonnement. Als u een service toevoegt of een aankoop doet, moet u 24 uur wachten voordat de gegevens worden weergegeven. Standaard worden in Kostenanalyse de kosten voor uw huidige bereik weergegeven. In de onderstaande schermopname worden bijvoorbeeld kosten voor de factureringsrekening van Contoso weergegeven. Gebruik Bereik om over te schakelen naar een ander bereik in Kostenanalyse. Zie [Understand and work with scopes](../cost-management/understand-work-scopes.md#scopes) (Engelstalig) voor meer informatie over bereiken

    ![Schermopname van de kostenanalyseweergave in Azure Portal](./media/billing-getting-started/cost-analysis.png)

4. U kunt de weergave filteren op verschillende eigenschappen, zoals labels, resourcetype en tijdsbestek. Klik op **Filter toevoegen** om het filter voor een eigenschap toe te voegen en selecteer de gewenste filterwaarden. Selecteer **Exporteren** als u de weergave wilt exporteren als bestand met door komma's gescheiden waarden (CSV).

5. Daarnaast kunt u op de labels van het diagram klikken om de dagelijkse uitgavengeschiedenis voor dat label weer te geven. Bijvoorbeeld: In de onderstaande schermopname worden de dagelijkse kosten van het uitvoeren van uw VM's weergegeven door op virtuele machines te klikken.

    ![Schermopname van de uitgavengeschiedenisweergave in Azure Portal](./media/billing-getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Kosten optimaliseren en verlagen
Als u niet bekend bent met de principes van kostenbeheer, leest u [Uw cloudinvestering optimaliseren met Azure Cost Management](../cost-management/cost-mgt-best-practices.md).

In Azure Portal kunt u ook Azure-kosten optimaliseren en verlagen met behulp van automatisch afsluiten voor virtuele machines en Advisor-aanbevelingen.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Kostenbesparende functies overwegen zoals automatisch afsluiten voor virtuele machines

Afhankelijk van uw scenario kunt u automatisch afsluiten voor uw virtuele machines configureren in Azure Portal. Zie [Automatisch afsluiten voor virtuele machines met behulp van Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) voor meer informatie.

![Schermopname van de optie Automatisch afsluiten in de portal](./media/billing-getting-started/auto-shutdown.png)

Automatisch afsluiten is wat anders dan uitschakelen in de virtuele machine met behulp van aan-uitopties. Met Automatisch afsluiten worden uw virtuele machines gestopt en wordt de toewijzing hiervan ongedaan gemaakt om ervoor te zorgen dat er geen extra kosten meer in rekening worden gebracht. Zie de veelgestelde vragen over prijzen voor [virtuele Linux-machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [virtuele Windows-machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) over VM-statussen voor meer informatie.

Raadpleeg [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) voor meer kostenbesparende functies voor uw ontwikkelings- en testomgevingen.

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Azure Advisor-aanbevelingen inschakelen en beoordelen

[Azure Advisor](../advisor/advisor-overview.md) helpt u kosten te besparen door resources te identificeren die weinig worden gebruikt. Zoek naar **Advisor** in de Azure-portal:

![Schermopname van de knop Azure Advisor in Azure Portal](./media/billing-getting-started/advisor-button.png)

Selecteer **Kosten** aan de linkerkant. Op het tabblad **Kosten** ziet u aanbevelingen voor acties:

![Schermopname van een voorbeeld van de Advisor-aanbevelingen voor kosten](./media/billing-getting-started/advisor-action.png)

Lees de zelfstudie [Kosten optimaliseren op basis van aanbevelingen](../cost-management/tutorial-acm-opt-recommendations.md) voor een begeleide zelfstudie over kostenbesparende Advisor-aanbevelingen.

## <a name="review-charges-against-your-latest-invoice"></a>In rekening gebrachte kosten controleren op basis van uw meest recente factuur

Aan het einde van de factureringscyclus is uw factuur beschikbaar. U kunt ook [facturen en bestanden met gedetailleerde gebruiksgegevens downloaden](billing-download-azure-invoice-daily-usage-date.md) en deze vergelijken om te controleren of de juiste kosten in rekening zijn gebracht. Zie[Meer informatie over uw factuur voor Microsoft Azure](billing-understand-your-bill.md) voor meer informatie over het vergelijken van uw dagelijkse gebruik met uw factuur.

Als u Azure gebruikt via een Microsoft-klantovereenkomst (MCA), kunt u ook [uw factuur vergelijken met de transacties ](billing-mca-understand-your-bill.md#view-transactions-for-an-invoice-in-the-azure-portal) om inzicht te krijgen in de kosten op uw factuur.

## <a name="integrate-with-billing-and-consumption-apis"></a>Integreren met de API's voor facturerings- en verbruiksgegevens

Gebruik de Azure [Billing](https://docs.microsoft.com/rest/api/billing/)- en [Consumption](https://docs.microsoft.com/rest/api/consumption/)-API's om programmatisch facturerings-en kostengegevens op te halen. Gebruik zowel de RateCard-API als de gebruik-API om uw gefactureerde gebruik op te halen. Zie [Inzicht verkrijgen in het gebruik van Microsoft Azure-resources](billing-usage-rate-card-overview.md)voor meer informatie.

## <a name="other-offers"></a> Aanvullende resources en speciale gevallen

### <a name="ea-csp-and-sponsorship-customers"></a>EA-, CSP- en Sponsorship-klanten
Ga in gesprek met uw accountbeheerder of Azure-partner om aan de slag te gaan.

| Aanbieding | Resources |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise Agreement (EA) | [EA portal](https://ea.azure.com/), [helpdocumenten](https://ea.azure.com/helpdocs) en [Power BI-rapport](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | In gesprek gaan met uw provider |
| Azure Sponsorship | [Sponsorship-portal](https://www.microsoftazuresponsorships.com/) |

Als u IT voor een grote organisatie beheert, wordt aanbevolen de [zakelijke ondersteuning voor Azure](/azure/architecture/cloud-adoption-guide/subscription-governance) en het [zakelijke technische document voor IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) te lezen (PDF-downloads, alleen in het Engels).

### <a name="EA"></a> Enterprise Agreement-kostenweergaven in Azure Portal

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

Als u kosten wilt weergeven, hebt u toegang tot kosten- of factureringsgegevens nodig op rekening- of abonnementsniveau. De toegang is afhankelijk van het type factureringsrekening. Zie[Uw factureringsrekeningen weergeven in de Azure-portal](billing-view-all-accounts.md) voor meer informatie over factureringsrekeningen en het controleren van uw type factureringsrekening.

Als u toegang hebt tot Azure via een factureringsrekening van een Microsoft Online-serviceprogramma (MOSP), raadpleegt u [Toegang tot factureringsgegevens beheren voor Azure](billing-manage-access.md).

Als u toegang hebt tot Azure via een factureringsrekening van een Enterprise Agreement (EA), raadpleegt u [Informatie over beheerdersrollen voor Azure Enterprise Agreements in Azure ](billing-understand-ea-roles.md).

Als u toegang hebt tot Azure via een factureringsrekening van een Microsoft-klantovereenkomst (MCA), raadpleegt u [Informatie over beheerdersrollen voor Microsoft-klantovereenkomsten in Azure ](billing-understand-mca-roles.md).

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
