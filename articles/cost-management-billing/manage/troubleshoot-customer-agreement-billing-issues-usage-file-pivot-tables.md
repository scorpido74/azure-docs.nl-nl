---
title: Problemen met de facturering van de Azure Microsoft-klantovereenkomst oplossen met de draaitabellen van het gebruiksbestand
description: Dit artikel helpt u bij het oplossen van problemen met de facturering van de Microsoft-klantovereenkomst met behulp van draaitabellen die zijn gemaakt met uw CSV-gebruiksbestanden.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 32c0779f4086574aeaf8d38ea675f80fbd2c1ec7
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132224"
---
# <a name="troubleshoot-mca-billing-issues-with-usage-file-pivot-tables"></a>Problemen met de facturering van de Microsoft-klantovereenkomst oplossen met de draaitabellen van het gebruiksbestand

Dit artikel helpt u bij het oplossen van problemen met de facturering van de Microsoft-klantovereenkomst met behulp van draaitabellen in uw gebruiksbestanden. Azure-gebruiksbestanden bevatten al uw Azure-gebruiks- en verbruiksgegevens. De informatie in het bestand kan u helpen bij het volgende:

- Inzicht krijgen in de manier waarop Azure-reserveringen worden gebruikt en toegepast
- Gegevens in Azure Cost Management afstemmen op uw gefactureerde factuur
- Problemen met een kostenpiek oplossen
- Een restitutiebedrag berekenen voor een serviceovereenkomst

