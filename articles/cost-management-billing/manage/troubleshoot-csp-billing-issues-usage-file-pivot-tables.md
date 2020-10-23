---
title: Problemen met Azure CSP-facturering oplossen met de draaitabellen van het gebruiksbestand
description: Dit artikel helpt u bij het oplossen van problemen met de facturering van Azure Cloud Solution Provider (CSP) met behulp van draaitabellen die zijn gemaakt met uw CSV-gebruiksbestanden.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 6525d809805da3a19a0efe423306f18d8e67a646
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026383"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>Problemen met CSP-facturering oplossen met de draaitabellen van het gebruiksbestand

Dit artikel helpt u bij het oplossen van problemen met de facturering van Cloud Solution Provider (CSP) met behulp van draaitabellen in de afstemmingsbestanden (gebruiksbestanden) in het Partner Center. Azure-gebruiksbestanden bevatten al uw Azure-gebruiks- en verbruiksgegevens. De informatie in het bestand kan u helpen bij het volgende:

- Inzicht krijgen in de manier waarop Azure-reserveringen worden gebruikt en toegepast
- Gegevens in Azure Cost Management afstemmen op uw gefactureerde factuur
- Problemen met een kostenpiek oplossen
- Een restitutiebedrag berekenen voor een serviceovereenkomst

