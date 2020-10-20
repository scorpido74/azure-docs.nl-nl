---
title: Problemen met Azure EA-facturering oplossen met de draaitabellen van het gebruiksbestand
description: Dit artikel helpt u bij het oplossen van problemen met de facturering van Enterprise Agreement (EA) met behulp van draaitabellen die zijn gemaakt met uw CSV-gebruiksbestanden.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: a30f85ae1b65321a25ee93239374d2a8aae26769
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026297"
---
# <a name="troubleshoot-ea-billing-issues-with-usage-file-pivot-tables"></a>Problemen met EA-facturering oplossen met de draaitabellen van het gebruiksbestand

Dit artikel helpt u bij het oplossen van problemen met EA-facturering met behulp van draaitabellen in uw gebruiksbestanden. Azure-gebruiksbestanden bevatten al uw Azure-gebruiks- en verbruiksgegevens. De informatie in het bestand kan u helpen bij het volgende:

- Inzicht krijgen in de manier waarop Azure-reserveringen worden gebruikt en toegepast
- Gegevens in Azure Cost Management afstemmen op uw gefactureerde factuur
- Problemen met een kostenpiek oplossen
- Een restitutiebedrag berekenen voor een serviceovereenkomst

Door gebruik te maken van de gegevens in uw gebruiksbestanden, kunt u een beter inzicht krijgen in de gebruiksproblemen en deze vaststellen. Gebruiksbestanden worden gegenereerd in CSV-indeling (door komma's gescheiden). Omdat de gebruiksbestanden grote CSV-bestanden kunnen zijn, zijn ze gemakkelijker te bewerken en weer te geven als draaitabellen in een spreadsheettoepassing als Excel. Voorbeelden in dit artikel maken gebruik van Excel, maar u kunt elke gewenste spreadsheettoepassing gebruiken.

Alleen EA-beheerders, accounteigenaren en afdelingsbeheerders hebben toegang tot het downloaden van gebruiksbestanden.

## <a name="get-the-data-and-format-it"></a>De gegevens ophalen en opmaken

Omdat Azure-gebruiksbestanden een CSV-indeling hebben, moet u de gegevens voorbereiden voor gebruik in Excel. Gebruik de volgende stappen om de gegevens als tabel op te maken.

1. Download de gebruiksgegevensversie 2 met Alle kosten-bestand (gebruik en aankoop) met behulp van de instructies in [Gebruik voor EA-klanten downloaden](https://docs.microsoft.com/azure/cost-management-billing/manage/download-azure-invoice-daily-usage-date#download-usage-for-ea-customers).
1. Open het bestand in Excel.
1. De niet-opgemaakte gegevens lijken op het volgende voorbeeld.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" :::
1. Selecteer het eerste veld in de tabel met de titel van de eerste kolom **BillingAccountID**.
1. Druk op Ctrl+Shift+pijl-omlaag en vervolgens op Ctrl+Shift+pijl-rechts om alle gegevens in de tabel te selecteren.
1. Selecteer in het bovenste menu **Invoegen** > **Tabel**. Selecteer in het vak Tabel maken de optie **Mijn tabel bevat headers** en selecteer vervolgens **OK**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" :::
1. Selecteer in het bovenste menu **Invoegen** > **Draaitabel** en selecteer vervolgens **OK**. Met deze actie wordt een nieuw blad in het bestand gemaakt. Hiermee gaat u naar het gebied van de draaitabel aan de rechter kant van het blad.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

Het gebied met draaitabelvelden is een gebied voor slepen en neerzetten. Ga door naar de volgende sectie om de draaitabel te maken.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Draaitabel maken om Azure-kosten per resource weer te geven

In deze sectie maakt u een draaitabel waarin u problemen met het algemene Azure-gebruik kunt oplossen. Met de voorbeeldtabel kunt u onderzoeken welke service de meeste resources verbruikt. U kunt ook de resources weergeven die het meeste kosten en zien hoe een service wordt gefactureerd.

1. Sleep in het gebied van de draaitabelvelden **Metercategorie** en **Product** naar het gedeelte **Rijen**. Plaats **Product** onder **Metercategorie**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Voeg vervolgens de kolom **Kosten** toe aan de sectie **Waarden**. U kunt in plaats daarvan ook de kolom Verbruikte hoeveelheid gebruiken om informatie over verbruikseenheden en transacties te verkrijgen. Bijvoorbeeld GB en Uren. Of transacties in plaats van kosten in verschillende valuta, zoals USD, EUR en INR.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. U hebt nu een dashboard voor onderzoek naar algemeen verbruik. U kunt filteren op een specifieke service met behulp van de filteropties in de draaitabel.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Als u een tweede niveau in een draaitabel wilt filteren, bijvoorbeeld een resource, selecteert u een item op het tweede niveau in de tabel.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Sleep de kolom **ResourceID** naar het gebied **Rijen** onder **Product** om de kosten van elke service per resource te bekijken. Als u gedetailleerde prijsinformatie wilt weergeven, bekijkt u de Eenheidsprijs van uw organisatie en zoekt u naar **Product** in de eerste kolom van de prijslijst.
1. Voeg de kolom **Datum** toe aan het gebied **Kolommen** om het dagelijkse verbruik van het product te bekijken.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. Vouw maanden uit of vouw ze samen met de **+** -symbolen voor de kolom van elke maand.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::  
    Het toevoegen van de kolommen **Kosten** en **Hoeveelheid** in het gebied **Waarden** is optioneel. Hiermee maakt u twee kolommen voor elke sectie met gegevens onder elke maand en dag wanneer de kolom Datum zich in de sectie Kolommen van de draaitabel bevindt.
1. Voor extra filters kunt u de kolommen Abonnement/id, Afdeling, ResourceGroep, Tags of Kostencentrum toevoegen aan het gebied met **Filters** en het gewenste item selecteren.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Een draaitabel maken om de kosten voor een specifieke resource weer te geven

Eén resource kan verschillende kosten voor verschillende services met zich meebrengen. Een virtuele machine kan bijvoorbeeld kosten voor rekenkracht, OS-licenties, bandbreedte (gegevensoverdracht), RI-gebruik en opslag voor momentopnamen met zich meebrengen. Wanneer u het totale gebruik voor specifieke resources wilt bekijken, kunt u aan de hand van de volgende stappen een dashboard maken om het algehele gebruik van uw gebruiksbestanden weer te geven.

1. Sleep in het menu aan de rechterkant **ResourceID** naar de sectie **Filter** in het draaitabelmenu.
1. Selecteer de resource waarvoor u de kosten wilt zien. Typ in het vak **Zoeken** om een resourcenaam op te zoeken.
1. Voeg **Metercategorie** en **Product** toe aan de sectie Rijen. Plaats **Product** onder **Metercategorie**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. Voeg vervolgens de kolom **Kosten** toe aan de sectie **Waarden**. U kunt in plaats daarvan ook de kolom Verbruikte hoeveelheid gebruiken om informatie over verbruikseenheden en transacties te verkrijgen. Bijvoorbeeld GB en Uren. Of transacties in plaats van kosten in verschillende valuta, zoals USD, EUR en INR. U hebt nu een dashboard waarin alle services worden weergegeven die de resource verbruikt.
1. Voeg de kolom **Datum** toe aan de sectie **Kolommen**. Het dagelijkse verbruik wordt weergegeven.
1. U kunt het gebruik van de symbolen **+** in de kolom van elke maand uitbreiden en verminderen.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Voorbeeld met niet-opgemaakte gegevens in Excel" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Volgende stappen

- [Kosten verkennen en analyseren met kostenanalyse](../costs/quick-acm-cost-analysis.md).