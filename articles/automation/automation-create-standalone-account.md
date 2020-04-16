---
title: Een zelfstandig Azure Automation-account maken
description: In dit artikel vindt u de stappen van het maken, testen en gebruiken van een voorbeeldbeveiligingshoofdverificatie in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: 72a40363edf0e83eea26ee697ce992226da0db4f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392292"
---
# <a name="create-a-standalone-azure-automation-account"></a>Een zelfstandig Azure Automation-account maken

In dit artikel ziet u hoe u een Azure Automation-account maakt in de Azure-portal. U het portalautomation-account gebruiken om automatisering te evalueren en te leren over automatisering zonder aanvullende beheeroplossingen of integratie met Azure Monitor-logboeken te gebruiken. U deze beheeroplossingen toevoegen of integreren met Azure Monitor-logboeken voor geavanceerde bewaking van runbook-taken op elk moment in de toekomst.

Met een Automation-account u runbooks verifiëren door resources te beheren in Azure Resource Manager of het klassieke implementatiemodel. Met één Automation-account kunt u resources in alle regio's en abonnementen voor een bepaalde tenant beheren.

Wanneer u een Automatiseringsaccount maakt in de Azure-portal, wordt het **run as-account** automatisch gemaakt. Met dit account worden de volgende taken uitgevoerd:

* Hiermee maakt u een serviceprincipal in Azure Active Directory (Azure AD).
* Hiermee maakt u een certificaat.
* Wijst het RBAC (Contributor Role-Based Access Control) toe, dat Azure Resource Manager-resources beheert met behulp van runbooks.

Met dit account dat voor u is gemaakt, u snel beginnen met het bouwen en implementeren van runbooks om uw automatiseringsbehoeften te ondersteunen.

## <a name="permissions-required-to-create-an-automation-account"></a>Machtigingen die nodig zijn om een Automatiseringsaccount te maken

Als u een Automatiseringsaccount wilt maken of bijwerken en de in dit artikel beschreven taken wilt voltooien, moet u over de volgende bevoegdheden en machtigingen beschikken:

* Als u een Automatiseringsaccount wilt maken, moet uw Azure AD-gebruikersaccount worden `Microsoft.Automation` toegevoegd aan een rol met machtigingen die gelijkwaardig zijn aan de rol Eigenaar voor resources. Zie [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md)voor meer informatie.
* Als **app-registraties** zijn ingesteld op **Ja,** kunnen niet-beheerders in uw Azure AD-tenant in de Azure-portal onder**gebruikersinstellingen**van **Azure Active Directory** > **MANAGE** > active [directory-toepassingen registreren.](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions) Als **app-registraties** is ingesteld op **Nee,** moet de gebruiker die deze actie uitvoert, een globale beheerder zijn in Azure AD.

Als u geen lid bent van het Active Directory-exemplaar van het abonnement voordat u wordt toegevoegd aan de algemene administrator-/coadministratorrol van het abonnement, wordt u als gast aan Active Directory toegevoegd. In dit scenario ziet u dit bericht in het deelvenster Automatiseringsaccount toevoegen:`You do not have permissions to create.`

Als een gebruiker eerst wordt toegevoegd aan de algemene rol Administrator/Coadministrator, u de gebruiker verwijderen uit de Active Directory-instantie van het abonnement. U de gebruiker voorlezen aan de rol Gebruiker in Active Directory.

Ga als volgt te werk om gebruikersrollen te verifiëren:

1. Ga in de Azure-portal naar het deelvenster Azure Active Directory.
1. Selecteer **Gebruikers en groepen**.
1. Selecteer **Alle gebruikers**selecteren .
1. Nadat u een specifieke gebruiker hebt geselecteerd, selecteert u **Profiel**. De waarde van het kenmerk **Gebruikerstype** onder het profiel van de gebruiker mag niet **Gast**zijn.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Een nieuw automatiseringsaccount maken in de Azure-portal

Voer de volgende stappen uit om een Azure Automation-account in de Azure-portal te maken:

