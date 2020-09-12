---
title: Een Azure Automation uitvoeren als-account beheren
description: In dit artikel leest u hoe u uw uitvoeren als-account kunt beheren met Power shell of via de Azure Portal.
services: automation
ms.subservice: shared-capabilities
ms.date: 06/26/2020
ms.topic: conceptual
ms.openlocfilehash: cb804b21d6f5312c13bfdbf7b0fc0404961ba1e3
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "90005731"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Een Azure Automation uitvoeren als-account beheren

Uitvoeren als-accounts in Azure Automation bieden verificatie voor het beheren van resources in azure met behulp van de Azure-cmdlets. Wanneer u een uitvoeren als-account maakt, wordt er een nieuwe Service-Principal-gebruiker in Azure Active Directory (AD) gemaakt en wordt de rol Inzender toegewezen aan deze gebruiker op het abonnements niveau.

## <a name="types-of-run-as-accounts"></a>Typen run as-accounts

Azure Automation gebruikt twee typen uitvoeren als-accounts:

* Azure Run As-account
* Klassiek uitvoeren als-account van Azure

>[!NOTE]
>Azure Cloud Solution Provider-abonnementen (CSP) ondersteunen alleen het Azure Resource Manager model. Niet-Azure Resource Manager services zijn niet beschikbaar in het programma. Wanneer u een CSP-abonnement gebruikt, wordt het klassieke uitvoeren als-account van Azure niet gemaakt, maar wordt het uitvoeren als-account van Azure gemaakt. Zie [beschik bare Services in CSP-abonnementen](/azure/cloud-solution-provider/overview/azure-csp-available-services)voor meer informatie over CSP-abonnementen.

De service-principal voor een uitvoeren als-account heeft geen machtigingen om Azure AD standaard te lezen. Als u machtigingen wilt toevoegen om Azure AD te lezen of te beheren, moet u de machtigingen voor de Service-Principal verlenen onder **API-machtigingen**. Zie [machtigingen toevoegen voor toegang tot uw web-API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)voor meer informatie.

### <a name="run-as-account"></a>Run As-account

Het run as-account beheert resources van het [Resource Manager-implementatie model](../azure-resource-manager/management/deployment-models.md) . Hiermee worden de volgende taken uitvoeren.

