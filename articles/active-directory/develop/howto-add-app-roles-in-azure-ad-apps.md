---
title: App-rollen toevoegen en ophalen uit een token | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het toevoegen van app-rollen aan een toepassing die is geregistreerd in Azure Active Directory, het toewijzen van gebruikers en groepen aan deze rollen en het ontvangen ervan in de claim ' roles ' in het token.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02fe3c1ebc893dea259abcda3ea1d13ab96d68d5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706010"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Procedure: app-rollen toevoegen in uw toepassing en deze ontvangen in het token

Op rollen gebaseerd toegangs beheer (RBAC) is een populair mechanisme voor het afdwingen van autorisatie in toepassingen. Wanneer u RBAC gebruikt, verleent een beheerder machtigingen aan rollen en niet aan afzonderlijke gebruikers of groepen. De beheerder kan vervolgens rollen toewijzen aan verschillende gebruikers en groepen om te bepalen wie toegang heeft tot de inhoud en functionaliteit.

Met RBAC met toepassings rollen en functie claims kunnen ontwikkel aars veilig autorisaties in hun apps afdwingen met minder inspanning op hun eigen onderdeel.

Een andere benadering is het gebruik van Azure AD-groepen en-groepclaims, zoals wordt weer gegeven in het voor beeld van de [webapp-GroupClaims-DotNet-](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) code op github. Azure AD-groepen en toepassings rollen sluiten elkaar niet uit. ze kunnen samen worden gebruikt om nog nauw keuriger toegangs beheer te bieden.

## <a name="declare-roles-for-an-application"></a>Rollen voor een toepassing declareren

