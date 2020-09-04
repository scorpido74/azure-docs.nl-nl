---
title: 'Quickstart: Uw eerste statische web-app bouwen met Azure Static Web Apps'
description: Leer hoe u een Azure Static Web Apps-exemplaar bouwt met een front-end-framework van uw keuze.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: bbc06b657525880f22bd5fb38e902f906d438c9c
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565907"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Quickstart: Uw eerste statische web-app bouwen

Met Azure Static Web Apps worden websites gepubliceerd in een productieomgeving door apps te bouwen vanuit een GitHub-opslagplaats. In deze quickstart bouwt u vanuit een GitHub-opslagplaats een webtoepassing met behulp van een front-end-framework van uw keuze.

Als u nog geen Azure-abonnement hebt, [maakt u een gratis proefaccount](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Vereisten

- Een [GitHub](https://github.com)-account
- [Azure](https://portal.azure.com)-account

## <a name="create-a-repository"></a>Een opslagplaats maken

In dit artikel wordt gebruikgemaakt van een sjabloon voor GitHub-opslagplaatsen, zodat u heel eenvoudig een nieuwe opslagplaats kunt maken. De starter-apps voor de sjabloonfunctie werken met verschillende front-end-frameworks.

# <a name="angular"></a>[Angular](#tab/angular)

- Zorg ervoor dat u bent aangemeld bij GitHub en ga naar de volgende locatie om een nieuwe opslagplaats te maken
  - https://github.com/staticwebdev/angular-basic/generate
- Geef de opslagplaats de naam **mijn-eerste-statische-web-app**

# <a name="react"></a>[React](#tab/react)

- Zorg ervoor dat u bent aangemeld bij GitHub en ga naar de volgende locatie om een nieuwe opslagplaats te maken
  - https://github.com/staticwebdev/react-basic/generate
- Geef de opslagplaats de naam **mijn-eerste-statische-web-app**

# <a name="vue"></a>[Vue](#tab/vue)

- Zorg ervoor dat u bent aangemeld bij GitHub en ga naar de volgende locatie om een nieuwe opslagplaats te maken
  - https://github.com/staticwebdev/vue-basic/generate
- Geef de opslagplaats de naam **mijn-eerste-statische-web-app**

# <a name="no-framework"></a>[Geen framework](#tab/vanilla-javascript)

- Zorg ervoor dat u bent aangemeld bij GitHub en ga naar de volgende locatie om een nieuwe opslagplaats te maken
  - https://github.com/staticwebdev/vanilla-basic/generate
- Geef de opslagplaats de naam **mijn-eerste-statische-web-app**

> [!NOTE]
> Voor Azure Static Web Apps is minstens één HTML-bestand vereist om een web-app te maken. De opslagplaats die u in deze stap maakt, bevat één _index.html_-bestand.

---

Klik op de knop **Opslagplaats maken van sjabloon**.

:::image type="content" source="media/getting-started/create-template.png" alt-text="Opslagplaats maken van sjabloon":::

## <a name="create-a-static-web-app"></a>Statische web-app maken

Nu de opslagplaats is gemaakt, kunt u een statische web-app maken in de Azure-portal.

- Ga naar [Azure Portal](https://portal.azure.com)
- Klik op **Een resource maken**
- Zoek naar **Static Web Apps**
- Klik op **Static Web Apps (preview-versie)**
- Klik op **Maken**

### <a name="basics"></a>Basisbeginselen

Configureer uw nieuwe app en koppel deze aan een GitHub-opslagplaats.

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="Tabblad Basisbeginselen":::

- Selecteer uw _Azure-abonnement_
- Een nieuwe _Resourcegroep_ selecteren of maken
- Geef de app de naam **mijn-eerste-statische-web-app**.
  - Geldige tekens zijn `a-z` (hoofdlettergevoelig), `0-9` en `-`.
- Selecteer de _Regio_ het dichtst bij u in de buurt
- Selecteer de **gratis** _SKU_
- Klik op de knop **Aanmelden bij GitHub** en verifieer u bij GitHub

Nadat u zich hebt aangemeld bij GitHub, voert u de gegevens van de opslagplaats in.

:::image type="content" source="media/getting-started/repository-details.png" alt-text="Gegevens van opslagplaats":::

- Kies de gewenste _organisatie_
- Selecteer **mijn-eerste-statische-web-app** in de vervolgkeuzelijst _Opslagplaats_
- Selecteer **hoofd** in de vervolgkeuzelijst _Vertakking_
- Klik op de knop **Volgende: Build >** om de configuratie van de build te bewerken

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="Knop Volgende Build":::

> [!NOTE]
>  Als u geen opslagplaatsen ziet, moet u mogelijk Azure Static Web Apps autoriseren in GitHub. Blader naar de [startpagina van GitHub](https://github.com) en klik op de afbeelding van uw account om het vervolgkeuzemenu te openen. Klik op **Instellingen** en **Toepassingen > geautoriseerde OAuth-apps > Azure Static Web Apps** en selecteer **Toekennen**. Voor organisatie-opslagplaatsen moet u een eigenaar van de organisatie zijn om de machtigingen te verlenen.

### <a name="build"></a>Build

Voeg vervolgens configuratiegegevens toe die specifiek zijn voor het front-end-framework van uw keuze.

# <a name="angular"></a>[Angular](#tab/angular)

- Voer **/** in het vak _App-locatie_ in
- Wis de standaardwaarde in het vak _API-locatie_
- Voer **dist/angular-basic** in het vak _Locatie van app-artefact_ in

# <a name="react"></a>[React](#tab/react)

- Voer **/** in het vak _App-locatie_ in
- Wis de standaardwaarde in het vak _API-locatie_
- Voer **build** in het vak _Locatie van app-artefact_ in

# <a name="vue"></a>[Vue](#tab/vue)

- Voer **/** in het vak _App-locatie_ in
- Wis de standaardwaarde in het vak _API-locatie_
- Voer **dist** in het vak _Locatie van app-artefact_ in

# <a name="no-framework"></a>[Geen framework](#tab/vanilla-javascript)

- Voer **/** in het vak _App-locatie_ in
- Wis de standaardwaarde in het vak _API-locatie_
- Wis de standaardwaarde in het vak _Locatie van app-artefact_

---

Klik op de knop **Beoordelen en maken**.

:::image type="content" source="media/getting-started/review-create.png" alt-text="Knop Beoordelen en maken":::

Als u deze waarden wilt wijzigen nadat u de app hebt gemaakt, kunt u het [werkstroombestand](github-actions-workflow.md) bewerken.

### <a name="review--create"></a>Beoordelen en maken

Nadat de aanvraag is gevalideerd, kunt u doorgaan met maken van de toepassing.

Klik op de knop **Maken**

:::image type="content" source="media/getting-started/create-button.png" alt-text="Knop Maken":::

Zodra de resource is gemaakt, klikt u op de knop **Ga naar resource**

:::image type="content" source="media/getting-started/resource-button.png" alt-text="Knop Ga naar resource":::

## <a name="view-the-website"></a>De website weergeven

Het implementeren van een statische app heeft twee aspecten. Het eerste aspect is het inrichten van de onderliggende Azure-resources waaruit de app bestaat. Het tweede aspect is een werkstroom voor GitHub-acties waarmee de toepassing wordt gebouwd en gepubliceerd.

Voordat u naar de nieuwe statische site kunt navigeren, moet de uitvoering van de implementatiebuild eerst zijn voltooid.

In het overzichtsvenster van Static Web Apps wordt een reeks koppelingen weergegeven die u helpen te werken met uw web-app.

:::image type="content" source="media/getting-started/overview-window.png" alt-text="Overzichtsvenster":::

1. Als u op de banner klikt waarin staat ‘Klik hier om de status van de uitvoering van uw GitHub-acties te controleren’ wordt u naar de GitHub-acties geleid die worden uitgevoerd in uw opslagplaats. Zodra u hebt gecontroleerd of de implementatietaak is voltooid, kunt u naar de website navigeren via de gegenereerde URL.

2. Zodra de werkstroom voor GitHub-acties is voltooid, kunt u op de _URL_-koppeling klikken om de website te openen op een nieuw tabblad.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, kunt u het Azure Static Web Apps-exemplaar verwijderen door de volgende stappen te volgen:

1. Open de [Azure-portal](https://portal.azure.com)
1. Ga in de bovenste zoekbalk naar **mijn-eerste-statische-web-app**
1. Klik op de naam van de app
1. Klik op de knop **Verwijderen**
1. Klik op **Ja** om de verwijdering te bevestigen

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een API toevoegen](add-api.md)
