---
title: Een Azure Automation uitvoeren als-account beheren
description: In dit artikel leest u hoe u uw uitvoeren als-account kunt beheren met Power shell of via de Azure Portal.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: 3357cb40ff476a3cc0bce259930068aeccf2c10c
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767479"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Een Azure Automation uitvoeren als-account beheren

Uitvoeren als-accounts in Azure Automation bieden verificatie voor het beheren van resources op het Azure Resource Manager of het klassieke Azure-implementatie model met behulp van Automation-runbooks en andere automatiserings functies. Dit artikel bevat richt lijnen voor het beheren van een uitvoeren als-of klassiek uitvoeren als-account.

Zie [overzicht van Automation-account verificatie](automation-security-overview.md)voor meer informatie over Azure Automation account verificatie en richt lijnen met betrekking tot proces automatiserings scenario's.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Machtigingen voor het run as-account

In deze sectie worden de machtigingen voor reguliere run as-accounts en klassieke uitvoeren als-accounts gedefinieerd.

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

Als u lid bent van het Active Directory exemplaar van het abonnement waaraan de rol globale beheerder is toegewezen, kunt u ook een `You do not have permissions to create…` waarschuwing ontvangen op de pagina **Automation-account toevoegen** . In dit geval kunt u het verwijderen aanvragen van het Active Directory exemplaar van het abonnement en vervolgens aanvragen om opnieuw toe te voegen, zodat u een volledige gebruiker wordt in Active Directory.

Om te controleren of de situatie die het fout bericht produceert, is opgelost:

1. Selecteer in het deel venster Azure Active Directory in het Azure Portal de optie **gebruikers en groepen**.
2. Selecteer **alle gebruikers**.
3. Kies uw naam en selecteer vervolgens **profiel**.
4. Zorg ervoor dat de waarde van het kenmerk **gebruikers type** onder het profiel van de gebruiker niet is ingesteld op **gast**.

### <a name="permissions-required-to-create-or-manage-classic-run-as-accounts"></a><a name="permissions-classic"></a>Vereiste machtigingen voor het maken of beheren van klassieke uitvoeren als-accounts

