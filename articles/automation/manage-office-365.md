---
title: Office 365-services beheren met Azure Automation
description: Vertelt hoe u Azure Automation gebruiken om Office 365-abonnementsservices te beheren.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550420"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Office 365-services beheren met Azure Automation

U Azure Automation gebruiken voor het beheer van Office 365-abonnementsservices voor producten zoals Microsoft Word en Microsoft Outlook. Interacties met Office 365 worden ingeschakeld door [Azure Active Directory (Azure AD).](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) Zie [Azure AD gebruiken in Azure Automation om te verifiëren aan Azure](automation-use-azure-ad.md).

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Office 365-abonnementsservices te beheren in Azure Automation.

* Een Azure-abonnement. Zie [handleiding voor abonnementsbeslissing](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Een object Automation in Azure om de gebruikersaccountreferenties en runbooks vast te houden. Zie [Een inleiding tot Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro).
* Azure AD. Zie [Azure AD gebruiken in Azure Automation om te verifiëren aan Azure](automation-use-azure-ad.md).
* Een Office 365-tenant met een account. Zie [Uw Office 365-tenant instellen](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="installing-the-msonline-and-msonlineext-modules"></a>Het installeren van de MSOnline en MSOnlineExt modules

Voor het gebruik van Office 365 binnen Azure Automation`MSOnline` is Microsoft Azure Active Directory voor Windows PowerShell (module) vereist. U hebt ook de [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)module nodig , die azure AD-beheer in single- en multi-tenantomgevingen vereenvoudigt. Installeer de modules zoals beschreven in [Azure AD gebruiken in Azure Automation om te verifiëren in Azure](automation-use-azure-ad.md).

>[!NOTE]
>Als u MSOnline PowerShell wilt gebruiken, moet u lid zijn van Azure AD. Gastgebruikers kunnen de module niet gebruiken.

## <a name="creating-an-azure-automation-account"></a>Een Azure Automation-account maken

Als u de stappen in dit artikel wilt uitvoeren, hebt u een account nodig in Azure Automation. Zie [Een Azure Automation-account maken](automation-quickstart-create-account.md).
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>MsOnline en MSOnlineExt toevoegen als activa

Voeg nu de geïnstalleerde MSOnline- en MSOnlineExt-modules toe om office 365-functionaliteit in te schakelen. Raadpleeg [Modules beheren in Azure Automation.](shared-resources/modules.md)

1. Selecteer **automatiseringsaccounts**in de Azure-portal .
2. Kies uw Automatiseringsaccount.
3. Selecteer **modulesgalerie** onder **Gedeelde bronnen**.
4. Zoek naar MSOnline.
5. Selecteer `MSOnline` de PowerShell-module en klik op **Importeren** om de module als een element te importeren.
6. Herhaal stap 4 en 5 `MSOnlineExt` om de module te zoeken en te importeren. 

## <a name="creating-a-credential-asset-optional"></a>Een referentieactief maken (optioneel)

Het is optioneel om een referentie-item te maken voor de office 365-beheergebruiker die machtigingen heeft om uw script uit te voeren. Het kan echter helpen om te voorkomen dat gebruikersnamen en wachtwoorden in PowerShell-scripts worden blootgesteld. Zie Een [referentieactief maken](automation-use-azure-ad.md#creating-a-credential-asset)voor instructies voor instructies.

## <a name="creating-an-office-365-service-account"></a>Een Office 365-serviceaccount maken

Als u abonnementsservices voor Office 365 wilt uitvoeren, hebt u een Office 365-serviceaccount met machtigingen nodig om te doen wat u wilt. U één globaal beheerdersaccount, één account per service of één functie of script gebruiken om uit te voeren. In ieder geval vereist het serviceaccount een complex en veilig wachtwoord. Zie [Office 365 voor Bedrijven instellen](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Verbinding maken met de Azure AD Online-service

>[!NOTE]
>Als u de CMDlets van de MSOnline-module wilt gebruiken, moet u ze uitvoeren vanaf Windows PowerShell. PowerShell Core ondersteunt deze cmdlets niet.

U de MSOnline-module gebruiken om verbinding te maken met Azure AD vanuit het Office 365-abonnement. De verbinding maakt gebruik van een Office 365-gebruikersnaam en -wachtwoord of maakt gebruik van multi-factor authenticatie (MFA). U verbinding maken via de Azure-portal of een Windows PowerShell-opdrachtprompt (hoeft niet te worden verhoogd).

Hieronder ziet u een PowerShell-voorbeeld. De [cmdlet Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) vraagt om referenties en `Msolcred` slaat deze op in de variabele. Vervolgens gebruikt de [connect-MsolService-cmdlet](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) de referenties om verbinding te maken met de onlineservice van de Azure-map. Als u verbinding wilt maken met een `AzureEnvironment` specifieke Azure-omgeving, gebruikt u de parameter.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Als u geen fouten ontvangt, hebt u verbinding gemaakt. Een snelle test is bijvoorbeeld `Get-MsolUser`het uitvoeren van een Office 365-cmdlet en de resultaten. Als u fouten ontvangt, moet u er rekening mee houden dat een veelvoorkomend probleem een onjuist wachtwoord is.

>[!NOTE]
>U de AzureRM-module of de Az-module ook gebruiken om verbinding te maken met Azure AD vanuit het Office 365-abonnement. De cmdlet voor de hoofdverbinding is [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Deze cmdlet `AzureEnvironmentName` ondersteunt de parameter voor specifieke Office 365-omgevingen.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>Een PowerShell-runbook maken vanuit een bestaand script

U hebt toegang tot office 365-functionaliteit vanuit een PowerShell-script. Hier is een voorbeeld van een `Office-Credentials` script voor `admin@TenantOne.com`een referentie met de naam van . Hiermee `Get-AutomationPSCredential` wordt de Office 365-referentie geïmporteerd.

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

U uw script gebruiken in een Azure Automation-runbook. Voor verschillende doeleinden gebruiken we het powershell-runbook-type.

1. Maak een nieuwe PowerShell-runbook. Raadpleeg [een Azure Automation-runbook maken](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. Selecteer in uw automatiseringsaccount **Runbooks** onder **Procesautomatisering**.
3. Selecteer het nieuwe runbook en klik op **Bewerken**.
4. Kopieer uw script en plak het in de tekstuele editor voor het runbook.
5. Selecteer **ASSETS**en vouw **referenties** uit en controleer of de Office 365-referentie aanwezig is.
6. Klik op **Opslaan**.
7. Selecteer **Het deelvenster Testen**en klik vervolgens op **Start** om te beginnen met het testen van uw runbook. Zie [Runbooks beheren in Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. Wanneer het testen is voltooid, verlaat u het deelvenster Testen.

## <a name="publishing-and-scheduling-the-runbook"></a>Het runbook publiceren en plannen

Zie [Runbooks beheren in Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks)als u uw runbook wilt publiceren en vervolgens plannen.

## <a name="next-steps"></a>Volgende stappen

* U vindt informatie over automatiseringsreferenties in [Referentie-elementen in Azure Automation](shared-resources/credentials.md).
* Zie [Modules beheren in Azure Automation](shared-resources/modules.md) om erachter te komen hoe u met automatiseringsmodules werken.
* Zie [Runbooks beheren in Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks)voor een overzicht van runbookbeheer.
* Zie [Een runbook starten in Azure Automation](automation-starting-a-runbook.md)voor meer informatie over de methoden die kunnen worden gebruikt om een runbook te starten in Azure Automation.
* Zie de [PowerShell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over PowerShell, inclusief taalverwijzing en leermodules.