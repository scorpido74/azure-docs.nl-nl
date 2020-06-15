---
title: 'Zelfstudie: Een Hugo-site publiceren in Azure Static Web Apps'
description: Meer informatie over hoe u een Hugo-toepassing implementeert in Azure Static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: b65213bd87f6b82391733a135e096077127765d7
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344013"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Zelfstudie: Een Hugo-site publiceren in Azure Static Web Apps Preview

In dit artikel ziet u hoe u een [Hugo](https://gohugo.io/)-webtoepassing maakt en implementeert in [Azure Static Web Apps](overview.md). Het uiteindelijke resultaat is een nieuwe Azure Static Web Apps-resource met de bijbehorende GitHub-acties, waarmee u beheert hoe de app wordt gebouwd en gepubliceerd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Een Hugo-app maken
> - Een Azure Static Web Apps-resource instellen
> - De Hugo-app implementeren in Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. Als u nog geen account hebt, kunt u [gratis een account maken](https://azure.microsoft.com/free/).
- Een GitHub-account. Als u nog geen account hebt, kunt u [gratis een account maken](https://github.com/join).

## <a name="create-a-hugo-app"></a>Een Hugo-app maken

Een Hugo-app maken met behulp van de Hugo-CLI (opdrachtregelinterface):

1. Volg de [installatiehandleiding](https://gohugo.io/getting-started/installing/) voor Hugo in uw besturingssysteem.

1. Open een terminal

1. Voer de Hugo-CLI uit om een nieuwe app te maken.

   ```bash
   hugo new site static-app
   ```

1. Ga naar de nieuwe app.

   ```bash
   cd static-app
   ```

1. Initialiseer een Git-opslagplaats.

   ```bash
    git init
   ```

1. Voeg vervolgens een thema toe aan de site door een thema te installeren als een Git-submodule, en deze vervolgens op te geven in het Hugo-configuratiebestand.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Voer de wijzigingen door.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Push de toepassing naar GitHub

U hebt een opslagplaats in GitHub nodig om verbinding te maken met Azure Static Web Apps. In de volgende stappen ziet u hoe u een opslagplaats maakt voor uw site.

1. Maak in [https://github.com/new](https://github.com/new) een lege GitHub-opslagplaats (maak geen Leesmij-bestand) met de naam **statische-hugo-app**.

1. Voeg de GitHub-opslagplaats als externe locatie toe aan de lokale opslagplaats. Zorg ervoor dat u de tijdelijke aanduiding `<YOUR_USER_NAME>` in de volgende opdracht vervangt door uw GitHub-gebruikersnaam.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Push de lokale opslagplaats naar GitHub.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>De web-app implementeren

De volgende stappen laten zien hoe u een nieuwe statische site-app maakt en deze implementeert in een productieomgeving.

### <a name="create-the-application"></a>De toepassing maken

1. Ga naar [Azure Portal](https://portal.azure.com)
1. Klik op **Een resource maken**
1. Zoek **Static Web Apps**
1. Klik op **Static Web Apps (preview)**
1. Klik op **Maken**.

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="Een Azure Static Web Apps-resource maken in de portal":::

1. Accepteer bij **Abonnement** het abonnement dat wordt vermeld, of selecteer een nieuw abonnement in de vervolgkeuzelijst.

1. Selecteer in _Resourcegroep_ de optie **Nieuw**. Voer bij _Naam van nieuwe resourcegroep_ in: **statische-hugo-app**. Selecteer vervolgens **OK**.

1. Voer vervolgens een naam in voor uw app in het vak **Naam**. Geldige tekens zijn `a-z`, `A-Z`, `0-9` en `-`.

1. Selecteer voor _Regio_ een beschikbare regio dicht bij u in de buurt.

1. Selecteer voor _SKU_ de optie **Gratis**.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Ingevulde gegevens":::

1. Klik op de knop **Aanmelden bij GitHub**.

1. Selecteer de **Organisatie** waaronder u de opslagplaats hebt gemaakt.

1. Selecteer de **statische-hugo-app** als de _Opslagplaats_.

1. Selecteer **hoofd** voor de _Vertakking_.

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Voltooide GitHub-gegevens":::

### <a name="build"></a>Ontwikkelen

Vervolgens voegt u configuratie-instellingen toe die tijdens het bouwproces worden gebruikt om de app te bouwen. Met de volgende instellingen wordt het werkstroombestand voor GitHub-acties geconfigureerd.

1. Klik op de knop **Volgende: Build >** om de configuratie van de build te bewerken

1. Stel _App-locatie_ in op **Openbaar**.

1. Laat _Locatie van app-artefact_ leeg.

   Een waarde voor _API-locatie_ is niet nodig omdat u momenteel geen API implementeert.

### <a name="review-and-create"></a>Controleren en maken

1. Klik op de knop **Beoordelen en maken** om te controleren of de details kloppen.

1. Klik op **Maken** om te beginnen met het maken van de Azure Static Web Apps-resource, en richt een GitHub-actie in voor de implementatie.

1. Zodra de implementatie is voltooid, gaat u naar de terminal en haalt u de doorvoer op met de GitHub-actie op de computer.

   ```bash
   git pull
   ```

1. Open de Hugo-app in een teksteditor en open het bestand _.github/workflows/azure-pages-<NAAM_VAN-WERKSTROOM>.yml_.

1. Vervang de regel `- uses: actions/checkout@v2` (regel 18) door het volgende, om de Hugo-toepassing te bouwen. Als u Hugo Extended vereist, verwijdert u het commentaar van `extended: true`.

   ```yml
   - uses: actions/checkout@v2
     with:
       submodules: true  # Fetch Hugo themes (true OR recursive)
       fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

   - name: Setup Hugo
     uses: peaceiris/actions-hugo@v2.4.11
     with:
       hugo-version: "latest"  # Hugo version: latest OR x.y.z
       # extended: true

   - name: Build
     run: hugo
   ```
   
   Zie [peaceiris/actions-hugo](https://github.com/peaceiris/actions-hugo) voor meer informatie over het installeren van Hugo op GitHub Actions Runner.

1. Voer de bijgewerkte werkstroom door en push deze naar GitHub.

   ```bash
   git add -A
   git commit -m "Updating GitHub Actions workflow"
   git push
   ```

1. Wacht totdat de GitHub-actie is voltooid.

1. Klik in het _Overzichtsvenster_ van de Azure-portal met onlangs gemaakte Azure Static Web App-resources op de _URL_-koppeling om de geïmplementeerde toepassing te openen.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Geïmplementeerde toepassing":::

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een aangepast domein toevoegen](custom-domain.md)