1. Meld u aan bij de Azure-portal met een account dat lid is van de rol abonnementsbeheerders en een medebeheerder van het abonnement.
1. Selecteer **+ Een resource maken**.
1. Zoeken naar **automatisering**. Selecteer **Automatisering**in de zoekresultaten .

   ![Zoeken naar en selecteer &-besturingselement voor automatisering in de Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Selecteer in het volgende scherm **Nieuw maken**.

   ![Automatiseringsaccount toevoegen](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Als u het volgende bericht ziet in het deelvenster Automatiseringsaccount toevoegen, is uw account geen lid van de rol abonnementbeheerders en een medebeheerder van het abonnement.
   >
   > ![Waarschuwing voor automatiseringsaccount toevoegen](media/automation-create-standalone-account/create-account-without-perms.png)

1. Voer in het deelvenster Automatiseringsaccount toevoegen een naam in voor uw nieuwe automatiseringsaccount in het veld **Naam.** Je deze naam niet veranderen nadat deze is gekozen. 

    > [!NOTE]
    > Naam van automatiseringsaccount is uniek per regio en resourcegroep. Namen voor verwijderde automatiseringsaccounts zijn mogelijk niet onmiddellijk beschikbaar.

1. Als u meer dan één abonnement hebt, gebruikt u het veld **Abonnement** om het abonnement op te geven dat voor het nieuwe account moet worden gebruikt.
1. Voer **voor resourcegroep**een nieuwe of bestaande resourcegroep in of selecteer deze.
1. Selecteer **voor Locatie**een Azure-datacenterlocatie.
1. Controleer voor de optie **Azure Run As-account maken** of **Ja** is geselecteerd en klik op **Maken**.

   > [!NOTE]
   > Als u ervoor kiest het run as-account niet te maken door **Nee** te selecteren voor **Azure Run Als-account maken,** wordt er een bericht weergegeven in het deelvenster Automatiseringsaccount toevoegen. Hoewel het account is gemaakt in de Azure-portal, heeft het account geen overeenkomstige verificatie-identiteit in uw abonnement op het klassieke implementatiemodel of in de directoryservice voor Azure Resource Manager-abonnementen. Daarom heeft het automatiseringsaccount geen toegang tot bronnen in uw abonnement. Dit voorkomt dat runbooks die naar dit account verwijzen, taken kunnen verifiëren en uitvoeren ten opzichte van resources in die implementatiemodellen.
   >
   > ![Waarschuwing voor automatiseringsaccount toevoegen](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Wanneer de serviceprincipal niet is gemaakt, wordt de rol Inzender niet toegewezen.
   >

1. Als u de voortgang van het aanmaken van het automatiseringsaccount wilt bijhouden, selecteert u **Meldingen** in het menu.

### <a name="resources-included"></a>Beschikbare resources

Wanneer het Automation-account is gemaakt, worden er automatisch verschillende resources voor u gemaakt. Na het maken kunnen deze runbooks veilig worden verwijderd als u ze niet wilt bewaren. De Run As-accounts kunnen worden gebruikt om te verifiëren voor uw account in een runbook en moet worden overgelaten, tenzij u een andere account maakt of deze niet nodig hebt. In de volgende tabel vindt u een overzicht van de bronnen voor het Uitvoeren als-account.

| Resource | Beschrijving |
| --- | --- |
| AzureAutomationTutorial Runbook |Een voorbeeld van een grafisch runbook dat laat zien hoe u zich verifiëren met het account Uitvoeren als. Het runbook krijgt alle Resource Manager-bronnen. |
| AzureAutomationTutorialScript Runbook |Een voorbeeld van PowerShell-runbook dat laat zien hoe u zich verifiëren met het Run As-account. Het runbook krijgt alle Resource Manager-bronnen. |
| AzureAutomationTutorialPython2-runbook |Een voorbeeld van Python-runbook dat laat zien hoe u zich verifiëren met het Run As-account. In het runbook worden alle resourcegroepen weergegeven die in het abonnement aanwezig zijn. |
| AzureRunAsCertificate |Een certificaatitem dat automatisch wordt gemaakt wanneer het automatiseringsaccount wordt gemaakt of met behulp van een PowerShell-script voor een bestaand account. Het certificaat wordt geverifieerd met Azure, zodat u Azure Resource Manager-resources beheren vanuit runbooks. Dit certificaat is één jaar geldig. |
| AzureRunAsConnection |Een verbindingselement dat automatisch wordt gemaakt wanneer het automatiseringsaccount wordt gemaakt of met behulp van een PowerShell-script voor een bestaand account. |

## <a name="create-a-classic-run-as-account"></a>Een Klassiek run as-account maken

Classic Run As-accounts worden niet langer standaard gemaakt wanneer u een Azure Automation-account maakt. Als je nog steeds een Classic Run As-account nodig hebt:

1. Selecteer in uw Automatiseringsaccount **Uitvoeren als accounts** onder **Accountinstellingen**.
2. Selecteer **Azure Classic Run as Account**.
3. Klik **op Maken** om door te gaan met Het maken van een Klassiek uitvoeren als account.

## <a name="next-steps"></a>Volgende stappen

* Zie [Grafische ontwerpen in Azure Automation](automation-graphical-authoring-intro.md)voor meer informatie over grafisch ontwerpen.
* Zie [Mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md)om aan de slag te gaan met PowerShell-runbooks.
* Zie [Mijn eerste PowerShell-werkstroomrunboek](automation-first-runbook-textual.md)om aan de slag te gaan met PowerShell-werkstroom.
* Zie [Mijn eerste Python2 runbook](automation-first-runbook-textual-python2.md)om aan de slag te gaan met Python 2 runbooks.
* Zie [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)voor een PowerShell-cmdletreferentie.
