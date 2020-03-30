---
title: Jachtbladwijzers gebruiken voor gegevensonderzoeken in Azure Sentinel
description: In dit artikel wordt beschreven hoe u de Azure Sentinel-jachtbladwijzers gebruiken om gegevens bij te houden.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: eacc104bf7157732f91507f631bcda2cd166bfb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588685"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Gegevens bijhouden tijdens de jacht met Azure Sentinel

Threat hunting vereist meestal het herzien van bergen van log gegevens op zoek naar bewijs van kwaadaardig gedrag. Tijdens dit proces, onderzoekers vinden gebeurtenissen die ze willen onthouden, opnieuw bezoeken, en analyseren als onderdeel van het valideren van potentiële hypothesen en het begrijpen van het volledige verhaal van een compromis.

Door bladwijzers in Azure Sentinel te verkennen, u dit doen door de query's te behouden die u in **Azure Sentinel - Logboeken hebt**uitgevoerd, samen met de queryresultaten die u relevant acht. U ook uw contextuele waarnemingen vastleggen en uw bevindingen verwijzen door notities en tags toe te voegen. Gegevens met bladwijzers zijn zichtbaar voor jou en je teamgenoten voor eenvoudige samenwerking.

U uw gegevens met bladwijzers op elk gewenst moment opnieuw bekijken op het tabblad **Bladwijzers** van het **deelvenster Jacht.** U filter- en zoekopties gebruiken om snel specifieke gegevens voor uw huidige onderzoek te vinden. U uw gegevens met bladwijzers ook rechtstreeks bekijken in de **huntingbookmark-tabel** in uw werkruimte Log Analytics. Bijvoorbeeld:

> [!div class="mx-imgBorder"]
> ![Tabel HuntingBookmark bekijken](./media/bookmarks/bookmark-table.png)

Als u bladwijzers uit de tabel bekijkt, u gegevens met bladwijzers filteren, samenvatten en aansluiten bij andere gegevensbronnen, zodat u gemakkelijk zoeken naar bevestigend bewijs.

Momenteel in preview, als je iets vindt dat dringend moet worden aangepakt tijdens de jacht in uw logboeken, in een paar klikken, u een bladwijzer maken en promoten tot een incident, of de bladwijzer toevoegen aan een bestaand incident. Zie [Zelfstudie: Incidenten onderzoeken met Azure Sentinel](tutorial-investigate-cases.md)voor meer informatie over incidenten. 

Ook in preview u uw gegevens met bladwijzers visualiseren door op **Onderzoeken** te klikken op de bladwijzerdetails. Dit lanceert de onderzoekservaring waarin u uw bevindingen bekijken, onderzoeken en visueel communiceren met behulp van een interactief entiteitsdiagram en tijdlijn.

## <a name="add-a-bookmark"></a>Een bladwijzer toevoegen

1. Navigeer in de Azure-portal naar **Sentinel** > **Threat management** > **Hunting** om query's uit te voeren voor verdacht en afwijkend gedrag.

2. Select one of the hunting queries and on the right, in the hunting query details, select **Run Query**. 

3. Selecteer **Queryresultaten weergeven**. Bijvoorbeeld:
    
    > [!div class="mx-imgBorder"]
    > ![queryresultaten van Azure Sentinel-jacht weergeven](./media/bookmarks/new-processes-observed-example.png)
    
    Met deze actie worden de queryresultaten geopend in het deelvenster **Logboeken.**

4. Gebruik in de lijst met logboekqueryresultaten de selectievakjes om een of meer rijen te selecteren die de informatie bevatten die u interessant vindt.

5. Selecteer **Bladwijzer toevoegen:**
    
    > [!div class="mx-imgBorder"]
    > ![Jachtbladwijzer toevoegen aan query](./media/bookmarks/add-hunting-bookmark.png)

6. Werk aan de rechterkant in het **deelvenster Bladwijzer toevoegen** optioneel de bladwijzernaam bij, voeg tags en notities toe om u te helpen bepalen wat er interessant was aan het item.

