---
title: Verzamelingen maken voor mijn apps-portals in Azure Active Directory | Microsoft Documenten
description: Gebruik Mijn apps-verzamelingen om mijn apps-pagina's aan te passen voor een eenvoudigere Ervaring met Mijn apps voor uw eindgebruikers. Organiseer toepassingen in groepen met afzonderlijke tabbladen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91b9ffc9e3487e492c91cb0f5825d0b725f9410
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120093"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Collecties maken op de portal Mijn apps

Uw gebruikers kunnen de mijn apps-portal gebruiken om de cloudgebaseerde toepassingen waartoe ze toegang hebben te bekijken en te starten. Standaard worden alle toepassingen die een gebruiker kan openen samen op één pagina weergegeven. Als u een Azure AD Premium P1- of P2-licentie hebt, u verzamelingen instellen als u een Azure AD Premium P1- of P2-licentie hebt. Met een verzameling u toepassingen die gerelateerd zijn (bijvoorbeeld op functie, taak of project) groeperen en deze weergeven op een apart tabblad. Een verzameling past in wezen een filter toe op de toepassingen waartoe een gebruiker al toegang heeft, zodat de gebruiker alleen die toepassingen in de verzameling ziet die aan hen zijn toegewezen.

> [!NOTE]
> In dit artikel wordt uitgelegd hoe een beheerder verzamelingen kan in- en mogelijk maken. Zie [Verzamelingen](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)voor openen en gebruiken voor de eindgebruiker over het gebruik van de mijn apps-portal en verzamelingen.

## <a name="enable-the-latest-my-apps-features"></a>De nieuwste functies van Mijn apps inschakelen

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als gebruikersbeheerder of globale beheerder.

2. Ga naar Azure Active > **Directory-gebruikersinstellingen**. **Azure Active Directory**

3. Selecteer **onder Previews van gebruikersfuncties**de **optie Preview-instellingen voor gebruikersfuncties beheren**.

4. Kies **onder Gebruikers preview-functies voor Mijn apps**een van de volgende opties:
   * **Geselecteerd** - Hiermee worden de functies voor een specifieke groep ingeschakeld. Gebruik de optie **Een groep selecteren** om de groep te selecteren waarvoor u de functies wilt inschakelen.  
   * **Alles** - Hiermee kunnen de functies voor alle gebruikers worden ingezien.

> [!NOTE]
> Als u de mijn apps-portal `https://myapps.microsoft.com` wilt openen, kunnen gebruikers de `https://myapps.microsoft.com/contoso.com`koppeling of de aangepaste koppeling voor uw organisatie gebruiken, zoals . Nadat u de nieuwe Ervaring Mijn apps hebt ingeschakeld, wordt de **banner Met bijgewerkte mijn toepassingen boven** aan de pagina Mijn apps weergegeven en kunnen gebruikers **Proberen** selecteren om de nieuwe ervaring weer te geven. Als u de nieuwe ervaring niet meer wilt gebruiken, kunnen gebruikers Boven aan de pagina **Ja** selecteren in de banner **Nieuwe ervaring** verlaten.

## <a name="create-a-collection"></a>Een verzameling maken

Als u een verzameling wilt maken, moet u over een Azure AD Premium P1- of P2-licentie beschikken.

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als beheerder met een Azure AD Premium P1- of P2-licentie.

2. Ga naar **Azure Active Directory** > **Enterprise-toepassingen**.

3. Selecteer **Onder Beheren**de optie **Verzamelingen**.

