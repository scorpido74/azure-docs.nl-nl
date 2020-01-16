---
title: Inzicht in Cloudyn-rapporten in Azure kosten | Microsoft Docs
description: Dit artikel helpt u inzicht in de basisstructuur voor de rapporten voor management van Cloudyn kosten en functies.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: fff08bc30e0d1bae77a2aee641f6e211cea40beb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989109"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Inzicht in Cloudyn-rapporten voor kosten

Dit artikel helpt u inzicht in de basisstructuur voor de rapporten voor management van Cloudyn kosten en functies. De meeste Cloudyn-rapporten zijn intuïtief en een uniform uiterlijk hebben. Nadat u dit artikel hebt gelezen, gaan alle kostenbeheerrapporten gebruiken. Veel standard-functies zijn beschikbaar in de verschillende rapporten, zodat u kunt de rapporten moeiteloos navigeren. Rapporten kunnen worden aangepast en u kunt kiezen uit verschillende opties om te berekenen en de resultaten weer te geven.

## <a name="report-fields-and-options"></a>Rapportvelden en opties

Hier volgt een overzicht van een voorbeeld van het rapport Cost Over Time. De meeste Cloudyn-rapporten hebben een vergelijkbare indeling.

![Voorbeeld van het rapport Cost Over Time met genummerde gebieden die overeenkomt met beschrijvingen](./media/understanding-cost-reports/sample-report.png)

Elke genummerde gebied in de voorgaande afbeelding wordt beschreven in de volgende informatie:

1. **Datumbereik**

    Gebruik de lijst datumbereik voor het definiëren van een rapport tijdsinterval met behulp van een vooraf ingestelde of aangepast.
2. **Opgeslagen Filter**

    De lijst opgeslagen Filter gebruiken om op te slaan van de huidige groepen en filters die worden toegepast op het rapport. Opgeslagen filters zijn beschikbaar voor de kosten en prestaties van rapporten, waaronder:

      - Kostenanalyse
      - Toewijzing
      - Asset-management
      - Optimalisatie

   Typ de naam van een filter en klik op **opslaan**.

3. **Tags**

    Gebruik het gebied van de Tags aan groep door de tag categorieën. Labels die worden vermeld in het menu Azure afdeling of kosten center tags of ze zijn het kosten entiteits- en abonnement tags van Cloudyn. Selecteer de tags om resultaten te filteren. U kunt ook de naam van een tag (trefwoord) om resultaten te filteren typen.

    ![Voorbeeld van een lijst met tags voor het filteren van resultaten op](./media/understanding-cost-reports/select-options.png)

    Klik op **toevoegen** een nieuw filter toe te voegen.

    ![Filter toevoegen met opties en voorwaarden om te filteren op](./media/understanding-cost-reports/add-filter.png)

    Tag groeperen of filteren heeft geen betrekking op Azure-resources of groep resourcetags.

    Kostentoewijzing tag groeperen en filteren zijn beschikbaar in de **groepen** menu-optie.