7. Gebruik in de sectie **Querygegevens** de vervolgkeuzelijsten om informatie uit de queryresultaten voor de typen **entiteitaccount,** **Host**en **IP-adres** te extraheren. Met deze actie wordt het geselecteerde entiteitstype toegewezen aan een specifieke kolom uit het queryresultaat. Bijvoorbeeld:
    
    > [!div class="mx-imgBorder"]
    > ![Entiteitstypen toewijzen voor de jachtopbladwijzer](./media/bookmarks/map-entity-types-bookmark.png)
    
    Als u de bladwijzer in de onderzoeksgrafiek wilt weergeven (momenteel in voorbeeld) moet u ten minste één entiteitstype toewijzen dat **account,** **host**of **IP-adres**is . 

5. Klik **op Opslaan** om de wijzigingen te verbinden en voeg de bladwijzer toe. Alle gegevens met bladwijzers worden gedeeld met andere onderzoekers en zijn een eerste stap in de richting van een gezamenlijke onderzoekservaring.

 
> [!NOTE]
> De resultaten van de logboekquery ondersteunen bladwijzers wanneer dit deelvenster wordt geopend vanuit Azure Sentinel. U selecteert bijvoorbeeld **Algemene** > **logboeken in** de navigatiebalk, selecteert gebeurteniskoppelingen in de onderzoeksgrafiek of selecteert een waarschuwings-id uit de volledige details van een incident (momenteel in preview). U geen bladwijzers maken wanneer het deelvenster **Logboeken** wordt geopend vanaf andere locaties, zoals rechtstreeks vanuit Azure Monitor.

## <a name="view-and-update-bookmarks"></a>Bladwijzers weergeven en bijwerken 

1. Navigeer in de Azure-portal naar **Sentinel** > **Threat management** > **Hunting.** 

2. Selecteer het tabblad **Bladwijzers** om de lijst met bladwijzers weer te geven.

3. Gebruik het zoekvak of de filteropties om u te helpen een specifieke bladwijzer te vinden.

4. Selecteer afzonderlijke bladwijzers en bekijk de bladwijzerdetails in het deelvenster met rechtse details.

5. Breng uw wijzigingen aan indien nodig, die automatisch worden opgeslagen.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Bladwijzers verkennen in de onderzoeksgrafiek

> [!IMPORTANT]
> Het verkennen van bladwijzers in de onderzoeksgrafiek en de onderzoeksgrafiek zelf zijn momenteel in openbare preview.
> Deze functies worden geleverd zonder een servicelevelovereenkomst en worden niet aanbevolen voor productieworkloads.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

1. Navigeer in de Azure-portal naar het tabblad**Hunting** > **Bookmarks** voor **Sentinel** > **Threat Management** > en selecteer de bladwijzers of bladwijzers die u wilt onderzoeken.

2. Controleer in de bladwijzerdetails of ten minste één entiteit in kaart is gebracht. Voor **ENTITEITEN**ziet u bijvoorbeeld vermeldingen voor **IP,** **Machine**of **Account**.

3. Klik **op Onderzoeken** om de bladwijzer in de onderzoeksgrafiek weer te geven.

Zie [De onderzoeksgrafiek gebruiken om diep te duiken voor](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)instructies om de onderzoeksgrafiek te gebruiken.

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Bladwijzers toevoegen aan een nieuw of bestaand incident

> [!IMPORTANT]
> Het toevoegen van bladwijzers aan een nieuw of bestaand incident is momenteel in openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

1. Navigeer in de Azure-portal naar het tabblad**Hunting** > **Bookmarks** voor **Sentinel** > **Threat Management** > en selecteer de bladwijzers of bladwijzers die u aan een incident wilt toevoegen.

2. Selecteer **Incidentacties (Voorbeeld)** op de opdrachtbalk:
    
    > [!div class="mx-imgBorder"]
    > ![Bladwijzers toevoegen aan incident](./media/bookmarks/incident-actions.png)