Als u klassieke uitvoeren als-accounts wilt configureren of vernieuwen, moet u de rol co-beheerder hebben op het abonnements niveau. Zie voor meer informatie over de machtigingen voor klassieke abonnementen [Azure Classic Subscription Administrators](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Een uitvoeren als-account maken in Azure Portal

Voer de volgende stappen uit om uw Azure Automation-account bij te werken in de Azure Portal. Maak de uitvoeren als-en klassieke uitvoeren als-accounts afzonderlijk. Als u geen klassieke resources hoeft te beheren, kunt u alleen de Uitvoeren als-account van Azure maken.

1. Meld u bij Azure Portal aan met een account dat lid is van de rol Abonnementsbeheerders en dat medebeheerder is van het abonnement.

2. Zoek en selecteer **Automation-accounts**.

3. Selecteer op de pagina Automation-accounts uw Automation-account in de lijst.

4. Selecteer in het linkerdeel venster **uitvoeren als-accounts** in de sectie **account instellingen** .

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Selecteer de optie uitvoeren als-account.":::

5. Afhankelijk van het account dat u nodig hebt, gebruikt u het deel venster **uitvoeren als-account + Azure** of het **klassieke uitvoeren als-account van Azure** . Nadat u de overzichts informatie hebt bekeken, klikt u op **maken**.

    :::image type="content" source="media/manage-runas-account/automation-account-create-runas.png" alt-text="Selecteer de optie uitvoeren als-account.":::

6. Terwijl in Azure het Uitvoeren als-account wordt gemaakt, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen. Er wordt ook een banner weer gegeven met de mede deling dat het account wordt gemaakt. Het proces kan een paar minuten duren.

## <a name="create-a-run-as-account-using-powershell"></a>Een uitvoeren als-account maken met behulp van Power shell

De volgende lijst bevat de vereisten voor het maken van een uitvoeren als-account in Power shell met behulp van een script. Deze vereisten zijn van toepassing op beide typen run as-accounts.

* Windows 10 of Windows Server 2016 met Azure Resource Manager modules 3.4.1 en hoger. Het Power shell-script biedt geen ondersteuning voor eerdere versies van Windows.
* Azure PowerShell Power shell 6.2.4 of hoger. Zie [Azure PowerShell installeren en configureren](/powershell/azure/install-az-ps)voor meer informatie.
* Een Automation-account waarnaar wordt verwezen als de waarde voor de `AutomationAccountName` `ApplicationDisplayName` para meters en.
* Machtigingen die gelijk zijn aan de items in de [vereiste machtigingen voor het configureren van run as-accounts](#permissions).

`AutomationAccountName` `SubscriptionId` Voer de volgende stappen uit om de waarden voor,, en `ResourceGroupName` , de vereiste para meters voor het Power shell-script, op te halen.

1. Selecteer in de Azure Portal **Automation-accounts**.

1. Selecteer uw Automation-account op de pagina Automation-accounts.

1. Selecteer in de sectie account instellingen de optie **Eigenschappen**.

1. Noteer de waarden voor **naam**, **abonnements-id**en **resource groep** op de pagina **Eigenschappen** .

   ![Eigenschappen pagina voor Automation-account](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Power shell-script voor het maken van een uitvoeren als-account

Het Power shell-script biedt ondersteuning voor verschillende configuraties.

* Een Uitvoeren als-account maken met een zelfondertekend certificaat.
* Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat.
* Maak een Uitvoeren als-account en een klassiek Uitvoeren als-account via een certificaat dat is uitgegeven door de certificeringsinstantie van uw bedrijf.
* Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat in de cloud van Azure Government.

1. Down load het script en sla het op in een lokale map met behulp van de volgende opdracht.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Start Power shell met verhoogde gebruikers rechten en navigeer naar de map die het script bevat.

3. Voer een van de volgende opdrachten uit om een uitvoeren als-en/of klassiek uitvoeren als-account te maken op basis van uw vereisten.

    * Een uitvoeren als-account maken met een zelfondertekend certificaat.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een bedrijfscertificaat.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Als u een klassiek uitvoeren als-account hebt gemaakt met een openbaar certificaat (. cer-bestand) van een onderneming, gebruikt u dit certificaat. Met het script wordt het bestand gemaakt en opgeslagen in de map met tijdelijke bestanden op uw computer, onder het gebruikers profiel dat `%USERPROFILE%\AppData\Local\Temp` u hebt gebruikt om de Power shell-sessie uit te voeren. Zie [een beheer-API-certificaat uploaden naar de Azure Portal](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat in de cloud van Azure Government

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. Nadat het script is uitgevoerd, wordt u gevraagd om u te verifiëren bij Azure. Meld u aan met een account dat lid is van de rol abonnements beheerders. Als u een klassiek uitvoeren als-account maakt, moet uw account een mede beheerder van het abonnement zijn.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Een Uitvoeren als- of klassiek Uitvoeren als-account verwijderen

In deze sectie wordt beschreven hoe u een uitvoeren als-of klassiek uitvoeren als-account verwijdert. Als u deze actie uitvoert, blijft het Automation-account behouden. Nadat u het uitvoeren als-account hebt verwijderd, kunt u het opnieuw maken in de Azure Portal of met het Power shell-script.

1. Open in Azure Portal het Automation-account.

2. Selecteer in het linkerdeel venster **uitvoeren als-accounts** in de sectie account instellingen.

3. Selecteer op de pagina Uitvoeren als-accounts het Uitvoeren als- of klassieke Uitvoeren als-account dat u wilt verwijderen.

4. Klik in het deel venster Eigenschappen voor het geselecteerde account op **verwijderen**.

   ![Uitvoeren als-account verwijderen](media/manage-runas-account/automation-account-delete-runas.png)

5. Terwijl het account wordt verwijderd, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen.

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

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Selecteer de optie uitvoeren als-account." lightbox="media/manage-runas-account/verify-role-expanded.png":::

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

![Onvolledige Uitvoeren als-configuratiestatus](media/manage-runas-account/automation-account-runas-config-incomplete.png)

Wanneer u het uitvoeren als-account selecteert, wordt het volgende fout bericht weer gegeven in het deel venster Eigenschappen van account:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

U kunt deze problemen met het run as-account snel oplossen door het uitvoeren als-account te verwijderen en opnieuw te maken.

## <a name="next-steps"></a>Volgende stappen

* [Toepassings objecten en Service-Principal-objecten](../active-directory/develop/app-objects-and-service-principals.md).
* [Overzicht van certificaten voor Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