4. Selecteer **Nieuwe verzameling**. Voer op de pagina **Nieuwe verzameling** een **naam** voor de verzameling in (we raden u aan geen 'verzameling' in de naam te gebruiken. Voer vervolgens een **beschrijving in**.

   ![Nieuwe verzamelingspagina](media/acces-panel-collections/new-collection.png)

5. Selecteer het tabblad **Toepassingen.** Selecteer **+ Toepassing toevoegen**en selecteer vervolgens op de pagina Toepassingen **toevoegen** alle toepassingen die u aan de verzameling wilt toevoegen of gebruik het vak **Zoeken** om toepassingen te zoeken.

   ![Een toepassing toevoegen aan de verzameling](media/acces-panel-collections/add-applications.png)

6. Wanneer u klaar bent met het toevoegen van toepassingen, selecteert u **Toevoegen**. De lijst met geselecteerde toepassingen wordt weergegeven. U de pijl-omhoog gebruiken om de volgorde van toepassingen in de lijst te wijzigen. Als u een toepassing naar beneden wilt verplaatsen of uit de verzameling wilt verwijderen, selecteert u het menu **Meer** (**... ).**

7. Selecteer het tabblad **Eigenaren.** Selecteer **+ Gebruikers en groepen toevoegen**en selecteer vervolgens op de pagina Gebruikers en groepen **toevoegen** de gebruikers of groepen waaraan u eigendom wilt toewijzen. Wanneer u klaar bent met het selecteren van gebruikers en groepen, kiest **u Selecteren**.

9. Selecteer het tabblad **Gebruikers en groepen.** Selecteer **+ Gebruikers en groepen toevoegen**en selecteer vervolgens op de pagina Gebruikers en groepen **toevoegen** de gebruikers of groepen waaraan u de verzameling wilt toewijzen. Of gebruik het vak **Zoeken** om gebruikers of groepen te vinden. Wanneer u klaar bent met het selecteren van gebruikers en groepen, kiest **u Selecteren**.

   ![Gebruikers en groepen toevoegen](media/acces-panel-collections/add-users-and-groups.png)

11. Selecteer **Controleren + maken**. De eigenschappen voor de nieuwe collectie worden weergegeven.


## <a name="view-audit-logs"></a>Auditlogboeken weergeven

De controlelogboeken registreren mijn apps-verzamelingenbewerkingen, inclusief acties voor het maken van eindgebruikers. De volgende gebeurtenissen worden gegenereerd vanuit Mijn apps:

* Verzameling maken
* Verzameling bewerken
* Verzameling verwijderen
* Een toepassing starten (eindgebruiker)
* Selfservice-toepassing toevoegen (eindgebruiker)
* Selfservice-toepassing verwijderen (eindgebruiker)

U controlelogboeken openen in de [Azure-portal](https://portal.azure.com) door **Azure Active Directory** > **Enterprise Applications** > **Audit-logboeken** te selecteren in de sectie Activiteit. Selecteer Mijn **apps**voor **Service**.

## <a name="get-support-for-my-account-pages"></a>Ondersteuning krijgen voor mijn accountpagina's

Op de pagina Mijn apps kan een gebruiker **Mijn account** > **mijn account weergeven** selecteren om de accountinstellingen te openen. Op de pagina Azure AD **Mijn account** kunnen gebruikers hun beveiligingsgegevens, apparaten, wachtwoorden en meer beheren. Ze hebben ook toegang tot hun Office-accountinstellingen.

Als u een ondersteuningsaanvraag moet indienen voor een probleem met de Azure AD-accountpagina of de Pagina Office-account, voert u de volgende stappen uit zodat uw aanvraag correct is gerouteerd: 

* Open een ondersteuningsaanvraag vanuit de Azure-portal voor problemen met de pagina **'Mijn account'** van Azure AD. Ga naar **Azure portal** > **Azure Active Directory** > **Nieuwe ondersteuningsaanvraag**.

* Open een ondersteuningsverzoek vanuit het Microsoft 365-beheercentrum voor problemen met de pagina **'Mijn account'** van Office. Ga naar **Microsoft 365-beheercentrumondersteuning** > **Support**. 

## <a name="next-steps"></a>Volgende stappen
[Ervaringen van eindgebruikers voor toepassingen in Azure Active Directory](end-user-experiences.md)