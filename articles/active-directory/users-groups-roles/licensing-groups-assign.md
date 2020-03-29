---
title: Licenties toewijzen aan een groep - Azure Active Directory | Microsoft Documenten
description: Licenties toewijzen aan gebruikers door middel van Azure Active Directory-groepslicenties
services: active-directory
keywords: Azure AD-licenties
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 130ce05e332f4705feb4acd54cbeb25d25a82532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253063"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Licenties toewijzen aan gebruikers per groepslidmaatschap in Azure Active Directory

In dit artikel u productlicenties toewijzen aan een groep gebruikers en controleren of ze een correcte licentie hebben in Azure Active Directory (Azure AD).

In dit voorbeeld bevat de tenant een beveiligingsgroep genaamd **HR-afdeling**. Deze groep omvat alle leden van de afdeling personeelszaken (ongeveer 1.000 gebruikers). U wilt Office 365 Enterprise E3-licenties toewijzen aan de hele afdeling. De Yammer Enterprise-service die in het product is opgenomen, moet tijdelijk worden uitgeschakeld totdat de afdeling klaar is om deze te gaan gebruiken. U wilt ook Enterprise Mobility + Security-licenties implementeren in dezelfde groep gebruikers.

> [!NOTE]
> Sommige services van Microsoft zijn niet op alle locaties beschikbaar. Voordat een licentie aan een gebruiker kan worden toegewezen, moet de beheerder de eigenschap Gebruikslocatie op de gebruiker opgeven.
>
> Voor groepslicentietoewijzing erven gebruikers zonder opgegeven gebruikslocatie de locatie van de map. Als u gebruikers op meerdere locaties hebt, raden we u aan altijd de gebruikslocatie in te stellen als onderdeel van uw gebruikersstroom in Azure AD (bijvoorbeeld via AAD Connect-configuratie) - dat ervoor zorgt dat het resultaat van licentietoewijzing altijd correct is en gebruikers niet ontvangen diensten op locaties die niet zijn toegestaan.

## <a name="step-1-assign-the-required-licenses"></a>Stap 1: De vereiste licenties toewijzen