4. **Groepen in rapporten**

    Groepen gebruiken in Cost Analysis rapporten om weer te geven standaard, ingedeeld categorieën van facturering van gegevens in uw rapport.  Echter bekijken groepen in Cost Allocation rapporten weergeven op basis van een tag categorieën. Tag op basis van categorieën worden gedefinieerd in het model voor kostentoewijzing en standard gespecificeerde categorieën van factureringsgegevens.

    ![Eerste voorbeeld van de lijst met labels die u kunt groeperen op](./media/understanding-cost-reports/groups-tags01.png)

    ![Tweede voorbeeld van de lijst met labels die u kunt groeperen op](./media/understanding-cost-reports/groups-tags02.png)

    Groepen in de groep op basis van een tag categorieën zijn in Kostenrapporten toewijzing, onder andere:
      - Tags
      - groep resourcetags
      - Cloudyn kosten entity-tags
      - Abonnement tag categorieën voor doel voor het toewijzen van kosten

   Voorbeelden zijn onder andere:
   - Kostenplaats
   - Afdeling
   - Toepassing
   - Omgeving
   - Kosten code

     Hier volgt een lijst van ingebouwde groepen in rapporten beschikbaar:

     - **Kostentype**
     - Selecteer een kostentype of meerdere kostensoorten of Alles selecteren. Kostensoorten zijn onder andere:
       - Eenmalig
       - Ondersteuning
       - Gebruikskosten
     - **De klant**
       - Selecteer een specifieke klant, meerdere klanten, of alle klanten.
     - **Accountnaam**
       - De naam van het account of abonnement. In Azure is de naam van het Azure-abonnement.
     - **Geen account**
       - Selecteer een account, meerdere accounts of alle accounts. In Azure is de GUID van het Azure-abonnement.
     - **Bovenliggende Account**
       - Selecteer de bovenliggende account, meerdere accounts of selecteren.
     - **Service**
       - Selecteer een service, meerdere services, of alle services.
     - **Provider**
       - De cloudprovider waarin activa en kosten gekoppeld worden.
     - **Regio**
       - De regio waar de resource wordt gehost.
     - **Binnen een Beschikbaarheidszone**
       - AWS geïsoleerd locaties binnen een regio.
     - **Resourcetype**
       - Het type resource in gebruik.
     - **Sub-Type**
       - Selecteer het subtype.
     - **Bewerking**
       - Selecteer de bewerking of **Show all**.
     - **Prijsmodel**
       - Alle kosten vooraf
       - Geen kosten vooraf
       - Gedeeltelijk kosten vooraf
       - Op aanvraag
       - Reservering
       - Spot
     - **Type van de kosten in rekening gebracht**
       - Selecteer negatieve of positieve kosten type of beide.
     - **Tenants**
       - Of een virtuele machine wordt uitgevoerd als een toegewezen machine.
     - **Gebruikstype**
       - Gebruikstype zijn eenmalige kosten of terugkerende kosten.

5. **Filters**

    Enkelvoudige of meervoudige selectie filters gebruiken om te bereiken op geselecteerde waarden ingesteld. Als u wilt een filter wilt instellen, klikt u op **toevoegen** en selecteer vervolgens filtercategorieën en waarden.

