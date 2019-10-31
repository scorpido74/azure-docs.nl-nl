---
title: Werk ruimten maken voor mijn apps-portals in Azure Active Directory | Microsoft Docs
description: Gebruik mijn apps-werk ruimten om mijn apps-pagina's aan te passen voor een eenvoudiger mijn apps-ervaring voor uw eind gebruikers. Organiseer toepassingen in groepen met afzonderlijke tabbladen.
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
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9efe76f15e2f07495a9d95f69b0c21d2bca4ea8
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180548"
---
# <a name="create-workspaces-on-the-my-apps-preview-portal"></a>Werk ruimten maken in de portal mijn apps (preview-versie)

Uw gebruikers kunnen de portal mijn apps (preview) gebruiken voor het weer geven en starten van de Cloud toepassingen waartoe ze toegang hebben. Standaard worden alle toepassingen die een gebruiker kan openen, op één pagina weer gegeven. Als u een Azure AD Premium P1-of P2-licentie hebt, kunt u deze pagina beter organiseren voor uw gebruikers. Met een werk ruimte kunt u toepassingen die verwant zijn (bijvoorbeeld op basis van de functie, taak of project) groeperen en weer geven op een afzonderlijk tabblad. In een werk ruimte wordt in feite een filter toegepast op de toepassingen waarmee een gebruiker al toegang heeft, zodat de gebruiker alleen de toepassingen ziet in de werk ruimte die aan hen is toegewezen.

> [!NOTE]
> In dit artikel wordt beschreven hoe een beheerder werk ruimten kan inschakelen en maken. Zie voor informatie over het gebruik van de portals en werk ruimten van mijn apps [Access en werk ruimten gebruiken](https://docs.microsoft.com/azure/active-directory/user-help/).

## <a name="enable-my-apps-preview-features"></a>Preview-functies voor mijn apps inschakelen

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als gebruikers beheerder of globale beheerder.

2. Ga naar **Azure Active Directory** instellingen voor > **gebruiker**.

3. Onder preview- **functies voor gebruikers onderdelen**selecteert u de **voor beeld-instellingen voor gebruikers onderdelen beheren**.

4. Onder **gebruikers kunnen preview-functies voor mijn apps gebruiken**. Kies een van de volgende opties:
   * **Geselecteerd** : Hiermee schakelt u Preview-functies in voor een specifieke groep. Gebruik de optie **een groep selecteren** om de groep te selecteren waarvoor u Preview-functies wilt inschakelen.  
   * **Alles** : Hiermee schakelt u Preview-functies in voor alle gebruikers.

   ![Preview-functies voor gebruikers](media/access-panel-workspaces/user-preview-features.png)

> [!NOTE]
> Voor het openen van de portal mijn apps kunnen gebruikers de koppeling `https://myapps.microsoft.com` gebruiken, of de aangepaste koppeling voor uw organisatie, zoals `https://myapps.microsoft.com/contoso.com`. Als gebruikers niet worden omgeleid naar de preview-versie van mijn apps, moeten gebruikers `https://myapplications.microsoft.com` of `https://myapplications.microsoft.com/contoso.com`proberen.

## <a name="create-a-workspace"></a>Een werkruimte maken

Als u een werk ruimte wilt maken, moet u een Azure AD Premium P1 of P2-licentie hebben.

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als beheerder met een Azure AD Premium P1-of P2-licentie.

2. Ga naar **Azure Active Directory** > **bedrijfs toepassingen**.

3. Onder **beheren**selecteert u **werk ruimten (preview-versie)** .

4. Selecteer **nieuwe werk ruimte**. Voer op de pagina **nieuwe werk ruimte** een **naam** in voor de werk ruimte (we raden u aan om ' Workspace ' niet te gebruiken in de naam. Voer vervolgens een **Beschrijving**in.

   ![Een nieuwe werk ruimte maken](media/access-panel-workspaces/new-workspace.png)

5. Selecteer **Controleren + maken**. De eigenschappen van de nieuwe werk ruimte worden weer gegeven.

6. Selecteer het tabblad **toepassingen** . Selecteer onder **toepassingen toevoegen**de optie alle toepassingen die u wilt toevoegen aan de werk ruimte of gebruik het **zoekvak** om toepassingen te zoeken. 

   ![Toepassingen toevoegen aan de werk ruimte](media/access-panel-workspaces/add-applications.png)

7. Selecteer **Toevoegen**. De lijst met geselecteerde toepassingen wordt weer gegeven. U kunt de pijl-omhoog-en-omlaag gebruiken om de volg orde van de toepassingen in de lijst te wijzigen.

   ![Lijst met toepassingen in de werk ruimte](media/access-panel-workspaces/add-applications-list.png)

8. Selecteer het tabblad **gebruikers en groepen** . Selecteer **gebruiker toevoegen**om een gebruiker of groep toe te voegen. 

9. Selecteer op de pagina **leden selecteren** de gebruikers of groepen waaraan u de werk ruimte wilt toewijzen. Of gebruik het **zoekvak** om gebruikers of groepen te zoeken.

   ![Gebruikers en groepen toewijzen aan de werk ruimte](media/access-panel-workspaces/add-users-and-groups.png)

10. Wanneer u klaar bent met het selecteren van gebruikers en groepen, kiest u **selecteren**.

11. Als u de rol van een gebruiker wilt wijzigen van **Lees toegang** tot **eigenaar** of andersom, klikt u op de huidige rol en selecteert u een nieuwe rol.

    ![Rollen toewijzen aan gebruikers en groepen](media/access-panel-workspaces/users-groups-list-role.png)

## <a name="view-audit-logs"></a>Auditlogboeken weergeven

In de controle logboeken worden de bewerkingen voor werk ruimten van mijn apps vastgelegd, inclusief acties voor het maken van eind gebruikers van de werk ruimte. De volgende gebeurtenissen worden gegenereerd vanuit mijn apps:

* Werkruimte maken
* Werk ruimte bewerken
* Werk ruimte verwijderen
* Een toepassing starten (eind gebruiker)
* Self-service toepassing toevoegen (eind gebruiker)
* Self-service toepassing verwijderen (eind gebruiker)

U kunt de controle Logboeken openen in de [Azure Portal](https://portal.azure.com) door **Azure Active Directory** > **bedrijfs toepassingen** > **audit logboeken** te selecteren in de sectie activiteit. Voor de **service**selecteert u **mijn apps**.

   ![Rollen toewijzen aan gebruikers en groepen](media/access-panel-workspaces/audit-log-myapps.png)


## <a name="next-steps"></a>Volgende stappen
[Ervaringen van eind gebruikers voor toepassingen in Azure Active Directory](end-user-experiences.md)