Door gebruik te maken van de gegevens in uw gebruiksbestanden, kunt u een beter inzicht krijgen in de gebruiksproblemen en deze vaststellen. Gebruiksbestanden worden gegenereerd in CSV-indeling (door komma's gescheiden). Omdat de gebruiksbestanden grote CSV-bestanden kunnen zijn, zijn ze gemakkelijker te bewerken en weer te geven als draaitabellen in een spreadsheettoepassing als Excel. Voorbeelden in dit artikel maken gebruik van Excel, maar u kunt elke gewenste spreadsheettoepassing gebruiken.

Alleen factureringsbeheerders en globale beheerders hebben toestemming afstemmingsbestanden te downloaden. Zie [Informatie over het lezen van regelitems in de afstemmingsbestanden van het Partner Center](/partner-center/use-the-reconciliation-files) voor meer informatie.

## <a name="get-the-data-and-format-it"></a>De gegevens ophalen en opmaken

Omdat Azure-gebruiksbestanden een CSV-indeling hebben, moet u de gegevens voorbereiden voor gebruik in Excel. Gebruik de volgende stappen om de gegevens als tabel op te maken.

1. Download het gebruiksbestand met behulp van de instructies op [Uw factuur vinden](/partner-center/read-your-bill#find-your-bill).
1. Open het bestand in Excel.
1. De niet-opgemaakte gegevens lijken op het volgende voorbeeld.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. Selecteer het eerste veld in de tabel, **PartnerID**.
1. Druk op Ctrl+Shift+pijl-omlaag en vervolgens op Ctrl+Shift+pijl-rechts om alle gegevens in de tabel te selecteren.
1. Selecteer in het bovenste menu **Invoegen** > **Tabel**. Selecteer in het vak Tabel maken de optie **Mijn tabel bevat headers** en selecteer vervolgens **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" :::
1. Selecteer in het bovenste menu **Invoegen** > **Draaitabel** en selecteer vervolgens **OK**. Met deze actie wordt een nieuw blad in het bestand gemaakt en gaat u naar het gebied met de draaitabel aan de rechterkant van het blad.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

Het gebied met draaitabelvelden is een gebied voor slepen en neerzetten. Ga door naar de volgende sectie om de draaitabel te maken.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Draaitabel maken om Azure-kosten per resource weer te geven

In deze sectie maakt u een draaitabel waarin u problemen met het algemene Azure-gebruik kunt oplossen. Met de voorbeeldtabel kunt u onderzoeken welke service de meeste resources verbruikt. U kunt ook de resources weergeven die het meeste kosten en zien hoe een service wordt gefactureerd.

1. Sleep in het gebied met draaitabelvelden **Servicenaam** en **Resource** naar het gebied **Rijen**. Plaats **Resource** onder **Servicenaam**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Plaats vervolgens **Totaal incl. btw** in het gebied **Waarden**. U kunt in plaats daarvan ook de kolom Verbruikte hoeveelheid gebruiken om informatie over verbruikseenheden en transacties te verkrijgen. Bijvoorbeeld GB en Uren. Of transacties in plaats van kosten in verschillende valuta, zoals USD, EUR en INR.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. U hebt nu een dashboard voor onderzoek naar algemeen verbruik. U kunt filteren op een specifieke service met behulp van de filteropties in de draaitabel.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Als u een tweede niveau in een draaitabel wilt filteren, bijvoorbeeld een resource, selecteert u een item op het tweede niveau in de tabel.
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Voor aanvullende filters kunt u **SubscriptionID** en **Bedrijfsnaam klant** toevoegen aan het gebied **Filters** en het gewenste bereik selecteren.

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>Een draaitabel maken om het Azure-gebruik op datum weer te geven

In deze sectie maakt u een draaitabel waarin u problemen met het algemene Azure-gebruik op Verbruikte hoeveelheid en datum kunt oplossen. Het is handig om factuurpieken te identificeren op datum en service. U kunt ook de resources weergeven die het meeste kosten en zien hoe een service wordt gefactureerd.

Het afstemmingsbestand bevat twee tabellen. Een bevindt zich bovenaan (de hoofdtabel) en er is een andere tabel onder aan het document. Deze tweede tabel bevat veel dezelfde informatie, maar bevat geen prijsinformatie of details over de kosten. Het bevat wel een gebruiksdatum en de verbruikte hoeveelheid.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. Gebruik dezelfde stappen uit de sectie [De gegevens ophalen en opmaken](#get-the-data-and-format-it) om een Excel-tabel te maken met de informatie onder aan het afstemmingsbestand.
1. Wanneer de tabel klaar is en u een draaitabelblad hebt, gebruikt u dezelfde stappen uit de sectie create-pivot-table-to-view-azure-costs-by-resources om het dashboard voor te bereiden. In plaats van Totaal incl. btw plaatst u **Verbruikte hoeveelheid** in het gebied **Waarden**.
1. Voeg **Gebruiksdatum** aan de sectie met kolommen toe. De draaitabel moet eruitzien als het volgende voorbeeld.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. U hebt nu een dashboard waarin het gebruik per datum wordt weergegeven. U kunt een maand uitbreiden door het **+** -symbool te selecteren.

Het dashboard toont de verbruikte hoeveelheid in eenheden als GB, uren en overdrachten.

Als u de prijs per dag wilt weergeven, kunt u **Resource-GUID** aan het gebied **Rijen** toevoegen. Voeg in de bovenste tabel de eenheidsprijs (**ListPrice**) voor de resource toe. Vermenigvuldig **ListPrice** met **Verbruikte hoeveelheid** om uw kosten vóór btw te berekenen. De bedragen moeten overeenkomen.

Sommige resources (services) kennen een geschaalde prijs per verbruikte hoeveelheid. Sommige resources hebben bijvoorbeeld een hogere prijs voor de eerste verbruikte 100 GB en een lagere prijs voor de GB's die u later gebruikt. Houd rekening met geschaalde prijzen wanneer u kosten handmatig berekent.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Een draaitabel maken om de kosten voor een specifieke resource weer te geven

Eén resource kan verschillende kosten voor verschillende services met zich meebrengen. Een virtuele machine kan bijvoorbeeld kosten voor rekenkracht, OS-licenties, bandbreedte (gegevensoverdracht), RI-gebruik en opslag voor momentopnamen met zich meebrengen. Wanneer u het totale gebruik voor specifieke resources wilt bekijken, kunt u aan de hand van de volgende stappen een dashboard maken om het algehele gebruik van uw gebruiksbestanden weer te geven.

Afstemmingsbestanden bevatten geen resource-specifieke details. U gebruikt dus het bestand met het samengevoegde gebruik. Neem contact op met de [ondersteuningsafdeling voor facturering van Azure](https://go.microsoft.com/fwlink/?linkid=2083458) als u het samengevoegde gebruiksbestand voor uw abonnement wilt hebben. Geaggregeerde bestanden worden gegenereerd op abonnementsniveau. De niet-opgemaakte gegevens lijken op het volgende voorbeeld.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

Het bestand bevat de volgende kolommen.

- **UsageStart** en **UsageEnd**: datum voor elk regelitem (elke gebruikseenheid). Bijvoorbeeld elke dag.
- **MeteredResourceID** : in Azure komt dit overeen met de meter-id.
- **Eigenschappen** : bevat de exemplaar-id (resourcenaam) met andere details, zoals locatie.
- **Hoeveelheid**: verbruikte hoeveelheid in het afstemmingsbestand.

1. Selecteer het eerste veld in de tabel, **PartnerID**.  
1. Druk op Ctrl+Shift+pijl-omlaag en vervolgens op Ctrl+Shift+pijl-rechts om alle gegevens in de tabel te selecteren.
1. Selecteer in het bovenste menu **Invoegen** > **Tabel**. Selecteer in het vak Tabel maken de optie **Mijn tabel bevat headers** en selecteer vervolgens **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" :::
1. Selecteer in het bovenste menu **Invoegen** > **Draaitabel** en selecteer vervolgens **OK**. Met deze actie wordt een nieuw blad in het bestand gemaakt en gaat u naar het gebied met de draaitabel aan de rechterkant van het blad.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. Voeg vervolgens **MeteredResourceID** toe aan het gebied **Rijen** en voeg **Hoeveelheid** toe aan **Waarden**. De resultaten tonen de totale gebruiksgegevens. Plaats **UsageEndDateTime** in het gebied **Kolommen** voor meer informatie.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. Als u een totaalrapport wilt weergeven, voegt u onder **MeteredResourceID** **Eigenschappen** toe aan **Rijen**. Er wordt een volledig dashboard voor uw gebruik weergegeven.
1. Als u op een specifieke resource wilt filteren, voegt u **Eigenschappen** toe aan het gebied **Filters** en selecteert u het gewenste gebruik. Met de zoekfunctie kunt u een resourcenaam vinden.
    Als u de kosten voor de resource wilt weergeven, zoekt u de totaal verbruikte hoeveelheid en vermenigvuldigt u deze waarde met de prijs in de lijst. De prijs in de lijst is specifiek voor elke resource-GUID (MeteredResourceID). Als een resource meerdere MeteredResourceID's verbruikt, moet u de totale waarde voor elke id noteren.

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met Azure Cost Management voor partners](../costs/get-started-partners.md).