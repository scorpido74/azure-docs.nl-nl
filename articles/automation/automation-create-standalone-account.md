---
title: Een zelfstandig Azure Automation-account maken
description: Dit artikel begeleidt u stapsgewijs door de stappen voor het maken, testen en gebruiken van een voor beeld van een beveiligings-principal-verificatie in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: 22efb5e94049b975780c6f6ea69aa94a71cc9992
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373166"
---
# <a name="create-a-standalone-azure-automation-account"></a>Een zelfstandig Azure Automation-account maken

In dit artikel wordt beschreven hoe u een Azure Automation-account maakt in de Azure Portal. U kunt het portal Automation-account gebruiken om te evalueren en meer te weten te komen over Automation zonder extra beheer oplossingen of integratie met Azure Monitor-logboeken te gebruiken. U kunt deze beheer oplossingen toevoegen of de integratie met Azure Monitor logboeken voor geavanceerde bewaking van runbook-taken op elk gewenst moment in de toekomst.

Met een Automation-account kunt u runbooks verifiëren door resources te beheren in een Azure Resource Manager of het klassieke implementatie model. Met één Automation-account kunt u resources in alle regio's en abonnementen voor een bepaalde tenant beheren.

Wanneer u in de Azure Portal een Automation-account maakt, worden deze accounts automatisch gemaakt:

* **Run as-account**. Dit account voert de volgende taken uit:
  * Hiermee maakt u een Service-Principal in Azure Active Directory (Azure AD).
  * Hiermee maakt u een certificaat.
  * Hiermee wijst u het Access Control (RBAC) op basis van rollen toe, waarmee Azure Resource Manager bronnen met behulp van runbooks worden beheerd.

Met deze accounts die u voor u hebt gemaakt, kunt u snel runbooks bouwen en implementeren ter ondersteuning van uw automatiserings behoeften.

## <a name="permissions-required-to-create-an-automation-account"></a>Benodigde machtigingen voor het maken van een Automation-account

Als u een Automation-account wilt maken of bijwerken en de in dit artikel beschreven taken wilt uitvoeren, moet u over de volgende bevoegdheden en machtigingen beschikken:

