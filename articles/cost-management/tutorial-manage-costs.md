---
title: 'Zelfstudie: kosten beheren met Cloudyn in Azure | Microsoft Docs'
description: In deze zelfstudie leest u hoe u kosten kunt beheren met behulp van kostentoewijzing en rapporten over showback en chargeback.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: tutorial
ms.service: cost-management-billing
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: d4117e8a40f277c6ac0213272176b75a1c161eb1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229809"
---
# <a name="tutorial-manage-costs-by-using-cloudyn"></a>Zelfstudie: kosten beheren met Cloudyn

In Cloudyn kunt u kosten beheren en showback-rapporten maken door kosten toe te wijzen op basis van tags. Het proces van kostentoewijzing houdt in dat er kosten worden toegewezen aan uw verbruikte cloudresources. Kosten worden volledig toegewezen als al uw resources met behulp van tags in categorieën zijn ingedeeld. Nadat de kosten zijn toegewezen, kunt u met dashboards en rapporten showback of chargeback bieden aan uw gebruikers. Als u aan de slag gaat met Cloudyn hebben veel resources mogelijk nog geen tags of kunnen er geen tags aan worden toegewezen.

Stel dat u engineering-kosten vergoed wilt hebben. U moet aan het engineering-team laten zien dat u een bepaald bedrag nodig hebt, op basis van resourcekosten. U kunt ze een rapport laten zien voor alle verbruikte resources met de tag *engineering*.

In dit artikel worden de termen 'tags' en 'categorieën' soms door elkaar gebruikt. Categorieën zijn grote verzamelingen en kunnen veel zaken bevatten. Ze kunnen bedrijfsonderdelen, kostenplaatsen, webservices en andere zaken bevatten die worden getagd. Tags zijn naam/waarde-paren waarmee u resources kunt categoriseren en geconsolideerde facturerings gegevens kunt weer geven en beheren door dezelfde tag op meerdere resources en resource groepen toe te passen. In eerdere versies van de Azure-portal werd naar een *tagnaam* verwezen als *sleutel*. Tags worden gemaakt en opgeslagen door één Azure-abonnement. Tags in AWS bestaan uit sleutel/waarde-paren. Omdat voor zowel Azure als AWS gebruik wordt gemaakt van de term *sleutel*, wordt in Cloudyn die term ook gebruikt. In Category Manager worden sleutels (tagnamen) gebruikt om tags samen te voegen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Aangepaste tags gebruiken voor het toewijzen van kosten.
> * Showback- en chargeback-rapporten maken.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

- U moet een Azure-account hebben.
- U moet een proefregistratie of een betaald abonnement voor Cloudyn hebben.
- [Niet-geactiveerde accounts moeten worden geactiveerd](activate-subs-accounts.md) in de Cloudyn-portal.
- [Bewaking op gastniveau](azure-vm-extended-metrics.md) moet zijn ingeschakeld op uw virtuele machines.


## <a name="use-custom-tags-to-allocate-costs"></a>Aangepaste tags gebruiken voor het toewijzen van kosten

Cloudyn ontvangt taggegevens van resourcegroepen van Azure en vult taggegevens automatisch in voor resources. In Cost Allocation kunt u de kosten per resourcetag bekijken.

Met het Cost Allocation-model definieert u categorieën (tags) die intern worden toegepast op niet-gecategoriseerde (niet-getagde) resources. Zo kunnen uw kosten worden gegroepeerd en kunnen er regels worden opgegeven voor het verwerken van de niet-getagde kosten. De Cost Allocation-regels zijn opgeslagen instructies op basis waarvan kosten voor een specifieke service worden gedistribueerd naar andere services. Daarna worden voor de resources tags/categorieën weergegeven in *Cost Allocation*-rapporten. Selecteer hiervoor het model dat u hebt gemaakt.

Houd er rekening mee dat de taggegevens van die resources niet worden weergegeven in de *Cost Analysis*-rapporten. Daarnaast worden de tags die worden toegepast in Cloudyn (met Cost Allocation) niet naar Azure verzonden, dus u ziet ze niet in de Azure-portal.

Wanneer u kostentoewijzing start, is het eerste wat u doet het definiëren van het bereik met behulp van een kostenmodel. Het kostenmodel heeft geen invloed op de kosten, maar zorgt alleen voor de verdeling ervan. Wanneer u een kostenmodel maakt, kunt u uw gegevens segmenteren op kostenentiteit, rekening of abonnement, en op meerdere tags. Voorbeelden van veelgebruikte tags zijn een factureringstag, kostenplaats of groepsnaam. Tags kunnen ook helpen bij het uitvoeren van showback of chargeback naar andere onderdelen van uw organisatie.

