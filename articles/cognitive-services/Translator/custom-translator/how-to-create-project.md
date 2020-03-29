---
title: Een project maken - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u een project maakt en beheert in de aangepaste vertaler azure cognitive services.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5b3a6f2e903d8178554b7a076ae1277633569353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836565"
---
# <a name="create-a-project"></a>Een project maken

Een project is een container voor een model, documenten en tests. Elk project bevat automatisch alle documenten die in die werkruimte worden geüpload en die het juiste taalpaar hebben.

Het maken van project is de eerste stap in de richting van het bouwen van een model.

## <a name="create-a-project"></a>Een project maken:

1.  Klik in de portal [Aangepaste vertaler](https://portal.customtranslator.azure.ai) op Project maken.

    ![Project maken](media/how-to/how-to-create-project.png)

2.  Voer de volgende details over uw project in het dialoogvenster in:

    a.  Projectnaam (vereist): Geef uw project een unieke, betekenisvolle naam. Het is niet nodig om de talen in de titel te vermelden.

    b.  Beschrijving: Een korte samenvatting over het project. Deze beschrijving heeft geen invloed op het gedrag van de aangepaste vertaler of uw resulterende aangepaste systeem, maar kan u helpen onderscheid te maken tussen verschillende projecten.

    c.  Taalpaar (vereist): Selecteer de taal die u van en naar vertaalt.

    d.  Categorie (vereist): selecteer de categorie die het meest geschikt is voor uw project. De categorie beschrijft de terminologie en stijl van de documenten die u wilt vertalen.

    e.  Categoriebeschrijving: Gebruik dit veld om het specifieke veld of de industrie waarin u werkt beter te beschrijven. Als uw categorie bijvoorbeeld geneeskunde is, u een bepaald document, een dergelijke operatie of kindergeneeskunde toevoegen. De beschrijving heeft geen invloed op het gedrag van de aangepaste vertaler of uw resulterende aangepaste systeem.

    f.  Projectlabel: Het [projectlabel](workspace-and-project.md#project-labels) maakt onderscheid tussen projecten met hetzelfde taalpaar en dezelfde categorie. Gebruik als aanbevolen praktijk *alleen* een label als u van plan bent meerdere projecten te bouwen voor hetzelfde taalpaar en dezelfde categorie en toegang wilt hebben tot deze projecten met een andere CategoryID. Gebruik dit veld niet als u systemen bouwt voor slechts één categorie. Een projectlabel is niet vereist en niet handig om onderscheid te maken tussen taalparen. U hetzelfde label voor meerdere projecten gebruiken.

    ![Projectdialoogvenster maken](media/how-to/how-to-create-project-dialog.png)

3.  Klik op Maken

## <a name="view-project-details"></a>Projectdetails weergeven

Op de bestemmingspagina Aangepaste vertaler worden de eerste 10 projecten in uw werkruimte weergegeven. Het toont de projectnaam, het taalpaar, de categorie, de status en de BLEU-score.

Nadat u een project hebt geselecteerd, ziet u het volgende op de projectpagina:

- CategorieID: Een categoryID wordt gemaakt door de WorkspaceID, het projectlabel en de categoriecode samen te koppelen. U gebruikt de CategoryID met de API Tekstvertaler om aangepaste vertalingen te krijgen.

- Trein knop: gebruik deze knop om een [opleiding een model](how-to-train-model.md)te starten.

- Knop Documenten toevoegen: gebruik deze knop om [documenten te uploaden.](how-to-upload-document.md)

- Knop Documenten filteren: gebruik deze knop om specifieke documenten te filteren en te zoeken.

    ![Projectdetails weergeven](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het zoeken, bewerken, verwijderen van projecten](how-to-search-edit-delete-projects.md).
- Meer informatie over [het uploaden van documenten](how-to-upload-document.md) om vertaalmodellen te maken.