Door gebruik te maken van de gegevens in uw gebruiksbestanden, kunt u een beter inzicht krijgen in de gebruiksproblemen en deze vaststellen. Gebruiksbestanden worden gegenereerd in CSV-indeling (door komma's gescheiden). Omdat de gebruiksbestanden grote CSV-bestanden kunnen zijn, zijn ze gemakkelijker te bewerken en weer te geven als draaitabellen in een spreadsheettoepassing als Excel. Voorbeelden in dit artikel maken gebruik van Excel, maar u kunt elke gewenste spreadsheettoepassing gebruiken.

Alleen eigenaren van factureringsprofielen, medewerkers, lezers of factuurbeheerders hebben toestemming gebruiksbestanden te downloaden. Zie [Gebruiksgegevens voor uw Microsoft-klantovereenkomst downloaden](./download-azure-invoice-daily-usage-date.md#download-usage-for-your-microsoft-customer-agreement) voor meer informatie. 

## <a name="get-the-data-and-format-it"></a>De gegevens ophalen en opmaken

Omdat Azure-gebruiksbestanden een CSV-indeling hebben, moet u de gegevens voorbereiden voor gebruik in Excel. Gebruik de volgende stappen om de gegevens als tabel op te maken.

1. Download het gebruiksbestand met behulp van de instructies op [Uw gebruik downloaden in de Azure-portal](./download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal).
1. Open het bestand in Excel.
1. De niet-opgemaakte gegevens lijken op het volgende voorbeeld.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" alt-text="Voorbeeld met niet-opgemaakte gegevens" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" :::
1. Selecteer het eerste veld in de tabel, **invoiceID**.
1. Druk op Ctrl+Shift+pijl-omlaag en vervolgens op Ctrl+Shift+pijl-rechts om alle gegevens in de tabel te selecteren.
1. Selecteer in het bovenste menu **Invoegen** > **Tabel**. Selecteer in het vak Tabel maken de optie **Mijn tabel bevat headers** en selecteer vervolgens **OK**.  
:::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Voorbeeld met niet-opgemaakte gegevens" :::
1. Selecteer in het bovenste menu **Invoegen** > **Draaitabel** en selecteer vervolgens **OK**. Met deze actie wordt een nieuw blad in het bestand gemaakt en gaat u naar het gebied met de draaitabel aan de rechterkant van het blad.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Voorbeeld met niet-opgemaakte gegevens" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

Het gebied met draaitabelvelden is een gebied voor slepen en neerzetten. Ga door naar de volgende sectie om de draaitabel te maken.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Draaitabel maken om Azure-kosten per resource weer te geven

In deze sectie maakt u een draaitabel waarin u problemen met het algemene Azure-gebruik kunt oplossen. Met de voorbeeldtabel kunt u onderzoeken welke service de meeste resources verbruikt. U kunt ook de resources weergeven die het meeste kosten en zien hoe een service wordt gefactureerd.

1. Sleep in het gebied van de draaitabelvelden **Metercategorie** en **Product** naar het gedeelte **Rijen**. Plaats **Product** onder **Metercategorie**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Voorbeeld met niet-opgemaakte gegevens" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Voeg vervolgens de kolom **costInBillingCurrenty** toe aan de sectie **Waarden**. U kunt in plaats daarvan ook de kolom **Hoeveelheid** gebruiken om informatie over verbruikseenheden en transacties te verkrijgen. Bijvoorbeeld GB en Uren. Of transacties in plaats van kosten in verschillende valuta, zoals USD, EUR en INR.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Voorbeeld met niet-opgemaakte gegevens" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. U hebt nu een dashboard voor onderzoek naar algemeen verbruik. U kunt filteren op een specifieke service met behulp van de filteropties in de draaitabel.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Voorbeeld met niet-opgemaakte gegevens" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Als u een tweede niveau in een draaitabel wilt filteren, bijvoorbeeld een resource, selecteert u een item op het tweede niveau in de tabel.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Voorbeeld met niet-opgemaakte gegevens" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Sleep de kolom **ResourceID** naar het gebied **Rijen** onder **Product** om de kosten van elke service per resource te bekijken.
1. Voeg de kolom **Datum** toe aan het gebied **Kolommen** om het dagelijkse verbruik van het product te bekijken.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="Voorbeeld met niet-opgemaakte gegevens" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. Vouw maanden uit of vouw ze samen met de **+** -symbolen voor de kolom van elke maand.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Voorbeeld met niet-opgemaakte gegevens" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::

Het toevoegen van de kolommen **Kosten** en **Hoeveelheid** in het gebied **Waarden** is optioneel. Hiermee maakt u twee kolommen voor elke sectie met gegevens onder elke maand en dag wanneer de kolom Datum zich in de sectie Kolommen van de draaitabel bevindt.

Voor extra filters kunt u InvoiceSection, costCenter, SubscriptionID, ResourceGroupName of Tags toevoegen aan de sectie Filters en het gewenste bereik selecteren.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Een draaitabel maken om de kosten voor een specifieke resource weer te geven

Eén resource kan verschillende kosten voor verschillende services met zich meebrengen. Een virtuele machine kan bijvoorbeeld kosten voor rekenkracht, OS-licenties, bandbreedte (gegevensoverdracht), RI-gebruik en opslag voor momentopnamen met zich meebrengen. Wanneer u het totale gebruik voor specifieke resources wilt bekijken, kunt u aan de hand van de volgende stappen een dashboard maken om het algehele gebruik van uw gebruiksbestanden weer te geven.

1. Sleep in het menu aan de rechterkant **ResourceID** naar de sectie **Filter** in het draaitabelmenu.
1. Selecteer de resource waarvoor u de kosten wilt zien. Typ in het vak **Zoeken** om een resourcenaam op te zoeken.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" alt-text="Voorbeeld met niet-opgemaakte gegevens" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" :::
1. Voeg **meterCategory** en **Product** toe aan het gebied **Rijen**. Plaats **Product** onder **meterCategory**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="Voorbeeld met niet-opgemaakte gegevens" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. Voeg vervolgens de kolom **Uitgebreide kosten** toe aan de sectie **Waarden**. U kunt in plaats daarvan ook de kolom Verbruikte hoeveelheid gebruiken om informatie over verbruikseenheden en transacties te verkrijgen. Bijvoorbeeld GB en Uren. Of transacties in plaats van kosten in verschillende valuta, zoals USD, EUR en INR. U hebt nu een dashboard waarin alle services worden weergegeven die de resource verbruikt.
1. Voeg de kolom **Datum** toe aan de sectie **Kolommen**. Het dagelijkse verbruik wordt weergegeven.
1. U kunt een maandkolom uitvouwen en samenvouwen met de **+** -symbolen.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Voorbeeld met niet-opgemaakte gegevens" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Volgende stappen

- [Kosten verkennen en analyseren met kostenanalyse](../costs/quick-acm-cost-analysis.md).