---
title: Key Vault met Azure Monitor bewaken voor Key Vault (preview) | Microsoft Docs
description: In dit artikel wordt de Azure Monitor voor sleutel kluizen beschreven.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: 97bea90e67b9449a8f5fd7b333b9ac149abef2f8
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84945457"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>Bewaken van uw sleutel kluis service met Azure Monitor voor Key Vault (preview-versie)
Azure Monitor voor Key Vault (preview) biedt uitgebreide bewaking van uw sleutel kluizen door een uniforme weer gave te bieden van uw Key Vault-aanvragen, prestaties, fouten en latentie.
In dit artikel wordt uitgelegd hoe u de ervaring van Azure Monitor kunt opdoen en aanpassen voor Key Vault (preview).

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Inleiding tot Azure Monitor voor Key Vault (preview-versie)

Voordat u naar de ervaring gaat, moet u weten hoe de informatie wordt gepresenteerd en gevisualiseerd.
-    **Op schaal perspectief** met een moment opname weergave van de prestaties op basis van de aanvragen, uitsplitsing van fouten en een overzicht van de bewerkingen en latentie.
-   **Zoom analyse** van een bepaalde sleutel kluis uit om gedetailleerde analyse uit te voeren.
-    **Aanpasbaar** waar u kunt wijzigen welke metrische gegevens u wilt zien, wijzigen of instellen van drempel waarden die met uw limieten worden uitgelijnd en uw eigen werkmap opslaan. Grafieken in de werkmap kunnen worden vastgemaakt aan Azure-Dash boards.