* Er wordt een Azure AD-toepassing met een zelf-ondertekend certificaat gemaakt. Daarnaast wordt er voor de toepassing in Azure AD een service-principalaccount gemaakt en wordt aan dit account de rol Inzender toegewezen in uw huidige abonnement. U kunt de certificaat instelling wijzigen in eigenaar of een andere rol. Zie [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie.

* Hiermee maakt u een Automation-certificaat Asset `AzureRunAsCertificate` met de naam in het opgegeven Automation-account. De certificaat Asset bevat de persoonlijke sleutel van het certificaat dat door de Azure AD-toepassing wordt gebruikt.

* Hiermee wordt een Automation-verbindings element gemaakt `AzureRunAsConnection` met de naam in het opgegeven Automation-account. Het verbindings element bevat de toepassings-ID, Tenant-ID, abonnements-ID en vinger afdruk van het certificaat.

### <a name="azure-classic-run-as-account"></a>Klassieke Azure Uitvoeren als-account

Met het klassieke uitvoeren als-account van Azure worden de resources van het [klassieke implementatie model](../azure-resource-manager/management/deployment-models.md) beheerd. U moet een mede beheerder zijn van het abonnement om dit type account te maken of te vernieuwen.

Het klassieke uitvoeren als-account van Azure voert de volgende taken uit.

  * Hiermee maakt u een beheer certificaat in het abonnement.

  * Hiermee maakt u een Automation-certificaat Asset `AzureClassicRunAsCertificate` met de naam in het opgegeven Automation-account. Het certificaatasset bevat de persoonlijke sleutel van het certificaat die wordt gebruikt door het beheercertificaat.

  * Hiermee wordt een Automation-verbindings element gemaakt `AzureClassicRunAsConnection` met de naam in het opgegeven Automation-account. Het verbindings element bevat de naam van het abonnement, de abonnements-ID en de naam van de certificaat Asset.

>[!NOTE]
>Het klassieke uitvoeren als-account van Azure wordt niet standaard op hetzelfde moment gemaakt als u een Automation-account maakt. Dit account wordt afzonderlijk gemaakt volgens de stappen die verderop in dit artikel worden beschreven.

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>Machtigingen voor het uitvoeren als-account verkrijgen

In deze sectie worden de machtigingen voor reguliere run as-accounts en klassieke uitvoeren als-accounts gedefinieerd.

### <a name="get-permissions-to-configure-run-as-accounts"></a>Machtigingen voor het configureren van run as-accounts ophalen

Als u een uitvoeren als-account wilt maken of bijwerken, moet u specifieke bevoegdheden en machtigingen hebben. Een toepassings beheerder in Azure Active Directory en een eigenaar van een abonnement kan alle taken volt ooien. In een situatie waarin u een schei ding van taken hebt, ziet u in de volgende tabel een lijst van de taken, de gelijkwaardige cmdlet en de benodigde machtigingen:

|Taak|Cmdlet  |Minimale machtigingen  |Waar u de machtigingen instelt|
|---|---------|---------|---|
|Een Azure AD-toepassing maken|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Application Developer-rol<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > app-registraties |
|Voeg een referentie toe aan de toepassing.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Toepassings beheerder of globale beheerder<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > app-registraties|
|Een Azure AD-service-principal maken en ophalen|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Toepassings beheerder of globale beheerder<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > app-registraties|
|De Azure-rol voor de opgegeven Principal toewijzen of ophalen|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Beheerder of eigenaar van de gebruikers toegang of de volgende machtigingen hebben:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Abonnement](../role-based-access-control/role-assignments-portal.md)</br>Home >-abonnementen > \<subscription name\> -Access Control (IAM)|
|Een Automation-certificaat maken of verwijderen|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Inzender voor resource groep         |Resource groep voor Automation-account|
|Een Automation-verbinding maken of verwijderen|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Inzender voor resource groep |Resource groep voor Automation-account|

<sup>1</sup> gebruikers die geen beheerder zijn in uw Azure AD-TENANT kunnen [ad-toepassingen registreren](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) als de optie gebruikers van de Azure AD-Tenant **kunnen toepassingen registreren** op de pagina gebruikers instellingen is ingesteld op **Ja**. Als de instelling voor de registratie van de toepassing **Nee**is, moet de gebruiker die deze actie uitvoert, worden gedefinieerd in deze tabel.

Als u geen lid bent van het Active Directory exemplaar van het abonnement voordat u wordt toegevoegd aan de rol van globale beheerder van het abonnement, wordt u als gast toegevoegd. In dit geval ontvangt u een `You do not have permissions to create…` waarschuwing op de pagina **Automation-account toevoegen** .

Als u lid bent van het Active Directory exemplaar van het abonnement wanneer de rol globale beheerder is toegewezen, kunt u ook een `You do not have permissions to create…` waarschuwing ontvangen op de pagina **Automation-account toevoegen** . In dit geval kunt u het verwijderen aanvragen van het Active Directory exemplaar van het abonnement en vervolgens aanvragen om opnieuw toe te voegen, zodat u een volledige gebruiker wordt in Active Directory.

Om te controleren of de situatie die het fout bericht produceert, is opgelost:

1. Selecteer in het deel venster Azure Active Directory in het Azure Portal de optie **gebruikers en groepen**.
2. Selecteer **alle gebruikers**.
3. Kies uw naam en selecteer vervolgens **profiel**.
4. Zorg ervoor dat de waarde van het kenmerk **gebruikers type** onder het profiel van de gebruiker niet is ingesteld op **gast**.

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Machtigingen voor het configureren van klassieke uitvoeren als-accounts ophalen