1. Meld u aan bij het [**Azure AD-beheercentrum**](https://aad.portal.azure.com) met een licentiebeheerdersaccount. Om licenties te beheren, moet het account een licentiebeheerder, gebruikersbeheerder of globale beheerder zijn.

1. Selecteer **Licenties** om een pagina te openen waar u alle licentieproducten in de tenant zien en beheren.

1. Selecteer **onder Alle producten**zowel Office 365 Enterprise E5 als Enterprise Mobility + Security E3 door de productnamen te selecteren. Als u de toewijzing wilt starten, selecteert **u Toewijzen** boven aan de pagina.

   ![Producten selecteren om licenties toe te wijzen](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. Selecteer **op** de pagina Licentie toewijzen de optie **Gebruikers en groepen** om een lijst met gebruikers en groepen te openen.

1. Selecteer een gebruiker of groep en gebruik de knop **Selecteren** onder aan de pagina om uw selectie te bevestigen.

1. Klik **op** de pagina Licentie toewijzen op **Toewijzingsopties,** waarin alle serviceplannen worden weergegeven die zijn opgenomen in de twee producten die we eerder hebben geselecteerd. Zoek **Yammer Enterprise** en schakel deze **uit** om die service uit te schakelen van de productlicentie. Bevestig dit door onder aan **licentieopties**op **OK** te klikken .

   ![serviceplannen voor licenties selecteren](./media/licensing-groups-assign/assignment-options.png)
  
1. Als u de toewijzing wilt voltooien, klikt u op de **pagina Licentie toewijzen** onder aan de pagina **toewijzen.**

1. Een melding wordt weergegeven in de rechterbovenhoek die de status en de uitkomst van het proces weergeeft. Als de toewijzing aan de groep niet kan worden voltooid (bijvoorbeeld vanwege reeds bestaande licenties in de groep), klikt u op de melding om details van de fout weer te geven.

Wanneer Azure AD licenties aan een groep toewijst, verwerkt het alle bestaande leden van die groep. Dit proces kan enige tijd duren, variërend met de grootte van de groep. In de volgende stap wordt beschreven hoe u controleren of het proces is voltooid en u bepalen of er meer aandacht nodig is om problemen op te lossen.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Stap 2: Controleren of de eerste toewijzing is voltooid

1. Ga naar Azure Active > **Directory-groepen**. **Azure Active Directory** Selecteer de groep waaraan licenties zijn toegewezen.

1. Selecteer op de groepspagina **Licenties**. Hiermee u snel bevestigen of licenties volledig zijn toegewezen aan gebruikers en of er fouten zijn die u moet onderzoeken. De volgende informatie is beschikbaar:

   - Servicelicenties die momenteel aan de groep zijn toegewezen. Selecteer een item om de specifieke services weer te geven die zijn ingeschakeld en om wijzigingen aan te brengen.

   - Statusupdates van de laatste licentiewijzigingen, die beschikbaar zijn als de wijzigingen worden verwerkt of als de verwerking is voltooid voor alle gebruikersleden.

   - Informatie over gebruikerslicentietoewijzingen die in een foutstatus staan.

   ![licentiefouten en licentiestatus](./media/licensing-groups-assign/assignment-errors.png)

1. Zie meer gedetailleerde informatie over licentieverwerking onder **Azure Active Directory-gebruikers** > **en groepeert** > *groepsnaam* > **controlelogboeken**. Controleer de volgende activiteiten:

   - Activiteit: `Start applying group based license to users`. Dit wordt geregistreerd wanneer het systeem de wijziging van de licentietoewijzing op de groep oppikt en deze begint toe te passen op alle gebruikersleden. Het bevat informatie over de aangebrachte wijziging.

   - Activiteit: `Finish applying group based license to users`. Dit wordt geregistreerd wanneer het systeem klaar is met het verwerken van alle gebruikers in de groep. Het bevat een overzicht van hoeveel gebruikers zijn verwerkt en hoeveel gebruikers geen groepslicenties konden krijgen toegewezen.

   [Lees deze sectie](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) voor meer informatie over hoe auditlogs kunnen worden gebruikt om wijzigingen te analyseren die zijn aangebracht op groepsgebaseerde licenties.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Stap 3: Controleer op licentieproblemen en los deze op

1. Ga naar Azure Active > **Directory-groepen**en zoek de groep waaraan licenties zijn toegewezen. **Azure Active Directory**
1. Selecteer op de groepspagina **Licenties**. De melding boven aan de pagina laat zien dat er 10 gebruikers zijn waaraan geen licenties kunnen worden toegewezen. Open deze om een lijst te zien met alle gebruikers in een licentiefoutstatus voor deze groep.
1. In de kolom **Mislukte toewijzingen** wordt ons gezegd dat beide productlicenties niet aan de gebruikers kunnen worden toegewezen. De **kolom Topreden voor het mislukken** bevat de oorzaak van de fout. In dit geval zijn het **conflicterende serviceplannen.**

   ![licenties die niet konden worden toegewezen](./media/licensing-groups-assign/failed-assignments.png)

1. Selecteer een gebruiker die de pagina Licenties van de gebruiker **wilt openen.** Op deze pagina worden alle licenties weergegeven die momenteel aan de gebruiker zijn toegewezen. In dit voorbeeld heeft de gebruiker de Office 365 Enterprise E1-licentie die is overgenomen van de groep **Kiosk-gebruikers.** Dit is in strijd met de E3-licentie die het systeem probeerde toe te passen van de **HR-afdelingsgroep.** Als gevolg hiervan is geen van de licenties van die groep toegewezen aan de gebruiker.

   ![Alle licentieconflicten voor een gebruiker weergeven](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. Als u dit conflict wilt oplossen, verwijdert u de gebruiker uit de groep **Kiosk-gebruikers.** Nadat Azure AD de wijziging heeft verwerkt, worden de **HR-afdelingslicenties** correct toegewezen.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over de functieset voor licentietoewijzing met groepen:

- [Wat is groepsgebaseerde licenties in Azure Active Directory?](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [Licentieproblemen voor een groep vaststellen en oplossen in Azure Active Directory](licensing-groups-resolve-problems.md)
- [Gebruikers met een afzonderlijke licentie migreren naar licenties op basis van groepen in Azure Active Directory](licensing-groups-migrate-users.md)
- [Gebruikers migreren tussen productlicenties met groepslicenties in Azure Active Directory](licensing-groups-change-licenses.md)
- [Aanvullende scenario’s voor Azure Active Directory-licenties op basis van groepen](../active-directory-licensing-group-advanced.md)
- [PowerShell-voorbeelden voor groepslicenties in Azure Active Directory](licensing-ps-examples.md)