* Als u een Automation-account wilt maken, moet uw Azure AD-gebruikers account worden toegevoegd aan een rol met machtigingen die equivalent zijn aan de rol van eigenaar voor **micro soft. Automation** -resources. Zie voor meer informatie [Access Control op basis van rollen in azure Automation](automation-role-based-access-control.md).
* Als **app-registraties** is ingesteld op **ja**in de Azure Portal, kunt u, onder **Azure Active Directory** >  > **gebruikers instellingen** **beheren** , gebruikers die geen beheerder zijn in uw Azure AD-Tenant [Active Directory toepassingen registreren](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Als **app-registraties** is ingesteld op **Nee**, moet de gebruiker die deze actie uitvoert een globale beheerder zijn in azure AD.

Als u geen lid bent van het Active Directory exemplaar van het abonnement voordat u wordt toegevoegd aan de rol van de globale beheerder/cobeheerder van het abonnement, wordt u als gast toegevoegd aan Active Directory. In dit scenario ziet u dit bericht op de pagina **Automation-account toevoegen** : "u hebt geen machtiging om te maken."

Als een gebruiker eerst wordt toegevoegd aan de rol van globale beheerder/cobeheerdersrol, kunt u deze verwijderen uit het Active Directory exemplaar van het abonnement en deze vervolgens lezen voor de volledige gebruikersrol in Active Directory.

Gebruikers rollen controleren:

1. Ga in het Azure Portal naar het deel venster **Azure Active Directory** .
1. Selecteer **Gebruikers en groepen**.
1. Selecteer **alle gebruikers**.
1. Nadat u een specifieke gebruiker hebt geselecteerd, selecteert u **profiel**. De waarde van het kenmerk **gebruikers type** onder het profiel van de gebruiker mag niet **gast**zijn.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Een nieuw Automation-account maken in de Azure Portal

Voer de volgende stappen uit om een Azure Automation-account te maken in de Azure Portal:

1. Meld u aan bij de Azure Portal met een account dat lid is van de rol abonnements beheerders en een mede beheerder van het abonnement.
1. Selecteer **+ een resource maken**.
1. Zoek naar **Automation**. Selecteer in de zoek resultaten **Automation**.

   ![& Besturings element voor automatisering zoeken en selecteren in azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Selecteer in het volgende scherm **maken**.

   ![Automation-account toevoegen](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Als het volgende bericht wordt weer gegeven in het deel venster **Automation-account toevoegen** , is uw account geen lid van de rol abonnements beheerders en een mede beheerder van het abonnement.
   >
   > ![Waarschuwing voor Automation-account toevoegen](media/automation-create-standalone-account/create-account-without-perms.png)

1. Voer in het deel venster **Automation-account toevoegen** in het vak **naam** een naam in voor uw nieuwe Automation-account. Deze naam kan niet worden gewijzigd nadat deze is gekozen. *De namen van Automation-accounts zijn uniek per regio en resource groep. Namen voor Automation-accounts die zijn verwijderd, zijn mogelijk niet onmiddellijk beschikbaar.*
1. Als u meer dan één abonnement hebt, geeft u in het vak **abonnement** het abonnement op dat u wilt gebruiken voor het nieuwe account.
1. Voor **resource groep**, voert u een nieuwe of bestaande resource groep in of selecteert u deze.
1. Selecteer bij **locatie**een locatie van een Azure-Data Center.
1. Zorg ervoor dat **Ja** is geselecteerd voor de optie **Azure uitvoeren als-account** en selecteer vervolgens **maken**.

   > [!NOTE]
   > Als u ervoor kiest om het uitvoeren als-account niet te maken door **Nee** te selecteren voor het maken van een **uitvoeren als-account voor Azure**, wordt er een bericht weer gegeven in het deel venster **Automation-account toevoegen** . Hoewel het account wordt gemaakt in de Azure Portal, heeft het account geen overeenkomstige verificatie-id in het klassieke implementatie model abonnement of in de Directory service van het Azure Resource Manager abonnement. Daarom heeft het Automation-account geen toegang tot resources in uw abonnement. Hiermee wordt voor komen dat runbooks die verwijzen naar dit account, in staat zijn om taken te verifiëren en uit te voeren op resources in die implementatie modellen.
   >
   > ![Waarschuwing voor Automation-account toevoegen](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Wanneer de Service-Principal niet is gemaakt, wordt de rol Inzender niet toegewezen.
   >

1. Als u de voortgang van het maken van het Automation-account wilt bijhouden, selecteert u **meldingen**in het menu.

### <a name="resources-included"></a>Beschikbare resources

Wanneer het Automation-account is gemaakt, worden er automatisch verschillende resources voor u gemaakt. Na het maken kunnen deze runbooks veilig worden verwijderd als u deze niet wilt blijven gebruiken. De uitvoeren als-accounts kunnen worden gebruikt om te verifiëren bij uw account in een runbook, en moet blijven staan, tenzij u een andere maakt of niet nodig hebt. In de volgende tabel vindt u een overzicht van de bronnen voor het Uitvoeren als-account.

| Resource | Beschrijving |
| --- | --- |
| AzureAutomationTutorial Runbook |Een voor beeld van een grafisch runbook dat laat zien hoe u kunt verifiëren met behulp van het uitvoeren als-account. Het runbook haalt alle Resource Manager-resources op. |
| AzureAutomationTutorialScript Runbook |Een voor beeld van een Power shell-runbook dat laat zien hoe u kunt verifiëren met behulp van het uitvoeren als-account. Het runbook haalt alle Resource Manager-resources op. |
| AzureAutomationTutorialPython2-runbook |Een voor beeld van een python-runbook dat laat zien hoe u kunt verifiëren met behulp van het uitvoeren als-account. Het runbook bevat een lijst met alle resource groepen die aanwezig zijn in het abonnement. |
| AzureRunAsCertificate |Een certificaat Asset dat automatisch wordt gemaakt wanneer het Automation-account wordt gemaakt, of met behulp van een Power shell-script voor een bestaand account. Het certificaat wordt geverifieerd met Azure, zodat u Azure Resource Manager resources kunt beheren vanuit runbooks. Dit certificaat is één jaar geldig. |
| AzureRunAsConnection |Een verbindings element dat automatisch wordt gemaakt bij het maken van het Automation-account of met behulp van een Power shell-script voor een bestaand account. |

## <a name="classic-run-as-accounts"></a>Klassieke uitvoeren als-accounts

Klassieke uitvoeren als-accounts worden standaard niet meer gemaakt wanneer u een Azure Automation-account maakt. Als u nog steeds een klassiek uitvoeren als-account nodig hebt, moet u de volgende stappen uitvoeren.

1. Selecteer op de pagina **Automation-account** de optie **uitvoeren als-accounts** onder **account instellingen**.
2. Selecteer een **klassiek uitvoeren als-account voor Azure**.
3. Klik op **maken** om door te gaan met het maken van een klassiek uitvoeren als-account.

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over grafisch ontwerpen [grafisch ontwerpen in azure Automation](automation-graphical-authoring-intro.md).
* Zie [Mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md) om aan de slag te gaan met PowerShell-runbooks.
* Zie [Mijn eerste PowerShell Workflow-runbook](automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks
* Zie [Mijn eerste Python2-runbook](automation-first-runbook-textual-python2.md) om aan de slag te gaan met Python2-runbooks.

