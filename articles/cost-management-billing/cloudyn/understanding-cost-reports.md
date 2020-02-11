---
title: Inzicht in Cloudyn-rapporten voor kostenbeheer in Azure | Microsoft Docs
description: Dit artikel helpt u inzicht te krijgen in de basisstructuur en functies van Cloudyn-rapporten voor kostenbeheer.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ms.openlocfilehash: 89bcbf75396a1eda9de4a715739adb3a3696e137
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76773989"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Inzicht in Cloudyn-rapporten voor kostenbeheer

Dit artikel helpt u inzicht te krijgen in de basisstructuur en functies van Cloudyn-rapporten voor kostenbeheer. De meeste Cloudyn-rapporten zijn intuïtief en hebben een uniforme vormgeving. Nadat u dit artikel hebt gelezen, kunt u alle rapporten voor kostenbeheer gebruiken. Veel standaardfuncties zijn beschikbaar in de verschillende rapporten, zodat u gemakkelijk door de rapporten kunt navigeren. Rapporten kunnen worden aangepast, en u kunt kiezen uit verschillende opties om de resultaten te berekenen en weer te geven.

## <a name="report-fields-and-options"></a>Rapportvelden en -opties

Hier volgt een voorbeeld van het rapport Cost Over Time (Kosten in de loop van de tijd). De meeste Cloudyn-rapporten hebben een vergelijkbare indeling.

![Voorbeeld van het rapport Cost Over Time (Kosten in de loop van de tijd), met genummerde gebieden die overeenkomen met beschrijvingen](./media/understanding-cost-reports/sample-report.png)

Elk genummerd gebied in de voorgaande afbeelding wordt gedetailleerd beschreven in de volgende informatie:

1. **Datumbereik**

    Gebruik de lijst Datumbereik om een tijdsinterval voor een rapport te definiëren met behulp van een voorinstelling of aangepaste instelling.
2. **Opgeslagen filter**

    Met de lijst Opgeslagen filter kunt u de huidige groepen en filters opslaan die op het rapport worden toegepast. Opgeslagen filters zijn beschikbaar in kosten- en prestatierapporten, waaronder:

      - Kostenanalyse
      - Toewijzing
      - Asset-management
      - Optimalisatie

   Typ een filternaam en klik op **Opslaan**.

3. **Tags**

    Gebruik het gebied Tags om categorieën te groeperen op tags. Tags die in het menu worden weergegeven, zijn afdelings- of kostenplaatstags van Azure of kostenentiteits- en abonnementstags van Cloudyn. Selecteer tags om de resultaten te filteren. U kunt ook een tagnaam (sleutelwoord) typen om de resultaten te filteren.

    ![Voorbeeld van een lijst met tags waarop u resultaten kunt filteren](./media/understanding-cost-reports/select-options.png)

    Klik op **Toevoegen** als u een nieuw filter wilt toevoegen.

    ![Vak Filter toevoegen met opties en filtervoorwaarden](./media/understanding-cost-reports/add-filter.png)

    Het groeperen of filteren op tags is niet gerelateerd aan tags van Azure-resources of -resourcegroepen.

    U kunt kostentoewijzingen groeperen en filteren op tags via de menuoptie **Groepen**.

