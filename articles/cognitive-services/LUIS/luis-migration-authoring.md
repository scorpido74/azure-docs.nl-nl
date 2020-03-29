---
title: Migreren naar Azure-bron voor het ontwerpen
titleSuffix: Azure Cognitive Services
description: Migreren naar een Azure-bronsleutel voor het ontwerpen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: ec6f9592a4c149be382fab66cca27d929644d988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194506"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migreren naar een Azure-bronontwerpsleutel

Taalbegrip (LUIS) die verificatie maakt, is gewijzigd van een e-mailaccount naar een Azure-bron. Hoewel deze momenteel niet vereist is, wordt de overstap naar een Azure-bron in de toekomst afgedwongen.

## <a name="why-migrate"></a>Voordelen van migratie

Met behulp van een Azure-bron voor het ontwerpen u als eigenaar van de bron de toegang tot het ontwerpen beheren. U bronnen maken en naam maken om verschillende groepen auteurs te beheren.

U bent bijvoorbeeld de eigenaar van 2 LUIS-apps en u hebt verschillende leden die bijdragers zijn in elke app. U twee verschillende ontwerpbronnen maken en elke app aan elke afzonderlijke bron toewijzen. Wijs elk lid vervolgens toe als bijdrager aan de juiste ontwerpbron, afhankelijk van aan welke app ze samenwerken. De Azure-ontwerpbron beheert de autorisatie.

> [!Note]
> Vóór migratie staan co-auteurs bekend als _bijdragers_ op LUIS-app-niveau. Na migratie wordt de Azure-rol van _contribuant_ gebruikt voor dezelfde functionaliteit, maar op het Azure-bronniveau.

## <a name="what-is-migrating"></a>Wat is migreren?

Migratie omvat:

* Alle gebruikers van LUIS, eigenaren en bijdragers.
* **Alle** apps.
* Een **eenrichtingsmigratie.**

De eigenaar kan geen subset van apps kiezen om te migreren en het proces is niet omkeerbaar.

De migratie is niet:

* Een proces dat bijdragers verzamelt en automatisch wordt verplaatst of toevoegt aan de Azure-ontwerpbron. U, als eigenaar van de app, moet deze stap voltooien. Voor deze stap zijn machtigingen voor de juiste resource vereist.
* Een proces voor het maken en toewijzen van een voorspellingruntimebron. Als u een voorspelling runtime bron, dat is [een apart proces](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) en is ongewijzigd.

## <a name="how-are-the-apps-migrating"></a>Hoe migreren de apps?