Als u klassieke uitvoeren als-accounts wilt configureren of vernieuwen, moet u de rol co-beheerder hebben op het abonnements niveau. Zie voor meer informatie over de machtigingen voor klassieke abonnementen [Azure Classic Subscription Administrators](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Een uitvoeren als-account maken in Azure Portal

Voer de volgende stappen uit om uw Azure Automation-account bij te werken in de Azure Portal. Maak de uitvoeren als-en klassieke uitvoeren als-accounts afzonderlijk. Als u geen klassieke resources hoeft te beheren, kunt u alleen de Uitvoeren als-account van Azure maken.

1. Meld u bij Azure Portal aan met een account dat lid is van de rol Abonnementsbeheerders en dat medebeheerder is van het abonnement.

2. Zoek en selecteer **Automation-accounts**.

3. Selecteer op de pagina Automation-accounts uw Automation-account in de lijst.

4. Selecteer in het linkerdeel venster **uitvoeren als-accounts** in de sectie account instellingen.

5. Afhankelijk van welk account u nodig hebt, selecteert u **Uitvoeren als-account van Azure ** of **Klassiek Uitvoeren als-account van Azure**.

6. Afhankelijk van het gewenste account, gebruikt u het deel venster **Azure uitvoeren als** - **account toevoegen of klassiek uitvoeren als-accounts** toevoegen. Nadat u de overzichts informatie hebt bekeken, klikt u op **maken**.

7. Terwijl in Azure het Uitvoeren als-account wordt gemaakt, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen. Er wordt ook een banner weer gegeven met de mede deling dat het account wordt gemaakt. Het proces kan een paar minuten duren.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Een Uitvoeren als- of klassiek Uitvoeren als-account verwijderen

In deze sectie wordt beschreven hoe u een uitvoeren als-of klassiek uitvoeren als-account verwijdert. Als u deze actie uitvoert, blijft het Automation-account behouden. Nadat u het account hebt verwijderd, kunt u het opnieuw maken in de Azure Portal.

1. Open in Azure Portal het Automation-account.

2. Selecteer in het linkerdeel venster **uitvoeren als-accounts** in de sectie account instellingen.

3. Selecteer op de pagina Uitvoeren als-accounts het Uitvoeren als- of klassieke Uitvoeren als-account dat u wilt verwijderen.

4. Klik in het deel venster Eigenschappen voor het geselecteerde account op **verwijderen**.

   ![Uitvoeren als-account verwijderen](media/manage-runas-account/automation-account-delete-runas.png)

5. Terwijl het account wordt verwijderd, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen.

6. Nadat het account is verwijderd, kunt u het opnieuw maken door op de eigenschappenpagina Uitvoeren als-accounts de optie **Azure Uitvoeren als-account** te selecteren.

   ![Het Automation uitvoeren als-account opnieuw maken](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Een zelfondertekend certificaat verlengen

Het zelfondertekende certificaat dat u voor het uitvoeren als-account hebt gemaakt, verloopt één jaar na de aanmaak datum. Op een bepaald moment voordat het run as-account verloopt, moet u het certificaat vernieuwen. U kunt deze op elk gewenst moment vernieuwen voordat het verloopt.

Wanneer u het zelfondertekende certificaat verlengt, wordt het huidige geldige certificaat bewaard om ervoor te zorgen dat alle runbooks die in de wachtrij zijn geplaatst of actief actief zijn, en die worden geverifieerd met het uitvoeren als-account, niet negatief worden beïnvloed. Het certificaat blijft geldig tot de vervaldatum.

>[!NOTE]
>Als u denkt dat het uitvoeren als-account is aangetast, kunt u het zelfondertekende certificaat verwijderen en opnieuw maken.

>[!NOTE]
>Als u uw uitvoeren als-account hebt geconfigureerd voor het gebruik van een certificaat dat is uitgegeven door de certificerings instantie van uw bedrijf en u de optie voor het vernieuwen van een zelfondertekend certificaat gebruikt, wordt het ondernemings certificaat vervangen door een zelfondertekend certificaat.

Gebruik de volgende stappen om het zelfondertekende certificaat te vernieuwen.

1. Open in Azure Portal het Automation-account.

1. Selecteer **uitvoeren als-accounts** in de sectie account instellingen.

    ![Eigenschappendeelvenster voor Automation-account](media/manage-runas-account/automation-account-properties-pane.png)

1. Selecteer op de pagina uitvoeren als-accounts het uitvoeren als-account of het klassieke uitvoeren als-account waarvoor u het certificaat wilt vernieuwen.

1. Klik in het deel venster Eigenschappen voor het geselecteerde account op **certificaat vernieuwen**.

    ![Certificaat vernieuwen voor Uitvoeren als-account](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Terwijl het certificaat wordt gemaakt, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen.

## <a name="limit-run-as-account-permissions"></a>Machtigingen voor het uitvoeren als-account beperken

Als u het doel van automatisering wilt beheren op basis van resources in azure, kunt u het [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) script uitvoeren. Met dit script wordt de bestaande service-principal van het run as-account gewijzigd om een aangepaste roldefinitie te maken en te gebruiken. De rol heeft machtigingen voor alle resources, met uitzonde ring van [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Nadat u het **Update-AutomationRunAsAccountRoleAssignments.ps1** script hebt uitgevoerd, werken runbooks die toegang hebben tot Key Vault door het gebruik van run as-accounts niet meer. Voordat u het script uitvoert, moet u runbooks in uw account controleren op aanroepen naar Azure Key Vault. Als u toegang tot Key Vault van Azure Automation runbooks wilt inschakelen, moet u [het uitvoeren als-account toevoegen aan de machtigingen van Key Vault](#add-permissions-to-key-vault).

Als u de beperking wilt beperken, kunt u ook andere resource typen toevoegen aan het `NotActions` element van de definitie van de aangepaste functie. In het volgende voor beeld wordt de toegang beperkt tot `Microsoft.Compute/*` . Als u dit resource type toevoegt aan `NotActions` voor de roldefinitie, heeft de rol geen toegang tot een reken resource. Zie [inzicht in roldefinities voor Azure-resources](../role-based-access-control/role-definitions.md)voor meer informatie over functie definities.

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

U kunt bepalen of de service-principal die wordt gebruikt door het run as-account zich in de roldefinitie van de rol van Inzender bevindt of een aangepast item.

1. Ga naar uw Automation-account en selecteer **uitvoeren als-accounts** in de sectie account instellingen.
2. Selecteer een **uitvoeren als-account voor Azure**.
3. Selecteer **rol** om de roldefinitie te zoeken die wordt gebruikt.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Controleer de rol van het run as-account." lightbox="media/manage-runas-account/verify-role-expanded.png":::


U kunt ook de roldefinitie bepalen die wordt gebruikt door de run as-accounts voor meerdere abonnementen of Automation-accounts. Dit doet u met behulp van het [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) script in de PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Machtigingen toevoegen aan Key Vault

U kunt Azure Automation laten verifiëren of Key Vault en de service-principal van het run as-account een aangepaste roldefinitie gebruiken. U moet het volgende doen:

* Machtigingen verlenen aan Key Vault.
* Stel het toegangs beleid in.

U kunt het [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) script in de PowerShell Gallery gebruiken om uw uitvoeren als-account machtigingen te geven voor Key Vault. Zie [een Key Vault toegangs beleid toewijzen](/azure/key-vault/general/assign-access-policy-powershell) voor meer informatie over het instellen van machtigingen op Key Vault.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Onjuiste configuratie problemen oplossen voor Run as-accounts

Bepaalde configuratie-items die nodig zijn voor een uitvoeren als-of klassiek uitvoeren als-account, zijn mogelijk verwijderd of onjuist gemaakt tijdens de eerste installatie. Mogelijke instanties van een onjuiste configuratie zijn:

* Certificaatasset
* Verbindingsasset
* Het run as-account is verwijderd uit de rol Inzender
* Service-principal of toepassing in Azure AD

Voor dergelijke onjuiste configuratie-instanties detecteert het Automation-account de wijzigingen en geeft de status *onvolledig* weer in het deel venster Eigenschappen van run as-accounts voor het account.

![Onvolledige Uitvoeren als-configuratiestatus](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Wanneer u het uitvoeren als-account selecteert, wordt het volgende fout bericht weer gegeven in het deel venster Eigenschappen van account:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

U kunt deze problemen met het run as-account snel oplossen door het uitvoeren als-account te verwijderen en opnieuw te maken.

## <a name="next-steps"></a>Volgende stappen

* [Toepassings objecten en Service-Principal-objecten](../active-directory/develop/app-objects-and-service-principals.md).
* [Overzicht van certificaten voor Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
