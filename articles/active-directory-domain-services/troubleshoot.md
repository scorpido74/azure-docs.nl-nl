---
title: 'Azure Active Directory Domain Services: Gids voor probleem oplossing | Microsoft Docs'
description: Gids voor probleem oplossing voor Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: c5ec80e81381423bdfdee07b1c020343d14ed559
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617073"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services-probleemoplossings gids
In dit artikel vindt u tips voor het oplossen van problemen die zich kunnen voordoen bij het instellen of beheren van Azure Active Directory (AD) Domain Services.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>U kunt Azure AD Domain Services niet inschakelen voor uw Azure AD-adres lijst
In deze sectie leert u hoe u fouten kunt oplossen wanneer u probeert Azure AD Domain Services in te scha kelen voor uw Directory.

Kies de stappen voor het oplossen van problemen die overeenkomen met het fout bericht dat u tegen komt.

| **Fout bericht** | **Resolutie** |
| --- |:--- |
| *De naam contoso.com wordt al gebruikt in dit netwerk. Geef een naam op die niet in gebruik is.* |[Domein naam conflict in het virtuele netwerk](troubleshoot.md#domain-name-conflict) |
| *Domain Services kan niet worden ingeschakeld in deze Azure AD-tenant. De service heeft niet voldoende machtigingen voor de toepassing met de naam 'Azure AD Domain Services Sync'. Verwijder de toepassing met de naam 'Azure AD Domain Services Sync' en probeer vervolgens om Domain Services in te schakelen voor uw Azure AD-tenant.* |[Domain Services heeft niet de juiste machtigingen voor de Azure AD Domain Services Sync-toepassing](troubleshoot.md#inadequate-permissions) |
| *Domain Services kan niet worden ingeschakeld in deze Azure AD-tenant. De toepassing Domain Services in uw Azure AD-tenant heeft niet de vereiste machtigingen om Domain Services in te schakelen. Verwijder de toepassing met de toepassings-id d87dcbc6-a371-462e-88e3-28ad15ec4e64 en probeer vervolgens om Domain Services in te schakelen voor uw Azure AD-tenant.* |[De domein Services-toepassing is niet juist geconfigureerd in uw Tenant](troubleshoot.md#invalid-configuration) |
| *Domain Services kan niet worden ingeschakeld in deze Azure AD-tenant. De toepassing Microsoft Azure AD is uitgeschakeld in uw Azure AD-tenant. Schakel de toepassing met de toepassings-id 00000002-0000-0000-c000-000000000000 in en probeer vervolgens om Domain Services in te schakelen voor uw Azure AD-tenant.* |[De Microsoft Graph toepassing is uitgeschakeld in uw Azure AD-Tenant](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflict met domein naam
**Fout bericht:**

*De naam contoso.com wordt al gebruikt in dit netwerk. Geef een naam op die niet in gebruik is.*

**Herstel**

Zorg ervoor dat er geen bestaand domein met dezelfde domein naam beschikbaar is op het virtuele netwerk. Het domein met de naam contoso.com is bijvoorbeeld al beschikbaar op het geselecteerde virtuele netwerk. Later probeert u een Azure AD Domain Services beheerd domein in te scha kelen met dezelfde domein naam (dat wil zeggen ' contoso.com ') op dat virtuele netwerk. Er treedt een fout op bij het inschakelen van Azure AD Domain Services.

Deze fout wordt veroorzaakt door naam conflicten voor de domein naam op het virtuele netwerk. In dit geval moet u een andere naam gebruiken om het beheerde domein van Azure AD Domain Services in te stellen. U kunt ook de inrichting van het bestaande domein ongedaan maken en doorgaan met het inschakelen van Azure AD Domain Services.

### <a name="inadequate-permissions"></a>Ontoereikende machtigingen
**Fout bericht:**

*Domain Services kan niet worden ingeschakeld in deze Azure AD-tenant. De service heeft niet voldoende machtigingen voor de toepassing met de naam 'Azure AD Domain Services Sync'. Verwijder de toepassing met de naam 'Azure AD Domain Services Sync' en probeer vervolgens om Domain Services in te schakelen voor uw Azure AD-tenant.*

**Herstel**

Controleer of er een toepassing is met de naam Azure AD Domain Services Sync in uw Azure AD-adres lijst. Als deze toepassing bestaat, verwijdert u deze en schakelt u Azure AD Domain Services opnieuw in.

Voer de volgende stappen uit om te controleren of de toepassing aanwezig is en om deze te verwijderen als de toepassing bestaat:

1. Navigeer naar het gedeelte **toepassingen** van uw Azure AD-Directory in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Selecteer **alle toepassingen** in de vervolg keuzelijst **weer geven** . Selecteer in de vervolg keuzelijst **toepassingen status** de optie. Selecteer de **gewenste** optie in de vervolg keuzelijst **toepassings zichtbaarheid** .
3. Typ **Azure AD Domain Services Sync** in het zoekvak. Als de toepassing bestaat, klikt u erop en klikt u op de knop **verwijderen** op de werk balk om deze te verwijderen.
4. Nadat u de toepassing hebt verwijderd, kunt u opnieuw proberen Azure AD Domain Services opnieuw in te scha kelen.

### <a name="invalid-configuration"></a>Ongeldige configuratie
**Fout bericht:**

*Domain Services kan niet worden ingeschakeld in deze Azure AD-tenant. De toepassing Domain Services in uw Azure AD-tenant heeft niet de vereiste machtigingen om Domain Services in te schakelen. Verwijder de toepassing met de toepassings-id d87dcbc6-a371-462e-88e3-28ad15ec4e64 en probeer vervolgens om Domain Services in te schakelen voor uw Azure AD-tenant.*

**Herstel**

Controleer of u een toepassing met de naam ' AzureActiveDirectoryDomainControllerServices ' hebt (met een toepassings-id van d87dcbc6-a371-462e-88e3-28ad15ec4e64) in uw Azure AD-adres lijst. Als deze toepassing bestaat, moet u deze verwijderen en vervolgens opnieuw inschakelen Azure AD Domain Services.

Gebruik het volgende Power shell-script om de toepassing te zoeken en te verwijderen.

> [!NOTE]
> Dit script maakt gebruik van **Azure AD Power shell versie 2** -cmdlets. Lees de [AzureAD Power shell-referentie documentatie](https://msdn.microsoft.com/library/azure/mt757189.aspx)voor een volledige lijst met alle beschik bare cmdlets en down load de module.
>
>

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph uitgeschakeld
**Fout bericht:**

Domain Services kan niet worden ingeschakeld in deze Azure AD-Tenant. De toepassing Microsoft Azure AD is uitgeschakeld in uw Azure AD-tenant. Schakel de toepassing met de toepassings-id 00000002-0000-0000-C000-000000000000 te gebruiken in en probeer vervolgens Domain Services in te scha kelen voor uw Azure AD-Tenant.

**Herstel**

Controleer of u een toepassing hebt uitgeschakeld met de id 00000002-0000-0000-C000-000000000000 te gebruiken. Deze toepassing is de Microsoft Azure AD toepassing en biedt Graph API toegang tot uw Azure AD-Tenant. Azure AD Domain Services moet deze toepassing worden ingeschakeld om uw Azure AD-Tenant te synchroniseren met uw beheerde domein.

Om deze fout op te lossen, schakelt u deze toepassing in en probeert u domein Services in te scha kelen voor uw Azure AD-Tenant.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Gebruikers kunnen zich niet aanmelden bij het in Azure AD Domain Services beheerde domein
Als een of meer gebruikers in uw Azure AD-Tenant zich niet kunnen aanmelden bij het zojuist gemaakte beheerde domein, voert u de volgende stappen voor probleem oplossing uit:

* **Aanmelden met UPN-indeling:** Probeer u aan te melden met behulp van de UPN-indeling (bijvoorbeeld 'joeuser@contoso.com') in plaats van de SAMAccountName-indeling ('CONTOSO\joeuser'). De SAMAccountName kan automatisch worden gegenereerd voor gebruikers waarvan het UPN-voor voegsel langer is dan of gelijk is aan die van een andere gebruiker in het beheerde domein. De UPN-indeling is gegarandeerd uniek binnen een Azure AD-Tenant.

> [!NOTE]
> We raden u aan de UPN-indeling te gebruiken om u aan te melden bij het door Azure AD Domain Services beheerde domein.
>
>

* Zorg ervoor dat u [wachtwoordsynchronisatie hebt ingeschakeld](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) volgens de stappen beschreven in de handleiding.
* **Externe accounts:** Zorg ervoor dat het betreffende gebruikersaccount geen extern account in de Azure AD-tenant is. Voor beelden van externe accounts zijn micro soft-accounts (bijvoorbeeldjoe@live.com' ') of gebruikers accounts uit een externe Azure AD-adres lijst. Omdat Azure AD Domain Services geen referenties heeft voor dergelijke gebruikers accounts, kunnen deze gebruikers zich niet aanmelden bij het beheerde domein.
* **Gesynchroniseerde accounts:** Als de betrokken gebruikers accounts worden gesynchroniseerd vanuit een on-premises Directory, controleert u het volgende:

  * U hebt de [meest recente aanbevolen versie van Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)geïmplementeerd of bijgewerkt.
  * U hebt Azure AD Connect geconfigureerd om [een volledige synchronisatie uit te voeren](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).
  * Afhankelijk van de grootte van uw map kan het enige tijd duren voordat gebruikers accounts en referentie-hashes beschikbaar zijn in Azure AD Domain Services. Zorg ervoor dat u lang genoeg wacht voordat u opnieuw probeert te authenticeren.
  * Als het probleem zich blijft voordoen nadat u de voor gaande stappen hebt gecontroleerd, start u de Microsoft Azure AD Sync-service opnieuw op. Start vanaf de synchronisatie computer een opdracht prompt en voer de volgende opdrachten uit:

    1. net stop ' Microsoft Azure AD Sync '
    2. net start ' Microsoft Azure AD Sync '
* **Alleen Cloud accounts**: Als het betrokken gebruikers account een alleen-Cloud gebruikers account is, moet u ervoor zorgen dat de gebruiker zijn of haar wacht woord heeft gewijzigd nadat u Azure AD Domain Services hebt ingeschakeld. Door deze stap worden de referentie-hashes gegenereerd die zijn vereist voor Azure AD Domain Services.
* **Controleer of het gebruikers account actief is**: Als het account van een gebruiker is vergrendeld, kan het zich niet aanmelden totdat het account weer actief is. Vijf ongeldige wachtwoord pogingen binnen twee minuten op het beheerde domein zorgen ervoor dat een gebruikers account 30 minuten wordt vergrendeld. Na 30 minuten wordt het gebruikers account automatisch ontgrendeld.
  * Bij ongeldige wachtwoord pogingen op het beheerde domein wordt het gebruikers account in azure AD niet vergrendeld. Het gebruikers account is alleen vergrendeld binnen uw Azure AD Domain Services beheerde domein. Controleer de status van het gebruikers account met behulp van de Active Directory-beheer console (ADAC) voor het beheerde Azure AD DS-domein, niet in azure AD.
  * U kunt ook [een verfijnd wachtwoord beleid configureren dat de standaard drempelwaarde en duur van vergren deling wijzigt](https://docs.microsoft.com/azure/active-directory-domain-services/password-policy).

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Er zijn een of meer waarschuwingen voor uw beheerde domein

Zie waarschuwingen voor uw beheerde domein oplossen door het artikel [problemen met waarschuwingen oplossen](troubleshoot-alerts.md) te bezoeken.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Gebruikers die zijn verwijderd uit uw Azure AD-Tenant, worden niet verwijderd uit uw beheerde domein
Azure AD voorkomt dat u onbedoeld gebruikersobjecten verwijdert. Wanneer u een gebruikersaccount uit uw Azure AD-tenant verwijdert, wordt het overeenkomstige gebruikersobject verplaatst naar de Prullenbak. Wanneer deze Verwijder bewerking is gesynchroniseerd met uw beheerde domein, wordt het bijbehorende gebruikers account gemarkeerd als uitgeschakeld. Met deze functie kunt u het gebruikers account later herstellen of verwijderen.

Het gebruikers account blijft uitgeschakeld in uw beheerde domein, zelfs als u opnieuw een gebruikers account met dezelfde UPN maakt in uw Azure AD-adres lijst. Als u het gebruikers account uit uw beheerde domein wilt verwijderen, moet u het verwijderen uit uw Azure AD-Tenant.

Als u het gebruikers account volledig van uw beheerde domein wilt verwijderen, verwijdert u de gebruiker permanent uit uw Azure AD-Tenant. Gebruik de `Remove-MsolUser` Power shell-cmdlet `-RemoveFromRecycleBin` met de optie, zoals beschreven in dit [MSDN-artikel](/previous-versions/azure/dn194132(v=azure.100)).


## <a name="contact-us"></a>Contact opnemen
Neem contact op met het product team van Azure Active Directory Domain Services om [feedback te delen of voor ondersteuning](contact-us.md).