De [LUIS-portal](https://www.luis.ai) biedt het migratieproces.

U wordt gevraagd te migreren als:

* U hebt apps op het e-mailverificatiesysteem voor het ontwerpen.
* En u bent de app-eigenaar.

U het migratieproces uitstellen door het venster te annuleren. U wordt regelmatig gevraagd om te migreren totdat u migreert of de migratiedeadline is verstreken. U het migratieproces starten vanaf het vergrendelingspictogram van de bovenste navigatiebalk.

## <a name="migration-for-the-app-owner"></a>Migratie voor de eigenaar van de app

### <a name="before-you-migrate"></a>Voordat u migreert

* **Vereist,** u moet een [Azure-abonnement](https://azure.microsoft.com/free/)hebben. Voor een deel van het abonnementsproces is wel factureringsgegevens vereist. U echter de`F0`prijscategorie Gratis ( ) gebruiken wanneer u LUIS gebruikt.
* **Optioneel**maak je een back-up van de apps uit de lijst met apps van de LUIS-portal door elke app te exporteren of de [export-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)te gebruiken.
* **Sla eventueel**de lijst van elke medewerker van elke app op. Deze e-maillijst wordt verstrekt als onderdeel van het migratieproces.


**Het ontwerpen van uw LUIS-app is gratis,** aangegeven door de `F0` laag. Meer informatie [over prijsniveaus](luis-boundaries.md#key-limits).

Als u geen Azure-abonnement hebt, [meldt u zich aan](https://azure.microsoft.com/free/).

### <a name="migration-steps"></a>Migratiestappen

Volg [deze migratiestappen](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Nadat u bent gemigreerd

Na het migratieproces zijn al uw LUIS-apps nu toegewezen aan één LUIS-ontwerpbron.

U meer ontwerpbronnen maken en toewijzen vanaf de pagina **Azure-bronnen beheren >** in de _LUIS-portal_.

U bijdragers toevoegen aan de ontwerpbron vanuit de _Azure-portal_op de pagina **Toegangsbeheer (IAM)** voor die bron. Zie [Toegang tot bijdragen toevoegen voor](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)meer informatie .

|Portal|Doel|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Maak voorspelling en authoring middelen.<br>* Wijs bijdragers toe.|
|[LUIS](https://www.luis.ai)|* Migreren naar nieuwe bronnen voor het ontwerpen.<br>* Voorspellings- en ontwerpbronnen toewijzen of ongedaan maken aan apps van **> Azure-bronnenpagina beheren.**|

## <a name="migration-for-the-app-contributor"></a>Migratie voor de app-bijdrager

Elke gebruiker van LUIS moet migreren, inclusief bijdragers/bijdragers. Een medewerker moet migreren om toegang te hebben tot de app.

> [!Note]
> Als de eigenaar van de LUIS-app is gemigreerd en de medewerker als bijdrager aan de Azure-bron heeft toegevoegd, heeft de medewerker nog steeds geen toegang tot de app, tenzij hij ook migreert.

### <a name="before-the-app-is-migrated"></a>Voordat de app wordt gemigreerd

U ervoor kiezen een app waarop u een medewerker bent te exporteren en de app vervolgens terug te importeren in LUIS. Met het importproces wordt een nieuwe app gemaakt met een nieuwe app-id, waarvoor u de eigenaar bent.

### <a name="after-the-app-is-migrated"></a>Nadat de app is gemigreerd

De eigenaar van de app moet [uw e-mail toevoegen aan de Azure-ontwerpbron als medewerker.](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)

Na het migratieproces zijn alle apps die u bezit beschikbaar op de pagina **Mijn apps** van de LUIS-portal.

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>Problemen oplossen met het migratieproces voor LUIS-authoring

* LUIS-ontwerpsleutels zijn alleen zichtbaar in de LUIS-portal nadat het migratieproces is voltooid. Als u de ontwerpsleutels maakt, zoals met de LUIS CLI, moet de gebruiker het migratieproces nog steeds voltooien in de LUIS-portal.
* Als een gemigreerde gebruiker een niet-gemigreerde gebruiker toevoegt als bijdrager aan zijn azure-bron, heeft de niet-gemigreerde gebruiker geen toegang tot de apps, tenzij deze migreert.
* Als een niet-gemigreerde gebruiker geen eigenaar is van apps, maar een medewerker is van andere apps die eigendom zijn van anderen en de eigenaren het migratieproces hebben ondergaan, moet deze gebruiker migreren om toegang te hebben tot de apps.
* Als een niet-gemigreerde gebruiker een andere gemigreerde gebruiker als medewerker aan zijn app heeft toegevoegd, treedt er een fout op omdat u een gemigreerde gebruiker als medewerker niet aan een app toevoegen. De niet-gemigreerde gebruiker moet dan het migratieproces doorlopen en een azure-bron maken en de gemigreerde gebruiker toevoegen als bijdrager aan die bron.

U ontvangt een foutmelding tijdens het migratieproces als:
* Uw abonnement geeft u geen toestemming om resources voor Cognitieve Services te maken
* Uw migratie heeft een negatieve invloed op de runtime van toepassingen. Bij het migreren worden alle bijdragers uit uw apps verwijderd en wordt u als medewerker uit andere apps verwijderd. Dit proces betekent dat de sleutels die u hebt toegewezen ook worden verwijderd. De migratie wordt geblokkeerd als u sleutels in andere apps hebt toegewezen. Verwijder de sleutel die u veilig hebt toegewezen voordat u migreert. Als u weet dat de sleutel die u hebt toegewezen niet wordt gebruikt in de runtime, moet u deze verwijderen om vooruitgang te kunnen boeken in de migratie.

Toegang tot de Azure-bronlijst van uw app met de volgende URL-indeling:

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>Volgende stappen

* [Uw app migreren naar een auteursrechtbron](luis-migration-authoring-steps.md)
