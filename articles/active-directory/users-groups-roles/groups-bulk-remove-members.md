---
title: Groeps leden bulksgewijs verwijderen door een CSV-bestand te uploaden-Azure Active Directory | Microsoft Docs
description: Groeps leden verwijderen uit bulk bewerkingen in het Azure-beheer centrum.
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
ms.openlocfilehash: e9084b486681ded0c194c93f07a404f5f5e88fa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84728466"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Groeps leden bulksgewijs verwijderen in Azure Active Directory

Met Azure Active Directory-Portal (Azure AD) kunt u een groot aantal leden uit een groep verwijderen door een bestand met door komma's gescheiden waarden (CSV) te gebruiken om groeps leden bulksgewijs te verwijderen.

## <a name="understand-the-csv-template"></a>De CSV-sjabloon begrijpen

Down load en vul de CSV-sjabloon bulksgewijs uploaden in om Azure AD-groeps leden bulksgewijs toe te voegen. Uw CSV-sjabloon kan er als volgt uitzien:

![Werk blad voor upload-en aanroep-outs waarin het doel en de waarden voor elke rij en kolom worden uitgelegd](./media/groups-bulk-remove-members/template-example.png)

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

## <a name="to-bulk-remove-group-members"></a>Groeps leden bulksgewijs verwijderen

1. Meld u aan bij [de Azure Portal](https://portal.azure.com) met een Administrator-account van de gebruiker in de organisatie. Groeps eigenaren kunnen ook leden verwijderen van de groepen waarvan ze eigenaar zijn.
1. Selecteer **groepen**  >  **alle groepen**in azure AD.
1. Open de groep waaruit u leden wilt verwijderen en selecteer vervolgens **leden**.
1. Selecteer **leden verwijderen**op de pagina **leden** .
1. Selecteer op de pagina **groeps leden bulksgewijs verwijderen** de optie **downloaden** om de CSV-bestands sjabloon met de vereiste eigenschappen voor de groeps leden op te halen.

   ![De opdracht leden verwijderen bevindt zich op de profiel pagina voor de groep](./media/groups-bulk-remove-members/remove-panel.png)

1. Open het CSV-bestand en voeg een regel toe voor elk groepslid dat u wilt verwijderen uit de groep (de vereiste waarden zijn lid van de object-ID of User Principal Name). Sla het bestand op.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="Het CSV-bestand bevat namen en Id's van de groeps leden die moeten worden verwijderd":::

1. Blader op de pagina **groeps leden bulksgewijs verwijderen** onder **uw CSV-bestand uploaden**naar het bestand. Wanneer u het bestand selecteert, wordt de validatie van het CSV-bestand gestart.
1. Wanneer de bestands inhoud is gevalideerd, wordt het **bestand dat is geüpload**weer gegeven op de pagina Bulk Import. Als er fouten zijn, moet u deze oplossen voordat u de taak kunt indienen.
1. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om de Azure-bulk bewerking te starten waarmee de groeps leden uit de groep worden verwijderd.
1. Wanneer het verwijderen is voltooid, ziet u een melding dat de bulk bewerking is geslaagd.

## <a name="check-removal-status"></a>Verwijderings status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking** .

[![](media/groups-bulk-remove-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-remove-members/bulk-center.png#lightbox)

Selecteer de waarden onder de kolommen **# geslaagd**, **# fout**of **Totaal aantal aanvragen** voor meer informatie over elk regel item in de bulk bewerking. Als er fouten zijn opgetreden, worden de redenen voor de fout weer gegeven.

## <a name="bulk-removal-service-limits"></a>Limieten voor bulksgewijs verwijderen van service

Elke bulk activiteit voor het verwijderen van een lijst met groeps leden van kan Maxi maal één uur worden uitgevoerd. Hierdoor kan een lijst van ten minste 40.000 leden worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

- [Groeps leden bulksgewijs importeren](groups-bulk-import-members.md)
- [Leden van een groep downloaden](groups-bulk-download-members.md)
- [Een lijst met alle groepen downloaden](groups-bulk-download.md)
