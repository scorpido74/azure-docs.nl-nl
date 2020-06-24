---
title: Bulksgewijs uploaden om leden van een groep toe te voegen of te maken-Azure Active Directory | Microsoft Docs
description: Voeg groeps leden in bulk toe in het Azure Active Directory-beheer centrum.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 755e59a029b0a12e51db53c6c7d3bb00358af635
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84742009"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>Groeps leden bulksgewijs toevoegen in Azure Active Directory

Met Azure Active Directory-Portal (Azure AD) kunt u een groot aantal leden toevoegen aan een groep met behulp van een bestand met door komma's gescheiden waarden (CSV) voor het bulk importeren van groeps leden.

## <a name="understand-the-csv-template"></a>De CSV-sjabloon begrijpen

Down load en vul de CSV-sjabloon bulksgewijs uploaden in om Azure AD-groeps leden bulksgewijs toe te voegen. Uw CSV-sjabloon kan er als volgt uitzien:

![Werk blad voor upload-en aanroep-outs waarin het doel en de waarden voor elke rij en kolom worden uitgelegd](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>Structuur van CSV-sjabloon

De rijen in een gedownloade CSV-sjabloon zijn als volgt:

- **Versie nummer**: de eerste rij met het versie nummer moet worden opgenomen in de upload CSV.
- **Kolom koppen**: de indeling van de kolom koppen is de &lt; *item naam* &gt; [propertyname] &lt; *vereist of leeg* &gt; . Bijvoorbeeld `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Sommige oudere versies van de sjabloon hebben mogelijk een kleine variatie. Voor wijzigingen in groepslid maatschappen hebt u de mogelijkheid om te gebruiken id: lid object-ID of user principal name.
- **Voor beelden van rij**: we hebben in de sjabloon een rij met voor beelden van geldige waarden voor elke kolom opgenomen. U moet de voor beelden van de rij verwijderen en vervangen door uw eigen vermeldingen.

### <a name="additional-guidance"></a>Aanvullende richtlijnen

- De eerste twee rijen van de upload sjabloon mogen niet worden verwijderd of gewijzigd, of de upload kan niet worden verwerkt.
- De vereiste kolommen worden eerst weer gegeven.
- Het is niet raadzaam om nieuwe kolommen toe te voegen aan de sjabloon. Alle extra kolommen die u toevoegt, worden genegeerd en worden niet verwerkt.
- U wordt aangeraden de meest recente versie van de CSV-sjabloon zo vaak mogelijk te downloaden.
- Voeg ten minste twee Upn's of object-Id's van gebruikers toe om het bestand te uploaden.

## <a name="to-bulk-import-group-members"></a>Groeps leden bulksgewijs importeren

1. Meld u aan bij [de Azure Portal](https://portal.azure.com) met een Administrator-account van de gebruiker in de organisatie. Groeps eigenaren kunnen ook leden van groepen waarvan ze eigenaar zijn, bulksgewijs importeren.
1. Selecteer **groepen**  >  **alle groepen**in azure AD.
1. Open de groep waaraan u leden wilt toevoegen en selecteer vervolgens **leden**.
1. Selecteer **leden importeren**op de pagina **leden** .
1. Selecteer op de pagina **groeps leden Bulk Import** de optie **downloaden** om de CSV-bestands sjabloon met de vereiste eigenschappen voor de groeps leden op te halen.

    ![De opdracht leden importeren bevindt zich op de profiel pagina voor de groep](./media/groups-bulk-import-members/import-panel.png)

1. Open het CSV-bestand en voeg een regel toe voor elk groepslid dat u wilt importeren in de groep (de vereiste waarden zijn een **Member object-id** of **User Principal name**). Sla het bestand op.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="Het CSV-bestand bevat de namen en Id's van de leden die moeten worden geïmporteerd":::

1. Blader op de pagina **groeps leden Bulk Import** onder **uw CSV-bestand uploaden**naar het bestand. Wanneer u het bestand selecteert, wordt de validatie van het CSV-bestand gestart.
1. Wanneer de bestands inhoud is gevalideerd, wordt het **bestand dat is geüpload**weer gegeven op de pagina Bulk Import. Als er fouten zijn, moet u deze oplossen voordat u de taak kunt indienen.
1. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om de Azure bulk bewerking te starten waarmee de groeps leden in de groep worden geïmporteerd.
1. Wanneer de import bewerking is voltooid, ziet u een melding dat de bulk bewerking is geslaagd.

## <a name="check-import-status"></a>Import status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking** .

[![](media/groups-bulk-import-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-import-members/bulk-center.png#lightbox)

Selecteer de waarden onder de kolommen **# geslaagd**, **# fout**of **Totaal aantal aanvragen** voor meer informatie over elk regel item in de bulk bewerking. Als er fouten zijn opgetreden, worden de redenen voor de fout weer gegeven.

## <a name="bulk-import-service-limits"></a>Service limieten voor bulk import

Elke bulk activiteit voor het importeren van een lijst met groeps leden kan Maxi maal één uur worden uitgevoerd. Hiermee kan een lijst van ten minste 40.000 leden worden ingevoerd.

## <a name="next-steps"></a>Volgende stappen

- [Groeps leden bulksgewijs verwijderen](groups-bulk-remove-members.md)
- [Leden van een groep downloaden](groups-bulk-download-members.md)
- [Een lijst met alle groepen downloaden](groups-bulk-download.md)