Als u een aangepast model voor de toewijzing van kosten wilt maken, selecteert u **Costs** &gt; **Cost Management** &gt; **Cost Allocation 360°** in het menu van het rapport.

![Voorbeeld van een dashboard waarin u Cost Allocation 360 selecteert](./media/tutorial-manage-costs/cost-allocation-360.png)

Selecteer op de pagina **Cost Allocation 360** de optie **Add** en voer een naam en beschrijving in voor het kostenmodel. Selecteer alle accounts of individuele accounts. Als u individuele accounts wilt gebruiken, kunt u meerdere accounts selecteren van verschillende cloudserviceproviders. Klik vervolgens op **Categorization** om de gedetecteerde tags te kiezen voor het categoriseren van uw kostengegevens. Kies tags (categorieën) die u wilt opnemen in uw model. In het volgende voorbeeld is de tag **Unit** geselecteerd.

![Voorbeeld van onderverdeling in categorieën van kostenmodel](./media/tutorial-manage-costs/cost-model01.png)

In het voorbeeld ziet u dat $19.680 niet is gecategoriseerd (zonder tags).

Selecteer vervolgens **Uncategorized Resources** en selecteer de services die niet-toegewezen kosten hebben. Definieer vervolgens regels voor het toewijzen van kosten.

Stel dat u de Azure opslagkosten evenredig wilt verdelen over de virtuele machines van Azure (VM's). U selecteert dan eerst de service **Azure/Storage**, **Proportional to Categorized** en **Azure/VM**. Ten slotte selecteert u **Create**.

![Voorbeeld van toewijzingsregel van kostenmodel voor evenredige verdeling](./media/tutorial-manage-costs/cost-model02.png)



In een ander voorbeeld is het bijvoorbeeld wenselijk om alle Azure-netwerkkosten toe te rekenen aan een specifieke bedrijfseenheid in uw organisatie. Hiervoor selecteert u eerst de service **Azure/Network** en vervolgens selecteert u onder **Define Allocation Rule** de optie **Explicit Distribution**. Vervolgens stelt u het distributiepercentage in op 100 en selecteert u de bedrijfseenheid, **G&amp;A** in de volgende afbeelding:

![Voorbeeld van toewijzingsregel van kostenmodel voor een specifieke bedrijfseenheid](./media/tutorial-manage-costs/cost-model03.png)



Voor alle resterende niet-gecategoriseerde resources stelt u aanvullende toewijzingsregels op.

Als u niet-toegewezen gereserveerde instanties van AWS (Amazon Web Services) hebt, kunt u deze met **Reserved Instances** toewijzen aan getagde categorieën.

Als u informatie wilt bekijken over de keuzes die u hebt gemaakt voor het toewijzen van kosten, selecteert u **Summary**. Selecteer **Save As Draft** om uw gegevens op te slaan en later verder te werken aan de aanvullende regels. Als u de gegevens wilt opslaan en Cloudyn het model voor kostentoewijzing direct mag verwerken, selecteert u **Save and Activate**.

In de lijst met kostenmodellen wordt het nieuwe kostenmodel vermeld met de status **Processing**. Het kan even duren voordat de Cloudyn-database is bijgewerkt met uw kostenmodel. Als de verwerking is voltooid, wordt de status bijgewerkt naar **Completed**. U kunt gegevens uit uw kostenmodel nu bekijken in het rapport Cost Analysis, onder **Extended Filters** &gt; **Cost Model**.

### <a name="category-manager"></a>Category Manager

Category Manager is een hulpprogramma voor het opschonen van gegevens waarmee u de waarden van verschillende categorieën (tags) kunt samenvoegen om nieuwe categorieën te maken. Het is een eenvoudig, op regels gebaseerd hulpprogramma: u selecteert een categorie en maakt regels voor het samenvoegen van bestaande waarden. Stel dat u bestaande categorieën hebt voor **R&amp;D** en **dev**, die allebei dezelfde ontwikkelingsgroep vertegenwoordigen.

Klik in de rechterbovenhoek van de portal van Cloudyn op het tandwielpictogram en selecteer **Category Manager**. Als u een nieuwe categorie wilt maken, selecteert u het plusteken ( **+** ). Voer een naam in voor de categorie en voer vervolgens onder **Keys** de categoriesleutels in die u wilt opnemen in de nieuwe categorie.

Wanneer u een regel definieert, kunt u meerdere waarden toevoegen met behulp van een OR-voorwaarde. U kunt ook enkele eenvoudige bewerkingen op tekenreeksen uitvoeren. In beide gevallen klikt u op het beletselteken ( **...** ) rechts van **Rule**.

Als u een nieuwe regel wilt definiëren, kan dat in het gebied **Rules**. Voer bijvoorbeeld eerst **dev** in onder **Rules** en vervolgens **R&amp;D** onder **Actions**. Als u dat hebt gedaan, kunt u de nieuwe categorie opslaan.

De volgende afbeelding toont een voorbeeld van regels die zijn gemaakt voor een nieuwe categorie met de naam **Work-Load**:

![Voorbeeld van de nieuwe categorie werkbelasting](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Tagbronnen en rapporten

Taggegevens in Cloudyn-rapporten zijn afkomstig van drie locaties:

- API's van cloudprovider-resources
- API's van cloudproviders voor facturering
- Handmatig gemaakte tags uit de volgende bronnen:
    - Tags voor Cloudyn-entiteiten: door de gebruiker gedefinieerde metagegevens die zijn toegepast op Cloudyn entiteiten
    - Category Manager: een hulpprogramma voor het opschonen van gegevens waarmee u nieuwe tags kunt maken op basis van regels die worden toegepast op bestaande tags

Als u tags van cloudproviders in kostenrapporten van Cloudyn wilt zien, moet u een aangepast model voor kostentoewijzing maken met behulp van Cost Allocation 360. Ga hiervoor naar **Costs** > **Cost Management** > **Cost Allocation 360**, selecteer de gewenste tags en definieer regels voor het afhandelen van kosten zonder tag. Maak vervolgens een nieuw kostenmodel. Daarna kunt u in Cost Allocation Analysis rapporten weergeven om tags van Azure-resources te bekijken, te filteren en te sorteren.

Tags van Azure-resources worden alleen weergegeven in **Costs** > **Cost Allocation Analysis**-rapporten.

Factureringstags van cloudproviders worden weergegeven in alle kostenrapporten.

Tags van Cloudyn-entiteiten en tags die u handmatig maakt, worden in alle kostenrapporten weergegeven.


## <a name="create-showback-and-chargeback-reports"></a>Showback- en chargeback-rapporten maken

De methode die organisaties gebruiken om showback en chargeback uit te voeren, varieert aanzienlijk. U kunt echter alle dashboards en rapporten in de portal van Cloudyn gebruiken als de basis voor beide doeleinden. U kunt iedereen in uw organisatie toegang geven, zodat ze op aanvraag dashboards en rapporten kunnen bekijken. Alle kostenanalyserapporten ondersteunen showback omdat ze informatie bevatten over de resources die door gebruikers zijn verbruikt. Daarnaast bieden ze gebruikers de gelegenheid om in te zoomen op kosten- of gebruiksgegevens die specifiek zijn voor hun groep binnen uw organisatie.

Als u de resultaten van de kostentoewijzing wilt bekijken, opent u het kostenanalyserapport en selecteert u het kostenmodel dat u hebt gemaakt. Vervolgens voegt u een groepering door op een of meer van de tags die zijn geselecteerd in het kostenmodel.

![Rapport Kostenanalyse met een voorbeeld van gegevens met de nieuwe kosten](./media/tutorial-manage-costs/cost-analysis.png)

U kunt eenvoudig rapporten maken en opslaan die zijn toegespitst op specifieke services die zijn verbruikt door bepaalde groepen. Stel dat een afdeling binnen uw organisatie intensief gebruikmaakt van VM's van Azure. U kunt dan een rapport maken dat is gefilterd op VM's van Azure om het verbruik en de kosten inzichtelijk te maken.

Als u momentopname van de gegevens beschikbaar wilt stellen aan andere teams, kunt u een rapport exporteren in de PDF- of CSV-indeling.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Aangepaste tags gebruiken voor het toewijzen van kosten.
> * Showback- en chargeback-rapporten maken.



In de volgende zelfstudie leert u hoe u toegang tot gegevens beheert.

> [!div class="nextstepaction"]
> [Toegang tot gegevens beheren](tutorial-user-access.md)
