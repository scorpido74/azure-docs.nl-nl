---
title: Aangepaste Azure-rollen maken of bijwerken met de Azure-portal (Preview) - Azure RBAC
description: Meer informatie over het maken van aangepaste Azure-rollen voor Azure-rolebased access control (Azure RBAC) met behulp van de Azure-portal. Dit omvat het aanbieden, maken, bijwerken en verwijderen van aangepaste rollen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.openlocfilehash: 3204cdf51f3f37588f684f801a811f569b337d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77674868"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal-preview"></a>Aangepaste Azure-rollen maken of bijwerken met de Azure-portal (Voorbeeld)

> [!IMPORTANT]
> Aangepaste Azure-rollen met de Azure-portal staan momenteel in een openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Als de [ingebouwde Azure-rollen](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, u uw eigen aangepaste Azure-rollen maken. Net als ingebouwde rollen u aangepaste rollen toewijzen aan gebruikers, groepen en serviceprincipals bij abonnements- en resourcegroepscopes. Aangepaste rollen worden opgeslagen in een Azure Active Directory (Azure AD)-map en kunnen worden gedeeld tussen abonnementen. Elke map kan maximaal 5000 aangepaste rollen hebben. Aangepaste rollen kunnen worden gemaakt met behulp van de Azure-portal, Azure PowerShell, Azure CLI of de REST-API. In dit artikel wordt beschreven hoe u aangepaste rollen maakt met de Azure-portal (momenteel in preview).

## <a name="prerequisites"></a>Vereisten

Als u aangepaste rollen wilt maken, hebt u het belangrijkste nodig:

- Machtigingen voor het maken van aangepaste rollen, zoals [Eigenaar](built-in-roles.md#owner) of [Administrator voor gebruikerstoegang](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>Stap 1: Bepaal de machtigingen die u nodig hebt

Azure heeft duizenden machtigingen die u mogelijk opnemen in uw aangepaste rol. Hier volgen vier manieren waarop u de machtigingen bepalen die u aan uw aangepaste rol wilt toevoegen:

| Methode | Beschrijving |
| --- | --- |
| Bekijk bestaande rollen | U bestaande rollen bekijken om te zien welke machtigingen worden gebruikt. Zie [Azure ingebouwde rollen](built-in-roles.md)voor meer informatie. |
| Zoeken naar machtigingen op trefwoord | Wanneer u een aangepaste rol maakt met de Azure-portal, u zoeken naar machtigingen op trefwoord. U bijvoorbeeld zoeken naar *virtuele machine-* of *factureringsmachtigingen.* Deze zoekfunctionaliteit wordt later meer beschreven in [stap 4: Machtigingen](#step-4-permissions). |
| Alle machtigingen downloaden | Wanneer u een aangepaste rol maakt met de Azure-portal, u alle machtigingen downloaden als CSV-bestand en vervolgens in dit bestand zoeken. Klik **in** het deelvenster Machtigingen toevoegen op de knop **Alle machtigingen downloaden** om alle machtigingen te downloaden. Zie [Stap 4: Machtigingen](#step-4-permissions)voor meer informatie over het deelvenster Machtigingen toevoegen. |
| De machtigingen in de documenten weergeven | U de beschikbare machtigingen weergeven in de [hulpprogramma's van Azure Resource Manager-resources](resource-provider-operations.md). |

## <a name="step-2-choose-how-to-start"></a>Stap 2: Kiezen hoe te beginnen

Er zijn drie manieren waarop u beginnen met het maken van een aangepaste rol. U een bestaande rol klonen, helemaal opnieuw beginnen of beginnen met een JSON-bestand. De eenvoudigste manier is om een bestaande rol te vinden die de meeste machtigingen heeft die u nodig hebt en vervolgens te klonen en te wijzigen voor uw scenario. 

### <a name="clone-a-role"></a>Een rol klonen

Als een bestaande rol niet helemaal over de machtigingen beschikt die u nodig hebt, u deze klonen en vervolgens de machtigingen wijzigen. Volg deze stappen om een rol te klonen.

1. Open in de Azure-portal een abonnement of brongroep waar u de aangepaste rol wilt toewijzen en open vervolgens **Toegangsbeheer (IAM).**

    In de volgende schermafbeelding wordt de iam-pagina (Access control) weergegeven die is geopend voor een abonnement.

    ![Pagina Toegangsbeheer (IAM) voor een abonnement](./media/custom-roles-portal/access-control-subscription.png)

1. Klik op het tabblad **Rollen** om een lijst met alle ingebouwde en aangepaste rollen weer te geven.

1. Zoek naar een rol die u wilt klonen, zoals de rol Factureringslezer.

1. Klik aan het einde van de rij op de ellips (**...**) en klik vervolgens op **Kloon**.

    ![Contextmenu klonen](./media/custom-roles-portal/clone-menu.png)

    Hiermee wordt de aangepaste roleditor geopend met de optie **Kloon een roloptie** geselecteerd.

1. Ga naar [stap 3: Basics](#step-3-basics).

### <a name="start-from-scratch"></a>De procedure vanaf het begin uitvoeren

Als u dat liever hebt, u deze stappen volgen om een aangepaste rol helemaal opnieuw te starten.

1. Open in de Azure-portal een abonnement of brongroep waar u de aangepaste rol wilt toewijzen en open vervolgens **Toegangsbeheer (IAM).**

1. Klik **op Toevoegen** en klik vervolgens op Aangepaste rol toevoegen **(voorbeeld)**.

    ![Aangepast rollenmenu toevoegen](./media/custom-roles-portal/add-custom-role-menu.png)

    Hiermee wordt de aangepaste roleditor geopend met de optie **Start from scratch** geselecteerd.

1. Ga naar [stap 3: Basics](#step-3-basics).

### <a name="start-from-json"></a>Begin vanaf JSON

Als u dat liever hebt, u de meeste aangepaste rolwaarden opgeven in een JSON-bestand. U het bestand openen in de aangepaste roleditor, aanvullende wijzigingen aanbrengen en vervolgens de aangepaste rol maken. Volg deze stappen om te beginnen met een JSON-bestand.

1. Maak een JSON-bestand met de volgende indeling:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. Geef in het JSON-bestand waarden op voor de verschillende eigenschappen. Hier is een voorbeeld met een aantal waarden toegevoegd. Zie [Roldefinities begrijpen voor](role-definitions.md)informatie over de verschillende eigenschappen .

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. Open de pagina **Toegangsbeheer (IAM)** in de Azure-portal.

1. Klik **op Toevoegen** en klik vervolgens op Aangepaste rol toevoegen **(voorbeeld)**.

    ![Aangepast rollenmenu toevoegen](./media/custom-roles-portal/add-custom-role-menu.png)

    Hiermee wordt de aangepaste roleditor geopend.

1. Selecteer Op het tabblad Basisbeginselen in **basislijnmachtigingen**de optie **Start van JSON**.

1. Klik naast het vak Een bestand selecteren op de mapknop om het dialoogvenster Openen te openen.

1. Selecteer uw JSON-bestand en klik op **Openen**.

1. Ga naar [stap 3: Basics](#step-3-basics).

## <a name="step-3-basics"></a>Stap 3: Basisbeginselen

Op het tabblad **Basisbeginselen** geeft u de naam, beschrijving en basislijnmachtigingen op voor uw aangepaste rol.

1. Geef in het vak **Aangepaste rolnaam** een naam op voor de aangepaste rol. De naam moet uniek zijn voor de Azure AD-map. De naam kan letters, cijfers, spaties en speciale tekens bevatten.

1. Geef **in het** vak Beschrijving een optionele beschrijving op voor de aangepaste rol. Dit wordt de tooltip voor de aangepaste rol.

    De optie **Machtigingen basislijn** moet al zijn ingesteld op basis van de vorige stap, maar u wijzigen.

    ![Tabblad Basisbeginselen met opgegeven waarden](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Stap 4: Machtigingen

Op het tabblad **Machtigingen** geeft u de machtigingen op voor uw aangepaste rol. Afhankelijk van of u een rol hebt gekloond of dat u met JSON bent begonnen, kan op het tabblad Machtigingen al enkele machtigingen worden vermeld.

![Tabblad Machtigingen van aangepaste rol maken](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Machtigingen toevoegen of verwijderen

Volg deze stappen om machtigingen voor uw aangepaste rol toe te voegen of te verwijderen.

1. Als u machtigingen wilt toevoegen, klikt u op **Machtigingen toevoegen** om het deelvenster Machtigingen toevoegen te openen.

    In dit deelvenster worden alle beschikbare machtigingen weergegeven die zijn gegroepeerd in verschillende categorieën in een kaartindeling. Elke categorie vertegenwoordigt een *resourceprovider*, een service die Azure-resources levert.

1. Typ in het vak **Zoeken naar een machtiging** een tekenreeks om naar machtigingen te zoeken. Zoek bijvoorbeeld naar *factuur* om machtigingen met betrekking tot factuur te zoeken.

    Er wordt een lijst met kaartkaarten voor resourceprovider weergegeven op basis van uw zoektekenreeks. Zie [Resourceproviders voor Azure-services voor](../azure-resource-manager/management/azure-services-resource-providers.md)een lijst met hoe resourceproviders worden toegewezen aan Azure-services.

    ![Venster machtigingen toevoegen aan resourceprovider](./media/custom-roles-portal/add-permissions-provider.png)

1. Klik op een bronproviderkaart met mogelijk de machtigingen die u wilt toevoegen aan uw aangepaste rol, zoals **Microsoft Billing**.

    Een lijst met de beheermachtigingen voor die resourceprovider wordt weergegeven op basis van uw zoektekenreeks.

    ![Lijst met machtigingen toevoegen](./media/custom-roles-portal/add-permissions-list.png)

1. Als u op zoek bent naar machtigingen die van toepassing zijn op het gegevensvlak, klikt u op **Gegevensacties**. Anders laat u de ingeschakelde acties inschakelen op **Acties** om machtigingen weer te geven die van toepassing zijn op het beheervlak. Zie [Beheer en gegevensbewerkingen](role-definitions.md#management-and-data-operations)voor meer informatie over de verschillen tussen het beheervlak en het gegevensvlak.

1. Werk indien nodig de zoektekenreeks bij om uw zoekopdracht verder te verfijnen.

1. Zodra u een of meer machtigingen hebt gevonden die u aan uw aangepaste rol wilt toevoegen, voegt u een vinkje toe naast de machtigingen. Voeg bijvoorbeeld een vinkje toe naast **Other: Download Factuur** om de toestemming toe te voegen om facturen te downloaden.

1. Klik **op Toevoegen** om de machtigingslijst toe te voegen.

    De toestemming wordt `Actions` toegevoegd `DataActions`als een of een .

    ![Machtiging toegevoegd](./media/custom-roles-portal/permissions-list-add.png)

1. Als u machtigingen wilt verwijderen, klikt u op het pictogram verwijderen aan het einde van de rij. In dit voorbeeld, omdat een gebruiker niet de mogelijkheid `Microsoft.Support/*` nodig heeft om ondersteuningstickets te maken, kan de toestemming worden verwijderd.

### <a name="add-wildcard-permissions"></a>Wildcardmachtigingen toevoegen

Afhankelijk van hoe u ervoor hebt gekozen om te\*beginnen, hebt u mogelijk machtigingen met wildcards ( ) in uw lijst met machtigingen. Een wildcard\*( ) breidt een machtiging uit naar alles wat overeenkomt met de tekenreeks die u opgeeft. Stel dat u alle machtigingen met betrekking tot Azure Cost Management en export wilt toevoegen. U al deze machtigingen toevoegen:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

In plaats van al deze machtigingen toe te voegen, u gewoon een wildcard-machtiging toevoegen. De volgende wildcard-machtiging is bijvoorbeeld gelijk aan de vorige vijf machtigingen. Dit omvat ook eventuele toekomstige exportmachtigingen die kunnen worden toegevoegd.

```
Microsoft.CostManagement/exports/*
```

Als u een nieuwe wildcard-machtiging wilt toevoegen, u deze niet toevoegen met het deelvenster **Machtigingen toevoegen.** Als u een wildcard-machtiging wilt toevoegen, moet u deze handmatig toevoegen via het tabblad **JSON.** Zie [stap 6: JSON voor](#step-6-json)meer informatie.

### <a name="exclude-permissions"></a>Machtigingen uitsluiten

Als uw rol een\*wildcard ( ) toestemming heeft en u specifieke machtigingen wilt uitsluiten of aftrekken van die wildcard-machtiging, u deze uitsluiten. Stel dat u de volgende machtiging voor jokertekens hebt:

```
Microsoft.CostManagement/exports/*
```

Als u niet wilt dat een export wordt verwijderd, u de volgende verwijderingsmachtiging uitsluiten:

```
Microsoft.CostManagement/exports/delete
```

Wanneer u een machtiging uitsluit, `NotActions` `NotDataActions`wordt deze toegevoegd als een of . De effectieve beheermachtigingen worden berekend door `Actions` alle machtigingen op te `NotActions`tellen en vervolgens alle . De effectieve gegevensmachtigingen worden berekend door `DataActions` alle gegevens toe te `NotDataActions`voegen en vervolgens alle .

> [!NOTE]
> Het uitsluiten van een toestemming is niet hetzelfde als een weigering. Het uitsluiten van machtigingen is gewoon een handige manier om machtigingen af te trekken van een wildcard-toestemming.

1. Als u een machtiging wilt uitsluiten of aftrekken van een toegestane wildcard-machtiging, klikt u op **Machtigingen uitsluiten** om het deelvenster Machtigingen uitsluiten te openen.

    In dit deelvenster geeft u de beheer- of gegevensmachtigingen op die zijn uitgesloten of afgetrokken.

1. Zodra u een of meer machtigingen hebt gevonden die u wilt uitsluiten, voegt u een vinkje toe naast de machtigingen en klikt u op de knop **Toevoegen.**

    ![Venster Machtigingen uitsluiten - machtiging geselecteerd](./media/custom-roles-portal/exclude-permissions-select.png)

    De toestemming wordt `NotActions` toegevoegd `NotDataActions`als een of .

    ![Machtiging uitgesloten](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Stap 5: Toewijsbare scopes

Op het tabblad **Toewijsbare scopes** geeft u op waar uw aangepaste rol beschikbaar is voor toewijzing, zoals een abonnement of resourcegroep. Afhankelijk van hoe u ervoor hebt gekozen om te beginnen, kan dit tabblad het bereik weergeven waar u de pagina Toegangsbeheer (IAM) hebt geopend. Het instellen van toewijsbare scope op hoofdbereik ("/") wordt niet ondersteund. Voor dit voorbeeld u een beheergroep niet toevoegen als een toewijsbaar bereik.

1. Klik **op Toewijsbare scopes toevoegen** om het deelvenster Toewijsbare scopes toevoegen te openen.

    ![Tabblad Toewijsbare scopes](./media/custom-roles-portal/assignable-scopes.png)

1. Klik op een of meer scopes die u wilt gebruiken, meestal op uw abonnement.

    ![Toewijsbare scopes toevoegen](./media/custom-roles-portal/add-assignable-scopes.png)

1. Klik **op** de knop Toevoegen om het toewijsbare bereik toe te voegen.

## <a name="step-6-json"></a>Stap 6: JSON

Op het tabblad **JSON** ziet u uw aangepaste rol opgemaakt in JSON. Als u wilt, u de JSON direct bewerken. Als u een wildcard\*( ) toestemming wilt toevoegen, moet u dit tabblad gebruiken.

1. Als u de JSON wilt bewerken, klikt u op **Bewerken**.

    ![Tabblad JSON met aangepaste rol](./media/custom-roles-portal/json.png)

1. Breng wijzigingen aan in de JSON.

    Als de JSON niet correct is opgemaakt, ziet u een rode gekartelde lijn en een indicator in de verticale goot.

1. Klik op **Opslaan**als u klaar bent met bewerken .

## <a name="step-7-review--create"></a>Stap 7: Bekijken + maken

Op het tabblad **Controleren + maken** u uw aangepaste rolinstellingen bekijken.

1. Controleer uw aangepaste rolinstellingen.

    ![Tabblad Controleren + maken](./media/custom-roles-portal/review-create.png)

1. Klik **op Maken** om uw aangepaste rol te maken.

    Na enkele ogenblikken verschijnt er een berichtvak waarin wordt aangegeven dat uw aangepaste rol is gemaakt.

    ![Aangepast rollenbericht maken](./media/custom-roles-portal/custom-role-success.png)

    Als er fouten worden gedetecteerd, wordt een bericht weergegeven.

    ![Controleren + fout maken](./media/custom-roles-portal/review-create-error.png)

1. Uw nieuwe aangepaste rol weergeven in de lijst **Rollen.** Als u uw aangepaste rol niet ziet, klikt u op **Vernieuwen**.

     Het kan een paar minuten duren voordat uw aangepaste rol overal wordt weergegeven.

## <a name="list-custom-roles"></a>Aangepaste rollen opvragen

Volg deze stappen om uw aangepaste rollen te bekijken.

1. Open een abonnements- of resourcegroep en open **vervolgens Access-besturingselement (IAM)**.

1. Klik op het tabblad **Rollen** om een lijst met alle ingebouwde en aangepaste rollen weer te geven.

1. Selecteer **In** de lijst Type de optie **CustomRole** om alleen uw aangepaste rollen te bekijken.

    Als u zojuist uw aangepaste rol hebt gemaakt en deze niet in de lijst ziet, klikt u op **Vernieuwen**.

    ![Aangepaste rollijst](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Een aangepaste rol bijwerken

1. Zoals eerder in dit artikel is beschreven, opent u uw lijst met aangepaste rollen.

1. Klik op de ellips (**...**) voor de aangepaste rol die u wilt bijwerken en klik vervolgens op **Bewerken**. Houd er rekening mee dat u ingebouwde rollen niet bijwerken.

    De aangepaste rol wordt geopend in de editor.

    ![Aangepast rollenmenu](./media/custom-roles-portal/edit-menu.png)

1. Gebruik de verschillende tabbladen om de aangepaste rol bij te werken.

1. Zodra u klaar bent met uw wijzigingen, klikt u op Het tabblad **Controleren + maken** om uw wijzigingen te bekijken.

1. Klik **op** de knop Bijwerken om uw aangepaste rol bij te werken.

## <a name="delete-a-custom-role"></a>Een aangepaste rol verwijderen

1. Zoals eerder in dit artikel is beschreven, opent u uw lijst met aangepaste rollen.

1. Verwijder roltoewijzingen die met de aangepaste rol worden gebruikt.

1. Klik op de ellips (**...**) voor de aangepaste rol die u wilt verwijderen en klik vervolgens op **Verwijderen**.

    ![Aangepast rollenmenu](./media/custom-roles-portal/delete-menu.png)

    Het kan enkele minuten duren voordat uw aangepaste rol volledig is verwijderd.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Een aangepaste rol maken met Azure PowerShell](tutorial-custom-role-powershell.md)
- [Aangepaste rollen in Azure](custom-roles.md)
- [Azure Resource Manager-resourceproviderbewerkingen](resource-provider-operations.md)
