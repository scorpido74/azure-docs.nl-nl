---
title: Instellingen voor controlegereedschappen configureren - Inhoudsmoderator
titleSuffix: Azure Cognitive Services
description: Gebruik het hulpprogramma Controleren om uw team, tags, connectors, werkstromen en referenties voor inhoudsmoderator te configureren of op te halen.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220125"
---
# <a name="configure-the-review-tool"></a>Het beoordelingsprogramma configureren

De [tool Controleren](https://contentmoderator.cognitive.microsoft.com) heeft een aantal belangrijke functies die u openen via het menu **Instellingen** op het dashboard.

![Inhoudsmoderator Ook instellingenmenu controleren](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Team- en subteams beheren

Op het tabblad **Team** u&mdash;uw team- en subteamsgroepen beheren van gebruikers die op de hoogte kunnen worden gesteld wanneer bepaalde [menselijke beoordelingen](../review-api.md#reviews) worden gestart. U slechts één team hebben (dat u maakt wanneer u zich aanmeldt met het gereedschap Controleren), maar u meerdere subteams maken. De teambeheerder kan leden uitnodigen, hun machtigingen instellen en ze toewijzen aan verschillende subteams.

![Instellingen voor gereedschapsteam controleren](images/settings-2-team.png)

Subteams zijn handig voor het maken van escalatieteams of teams die zich toeleggen op het beoordelen van specifieke categorieën inhoud. U bijvoorbeeld inhoud voor volwassenen naar een apart team sturen voor verdere beoordeling.

In dit gedeelte wordt uitgelegd hoe u subteams maakt en snel onderweg beoordelingen toewijst. U echter [werkstromen](workflows.md) gebruiken om beoordelingen toe te wijzen op basis van specifieke criteria.

### <a name="create-a-subteam"></a>Een subteam maken

Ga naar de sectie **Subteams** en klik op **Subteam toevoegen**. Voer de naam van uw subteam in het dialoogvenster in en klik op **Opslaan**.

![Subteamnaam](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Teamleden uitnodigen

U iemand niet aan een subteam toewijzen als deze persoon nog geen lid is van het standaardteam, dus u moet eerst revisoren toevoegen aan het standaardteam. Klik **op Uitnodigen** op het tabblad **Team.**

![Gebruikers uitnodigen](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Teamgenoten toewijzen aan subteam

Klik **op** de knop Lid toevoegen om leden van uw standaardteam toe te wijzen aan een of meer subteams. U alleen bestaande gebruikers toevoegen aan een subteam. Als u nieuwe gebruikers wilt toevoegen die zich niet in het controleprogramma bevinden, nodigt u ze uit via de knop Uitnodigen op de pagina Teaminstellingen.

![Subteamleden toewijzen](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Beoordelingen toewijzen aan subteams

Zodra u uw subteams hebt gemaakt en leden hebt toegewezen, u [inhoudsbeoordelingen](../review-api.md#reviews) toewijzen aan die subteams. Dit gebeurt via het tabblad **Controleren** van de site.
Als u inhoud wilt toewijzen aan een subteam, klikt u op de ellips in de rechterbovenhoek, selecteert u **Verplaatsen naar**en selecteert u een subteam.

![Afbeeldingscontrole toewijzen aan subteam](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Schakelen tussen subteams

Als u lid bent van meer dan één subteam, u schakelen tussen deze subteams om te wijzigen welke inhoudsbeoordelingen voor u worden weergegeven. Selecteer op het tabblad **Controleren** het vervolgkeuzemenu met **de tekst Standaard** en selecteer **Subteam kiezen**. U de inhoudsrecensies voor verschillende subteams bekijken, maar alleen die waarvan u lid bent.

![Schakelen tussen subteams](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tags

Met het tabblad **Labels** u aangepaste moderatietags definiëren&mdash;naast de twee standaardmoderatietags **(a)** en**isadult** **isracy** **(r).** Wanneer u een aangepaste tag maakt, wordt deze beschikbaar in recensies naast de standaardtags. U wijzigen welke tags in beoordelingen worden weergegeven door de zichtbaarheidsinstellingen te wijzigen.

![Tagsweergave, inclusief de selectievakjes 'Zichtbaar'](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Aangepaste tags maken

Als u een nieuwe tag wilt maken, moet u een korte code, naam en beschrijving invoeren in de betreffende velden.

- **Korte code**: Voer een code van twee letters in voor uw tag. Voorbeeld: **cb**
- **Naam:** Voer een korte en beschrijvende tagnaam in kleine letters zonder spaties in. Voorbeeld: **isbullying**.
- **Beschrijving**: (optioneel) Voer een beschrijving in van het soort inhoud waarop uw tag is gericht. Voorbeeld: **Afbeeldingen of gevallen van cyberpesten**.

Klik **op Toevoegen** om een tag toe te voegen en klik op **Opslaan** wanneer u klaar bent met het maken van tags.

![Het hulpprogramma Controleren maakt een nieuw tagdialoogvenster](images/settings-3-tags.png)

### <a name="delete-tags"></a>Tags verwijderen

U aangepaste tags verwijderen door het prullenbakpictogram naast de vermeldingen in de lijst Labels te selecteren, maar u de standaardtags niet verwijderen.

## <a name="connectors"></a>Connectors

Met het tabblad **Connectors** u uw connectors beheren, die servicespecifieke plug-ins zijn die inhoud op verschillende manieren kunnen verwerken als onderdeel van [inhoudswerkstromen.](../review-api.md#workflows)

De standaardconnector wanneer u een werkstroom maakt, is de inhoudsmoderatorconnector, die inhoud kan markeren als **volwassen** of **pikant,** godslastering kan vinden, enzovoort. U echter andere connectoren gebruiken die hier worden vermeld, zolang u referenties hebt voor hun respectieve services (om bijvoorbeeld de Face-connector te gebruiken, moet u een [Face-abonnementssleutel](https://docs.microsoft.com/azure/cognitive-services/face/overview) krijgen).

Het [gereedschap Controleren](./human-in-the-loop.md) bevat de volgende connectors:

- Emotion
- Face
- PhotoDNA Cloud Service
- Tekstanalyse

### <a name="add-a-connector"></a>Een connector toevoegen

Als u een connector wilt toevoegen (en beschikbaar wilt maken voor gebruik in [inhoudswerkstromen),](../review-api.md#workflows)selecteert u de juiste **knop Verbinding maken.** Voer in het volgende dialoogvenster uw abonnementssleutel voor die service in. Wanneer u klaar bent, moet uw nieuwe connector boven aan de pagina worden weergegeven.

![Instellingen voor connectoren voor inhoudsmoderator](images/settings-4-connectors.png)

## <a name="workflows"></a>Werkstromen

Op het tabblad **Werkstromen** u uw [werkstromen](../review-api.md#workflows)beheren. Werkstromen zijn cloudfilters voor inhoud en ze werken met connectors om inhoud op verschillende manieren te sorteren en passende acties uit te voeren. Hier u uw werkstromen definiëren, bewerken en testen. Zie [Werkstromen definiëren en gebruiken](Workflows.md) voor richtlijnen voor hoe u dit doen.

![Werkstroominstellingen voor inhoudsmoderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Referenties

Het tabblad **Referenties** biedt snelle toegang tot uw abonnementssleutel voor inhoudsmoderator, die u nodig hebt om toegang te krijgen tot een van de moderatieservices van een REST-oproep of clientSDK.

![Referenties voor inhoudsmoderator](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Externe referenties gebruiken voor werkstromen

Met [de tool Controleren](https://contentmoderator.cognitive.microsoft.com) wordt een gratis proefsleutel gegenereerd voor Azure Content Moderator-services wanneer u zich aanmeldt, maar u deze ook configureren om een bestaande sleutel van uw Azure-account te gebruiken. Dit wordt aanbevolen voor grootschalige scenario's, omdat gratis proefsleutels strikte gebruikslimieten hebben[(prijzen en limieten).](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)

Als u een [inhoudsmoderatorbron](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) in Azure hebt gemaakt, navigeert u naar de bron in de Azure-portal en selecteert u het blade **Sleutels.** Kopieer een van je sleutels.

![Inhoudsmoderatorsleutels in de Azure-portal](images/credentials-azure-portal-keys.PNG)

Ga op het tabblad Referenties van het [gereedschap](https://contentmoderator.cognitive.microsoft.com) **Controleren** naar het deelvenster **Werkstroominstellingen,** selecteer **Bewerken**en plak de sleutel in het veld **Ocp-Apim-Abonnement-Sleutel.** Werkstromen die de moderatie-API's aanroepen, gebruiken nu uw Azure-referentie.

> [!NOTE]
> De andere twee velden in het deelvenster **Werkstroominstellingen** zijn voor aangepaste termen- en afbeeldingslijsten. Bekijk de [aangepaste termen](../try-terms-list-api.md) of [aangepaste afbeeldingen](../try-image-list-api.md) gidsen om meer te weten te komen over deze.

### <a name="use-your-azure-account-with-the-review-apis"></a>Uw Azure-account gebruiken met de revisie-API's

Als u uw Azure-sleutel wilt gebruiken met de controle-API's, moet u uw resource-id ophalen. Ga naar de bron Inhoudsmoderator in de Azure-portal en selecteer het blad **Eigenschappen.** Kopieer de waarde resource-id en plak deze in het veld **('s)** op de witte lijst van het tabblad Referenties van het gereedschap **Controleren.**

![Resource-id voor inhoudsmoderator in de Azure-portal](images/credentials-azure-portal-resourceid.PNG)

Als u uw abonnementssleutel op beide plaatsen hebt ingevoerd, wordt de testsleutel die bij uw account met de beoordelingstool wordt geleverd, niet gebruikt, maar blijft deze beschikbaar.

## <a name="next-steps"></a>Volgende stappen

Volg het [gereedschap Snel starten van](../quick-start.md) het gereedschap Controleren om het gereedschap Controleren te gebruiken in scenario's voor inhoudsbeheer.