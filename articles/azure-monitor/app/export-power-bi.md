---
title: Exporteren naar Power BI vanuit Azure Application Insights | Microsoft Documenten
description: Analytics-query's kunnen worden weergegeven in Power BI.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 0e17ca6e07ec76f0a7a1cb04f7aa13619fb9970c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663994"
---
# <a name="feed-power-bi-from-application-insights"></a>Power BI voeden vanuit toepassingsinzichten
[Power BI](https://www.powerbi.com/) is een reeks bedrijfstools waarmee u gegevens analyseren en inzichten delen. Rijke dashboards zijn beschikbaar op elk apparaat. U gegevens uit vele bronnen combineren, waaronder Analytics-query's van [Azure Application Insights.](../../azure-monitor/app/app-insights-overview.md)

Er zijn drie methoden voor het exporteren van Application Insights-gegevens naar Power BI:

* [**Analytics-query's exporteren**](#export-analytics-queries). Dit is de voorkeursmethode. Schrijf elke gewenste query en exporteer deze naar Power BI. U deze query op een dashboard plaatsen, samen met andere gegevens.
* [**Continue export en Azure Stream Analytics**](../../azure-monitor/app/export-stream-analytics.md). Deze methode is handig als u uw gegevens voor langere tijd wilt opslaan. Als u geen uitgebreide vereiste voor gegevensbewaring hebt, gebruikt u de querymethode voor analysevan exporteren. Continue export en Stream Analytics vereist meer werk aan het opzetten en extra opslagoverhead.
* **Power BI-adapter**. De set grafieken is vooraf gedefinieerd, maar u uw eigen query's toevoegen uit andere bronnen.

> [!NOTE]
> De Power BI-adapter is nu **afgeschaft.** De vooraf gedefinieerde grafieken voor deze oplossing worden gevuld door statische onbewerkbare query's. U hebt niet de mogelijkheid om deze query's te bewerken en afhankelijk van bepaalde eigenschappen van uw gegevens is het mogelijk dat de verbinding met Power BI succesvol is, maar er worden geen gegevens ingevuld. Dit is te wijten aan uitsluitingscriteria die zijn ingesteld binnen de hardcoded query. Hoewel deze oplossing voor sommige klanten nog steeds werkt, is de aanbevolen oplossing vanwege het gebrek aan flexibiliteit van de adapter het gebruik van de [**exportanalytics-queryfunctionaliteit.**](#export-analytics-queries)

## <a name="export-analytics-queries"></a>Analytics-query's exporteren
Met deze route u elke door u bevalt Analytics-query schrijven of exporteren vanuit Use Funnels en deze exporteren naar een Power BI-dashboard. (U toevoegen aan het dashboard dat door de adapter is gemaakt.)

### <a name="one-time-install-power-bi-desktop"></a>Eén keer: Power BI-bureaublad installeren
Als u de query Application Insights wilt importeren, gebruikt u de bureaubladversie van Power BI. Vervolgens u het publiceren op het web of naar uw Power BI-cloudwerkruimte. 

[Power BI-bureaublad installeren](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Een Analytics-query exporteren
1. [Open Analytics en schrijf uw query](../../azure-monitor/log-query/get-started-portal.md).
2. Test en verfijn de query totdat u tevreden bent met de resultaten. Zorg ervoor dat de query correct wordt uitgevoerd in Analytics voordat u deze exporteert.
3. Kies in het menu **Exporteren** de optie **Power BI (M)**. Sla het tekstbestand op.
   
    ![Schermafbeelding van Analytics, met het menu Exporteren gemarkeerd](./media/export-power-bi/analytics-export-power-bi.png)
4. Selecteer in Power BI-bureaublad de optie **Lege query** > voor gegevens**opvragen**. Selecteer vervolgens in de queryeditor onder **Weergave**de optie **Geavanceerde editor**.

    Plak het geëxporteerde M-taalscript in de geavanceerde editor.

    ![Schermafbeelding van Power BI-bureaublad, met geavanceerde editor gemarkeerd](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Als u wilt dat Power BI toegang krijgt tot Azure, moet u mogelijk referenties verstrekken. Gebruik **organisatieaccount** om u aan te melden met uw Microsoft-account.
   
    ![Schermafbeelding van het dialoogvenster Power BI-query-instellingen](./media/export-power-bi/power-bi-import-sign-in.png)

    Als u de referenties wilt verifiëren, gebruikt u de menuopdracht **Gegevensbroninstellingen** in de queryeditor. Zorg ervoor dat u de referenties opgeeft die u voor Azure gebruikt, die mogelijk verschillen van uw referenties voor Power BI.
6. Kies een visualisatie voor uw query en selecteer de velden voor de afmetingen x-as, y-as en segmentering.
   
    ![Schermafbeelding van power BI-bureaubladvisualisatieopties](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Publiceer uw rapport naar uw Power BI-cloudwerkruimte. Van daaruit u een gesynchroniseerde versie insluiten in andere webpagina's.
   
    ![Schermafbeelding van Power BI-bureaublad, met de knop Publiceren gemarkeerd](./media/export-power-bi/publish-power-bi.png)
8. Vernieuw het rapport handmatig met tussenpozen of stel een geplande vernieuwing in op de optiepagina.

### <a name="export-a-funnel"></a>Een trechter exporteren
1. [Maak je trechter.](../../azure-monitor/app/usage-funnels.md)
2. Selecteer **Power BI**.

   ![Schermafbeelding van de knop Power BI](./media/export-power-bi/button.png)

3. Selecteer in Power BI-bureaublad de optie **Lege query** > voor gegevens**opvragen**. Selecteer vervolgens in de queryeditor onder **Weergave**de optie **Geavanceerde editor**.

   ![Schermafbeelding van Power BI-bureaublad, met knop Lege query gemarkeerd](./media/export-power-bi/blankquery.png)

   Plak het geëxporteerde M-taalscript in de geavanceerde editor. 

   ![Schermafbeelding van Power BI-bureaublad, met geavanceerde editor gemarkeerd](./media/export-power-bi/advancedquery.png)

4. Selecteer items uit de query en kies een trechtervisualisatie.

   ![Schermafbeelding van power BI-bureaubladvisualisatieopties](./media/export-power-bi/selectsequence.png)

5. Wijzig de titel om deze betekenis te geven en publiceer uw rapport in uw Power BI-cloudwerkruimte. 

   ![Schermafbeelding van Power BI-bureaublad, waarbij titelwijziging is gemarkeerd](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Problemen oplossen

U fouten tegenkomen met betrekking tot referenties of de grootte van de gegevensset. Hier is wat informatie over wat te doen over deze fouten.

### <a name="unauthorized-401-or-403"></a>Ongeautoriseerd (401 of 403)
Dit kan gebeuren als uw vernieuwingstoken niet is bijgewerkt. Probeer deze stappen om ervoor te zorgen dat u nog steeds toegang hebt:

1. Meld u aan bij de Azure-portal en zorg ervoor dat u toegang hebt tot de bron.
2. Probeer de referenties voor het dashboard te vernieuwen.
3. Probeer de cache van uw PowerBI-bureaublad te wissen.


   Als je wel toegang hebt en het vernieuwen van de referenties werkt niet, open dan een support ticket.

### <a name="bad-gateway-502"></a>Bad Gateway (502)
Dit wordt meestal veroorzaakt door een Analytics-query die te veel gegevens retourneert. Probeer een kleiner tijdsbereik voor de query te gebruiken. 

Als het verminderen van de gegevensset die afkomstig is van de Analytics-query niet aan uw vereisten voldoet, u overwegen de [API](https://dev.applicationinsights.io/documentation/overview) te gebruiken om een grotere gegevensset op te halen. U als u de M-Query-export converteren naar de API.

1. Een [API-sleutel maken.](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)
2. Werk het Power BI M-script bij dat u vanuit Analytics hebt geëxporteerd door de URL van Azure Resource Manager te vervangen door de API voor Toepassingsinzichten.
   * Https **vervangen:\//management.azure.com/subscriptions/...**
   * met, **https:\//api.applicationinsights.io/beta/apps/...**
3. Werk ten slotte de referenties bij naar basic en gebruik uw API-sleutel.

**Bestaand script**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Bijgewerkt script**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Over sampling
Afhankelijk van de hoeveelheid gegevens die door uw toepassing worden verzonden, u de functie adaptieve bemonstering gebruiken, die slechts een percentage van uw telemetrie verzendt. Hetzelfde geldt als u handmatig steekproeven hebt ingesteld in de SDK of bij inname. [Meer informatie over sampling](../../azure-monitor/app/sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Power BI-adapter (afgeschaft)
Met deze methode wordt een compleet dashboard van telemetrie voor u gemaakt. De eerste gegevensset is vooraf gedefinieerd, maar u er meer gegevens aan toevoegen.

### <a name="get-the-adapter"></a>De adapter aanschaffen
1. Meld u aan bij [Power BI](https://app.powerbi.com/).
2. Open **Gegevens opvragen** ![Schermafbeelding van GetData-pictogram in de linkerbenedenhoek](./media/export-power-bi/001.png), **Services**.

    ![Schermafbeeldingen van Get from Application Insights-gegevensbron](./media/export-power-bi/002.png)

3. Selecteer **Nu oppakken** onder Toepassingsinzichten.

   ![Schermafbeeldingen van Get from Application Insights-gegevensbron](./media/export-power-bi/003.png)
4. Geef de details van uw Application **Insights-bron**op en meld u vervolgens aan .

    ![Schermafbeelding van Ophalen van de gegevensbron van Application Insights](./media/export-power-bi/005.png)

     Deze informatie is te vinden in het deelvenster Overzicht van toepassingsinzichten:

     ![Schermafbeelding van Ophalen van de gegevensbron van Application Insights](./media/export-power-bi/004.png)

5. Open de nieuw gemaakte Application Insights Power BI-app.

6. Wacht een minuut of twee tot de gegevens worden geïmporteerd.

    ![Schermafbeelding van de Power BI-adapter](./media/export-power-bi/010.png)

U het dashboard bewerken door de grafieken Van Application Insights te combineren met die van andere bronnen en met Analytics-query's. U meer grafieken in de visualisatiegalerie krijgen en elke grafiek heeft parameters die u instellen.

Na de eerste import worden het dashboard en de rapporten dagelijks bijgewerkt. U het vernieuwingsschema op de gegevensset beheren.

## <a name="next-steps"></a>Volgende stappen
* [Power BI - Leren](https://www.powerbi.com/learning/)
* [Zelfstudie voor Analytics](../../azure-monitor/log-query/get-started-portal.md)