Azure Monitor voor Key Vault worden logboeken en metrische gegevens gecombineerd om een algemene bewakings oplossing te bieden. Alle gebruikers hebben toegang tot de metrische gegevens voor bewaking, maar het opnemen van op Logboeken gebaseerde visualisaties vereist mogelijk dat gebruikers [logboek registratie van hun Azure Key Vault inschakelen](https://docs.microsoft.com/azure/key-vault/key-vault-logging).

## <a name="configuring-your-key-vaults-for-monitoring"></a>Uw sleutel kluizen voor bewaking configureren

> [!NOTE]
> Het inschakelen van Logboeken is een betaalde service die extra bewakings mogelijkheden biedt.

1. Het tabblad bewerkingen & latentie helpt u bij het bepalen van het aantal en de sleutel kluizen die zijn ingeschakeld. Als u wilt verzamelen, selecteert u de knop **inschakelen** , waarmee u een afzonderlijke werkmap kunt weer geven met een lijst met de sleutel kluizen waarvoor Diagnostische logboeken moeten worden ingeschakeld.

    ![Scherm afbeelding van het tabblad bewerkingen en latentie met blauwe knop inschakelen](./media/key-vaults-insights-overview/enable-logging.png)

2. Als u Diagnostische logboeken wilt inschakelen, klikt u op de kolom **inschakelen** onder de acties en maakt u een nieuwe diagnostische instelling waarmee logboeken naar een log Analytics-werk ruimte worden verzonden. Het is raadzaam om alle logboeken naar dezelfde werk ruimte te verzenden.

3. Zodra de diagnostische instellingen zijn opgeslagen, kunt u alle op Logboeken gebaseerde grafieken en visualisaties onder de Key Vault Insights weer geven. Het kan enkele minuten tot uur duren voordat de logboeken zijn gevuld.

4. Lees de [volledige hand leiding](https://docs.microsoft.com/azure/key-vault/key-vault-logging)voor meer informatie over het inschakelen van Diagnostische logboeken voor uw Key Vault-service.

## <a name="view-from-azure-monitor"></a>Weer geven van Azure Monitor

Vanuit Azure Monitor kunt u de aanvraag, latentie en fout gegevens van meerdere sleutel kluizen in uw abonnement bekijken en prestatie problemen en beperkings scenario's identificeren.

Voer de volgende stappen uit om het gebruik en de bewerkingen van uw opslag accounts in al uw abonnementen weer te geven:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/)

2. Selecteer **monitor** in het linkerdeel venster in het Azure Portal en selecteer in de sectie inzichten de optie **sleutel kluizen (preview-versie)**.

![Scherm afbeelding van overzichts ervaring met meerdere grafieken](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Overzichts werkmap

In de overzichts werkmap voor het geselecteerde abonnement worden in de tabel de metrische gegevens van de interactieve sleutel kluis voor sleutel kluizen gegroepeerd in het abonnement weer gegeven. U kunt de resultaten filteren op basis van de opties die u selecteert in de volgende vervolg keuzelijsten:

* Abonnementen: alleen abonnementen met sleutel kluizen worden weer gegeven.

* Sleutel kluizen: standaard zijn Maxi maal 5 sleutel kluizen vooraf geselecteerd. Als u in de scope selector alle of meerdere sleutel kluizen selecteert, worden er Maxi maal 200 sleutel kluizen geretourneerd. Als u bijvoorbeeld een totaal van 573 sleutel kluizen hebt in drie abonnementen die u hebt geselecteerd, worden er slechts 200 kluizen weer gegeven.

* Tijds bereik: standaard wordt de laatste 24 uur aan gegevens weer gegeven op basis van de overeenkomstige gemaakte selecties.

Op de tegel item, onder de vervolg keuzelijst, wordt het totale aantal sleutel kluizen in de geselecteerde abonnementen samengevouwen en wordt weer gegeven hoeveel er is geselecteerd. Er zijn voorwaardelijke kleurgecodeerde Heatmaps voor de kolommen van de werkmap waarin de aanvraag, fouten en latentie gegevens worden gerapporteerd. De diepste kleur heeft de hoogste waarde en een lichtere kleur op basis van de laagste waarden.

## <a name="failures-workbook"></a>Werkmap met fouten

Selecteer **fouten** boven aan de pagina en het tabblad fouten wordt geopend. Hier ziet u de API-treffers, de frequentie van de tijd, samen met de hoeveelheid van bepaalde reactie codes.

![Scherm opname van fouten werkmap](./media/key-vaults-insights-overview/failures.png)

Er zijn voorwaardelijke kleurcoderings-of Heatmaps voor kolommen in de werkmap die de metrische gegevens van API-treffers met een blauw waarde rapporteren. De diepste kleur heeft de hoogste waarde en een lichtere kleur op basis van de laagste waarden.

In de werkmap worden successen (2xx-status codes) weer gegeven, authenticatie fouten (401/403-status codes), beperking (429-status codes) en andere fouten (4xx-status codes).

Als u beter wilt begrijpen wat elk van de status codes vertegenwoordigen, raden we u aan de documentatie over [Azure Key Vault status-en respons codes](https://docs.microsoft.com/azure/key-vault/authentication-requests-and-responses)te lezen.

## <a name="operations--latency-workbook"></a>Werkmap voor bewerkingen & latentie

Selecteer **bewerkingen & latentie** boven aan de pagina en het tabblad **bewerkingen & latentie** wordt geopend. Op dit tabblad kunt u de sleutel kluizen voor bewaking onboarden. Zie de sectie [uw sleutel kluizen configureren voor bewaking](#configuring-your-key-vaults-for-monitoring) voor meer gedetailleerde stappen.

U kunt zien hoeveel sleutel kluizen er zijn ingeschakeld voor de logboek registratie. Als ten minste één kluis juist is geconfigureerd, kunt u tabellen weer geven waarin de bewerkingen en status codes voor elk van uw sleutel kluizen worden weer gegeven. U kunt klikken op de sectie Details voor een rij om aanvullende informatie over de afzonderlijke bewerking op te halen.

![Scherm afbeelding van bewerkingen en latentie grafieken](./media/key-vaults-insights-overview/logs.png)

Als u geen gegevens voor deze sectie ziet, gaat u naar de bovenste sectie over het inschakelen van Logboeken voor Azure Key Vault of raadpleegt u de sectie probleem oplossing hieronder.

## <a name="view-from-a-key-vault-resource"></a>Weer geven vanuit een Key Vault resource

Direct toegang tot Azure Monitor voor Key Vault vanuit een sleutel kluis:

1. Selecteer in de Azure Portal sleutel kluizen.

2. Kies een sleutel kluis in de lijst. Klik in de sectie bewaking op inzichten (preview-versie).

Deze weer gaven zijn ook toegankelijk door de resource naam van een sleutel kluis te selecteren in het Azure Monitor niveau van de werkmap.

![Scherm opname van de weer gave van een sleutel kluis resource](./media/key-vaults-insights-overview/key-vault-resource-view.png)

Op de **overzichts** werkmap voor de sleutel kluis worden verschillende prestatie gegevens weer gegeven die u helpen om snel te beoordelen:

- Interactieve prestatie grafieken met de meest essentiële gegevens met betrekking tot sleutel kluis transacties, latentie en beschik baarheid.

- Metrische gegevens en status tegels markeren Beschik baarheid van de service, het totale aantal trans acties voor de sleutel kluis resource en de algehele latentie.

Als u een van de andere tabbladen voor **fouten** of **bewerkingen** selecteert, worden de respectieve werkmappen geopend.

![Scherm opname van fouten weergave](./media/key-vaults-insights-overview/resource-failures.png)

De werkmap bevat een uitsplitsing van de resultaten van alle belang rijke kluis aanvragen in het geselecteerde tijds bestek en biedt categorisatie op geslaagde (2xx), authenticatie fouten (401/403), gashendel (429) en andere fouten.

![Scherm opname van de weer gave bewerkingen](./media/key-vaults-insights-overview/operations.png)

Met de operations-werkmap kunnen gebruikers dieper worden uitgebreid naar de volledige details van alle trans acties, die kunnen worden gefilterd op de resultaat status met behulp van de tegels op het hoogste niveau.

![Scherm opname van de weer gave bewerkingen](./media/key-vaults-insights-overview/info.png)

Gebruikers kunnen ook weer gaven bereiken op basis van specifieke transactie typen in de bovenste tabel, waarmee de lagere tabel dynamisch wordt bijgewerkt, waar gebruikers de volledige bewerkings details kunnen bekijken in een pop-upcontext venster.

>[!NOTE]
> Houd er rekening mee dat gebruikers de diagnostische instellingen moeten hebben ingeschakeld om deze werkmap weer te geven. Meer informatie over het inschakelen van de diagnostische instelling vindt u meer informatie over [Azure Key Vault logboek registratie](https://docs.microsoft.com/azure/key-vault/general/logging).

## <a name="pin-and-export"></a>Vastmaken en exporteren

U kunt een van de metrische gedeelten aan een Azure-dash board vastmaken door het pictogram punaise rechtsboven in de sectie te selecteren.

Het overzicht van het abonnement op meerdere abonnementen en sleutel kluizen of fouten werkmappen ondersteunen het exporteren van de resultaten in Excel-indeling door het Download pictogram links van het pictogram punaise te selecteren.

![Scherm afbeelding van het pictogram pincode geselecteerd](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Azure Monitor voor Key Vault aanpassen

In deze sectie worden algemene scenario's beschreven voor het bewerken van de werkmap om uw behoeften aan te passen aan uw gegevens analyse:
*  De werkmap zo beperken dat er altijd een bepaald abonnement of een sleutel kluis wordt geselecteerd
* Metrische gegevens in het raster wijzigen
* De drempel waarde voor aanvragen wijzigen
* De kleur weergave wijzigen

U kunt de aanpassingen beginnen door de bewerkings modus in te scha kelen door de knop **aanpassen** te selecteren in de bovenste werk balk.

![Scherm afbeelding van de knop aanpassen](./media/key-vaults-insights-overview/customize.png)

Aanpassingen worden opgeslagen in een aangepaste werkmap om te voor komen dat de standaard configuratie in onze gepubliceerde werkmap wordt overschreven. Werkmappen worden opgeslagen in een resource groep, hetzij in het gedeelte Mijn rapporten dat persoonlijk is voor u of in de sectie gedeelde rapporten die toegankelijk is voor iedereen die toegang heeft tot de resource groep. Nadat u de aangepaste werkmap hebt opgeslagen, moet u naar de galerie met werkmappen gaan om deze te starten.

![Scherm opname van de werkmap galerie](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Een abonnement of sleutel kluis opgeven

Door de volgende stappen uit te voeren, kunt u het overzicht van het abonnement op meerdere abonnementen en de sleutel kluis configureren of de werkmappen zodanig instellen dat er voor elke uitvoering een bepaald abonnement of een of meer sleutel kluis (s) wordt opgegeven:

1. Selecteer **monitor** in de portal en selecteer vervolgens **sleutel kluizen (preview)** in het linkerdeel venster.
2. Selecteer in de werk blad **overzicht** de optie **bewerken**in de opdracht balk.
3. Selecteer in de vervolg keuzelijst **abonnementen** een of meer abonnementen die door yo moeten worden gebruikt als standaard. Houd er rekening mee dat de werkmap ondersteuning biedt voor het selecteren van Maxi maal 10 abonnementen.
4. Selecteer in de vervolg keuzelijst **sleutel kluizen** een of meer accounts die u wilt gebruiken als de standaard instelling. Houd er rekening mee dat de werkmap het selecteren van Maxi maal 200 opslag accounts ondersteunt.
5. Selecteer **Opslaan als** in de opdracht balk om een kopie van de werkmap met uw aanpassingen op te slaan en klik vervolgens op **gereed bewerken** om terug te keren naar de Lees modus.

## <a name="troubleshooting"></a>Problemen oplossen

Raadpleeg voor algemene richt lijnen voor probleem oplossing het [artikel specifieke informatie over probleem](troubleshoot-workbooks.md)oplossing op basis van een werkmap.

Deze sectie helpt u bij het diagnosticeren en oplossen van problemen met enkele veelvoorkomende problemen die kunnen optreden bij het gebruik van Azure Monitor voor Key Vault (preview). Gebruik de onderstaande lijst om de informatie te vinden die relevant is voor uw specifieke probleem.

### <a name="resolving-performance-issues-or-failures"></a>Prestatie problemen of fouten oplossen

Raadpleeg de [documentatie van Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)als u wilt helpen bij het oplossen van problemen met de sleutel kluis die u identificeert met Azure Monitor voor Key Vault (preview).

### <a name="why-can-i-only-see-200-key-vaults"></a>Waarom kan ik alleen 200 sleutel kluizen zien

Er geldt een limiet van 200 sleutel kluizen die kunnen worden geselecteerd en weer gegeven. Ongeacht het aantal geselecteerde abonnementen, heeft het aantal geselecteerde sleutel kluizen een limiet van 200.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Waarom zie ik niet al mijn abonnementen in de abonnements kiezer

Er worden alleen abonnementen weer gegeven die sleutel kluizen bevatten, die u hebt gekozen in het geselecteerde abonnements filter, die zijn geselecteerd in de ' map + abonnement ' in de koptekst van Azure Portal.

![Scherm opname van het abonnements filter](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>Er wordt een fout bericht weer gegeven dat de query het maximum aantal toegestane werk ruimten/regio's overschrijdt, wat u nu kunt doen

Momenteel geldt een limiet van 25 regio's en 200 werk ruimten, om uw gegevens weer te geven, moet u het aantal abonnementen en/of resource groepen verlagen.

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Ik wil wijzigingen aanbrengen of extra visualisaties toevoegen aan Key Vault inzichten, hoe doe ik dit

Als u wijzigingen wilt aanbrengen, selecteert u de modus ' bewerken ' om de werkmap te wijzigen. vervolgens kunt u uw werk opslaan als een nieuwe werkmap die is gekoppeld aan een aangewezen abonnement en resource groep.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Wat is de tijd korrel zodra een deel van de werkmappen is vastgemaakt

We gebruiken de ' automatische ' tijd korrels, dus afhankelijk van het geselecteerde tijds bereik.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Wat is het tijds bereik wanneer een deel van de werkmap is vastgemaakt

Het tijds bereik is afhankelijk van de instellingen van het dash board.

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>Waarom zie ik geen gegevens voor mijn Key Vault in de secties bewerkingen & latentie

Als u gegevens op basis van uw logboeken wilt weer geven, moet u Logboeken inschakelen voor elk van de sleutel kluizen die u wilt bewaken. Dit kan worden gedaan onder de diagnostische instellingen voor elke sleutel kluis. U moet uw gegevens verzenden naar een aangewezen Log Analytics-werk ruimte.

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>Ik heb logboeken al ingeschakeld voor mijn Key Vault, waarom kan ik mijn gegevens nog steeds niet zien onder bewerkingen & latentie

Op dit moment werken Diagnostische logboeken niet met terugwerkende kracht, zodat de gegevens alleen worden weer gegeven wanneer er acties zijn uitgevoerd voor uw sleutel kluizen. Daarom kan het enige tijd duren, variërend van uren tot een dag, afhankelijk van de manier waarop uw sleutel kluis actief is.

Daarnaast kunt u, als u een groot aantal sleutel kluizen en abonnementen hebt geselecteerd, uw gegevens mogelijk niet weer geven vanwege de beperkingen van de query. Als u uw gegevens wilt bekijken, moet u mogelijk het aantal geselecteerde abonnementen of sleutel kluizen verminderen. 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>Wat moet ik doen als ik andere gegevens wil zien of mijn eigen visualisaties wil maken? Hoe kan ik wijzigingen aanbrengen in de Key Vault Insights

U kunt de bestaande werkmap bewerken met behulp van de bewerkings modus en vervolgens uw werk opslaan als een nieuwe werkmap die al uw nieuwe wijzigingen heeft.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de scenario's werkmappen zijn ontworpen voor ondersteuning, het ontwerpen van nieuwe en het aanpassen van bestaande rapporten en meer door [interactieve rapporten maken met Azure monitor werkmappen](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)te controleren.