3. Selecteer **Nieuw incident maken** of **Toevoegen aan bestaande incidenten,** indien vereist. Daarna kunt u het volgende doen:
    
    - Voor een nieuw incident: werk optioneel de details voor het incident bij en selecteer **Vervolgens Maken**.
    - Als u een bladwijzer aan een bestaand incident wilt toevoegen: Selecteer één incident en selecteer **Toevoegen**. 

Als u de bladwijzer in het incident wilt bekijken: Navigeer naar **Sentinel** > **Threat management** > **Incidenten** en selecteer het incident met uw bladwijzer. Selecteer **Volledige details weergeven**en selecteer vervolgens het tabblad **Bladwijzers.**

> [!TIP]
> Als alternatief voor de optie **Incidentacties (Voorbeeld)** op de opdrachtbalk u het contextmenu (**...**) gebruiken voor een of meer bladwijzers om opties te selecteren voor **Het maken van nieuw incident,** **Toevoegen aan bestaande incidenten**en Verwijderen **van incident**. 

## <a name="view-bookmarked-data-in-logs"></a>Gegevens met bladwijzers weergeven in logboeken

Als u query's, resultaten of hun geschiedenis met bladwijzers wilt weergeven, selecteert u de bladwijzer op het tabblad**Bladwijzers** **jagen** > en gebruikt u de koppelingen in het detailvenster: 

- **Bronquery weergeven** om de bronquery weer te geven in het deelvenster **Logboeken.**

- **Bekijk bladwijzerlogboeken** om alle metagegevens van bladwijzers weer te geven, waaronder wie de update heeft uitgevoerd, de bijgewerkte waarden en het tijdstip waarop de update heeft plaatsgevonden.

U ook de ruwe bladwijzergegevens voor alle bladwijzers weergeven door **Bladwijzerlogboeken te** selecteren op de opdrachtbalk op het tabblad**Bladwijzers** **jagen:** > 

> [!div class="mx-imgBorder"]
> ![Bladwijzerlogboeken](./media/bookmarks/bookmark-logs.png)

In deze weergave worden al uw bladwijzers met bijbehorende metagegevens weergegeven. U KQL-query's [(Keyword Query Language)](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) gebruiken om naar de nieuwste versie van de specifieke bladwijzer te filteren die u zoekt.

> [!NOTE]
> Er kan een aanzienlijke vertraging (gemeten in minuten) zijn tussen het moment dat u een bladwijzer maakt en wanneer deze wordt weergegeven op het tabblad **Bladwijzers.**

## <a name="delete-a-bookmark"></a>Een bladwijzer verwijderen
 
1.  Navigeer in de Azure-portal naar het tabblad**Hunting** > **bookmarks** voor **Sentinel** > **Threat management** > en selecteer de bladwijzers of bladwijzers die u wilt verwijderen. 

2. Klik met de rechtermuisknop op uw selecties en selecteer de optie om de bladwijzer of bladwijzers te verwijderen. Verwijder bijvoorbeeld **bladwijzer als** u slechts één bladwijzer hebt geselecteerd en Twee **bladwijzers verwijderen** als u twee bladwijzers hebt geselecteerd.
    
Als u de bladwijzer verwijdert, wordt de bladwijzer uit de lijst op het tabblad **Bladwijzer** verwijderd. De **HuntingBookmark-tabel** voor uw loganalytics-werkruimte blijft eerdere bladwijzervermeldingen bevatten, maar de laatste vermelding wijzigt de **SoftDelete-waarde** in true, waardoor het gemakkelijk is om oude bladwijzers uit te filteren. Als u een bladwijzer verwijdert, worden geen entiteiten verwijderd uit de onderzoekservaring die zijn gekoppeld aan andere bladwijzers of waarschuwingen. 


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een jachtonderzoek uitvoeren met behulp van bladwijzers in Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:


- [Proactief op zoek naar bedreigingen](hunting.md)
- [Notitieblokken gebruiken om geautomatiseerde jachtcampagnes uit te voeren](notebooks.md)