Toepassings rollen worden gedefinieerd in de [Azure Portal](https://portal.azure.com).  Wanneer een gebruiker zich aanmeldt bij de toepassing, verzendt Azure AD een `roles` claim voor elke rol die de gebruiker afzonderlijk heeft verleend aan de gebruiker en aan het groepslid maatschap.  De toewijzing van gebruikers en groepen aan rollen kan worden uitgevoerd via de gebruikers interface van de portal of via een programma met behulp van [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview).

Een app-functie maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies vervolgens de Azure Active Directory Tenant met de app-registratie waaraan u een app-functie wilt toevoegen.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **beheren**de optie **app-registraties**en selecteer vervolgens de toepassing waarvoor u de app-rollen wilt definiëren.
1. **App-rollen selecteren | Preview**en selecteer app- **functie maken**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Het deel venster app-rollen van een app-registratie in de Azure Portal":::
1. Voer in het deel venster **app-rollen maken** de instellingen voor de rol in. In de tabel die volgt op de afbeelding worden de instellingen en de bijbehorende para meters beschreven.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Met de app-rollen van een app-registratie wordt een context deel venster gemaakt in het Azure Portal":::

    | Veld | Beschrijving | Voorbeeld |
    |-------|-------------|---------|
    | **Weergavenaam** | Weergave naam voor de app-rol die wordt weer gegeven in de ervaringen beheerder en app-toewijzing. Deze waarde mag spaties bevatten.  | `Survey Writer` |
    | **Toegestane leden typen** | Hiermee geeft u op of deze app-rol kan worden toegewezen aan gebruikers, toepassingen of beide. | `Users/Groups` |
    | **Waarde** | Hiermee geeft u de waarde van de functie claim op die de toepassing moet verwachten in het token. De waarde moet exact overeenkomen met de teken reeks waarnaar wordt verwezen in de code van de toepassing. De waarde mag geen spaties bevatten. | `Survey.Create` |
    | **Beschrijving** | Een gedetailleerde beschrijving van de app-rol die wordt weer gegeven tijdens de toewijzing van de beheer-app en de toestemming. | `Writers can create surveys.` |
    | **Wilt u deze app-functie inschakelen?** | Hiermee wordt aangegeven of de app-functie is ingeschakeld. Als u een app-functie wilt verwijderen, schakelt u dit selectie vakje uit en past u de wijziging toe voordat u probeert de bewerking verwijderen uit te voeren. | *Ingeschakeld* |

1. Selecteer **Toepassen** om uw wijzigingen op te slaan.

> [!NOTE]
> Het aantal rollen dat u toevoegt, telt de grenzen die zijn gedefinieerd voor een toepassings manifest. Voor informatie over deze limieten, zie de sectie  [manifest limieten](./reference-app-manifest.md#manifest-limits) van [Azure Active Directory app-manifest verwijzing](reference-app-manifest.md).

## <a name="assign-users-and-groups-to-roles"></a>Gebruikers en groepen toewijzen aan rollen

Wanneer u app-rollen in uw toepassing hebt toegevoegd, kunt u gebruikers en groepen toewijzen aan de rollen.

1. Selecteer in **Azure Active Directory** in het Azure Portal **bedrijfs toepassingen** in het navigatie menu aan de linkerkant.
1. Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

     Als uw toepassing niet wordt weer gegeven in de lijst, gebruikt u de filters boven aan de lijst **alle toepassingen** om de lijst te beperken, of schuif omlaag in de lijst om de toepassing te zoeken.

1. Selecteer de toepassing waarin u gebruikers of beveiligings groep aan rollen wilt toewijzen.
1. Selecteer onder **Beheren** de optie **Gebruikers en groepen**.
1. Selecteer **gebruiker toevoegen** om het deel venster **toewijzing toevoegen** te openen.
1. Selecteer de selector **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

     Er wordt een lijst met gebruikers en beveiligings groepen weer gegeven. U kunt zoeken naar een bepaalde gebruiker of groep en meerdere gebruikers en groepen selecteren die in de lijst worden weer gegeven.

1. Zodra u gebruikers en groepen hebt geselecteerd, selecteert u de knop **selecteren** om door te gaan.
1. Selecteer **rol selecteren** in het deel venster **toewijzing toevoegen** . Alle rollen die u voor de toepassing hebt gedefinieerd, worden weer gegeven.
1. Kies een rol en selecteer de knop **selecteren** .
1. Selecteer de knop **toewijzen** om de toewijzing van gebruikers en groepen aan de app te volt ooien.
1. Controleer of de gebruikers en groepen die u hebt toegevoegd, worden weer gegeven in de lijst **gebruikers en groepen** .

## <a name="receive-roles-in-tokens"></a>Rollen ontvangen in tokens

Wanneer de gebruikers die zijn toegewezen aan de verschillende app-rollen zich aanmelden bij de toepassing, krijgen hun tokens hun toegewezen rollen in de `roles` claim.

## <a name="web-api-application-permissions"></a>Web-API-toepassings machtigingen

U kunt app-rollen definiëren die bestemd zijn voor **gebruikers/groepen**, **toepassingen**of **beide**. Wanneer u **toepassingen** of **beide**selecteert, wordt de rol van de app weer gegeven als een toepassings machtiging in de **API-machtigingen**van een client-app.

De toepassings machtiging in een registratie van een client-app selecteren nadat u een rol hebt gedefinieerd die als doel heeft voor **toepassingen** of voor **beide**:

1. In **Azure Active Directory** in het Azure Portal selecteert u **app-registraties**en selecteert u vervolgens de registratie van de client-app.
1. Selecteer onder **Beheren** de optie **API-machtigingen**.
1. Selecteer **een machtiging**  >  **mijn api's**toevoegen.
1. Selecteer de app waaraan u de toepassingsrol hebt toegevoegd en selecteer vervolgens **toepassings machtigingen**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over app-rollen met de volgende bronnen:

- Code voorbeeld: [autorisatie toevoegen met behulp van app-rollen & rol claims aan een ASP.net core web-app](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles) (github)
- Video: [autorisatie in uw toepassingen implementeren met het micro soft Identity platform ](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
- [Azure Active Directory-app-manifest](./reference-app-manifest.md)
- [Azure AD-toegangs tokens](access-tokens.md)
- [Azure AD `id_tokens`](id-tokens.md)
