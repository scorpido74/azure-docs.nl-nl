---
title: Eigenaren en gebruikers toevoegen in Azure DevTest Labs| Microsoft Documenten
description: Eigenaren en gebruikers toevoegen in Azure DevTest Labs met behulp van de Azure-portal of PowerShell
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: spelluru
ms.openlocfilehash: a9426c20ae23fd3dad4cdba25590ff2eac271896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284276"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Eigenaren en gebruikers toevoegen in Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Toegang in Azure DevTest Labs wordt beheerd door [RBAC (Azure Role-Based Access Control).](../role-based-access-control/overview.md) Met RBAC u taken binnen uw team scheiden in *rollen* waarbij u alleen de hoeveelheid toegang verleent die gebruikers nodig hebben om hun taken uit te voeren. Drie van deze RBAC rollen zijn *Owner*, *DevTest Labs Gebruiker,* en *Contributor*. In dit artikel leert u welke acties kunnen worden uitgevoerd in elk van de drie belangrijkste RBAC-rollen. Van daaruit leert u hoe u gebruikers toevoegt aan een lab - zowel via de portal als via een PowerShell-script, en hoe u gebruikers op abonnementsniveau toevoegen.

## <a name="actions-that-can-be-performed-in-each-role"></a>Acties die in elke rol kunnen worden uitgevoerd
Er zijn drie hoofdrollen die u een gebruiker toewijzen:

* Eigenaar
* DevTest Labs-gebruiker
* Inzender

In de volgende tabel worden de acties geïllustreerd die gebruikers in elk van deze rollen kunnen uitvoeren:

| **Acties die gebruikers in deze rol kunnen uitvoeren** | **DevTest Labs-gebruiker** | **Eigenaar** | **Inzender** |
| --- | --- | --- | --- |
| **Labtaken** | | | |
| Gebruikers toevoegen aan een lab |Nee |Ja |Nee |
| Kosteninstellingen bijwerken |Nee |Ja |Ja |
| **VM-basistaken** | | | |
| Aangepaste afbeeldingen toevoegen en verwijderen |Nee |Ja |Ja |
| Formules toevoegen, bijwerken en verwijderen |Ja |Ja |Ja |
| Azure Marketplace-afbeeldingen op de witte lijst whitelisten |Nee |Ja |Ja |
| **VM-taken** | | | |
| VM's maken |Ja |Ja |Ja |
| VM's starten, stoppen en verwijderen |Alleen VM's gemaakt door de gebruiker |Ja |Ja |
| VM-beleid bijwerken |Nee |Ja |Ja |
| Gegevensschijven toevoegen/verwijderen aan/van VM's |Alleen VM's gemaakt door de gebruiker |Ja |Ja |
| **Artefact-taken** | | | |
| Artefact-opslagplaatsen toevoegen en verwijderen |Nee |Ja |Ja |
| Artefacten toepassen |Ja |Ja |Ja |

