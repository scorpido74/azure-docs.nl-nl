---
title: Een Azure IoT Central-toepassing exporteren | Microsoft Documenten
description: Als oplossingsmanager wil ik een toepassingssjabloon exporteren om deze te kunnen hergebruiken.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f50c7e8dcb33fd2ed95829286aaf815926d9fb3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157602"
---
# <a name="export-your-application"></a>Uw toepassing exporteren



In dit artikel wordt beschreven hoe u als oplossingsmanager een IoT Central-toepassing exporteren om deze te kunnen hergebruiken.

U hebt hiervoor twee opties:

- U een kopie van uw toepassing maken als u alleen een dubbele kopie van uw toepassing hoeft te maken.
- U een toepassingssjabloon maken vanuit uw toepassing als u van plan bent meerdere kopieën te maken.

## <a name="copy-your-application"></a>Uw toepassing kopiëren

U een kopie van elke toepassing maken, zonder apparaatinstanties, apparaatgegevensgeschiedenis en gebruikersgegevens. De kopie maakt gebruik van een standaard prijsplan waarvoor u in rekening wordt gebracht. U geen toepassing maken die het gratis prijsplan gebruikt door een toepassing te kopiëren.

Selecteer **Kopiëren**. Voer in het dialoogvenster de details voor de nieuwe toepassing in. Selecteer **vervolgens Kopiëren** om te bevestigen dat u door wilt gaan. Zie [Snel](quick-deploy-iot-central.md) aan de slag voor meer informatie over de velden in het formulier.

![Pagina Toepassingsinstellingen](media/howto-use-app-templates/appcopy2.png)

Nadat de bewerking voor app-kopieën is geslaagd, u via de koppeling naar de nieuwe toepassing navigeren.

![Pagina Toepassingsinstellingen](media/howto-use-app-templates/appcopy3a.png)

Het kopiëren van een toepassing kopieert ook de definitie van regels en e-mailactie. Sommige acties, zoals Flow en Logic Apps, zijn gekoppeld aan specifieke regels via de regel-id. Wanneer een regel naar een andere toepassing wordt gekopieerd, krijgt deze een eigen regel-id. In dit geval moeten gebruikers een nieuwe actie maken en vervolgens de nieuwe regel ermee koppelen. Over het algemeen is het een goed idee om de regels en acties te controleren om ervoor te zorgen dat ze up-to-date zijn in de nieuwe app.

> [!WARNING]
> Als een dashboard tegels bevat die informatie over specifieke apparaten weergeven, worden de tegels weergegeven **De gevraagde bron is niet gevonden** in de nieuwe toepassing. U moet deze tegels opnieuw configureren om informatie over apparaten in uw nieuwe toepassing weer te geven.

## <a name="create-an-application-template"></a>Een toepassingssjabloon maken

Wanneer u een Azure IoT Central-toepassing maakt, u kiezen uit ingebouwde voorbeeldsjablonen. U ook uw eigen toepassingssjablonen maken van bestaande IoT Central-toepassingen. U vervolgens uw eigen toepassingssjablonen gebruiken wanneer u nieuwe toepassingen maakt.

Wanneer u een toepassingssjabloon maakt, worden de volgende items uit uw bestaande toepassing vermeld:

- Het standaardtoepassingsdashboard, inclusief de dashboardindeling en alle tegels die u hebt gedefinieerd.
- Apparaatsjablonen, waaronder metingen, instellingen, eigenschappen, opdrachten en dashboard.
- Regels. Alle regeldefinities zijn opgenomen. Acties, met uitzondering van e-mailacties, zijn echter niet inbegrepen.
- Apparaatsets, inclusief hun voorwaarden en dashboards.

> [!WARNING]
> Als een dashboard tegels bevat die informatie over specifieke apparaten weergeven, worden de tegels weergegeven **De gevraagde bron is niet gevonden** in de nieuwe toepassing. U moet deze tegels opnieuw configureren om informatie over apparaten in uw nieuwe toepassing weer te geven.

Wanneer u een toepassingssjabloon maakt, worden de volgende items niet opgenomen:

- Apparaten
- Gebruikers
- Functiedefinities
- Definities voor continue gegevensexport

Voeg deze items handmatig toe aan toepassingen die zijn gemaakt met een toepassingssjabloon.

Ga als u een toepassingssjabloon maken op basis van een bestaande IoT Central-toepassing:

1. Ga naar de sectie **Administratie** in uw aanvraag.
1. Selecteer **Toepassingssjabloon exporteren**.
1. Voer op de pagina **Toepassingssjabloon exporteren** een naam en beschrijving in voor uw sjabloon.
1. Selecteer de knop **Exporteren** om de toepassingssjabloon te maken. U nu de **deelbare koppeling** kopiëren waarmee iemand een nieuwe toepassing kan maken op basis van de sjabloon:

![Een toepassingssjabloon maken](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Een toepassingssjabloon gebruiken

Als u een toepassingssjabloon wilt gebruiken om een nieuwe IoT Central-toepassing te maken, hebt u een eerder gemaakte **shareable link**nodig. Plak de **deelbare koppeling** in de adresbalk van uw browser. De **pagina Een toepassingspagina maken** wordt weergegeven met de aangepaste toepassingssjabloon:

![Een toepassing maken op basis van een sjabloon](media/howto-use-app-templates/create-app.png)

Selecteer uw prijsplan en vul de andere velden op het formulier in. Selecteer vervolgens **Maken** om een nieuwe IoT Central-toepassing te maken op de toepassingssjabloon.

### <a name="manage-application-templates"></a>Toepassingssjablonen beheren

Op de pagina **Exporteren van toepassingssjablonen** u de toepassingssjabloon verwijderen of bijwerken.

Als u een toepassingssjabloon verwijdert, u de eerder gegenereerde gekoppeld niet meer gebruiken om nieuwe toepassingen te maken.

Als u uw toepassingssjabloon wilt bijwerken, wijzigt u de naam of beschrijving van de sjabloon op de pagina **Exporteren van toepassingssjabloon.** Selecteer vervolgens opnieuw de knop **Exporteren.** Met deze actie genereert u een nieuwe **shareable-koppeling** en wordt elke eerdere **URL van shareable-koppelingen** ongeldig gemaakt.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u toepassingssjablonen moet gebruiken, is de voorgestelde volgende stap om te leren hoe [U IoT Central beheren vanuit de Azure-portal](howto-manage-iot-central-from-portal.md)