6. **Kosten Model**

    Selecteer een kostenmodel dat u eerder hebt gemaakt met Cost Allocation 360 met behulp van kostenmodel. Mogelijk hebt u meerdere modellen voor Cloudyn-kosten, afhankelijk van uw vereisten van de toewijzing van kosten. Sommige van uw organisatie teams mogelijk kosten toewijzing eisen die van anderen verschillen. Elk team kan hun eigen toegewezen kostenmodel hebben.

    Zie voor meer informatie over het maken van een definitie van cost allocation model [aangepaste tags gebruiken voor het toewijzen van kosten](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Afschrijving**

    Gebruik afschrijving in Cost Allocation rapporten om niet-gebruik weer te geven op basis van servicekosten of eenmalige kosten van leveranciers en hun kosten na verloop van tijd tijdens hun levensduur gelijkmatig verdeeld. Voorbeelden van eenmalige kosten zijn onder andere:
    - Jaarlijkse kosten van ondersteuning
    - Jaarlijkse kosten van security-onderdelen
    - Kopen van gereserveerde instanties kosten
    - Sommige items voor de Azure Marketplace.

   Selecteer onder afschrijving, **afgeschreven kosten** of **werkelijke kosten**.

8. **Resolutie**

    Gebruik het probleem zou moeten de resolutie van de tijd binnen het geselecteerde datumbereik selecteren. De resolutie van uw tijd bepaalt hoe eenheden worden weergegeven in het rapport en kunnen worden:
    - Dagelijks
    - Elke week
    - Maandelijks
    - Per kwartaal
    - Jaarlijks

9. **Toewijzingsregels voor**

    Toewijzingsregels gebruiken om te passen of uitschakelen van de kostentoewijzing kosten opnieuw berekenen. U kunt inschakelen of uitschakelen van de herberekening van de toewijzing van kosten voor factureringsgegevens. De herberekening is van toepassing op de geselecteerde categorieën in het rapport. Hiermee kunt u beoordelen effect herberekening voor de toewijzing van kosten op basis van onbewerkte gegevens van de facturering.

10. **Niet-gecategoriseerde**

    Gebruik niet-gecategoriseerd wilt opnemen of uitsluiten van niet-gecategoriseerde kosten in het rapport.

11. **Velden weergeven/verbergen**

    De optie weergeven/verbergen heeft geen effect in rapporten.

12. **Weergave-indelingen**

    Weergave-indelingen gebruiken verschillende weergaven voor graph of de tabel selecteren.

    ![De symbolen van het weergave-indelingen die u kunt selecteren](./media/understanding-cost-reports/display-formats.png)

13. **Multi-color**

    Gebruik meerdere kleur om in te stellen de kleur van de grafieken in uw rapport.

14. **Acties**

    Gebruik acties wilt opslaan, exporteren of het rapport plant.

15. **Beleid**

    Hoewel niet afgebeeld, bevatten sommige rapporten een beleid voor het berekenen van geschatte kosten. In deze rapporten, de **geconsolideerde** beleid bevat aanbevelingen voor alle accounts en abonnementen onder de huidige entiteit, zoals Microsoft inschrijvings- of AWS afspelen. De **zelfstandige** beleid bevat aanbevelingen voor een account of abonnement alsof er zijn geen andere abonnementen. Is afhankelijk van het beleid dat u selecteert op de optimalisatie-strategie door uw organisatie gebruikt. Kosten schattingen zijn gebaseerd op de afgelopen 30 dagen van het gebruik van.

## <a name="save-and-schedule-reports"></a>Opslaan en rapporten plannen

Nadat u een rapport maakt, kunt u deze kunt opslaan voor toekomstig gebruik. Opgeslagen rapporten zijn beschikbaar in **hulpprogramma's voor mijn** > **mijn rapporten**. Als u wijzigingen in een bestaand rapport aanbrengen en deze opslaat, wordt het rapport wordt opgeslagen als een nieuwe versie. Of, kunt u deze opslaan als een nieuw rapport.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Een rapport opslaan in de Cloudyn-portal

Wanneer u een rapport bekijkt, klikt u op **acties** en selecteer vervolgens **opslaan in Mijn rapporten**. Het rapport een naam en voeg vervolgens een uw eigen URL of de automatisch gemaakte URL gebruiken. U kunt eventueel **delen** het rapport openbaar met anderen in uw organisatie of als u deze aan uw entiteit kunt delen. Als u het rapport niet te delen, een persoonlijke rapport blijft en dat alleen kunt u bekijken. Sla het rapport op.


### <a name="save-a-report-to-cloud-provider-storage"></a>Een rapport naar de cloud storage provider opslaan

Als u wilt een rapport opslaan in uw cloudserviceprovider, moet u al zijn geconfigureerd een storage-account. Wanneer u een rapport bekijkt, klikt u op **acties** en selecteer vervolgens **rapport plant**. Het rapport een naam en voeg vervolgens een uw eigen URL of de automatisch gemaakte URL gebruiken. Selecteer **opslaan in storage** en selecteer het opslagaccount of een nieuwe toevoegen. Voer een voorvoegsel dat wordt toegevoegd aan de naam van het rapport. Selecteer een CSV-of JSON-bestands indeling en sla het rapport op.

### <a name="schedule-a-report"></a>Een rapport plannen

U kunt rapporten uitvoeren met regelmatige tussenpozen en u kunt deze verzonden naar een ontvanger lijst of cloud serviceprovider storage-account. Wanneer u een rapport bekijkt, klikt u op **acties** en selecteer vervolgens **rapport plant**. U kunt het rapport per e-mail verzenden en opslaan op een storage-account. Onder **planning**, selecteert u het interval (dagelijks, wekelijks of maandelijks). Selecteer de dag of datums te leveren en selecteer de tijd voor wekelijkse en maandelijkse. Sla het geplande rapport op. Als u de indeling van het Excel-rapport selecteert, wordt het rapport als een bijlage verzonden. Wanneer u inhoud e-mailindeling selecteert, worden de rapportresultaten die worden weergegeven in de grafiekindeling als een grafiek geleverd.

### <a name="export-a-report-as-a-csv-file"></a>Een rapport exporteren als een CSV-bestand

Wanneer u een rapport bekijkt, klikt u op **acties** en selecteer vervolgens **alle rapportgegevens exporteren**. Een pop-upvenster wordt weergegeven en een CSV-bestand wordt gedownload.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de rapporten die zijn opgenomen in Cloudyn op [rapporten van Cloudyn gebruik](../../cost-management/use-reports.md).
- Meer informatie over het gebruik van rapporten maken [dashboards](../../cost-management/dashboards.md).