4. **Groepen in rapporten**

    Gebruik groepen in kostenanalyserapporten om gespecificeerde standaardcategorieën van factureringsgegevens in uw rapport weer te geven.  In groepen in kostentoewijzingsrapporten worden wel op tags gebaseerde categorieën weergegeven. Op tags gebaseerde categorieën worden gedefinieerd in het kostentoewijzingsmodel en gespecificeerde standaardcategorieën van factureringsgegevens.

    ![Eerste voorbeeld van een lijst met tags waarop u kunt groeperen](./media/understanding-cost-reports/groups-tags01.png)

    ![Tweede voorbeeld van een lijst met tags waarop u kunt groeperen](./media/understanding-cost-reports/groups-tags02.png)

    In kostentoewijzingsrapporten kunnen groepen in op tags gebaseerde categorieën het volgende omvatten:
      - Tags
      - tags van resourcegroepen
      - tags van Cloudyn-kostenentiteiten
      - Abonnementstagcategorieën voor kostentoewijzingsdoeleinden

   Voorbeelden hiervan zijn:
   - Kostenplaats
   - Afdeling
   - Toepassing
   - Omgeving
   - Kostencode

     Hier volgt een lijst met ingebouwde groepen die beschikbaar zijn in rapporten:

     - **Kostentype**
     - Selecteer een kostentype, meerdere kostentypen of alle kostentypen. Mogelijke kostentypen zijn:
       - Eenmalige kosten
       - Ondersteuning
       - Gebruikskosten
     - **Klant**
       - Selecteer een specifieke klant, meerdere klanten of alle klanten.
     - **Accountnaam**
       - De naam van het account of abonnement. In Azure is dit de naam van het Azure-abonnement.
     - **Accountnummer**
       - Selecteer een account, meerdere accounts of alle accounts. In Azure is dit de GUID van het Azure-abonnement.
     - **Bovenliggend account**
       - Selecteer het bovenliggende account, meerdere accounts of alle accounts.
     - **Service**
       - Selecteer een service, meerdere services of alle services.
     - **Provider**
       - De cloudprovider waar assets en uitgaven zijn gekoppeld.
     - **Regio**
       - Regio waar de resource wordt gehost.
     - **Beschikbaarheidszone**
       - Geïsoleerde AWS-locaties binnen een regio.
     - **Resourcetype**
       - Het type resource dat wordt gebruikt.
     - **Subtype**
       - Selecteer het subtype.
     - **Bewerking**
       - Selecteer de bewerking of selecteer **Alles weergeven**.
     - **Prijsmodel**
       - Alles vooruit
       - Niets vooruit
       - Gedeeltelijk vooruit
       - Op aanvraag
       - Reservering
       - Spot
     - **Kostentype**
       - Selecteer een negatief of positief kostentype, of beide.
     - **Tenancy**
       - Hiermee wordt aangegeven of een machine wordt uitgevoerd als een toegewezen machine.
     - **Gebruikstype**
       - Dit kunnen eenmalige kosten of terugkerende kosten zijn.

5. **Filters**

    Gebruik een of meer filters om bereiken in te stellen op bepaalde waarden. Als u een filter wilt instellen, klikt u op **Toevoegen** en selecteert u vervolgens filtercategorieën en -waarden.

6. **Kostenmodel**

    Gebruik Kostenmodel om een kostenmodel te selecteren dat u eerder hebt gemaakt met Kostentoewijzing 360. U hebt mogelijk meerdere Cloudyn-kostenmodellen, afhankelijk van uw vereisten voor kostentoewijzingen. Sommige teams van uw organisatie kunnen andere kostentoewijzingsvereisten hebben. Elk team kan een eigen toegewezen kostenmodel hebben.

    Zie [Aangepaste tags voor kostentoewijzingen gebruiken](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) voor informatie over het maken van een definitie voor een kostentoewijzingsmodel.

7. **Afschrijving**

    Gebruik afschrijving in kostentoewijzingsrapporten om niet op gebruik gebaseerde servicekosten of eenmalig te betalen kosten weer te geven. De kosten in deze rapporten worden gelijkmatig verspreid over hun levensduur. Voorbeelden van eenmalige kosten kunnen het volgende omvatten:
    - Jaarlijkse kosten voor ondersteuning
    - Jaarlijkse kosten voor beveiligingsonderdelen
    - Kosten voor aankoop van gereserveerde instanties
    - Bepaalde Azure Marketplace-items.

   Selecteer **Afgeschreven kosten** of **Werkelijke kosten** onder Afschrijving.

8. **Afsluiting**

    Gebruik Afsluiting om de afsluitingstijd binnen het geselecteerde datumbereik te selecteren. De afsluitingstijd bepaalt hoe eenheden worden weergegeven in het rapport. Mogelijke frequenties zijn:
    - Dagelijks
    - Wekelijks
    - Maandelijks
    - Driemaandelijks
    - Jaarlijks

9. **Toewijzingsregels**

    Gebruik toewijzingsregels om de herberekening van kosten voor kostentoewijzingen toe te passen of uit te schakelen. U kunt de herberekening van kostentoewijzingen voor factureringsgegevens in- of uitschakelen. De herberekening is van toepassing op de geselecteerde categorieën in het rapport. Zo kunt u het effect van de herberekening van een kostentoewijzing op onbewerkte factureringsgegevens beoordelen.

10. **Niet-gecategoriseerd**

    Gebruik Niet-gecategoriseerd om niet-categoriseerde kosten in het rapport op te nemen of uit te sluiten.

11. **Velden weergeven/verbergen**

    De optie Weergeven/Verbergen is niet van invloed op rapporten.

