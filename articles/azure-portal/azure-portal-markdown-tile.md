---
title: Een aangepaste afwaarderingstegel gebruiken op Azure-dashboards
description: Meer informatie over het toevoegen van een afwaarderingstegel aan een Azure-dashboard om statische inhoud weer te geven
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/08/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 74102423461a56bb6fc19c2eb9874f96a76e34e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310710"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Een afwaarderingstegel op Azure-dashboards gebruiken om aangepaste inhoud weer te geven

U een afwaarderingstegel toevoegen aan uw Azure-dashboards om aangepaste, statische inhoud weer te geven. U bijvoorbeeld basisinstructies, een afbeelding of een set hyperlinks op een markeringstegel weergeven.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Een afwaarderingstegel toevoegen aan uw dashboard

1. Selecteer **Dashboard** op de zijbalk van de Azure-portal.

   ![Schermafbeelding van de zijbalk van de portal](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Als u aangepaste dashboards hebt gemaakt, gebruikt u in de dashboardweergave de vervolgkeuzelijst om het dashboard te selecteren waar de aangepaste markeringstegel moet worden weergegeven. Selecteer het pictogram Bewerken om de **Tegelgalerie**te openen .

   ![Schermafbeelding van de weergave dashboardbewerking](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. Zoek in de **tegelgalerie**de tegel met de naam **Markdown** en selecteer **Toevoegen**. De tegel wordt aan het dashboard toegevoegd en het deelvenster **Markering bewerken** wordt geopend.

1. Voer waarden in voor **Titel** en **Ondertitel,** die op de tegel worden weergegeven nadat u naar een ander veld bent gegaan.

   ![Schermafbeelding van resultaten van het invoeren van titel en ondertitel](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Selecteer een van de opties voor het opnemen van **afwaarderingsinhoud: Inline-bewerking** of **Inhoud invoegen met URL**.

   - Selecteer **Inline-bewerking** als u direct afwaardering wilt invoeren.

      ![Schermafbeelding van het invoeren van inline-inhoud](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Selecteer **Inhoud invoegen met URL** als u bestaande markeringsinhoud wilt gebruiken die online wordt gehost.

      ![Schermafbeelding van het invoeren van URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Voor extra beveiliging u een afwaarderingsbestand maken en opslaan in een [blob met Azure-opslagaccount waar versleuteling is ingeschakeld](../storage/common/storage-service-encryption.md)en vervolgens het bestand aanwijzen met de URL-optie. De markeringsinhoud wordt versleuteld via de versleutelingsopties van het opslagaccount. Alleen gebruikers met machtigingen voor het bestand kunnen de markeringsinhoud op het dashboard zien.

1. Selecteer **Gereed** om het deelvenster **Markering bewerken te** sluiten. De inhoud wordt weergegeven op de tegel Markdown, waarmee u het formaat wijzigen door de handgreep in de rechterbenedenhoek te slepen.

   ![Schermafbeelding van aangepaste afwaarderingstegel](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Markdown-inhoudsmogelijkheden en -beperkingen

U elke combinatie van platte tekst, markdownsyntaxis en HTML-inhoud op de markeringstegel gebruiken. De Azure-portal maakt gebruik van een open-sourcebibliotheek met de naam _gemarkeerd_ om uw inhoud om te zetten in HTML die op de tegel wordt weergegeven. De HTML die door _gemarkeerd wordt_ geproduceerd, wordt vooraf verwerkt door de portal voordat deze wordt weergegeven. Met deze stap u ervoor zorgen dat uw aanpassing geen invloed heeft op de beveiliging of lay-out van de portal. Tijdens die voorbewerking wordt elk deel van de HTML dat een potentiële bedreiging vormt, verwijderd. De volgende typen inhoud zijn niet toegestaan door de portal:

* JavaScript `<script>` – tags en inline JavaScript-evaluaties worden verwijderd.
* iframes `<iframe>` - tags worden verwijderd.
* Stijl `<style>` - tags worden verwijderd. Inline-stijlkenmerken op HTML-elementen worden niet officieel ondersteund. U vinden dat sommige inline stijlelementen voor u werken, maar als zij met de lay-out van het portaal interfereren, konden zij op elk ogenblik ophouden werkend. De tegel Markdown is bedoeld voor basisstatische inhoud die de standaardstijlen van de portal gebruikt.

## <a name="next-steps"></a>Volgende stappen

* Zie [Dashboards maken en delen in de Azure-portal](../azure-portal/azure-portal-dashboards.md) als u een aangepast dashboard wilt maken.
