---
title: Meldingen van aanmeldingsfouten oplossen | Microsoft Documenten
description: Meer informatie over het oplossen van aanmeldingsfouten met Azure Active Directory-rapporten in de Azure-portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec5fe7f62e8537a7f687202d365eb37d43b48b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008062"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>How to: Aanmeldingsfouten oplossen met Azure Active Directory-rapporten

Met [het aanmeldingsrapport](concept-sign-ins.md) in Azure Active Directory (Azure AD) u antwoorden vinden op vragen over het beheren van toegang tot de toepassingen in uw organisatie, waaronder:

- Wat is het aanmeldingspatroon van een gebruiker?
- Hoeveel keer hebben gebruikers zich aangemeld gedurende een week?
- Wat is de status van deze aanmeldingen?


Bovendien kan het aanmeldingsrapport u ook helpen bij het oplossen van aanmeldingsfouten voor gebruikers in uw organisatie. In deze handleiding leert u hoe u een aanmeldingsfout in het aanmeldingsrapport isoleren en deze gebruiken om de hoofdoorzaak van de fout te begrijpen.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende zaken nodig:

* Een Azure AD-tenant met een premiumlicentie (P1/P2). Zie [Aan de slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw versie van Azure Active Directory te upgraden.
* Een gebruiker die zich in de **globale beheerder,** **beveiligingsbeheerder,** **beveiligingslezer**of **rapportlezerrol** voor de tenant bevindt. Bovendien kan elke gebruiker toegang krijgen tot eigen aanmeldingen. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Aanmeldingsfouten oplossen met behulp van het aanmeldingsrapport

1. Ga naar de [Azure-portal](https://portal.azure.com) en selecteer uw map.
2. Selecteer **Azure Active Directory** en selecteer in de sectie **Controle** de optie **Aanmeldingen**. 
3. Gebruik de meegeleverde filters om de fout te beperken, met de gebruikersnaam of object-id, de naam van de toepassing of de datum. Selecteer bovendien **Fout in** de vervolgkeuzelijst **Status** om alleen de mislukte aanmeldingen weer te geven. 

    ![Resultaten filteren](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identificeer de mislukte aanmelding die u wilt onderzoeken. Selecteer deze optie om het venster met extra details te openen met meer informatie over de mislukte aanmelding. Noteer de **foutcode aanmelding** en **reden tot fout .** 

    ![Record selecteren](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. U deze informatie ook vinden op het tabblad **Probleemoplossing en ondersteuning** in het detailvenster.

    ![Probleemoplossing en ondersteuning](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. De reden voor de fout beschrijft de fout. In het bovenstaande scenario is de foutreden bijvoorbeeld **ongeldige gebruikersnaam of wachtwoord of ongeldige on-premises gebruikersnaam of wachtwoord.** De oplossing is om gewoon weer inloggen met de juiste gebruikersnaam en wachtwoord.

7. U aanvullende informatie krijgen, waaronder ideeën voor herstel, door te zoeken naar de foutcode, **50126** in dit voorbeeld, in de verwijzing naar de foutcodes van [aanmeldingen.](reference-sign-ins-error-codes.md) 

8. Als al het andere faalt of als het probleem blijft bestaan ondanks het nemen van de aanbevolen actie, [opent u een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) volgens de stappen op het tabblad **Probleemoplossing en ondersteuning.** 

## <a name="next-steps"></a>Volgende stappen

* [Verwijzing naar foutcodes van aanmelding](reference-sign-ins-error-codes.md)
* [Overzicht van aanmeldingsrapport](concept-sign-ins.md)
