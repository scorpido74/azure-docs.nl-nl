---
title: Toegang tot Azure-gegevenscatalogus beveiligen
description: In dit artikel wordt uitgelegd hoe u een gegevenscatalogus en de bijbehorende gegevenselementen in Azure-gegevenscatalogus beveiligen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b0972be2b8a6e05d3d90cde7354b4890ea95cbe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976769"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Toegang tot gegevenscatalogus- en gegevenselementenassets beveiligen

> [!IMPORTANT]
> Deze functie is alleen beschikbaar in de standaardeditie van Azure Data Catalog.

Met Azure Data Catalog u opgeven wie toegang heeft tot de gegevenscatalogus en welke bewerkingen (registreren, annoteren, eigendom nemen) ze kunnen uitvoeren op metagegevens in de catalogus. 

## <a name="catalog-users-and-permissions"></a>Catalogusgebruikers en machtigingen

Ga als u een gebruiker of een groep toegang geven tot een gegevenscatalogus en stelt machtigingen in:

1. Klik op de [startpagina van uw gegevenscatalogus](https://www.azuredatacatalog.com)op **Instellingen** op de werkbalk.

   ![Knop Startpagina-instellingen voor Azure Data Catalog](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. Vouw op de pagina Instellingen de sectie **Catalogusgebruikers** uit.

   ![Knop Azure-gegevenscatalogusgebruikers toevoegen](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. Klik op**toevoegen**.

4. Voer de volledig gekwalificeerde **gebruikersnaam** of -naam van de **beveiligingsgroep** in de Azure Active Directory (AAD) in die aan de catalogus is gekoppeld. Gebruik komma (',') als scheidingsteken als u meer dan één gebruiker of groep toevoegt.

   ![Azure Data Catalog-gebruikers - gebruikers of groepen](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. Druk **op ENTER** of **TAB** uit het tekstvak. 

6. Controleer of alle machtigingen (**Annotate**, **Register**en **Take Ownership**) standaard aan deze gebruikers of groepen zijn toegewezen. Dat wil zeggen dat de gebruiker of groep [gegevensactiva]( data-catalog-how-to-register.md)kan registreren , [gegevensactiva kan annoteren]( data-catalog-how-to-annotate.md)en [eigenaar kan worden van gegevensactiva]( data-catalog-how-to-manage.md). 

   ![Azure Data Catalog-gebruikers - standaardmachtigingen](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Als u een gebruiker of groep alleen de leestoegang tot de catalogus wilt geven, wist u de **optie annoteren** voor die gebruiker of groep. Wanneer u dit doet, kan de gebruiker of groep gegevenselementen in de catalogus niet annoteren, maar ze kunnen ze wel bekijken. 

8. Als u een gebruiker of groep wilt ontzeggen gegevensassets te registreren, moet u de **registeroptie** voor die gebruiker of groep wissen.

9. Als u wilt voorkomen dat een gebruiker eigenaar wordt van een gegevenselement, ziet u de optie **eigendom overnemen** voor die gebruiker of groep. 

10. Als u een gebruiker/groep wilt verwijderen uit catalogusgebruikers, klikt u onder aan de lijst op **x** voor de gebruiker/groep. 

   ![Gebruikers van azure datacataloguscatalogus - pictogram gebruiker X verwijderen](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > We raden u aan beveiligingsgroepen toe te voegen aan gebruikers van catalogi in plaats van gebruikers rechtstreeks toe te voegen en machtigingen toe te wijzen. Voeg vervolgens gebruikers toe aan de beveiligingsgroepen die overeenkomen met hun rollen en hun vereiste toegang tot de catalogus.

## <a name="special-considerations"></a>Bijzondere overwegingen

- De machtigingen die aan beveiligingsgroepen zijn toegewezen, zijn additief. Een gebruiker zit bijvoorbeeld in twee groepen. De ene groep heeft annotaatmachtigingen en de andere groep heeft geen annoterende machtigingen. Vervolgens heeft de gebruiker annotaatmachtigingen. 
- De machtigingen die expliciet aan een gebruiker zijn toegewezen, overschrijven de machtigingen die zijn toegewezen aan groepen waartoe de gebruiker behoort. In het vorige voorbeeld hebt u bijvoorbeeld expliciet de gebruiker toegevoegd aan catalogusgebruikers en geen annotatemachtigingen toegewezen. De gebruiker kan gegevenselementen niet annoteren, ook al is de gebruiker lid van een groep die wel annotaatmachtigingen heeft.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met Azure Data Catalog](data-catalog-get-started.md)
