---
title: Office 365-services beheren met Azure Automation
description: Hier wordt uitgelegd hoe u Azure Automation kunt gebruiken om Office 365-abonnements services te beheren.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550420"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Office 365-services beheren met Azure Automation

U kunt Azure Automation gebruiken voor het beheer van Office 365-abonnements Services, voor producten zoals micro soft Word en micro soft Outlook. Interacties met Office 365 zijn ingeschakeld door [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Zie [Azure AD gebruiken in azure Automation om te verifiëren bij Azure](automation-use-azure-ad.md).

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig voor het beheren van Office 365-abonnements Services in Azure Automation.

* Een Azure-abonnement. Zie de [hand leiding voor abonnements beslissingen](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Een Automation-object in azure voor de referenties van het gebruikers account en runbooks. Bekijk [een inleiding tot Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro).
* Azure AD. Zie [Azure AD gebruiken in azure Automation om te verifiëren bij Azure](automation-use-azure-ad.md).
* Een Office 365-Tenant met een account. Zie [uw Office 365-Tenant instellen](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="installing-the-msonline-and-msonlineext-modules"></a>De MSOnline-en MSOnlineExt-modules installeren

Het gebruik van Office 365 binnen Azure Automation vereist Microsoft Azure Active Directory voor Windows Power`MSOnline` shell (module). U hebt ook de module [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)nodig, waarmee u Azure AD-beheer in omgevingen met één of meerdere tenants vereenvoudigt. Installeer de modules zoals beschreven in [Azure AD gebruiken in azure Automation om te verifiëren bij Azure](automation-use-azure-ad.md).

>[!NOTE]
>Als u MSOnline Power shell wilt gebruiken, moet u lid zijn van Azure AD. Gast gebruikers kunnen de module niet gebruiken.

## <a name="creating-an-azure-automation-account"></a>Een Azure Automation-account maken

U hebt een account in Azure Automation nodig om de stappen in dit artikel te volt ooien. Zie [een Azure Automation-account maken](automation-quickstart-create-account.md).
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>MSOnline en MSOnlineExt als assets toevoegen

Voeg nu de geïnstalleerde MSOnline-en MSOnlineExt-modules toe om de Office 365-functionaliteit in te scha kelen. Raadpleeg [modules beheren in azure Automation](shared-resources/modules.md).

1. Selecteer in de Azure Portal **Automation-accounts**.
2. Kies uw Automation-account.
3. Selecteer **modules galerie** onder **gedeelde resources**.
4. Zoek naar MSOnline.
5. Selecteer de `MSOnline` Power shell-module en klik op **importeren** om de module als een Asset te importeren.
6. Herhaal stap 4 en 5 om de `MSOnlineExt` module te zoeken en te importeren. 

## <a name="creating-a-credential-asset-optional"></a>Een referentie-activum maken (optioneel)

Het is optioneel om een referentie-element te maken voor de Office 365-gebruiker met beheerders rechten die gemachtigd is om uw script uit te voeren. U kunt er echter voor zorgen dat gebruikers namen en wacht woorden in Power shell-scripts worden weer gegeven. Zie [een referentie-Asset maken](automation-use-azure-ad.md#creating-a-credential-asset)voor instructies.

## <a name="creating-an-office-365-service-account"></a>Een Office 365-service account maken

Als u Office 365-abonnements Services wilt uitvoeren, hebt u een Office 365 service-account nodig met de juiste machtigingen. U kunt één algemeen beheerders account gebruiken, één account per service of een functie of script hebben om uit te voeren. In elk geval is een complex en veilig wacht woord vereist voor het service account. Zie [Office 365 voor bedrijven instellen](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Verbinding maken met de Azure AD online-service

>[!NOTE]
>Als u de MSOnline-module-cmdlets wilt gebruiken, moet u deze uitvoeren vanuit Windows Power shell. Power shell core biedt geen ondersteuning voor deze cmdlets.

U kunt de MSOnline-module gebruiken om vanuit het Office 365-abonnement verbinding te maken met Azure AD. De verbinding maakt gebruik van een Office 365-gebruikers naam en-wacht woord of gebruikt multi-factor Authentication (MFA). U kunt verbinding maken met behulp van de Azure Portal of een Windows Power shell-opdracht prompt (heeft geen verhoogde bevoegdheden nodig).

Hieronder ziet u een voor beeld van Power shell. Met de cmdlet [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) wordt om referenties gevraagd en opgeslagen in `Msolcred` de variabele. Vervolgens maakt de [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) -cmdlet gebruik van de referenties om verbinding te maken met de online service Azure Directory. Als u verbinding wilt maken met een specifieke Azure-omgeving, gebruikt `AzureEnvironment` u de para meter.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Als er geen fouten worden weer gegeven, hebt u verbinding gemaakt. Een snelle test is een Office 365-cmdlet uit te voeren, bijvoorbeeld `Get-MsolUser`, en de resultaten te bekijken. Als u fouten ontvangt, moet u er rekening mee houden dat een veelvoorkomend probleem een onjuist wacht woord is.

>[!NOTE]
>U kunt ook de module AzureRM of de module AZ gebruiken om vanuit het Office 365-abonnement verbinding te maken met Azure AD. De hoofd verbindings-cmdlet is [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Met deze cmdlet wordt `AzureEnvironmentName` de para meter voor specifieke Office 365-omgevingen ondersteund.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>Een Power shell-runbook maken op basis van een bestaand script

U opent de Office 365-functionaliteit vanuit een Power shell-script. Hier volgt een voor beeld van een script voor een referentie `Office-Credentials` met de naam met `admin@TenantOne.com`de gebruikers naam van. De oplossing `Get-AutomationPSCredential` wordt gebruikt voor het importeren van de Office 365-referentie.

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="running-the-script-in-a-runbook"></a>Het script uitvoeren in een runbook

U kunt uw script gebruiken in een Azure Automation runbook. We gebruiken bijvoorbeeld het type Power shell-runbook.

1. Maak een nieuw Power shell-runbook. Raadpleeg [een Azure Automation Runbook maken](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. Selecteer in uw Automation-account **Runbooks** onder **proces automatisering**.
3. Selecteer het nieuwe runbook en klik op **bewerken**.
4. Kopieer uw script en plak het in de tekst editor voor het runbook.
5. Selecteer **assets**, vouw vervolgens **referenties** uit en controleer of de Office 365-referentie daar aanwezig is.
6. Klik op **Opslaan**.
7. Selecteer **test deel venster**en klik vervolgens op **starten** om te beginnen met het testen van uw runbook. Zie [Runbooks beheren in azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. Wanneer het testen is voltooid, sluit u af vanuit het test venster.

## <a name="publishing-and-scheduling-the-runbook"></a>Het runbook publiceren en plannen

Als u uw runbook wilt publiceren en vervolgens wilt plannen, raadpleegt u [Runbooks beheren in azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Volgende stappen

* Informatie over Automation-referentie-assets vindt u in [referentie-assets in azure Automation](shared-resources/credentials.md).
* Zie [modules in azure Automation beheren](shared-resources/modules.md) voor meer informatie over het werken met Automation-modules.
* Zie [Runbooks beheren in azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks)voor een overzicht van runbook-beheer.
* Zie [starten van een runbook in azure Automation](automation-starting-a-runbook.md)voor meer informatie over de methoden die kunnen worden gebruikt om een runbook te starten in azure Automation.
* Zie de [Power shell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.