12. **Weergave-indelingen**

    Gebruik Weergave-indelingen om verschillende grafiek- of tabelweergaven te selecteren.

    ![Symbolen van weergave-indelingen die u kunt selecteren](./media/understanding-cost-reports/display-formats.png)

13. **Meerdere kleuren**

    Gebruik Meerdere kleuren om de kleuren van grafieken in uw rapport in te stellen.

14. **Acties**

    Gebruik Acties om het rapport op te slaan, te exporteren of te plannen.

15. **Beleid**

    Hoewel niet afgebeeld, bevatten sommige rapporten een beleid voor de berekening van verwachte kosten. In deze rapporten toont het **geconsolideerde** beleid aanbevelingen voor alle accounts en abonnementen onder de huidige entiteit, zoals een inschrijving bij Microsoft of een betaler bij AWS. Het **zelfstandige** beleid toont aanbevelingen voor één account of abonnement alsof er geen andere abonnementen zijn. Het beleid dat u selecteert, is afhankelijk van de optimalisatiestrategie van uw organisatie. Kostenprognoses zijn gebaseerd op het gebruik van de laatste 30 dagen.

## <a name="save-and-schedule-reports"></a>Rapporten opslaan en plannen

Nadat u een rapport hebt gemaakt, kunt u het opslaan voor toekomstig gebruik. Opgeslagen rapporten zijn beschikbaar in **Mijn hulpmiddelen** > **Mijn rapporten**. Als u een bestaand rapport wijzigt en opslaat, wordt het rapport opgeslagen als een nieuwe versie. U kunt het ook opslaan als een nieuw rapport.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Een rapport opslaan in de Cloudyn-portal

Klik vanuit een weergegeven rapport op **Acties** en selecteer vervolgens **Opslaan in mijn rapporten**. Typ een naam voor het rapport en voeg uw eigen URL toe of gebruik de automatisch gemaakte URL. Desgewenst kunt u het rapport **delen** met anderen in uw organisatie of het delen met uw entiteit. Als u het rapport niet deelt, blijft het een persoonlijk rapport dat alleen u kunt bekijken. Sla het rapport op.


### <a name="save-a-report-to-cloud-provider-storage"></a>Een rapport opslaan in een opslagplaats van een cloudprovider

Om een rapport te kunnen opslaan bij uw cloudserviceprovider, moet u al een opslagaccount hebben geconfigureerd. Klik vanuit een weergegeven rapport op **Acties** en selecteer vervolgens **Rapport plannen**. Typ een naam voor het rapport en voeg uw eigen URL toe of gebruik de automatisch gemaakte URL. Selecteer **Opslaan in opslag** en selecteer vervolgens het opslagaccount of voeg een nieuw opslagaccount toe. Voer een voorvoegsel in dat wordt toegevoegd aan de bestandsnaam van het rapport. Selecteer de bestandsindeling CSV of JSON en sla het rapport op.

### <a name="schedule-a-report"></a>Een rapport plannen

U kunt rapporten uitvoeren op geplande intervallen en u kunt ze verzenden naar een lijst met ontvangers of een opslagaccount bij een cloudserviceprovider. Klik vanuit een weergegeven rapport op **Acties** en selecteer vervolgens **Rapport plannen**. U kunt het rapport per e-mail verzenden en opslaan in een opslagaccount. Onder **Planning** selecteert u het interval (dagelijks, wekelijks of maandelijks). Voor wekelijks en maandelijks selecteert u de gewenste dag of datums voor uitvoering, en de tijd. Sla het geplande rapport op. Als u de indeling voor een Excel-rapport selecteert, wordt het rapport verzonden als een bijlage. Wanneer u de indeling voor e-mailinhoud selecteert, worden rapportresultaten die worden weergegeven in diagramindeling geleverd als een grafiek.

### <a name="export-a-report-as-a-csv-file"></a>Een rapport exporteren als een CSV-bestand

Klik vanuit een weergegeven rapport op **Acties** en selecteer vervolgens **Alle rapportgegevens exporteren**. Er wordt een pop-upvenster weergegeven waarin een CSV-bestand wordt gedownload.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de rapporten die zijn opgenomen in Cloudyn op [Use Cloudyn reports](../../cost-management/use-reports.md) (Cloudyn-rapporten gebruiken).
- Meer informatie over het gebruik van rapporten voor het maken van [dashboards](../../cost-management/dashboards.md).