> [!NOTE]
> Wanneer een gebruiker een VM maakt, wordt die gebruiker automatisch toegewezen aan de rol **Eigenaar** van de gemaakte VM.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Een eigenaar of gebruiker toevoegen op labniveau
Eigenaren en gebruikers kunnen op labniveau worden toegevoegd via de Azure-portal. Een gebruiker kan een externe gebruiker zijn met een geldig [Microsoft-account (MSA).](devtest-lab-faq.md#what-is-a-microsoft-account)
De volgende stappen begeleiden u bij het proces van het toevoegen van een eigenaar of gebruiker aan een lab in Azure DevTest Labs:

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met laboratoria het gewenste lab.
4. Selecteer **Configuratie en beleid**op het blad van het lab. 
5. Selecteer **op** de pagina Configuratie en beleid de optie **Toegangsbeheer (IAM)** in het menu aan de linkerkant. 
6. Selecteer **Roltoewijzing toevoegen** op de werkbalk om een gebruiker aan een rol toe te voegen.
1. Ga in het venster **Machtigingen toevoegen** de volgende acties uit: 
    1. Selecteer een rol (bijvoorbeeld: DevTest Labs-gebruiker). In de sectie [Acties die in elke rol kunnen worden uitgevoerd,](#actions-that-can-be-performed-in-each-role) worden de verschillende acties weergegeven die gebruikers kunnen uitvoeren in de rollen Eigenaar, DevSterkste gebruiker en Inzender.
    2. Selecteer de gebruiker die aan de rol moet worden toegevoegd. 
    3. Selecteer **Opslaan**. 
11. Wanneer u terugkeert naar **het** gebruikersblad, is de gebruiker toegevoegd.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Een externe gebruiker toevoegen aan een lab met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Naast het toevoegen van gebruikers in de Azure-portal, u een externe gebruiker toevoegen aan uw lab met behulp van een PowerShell-script. Wijzig in het volgende voorbeeld de parameterwaarden onder de opmerking Waarden om de opmerking **te wijzigen.**
U `subscriptionId`de `labResourceGroup`waarden `labName` en waarden ophalen uit het labblad in de Azure-portal.

> [!NOTE]
> Het voorbeeldscript gaat ervan uit dat de opgegeven gebruiker als gast aan de Active Directory is toegevoegd en mislukt als dat niet het geval is. Als u een gebruiker wilt toevoegen die niet in de Active Directory aan een lab is, gebruikt u de Azure-portal om de gebruiker toe te wijzen aan een rol zoals geïllustreerd in de sectie, [Een eigenaar of gebruiker toevoegen op laboratoriumniveau.](#add-an-owner-or-user-at-the-lab-level)   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Connect-AzAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Een eigenaar of gebruiker toevoegen op abonnementsniveau
Azure-machtigingen worden gepropageerd van bovenliggend e-bereik naar onderliggend bereik in Azure. Eigenaren van een Azure-abonnement dat labs bevat, zijn daarom automatisch eigenaar van die labs. Ze zijn ook eigenaar van de VM's en andere bronnen die zijn gemaakt door de gebruikers van het lab en de Azure DevTest Labs-service. 

U extra eigenaren toevoegen aan een lab via het lemmet van het lab in de [Azure-portal.](https://go.microsoft.com/fwlink/p/?LinkID=525040) De omvang van het beheer van de toegevoegde eigenaar is echter beperkter dan het bereik van de eigenaar van het abonnement. De toegevoegde eigenaren hebben bijvoorbeeld geen volledige toegang tot sommige resources die in het abonnement zijn gemaakt door de DevTest Labs-service. 

Voer de volgende stappen uit om een eigenaar toe te voegen aan een Azure-abonnement:

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **Alle services**en selecteer Vervolgens **Abonnementen** in de lijst.
3. Selecteer het gewenste abonnement.
4. Selecteer **Access-pictogram.** 
   
    ![Toegang tot gebruikers](./media/devtest-lab-add-devtest-user/access-users.png)
5. Selecteer op het blad **Gebruikers** de optie **Toevoegen**.
   
    ![Gebruiker toevoegen](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. Selecteer **Eigenaar**in **het rolblad selecteren** .
7. Voer in het **mes Gebruikers toevoegen** het e-mailadres of de naam in van de gebruiker die u als eigenaar wilt toevoegen. Als de gebruiker niet kan worden gevonden, krijgt u een foutmelding waarin het probleem wordt uitgelegd. Als de gebruiker wordt gevonden, wordt die gebruiker vermeld onder het tekstvak **Gebruiker.**
8. Selecteer de aangelokaliseerde gebruikersnaam.
9. Kies **Selecteren**.
10. Selecteer **OK** om het access blade **toevoegen** te sluiten.
11. Wanneer u terugkeert naar **het** gebruikersblad, is de gebruiker als eigenaar toegevoegd. Deze gebruiker is nu eigenaar van alle labs die onder dit abonnement zijn gemaakt en kan dus eigenaartaken uitvoeren. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

