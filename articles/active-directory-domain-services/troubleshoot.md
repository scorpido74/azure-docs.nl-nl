---
title: Problemen met Azure Directory Domain Services | Microsoft Docs'
description: Meer informatie over het oplossen van veelvoorkomende fouten bij het maken of beheren van Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 84efe294533186fdcf2e0a3356a7d6b01eccaf5f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654397"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Veelvoorkomende fouten en stappen voor probleemoplossing voor Azure Active Directory Domain Services

Als centraal onderdeel van identiteit en verificatie voor toepassingen heeft Azure Active Directory Domain Services (Azure AD DS) soms problemen. Als u problemen ondervindt, zijn er enkele veelvoorkomende foutberichten en bijbehorende stappen voor het oplossen van problemen om u te helpen de zaken weer te laten werken. U op elk gewenst moment ook [een Azure-ondersteuningsaanvraag openen][azure-support] voor extra hulp bij het oplossen van problemen.

In dit artikel worden stappen voor het oplossen van problemen voor veelvoorkomende problemen in Azure AD DS weergegeven.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>U Azure AD Domain Services niet inschakelen voor uw Azure AD-map

Als u problemen ondervindt bij het inschakelen van Azure AD DS, controleert u de volgende veelvoorkomende fouten en stappen om deze op te lossen:

| **Voorbeeldfoutbericht** | **Afsluiting** |
| --- |:--- |
| *De naam aaddscontoso.com is al in gebruik op dit netwerk. Geef een naam op die niet in gebruik is.* |[Conflict met domeinnaam in het virtuele netwerk](troubleshoot.md#domain-name-conflict) |
| *Domeinservices kunnen niet worden ingeschakeld in deze Azure AD-tenant. De service beschikt niet over voldoende machtigingen voor de toepassing 'Azure AD Domain Services Sync'. Verwijder de toepassing 'Azure AD Domain Services Sync' en probeer vervolgens Domain Services in te schakelen voor uw Azure AD-tenant.* |[Domain Services beschikt niet over voldoende machtigingen voor de Azure AD Domain Services Sync-toepassing](troubleshoot.md#inadequate-permissions) |
| *Domeinservices kunnen niet worden ingeschakeld in deze Azure AD-tenant. De toepassing Domain Services in uw Azure AD-tenant heeft niet de vereiste machtigingen om Domain Services in te schakelen. Verwijder de toepassing met de toepassings-id d87dcbc6-a371-462e-88e3-28ad15ec4e64 en probeer vervolgens Domain Services in te schakelen voor uw Azure AD-tenant.* |[De toepassing Domain Services is niet goed geconfigureerd in uw Azure AD-tenant](troubleshoot.md#invalid-configuration) |
| *Domeinservices kunnen niet worden ingeschakeld in deze Azure AD-tenant. De Microsoft Azure AD-toepassing is uitgeschakeld in uw Azure AD-tenant. Schakel de toepassing in met de toepassings-id 00000002-0000-0000-c000-000000000000000 en probeer vervolgens Domeinservices in te schakelen voor uw Azure AD-tenant.* |[De Microsoft Graph-toepassing is uitgeschakeld in uw Azure AD-tenant](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflict met domeinnaam

**Foutbericht**

*De naam aaddscontoso.com is al in gebruik op dit netwerk. Geef een naam op die niet in gebruik is.*

**Afsluiting**

Controleer of u geen bestaande AD DS-omgeving hebt met dezelfde domeinnaam op dezelfde of een peered, virtueel netwerk. U bijvoorbeeld een AD DS-domein hebben met de naam *aaddscontoso.com* dat wordt uitgevoerd op Azure VM's. Wanneer u een door Azure AD DS beheerd domein met dezelfde domeinnaam van *aaddscontoso.com* op het virtuele netwerk probeert in te schakelen, mislukt de aangevraagde bewerking.

Deze fout is te wijten aan naamconflicten voor de domeinnaam op het virtuele netwerk. Een DNS-lookup controleert of een bestaande AD DS-omgeving reageert op de aangevraagde domeinnaam. Als u deze fout wilt oplossen, gebruikt u een andere naam om uw door Azure AD DS beheerde domein in te stellen of het bestaande AD DS-domein te de-provisionen en vervolgens opnieuw azure AD DS in te schakelen.

### <a name="inadequate-permissions"></a>Onvoldoende machtigingen

**Foutbericht**

*Domeinservices kunnen niet worden ingeschakeld in deze Azure AD-tenant. De service beschikt niet over voldoende machtigingen voor de toepassing 'Azure AD Domain Services Sync'. Verwijder de toepassing 'Azure AD Domain Services Sync' en probeer vervolgens Domain Services in te schakelen voor uw Azure AD-tenant.*

**Afsluiting**

Controleer of er een toepassing met de naam *Azure AD Domain Services Sync* in uw Azure AD-map staat. Als deze toepassing bestaat, verwijdert u deze en probeert u azure AD DS opnieuw in te schakelen. Voer de volgende stappen uit om te controleren of een bestaande toepassing deze wilt verwijderen en indien nodig te verwijderen:

1. Selecteer Azure Active **Directory** in de Azure-portal in het linkernavigatiemenu.
1. Selecteer **Enterprise-toepassingen**. Kies *Alle toepassingen* in het vervolgkeuzemenu **Toepassingstype** en selecteer **Vervolgens Toepassen**.
1. Voer in het zoekvak *Azure AD Domain Services Sync*in. Als de toepassing bestaat, selecteert u deze en kiest **u Verwijderen**.
1. Zodra u de toepassing hebt verwijderd, probeert u Azure AD DS opnieuw in te schakelen.

### <a name="invalid-configuration"></a>Ongeldige configuratie

**Foutbericht**

*Domeinservices kunnen niet worden ingeschakeld in deze Azure AD-tenant. De toepassing Domain Services in uw Azure AD-tenant heeft niet de vereiste machtigingen om Domain Services in te schakelen. Verwijder de toepassing met de toepassings-id d87dcbc6-a371-462e-88e3-28ad15ec4e64 en probeer vervolgens Domain Services in te schakelen voor uw Azure AD-tenant.*

**Afsluiting**

Controleer of u een bestaande toepassing hebt met de naam *AzureActiveDirectoryDomainControllerServices* met een toepassings-id van *d87dcbc6-a371-462e-88e3-28ad15ec4e64* in uw Azure AD-directory. Als deze toepassing bestaat, verwijdert u deze en probeert u vervolgens opnieuw Azure AD DS in te schakelen.

Gebruik het volgende PowerShell-script om naar een bestaande toepassingsinstantie te zoeken en indien nodig te verwijderen:

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

### <a name="microsoft-graph-disabled"></a>Microsoft Graph uitgeschakeld

**Foutbericht**

*Domeinservices kunnen niet worden ingeschakeld in deze Azure AD-tenant. De Microsoft Azure AD-toepassing is uitgeschakeld in uw Azure AD-tenant. Schakel de toepassing in met de toepassings-id 00000002-0000-0000-c000-000000000000000 en probeer vervolgens Domeinservices in te schakelen voor uw Azure AD-tenant.*

**Afsluiting**

Controleer of u een toepassing hebt uitgeschakeld met de id *00000002-0000-0000-c000-0000000000000000000*. Deze toepassing is de Microsoft Azure AD-toepassing en biedt Graph API toegang tot uw Azure AD-tenant. Als u uw Azure AD-tenant wilt synchroniseren, moet deze toepassing zijn ingeschakeld.

Voer de volgende stappen uit om de status van deze toepassing te controleren en indien nodig in te schakelen:

1. Selecteer Azure Active **Directory** in de Azure-portal in het linkernavigatiemenu.
1. Selecteer **Enterprise-toepassingen**. Kies *Alle toepassingen* in het vervolgkeuzemenu **Toepassingstype** en selecteer **Vervolgens Toepassen**.
1. Voer in het zoekvak *00000002-0000-0000-c000-00000000000000000*. Selecteer de toepassing en kies **Eigenschappen**.
1. Als **ingeschakeld voor gebruikers om zich aan te melden** is ingesteld op *Nee,* stelt u de waarde in op *Ja*, selecteert u **Opslaan**.
1. Zodra u de toepassing hebt ingeschakeld, probeert u Azure AD DS opnieuw in te schakelen.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Gebruikers kunnen zich niet aanmelden bij het in Azure AD Domain Services beheerde domein

Als een of meer gebruikers in uw Azure AD-tenant zich niet kunnen aanmelden bij het beheerde Azure AD DS-domein, voert u de volgende stappen voor het oplossen van problemen uit:

* **Indeling referenties** : probeer de UPN-indeling te `dee@aaddscontoso.onmicrosoft.com`gebruiken om referenties op te geven, zoals . De UPN-indeling is de aanbevolen manier om referenties op te geven in Azure AD DS. Controleer of deze UPN correct is geconfigureerd in Azure AD.

    De *SAMAccountName* voor uw account, zoals *AADDSCONTOSO\driley,* kan automatisch worden gegenereerd als er meerdere gebruikers zijn met hetzelfde UPN-voorvoegsel in uw tenant of als uw UPN-voorvoegsel te lang is. Daarom kan de *SAMAccountName-indeling* voor uw account afwijken van wat u verwacht of gebruikt in uw on-premises domein.

* **Wachtwoordsynchronisatie** : controleer of u wachtwoordsynchronisatie hebt ingeschakeld voor [alleen cloudgebruikers][cloud-only-passwords] of voor [hybride omgevingen met Azure AD Connect.][hybrid-phs]
    * **Hybride gesynchroniseerde accounts:** Als de betreffende gebruikersaccounts zijn gesynchroniseerd vanuit een on-premises map, controleert u de volgende gebieden:
    
      * U hebt de [meest recente aanbevolen versie van Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)geïmplementeerd of bijgewerkt.
      * U hebt Azure AD Connect geconfigureerd om een volledige synchronisatie uit te [voeren.][hybrid-phs]
      * Afhankelijk van de grootte van uw map kan het even duren voordat gebruikersaccounts en referenties beschikbaar zijn in Azure AD DS. Zorg ervoor dat u lang genoeg wacht voordat u probeert te verifiëren tegen het beheerde domein.
      * Als het probleem blijft bestaan nadat de vorige stappen zijn geverifieerd, probeert u de *Microsoft Azure AD Sync Service*opnieuw te starten. Open vanaf uw Azure AD Connect-server een opdrachtprompt en voer de volgende opdrachten uit:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Cloud-only accounts:** Als het getroffen gebruikersaccount een cloudaccount is, controleert u of de [gebruiker zijn wachtwoord heeft gewijzigd nadat u Azure AD DS hebt ingeschakeld.][cloud-only-passwords] Deze wachtwoordreset zorgt ervoor dat de vereiste referenties voor Azure AD Domain Services worden gegenereerd.

* **Controleer of het gebruikersaccount actief is:** standaard zorgen vijf ongeldige wachtwoordpogingen binnen 2 minuten op het beheerde domein ervoor dat een gebruikersaccount gedurende 30 minuten wordt vergrendeld. De gebruiker kan zich niet aanmelden terwijl het account is vergrendeld. Na 30 minuten wordt het gebruikersaccount automatisch ontgrendeld.
  * Ongeldige wachtwoordpogingen in het beheerde Azure AD DS-domein sluiten het gebruikersaccount in Azure AD niet uit. Het gebruikersaccount is alleen buitengesloten binnen het beheerde domein. Controleer de status van het gebruikersaccount in de *Active Directory Administrative Console (ADAC)* met behulp van de [beheer-VM][management-vm], niet in Azure AD.
  * U ook [fijnkorrelig wachtwoordbeleid configureren][password-policy] om de standaarddrempel en duur van de uitsluiting te wijzigen.

* **Externe accounts** : controleer of het betreffende gebruikersaccount geen extern account is in de Azure AD-tenant. Voorbeelden van externe accounts zijn `dee@live.com` Microsoft-accounts zoals of gebruikersaccounts uit een externe Azure AD-map. Azure AD DS slaat geen referenties op voor externe gebruikersaccounts, zodat ze zich niet kunnen aanmelden bij het beheerde domein.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Er zijn een of meer waarschuwingen op uw beheerde domein

Als er actieve waarschuwingen zijn op het beheerde Azure AD DS-domein, kan dit voorkomen dat het verificatieproces correct werkt.

Als u wilt zien of er actieve waarschuwingen zijn, [controleert u de status van een door Azure AD DS beheerd domein][check-health]. Als er waarschuwingen worden weergegeven, [los je deze op en los je deze op.][troubleshoot-alerts]

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Gebruikers die uit uw Azure AD-tenant zijn verwijderd, worden niet verwijderd uit uw beheerde domein

Azure AD beschermt tegen het per ongeluk verwijderen van gebruikersobjecten. Wanneer u een gebruikersaccount verwijdert uit een Azure AD-tenant, wordt het bijbehorende gebruikersobject verplaatst naar de prullenbak. Wanneer deze verwijderingsbewerking is gesynchroniseerd met uw door Azure AD DS beheerde domein, wordt het bijbehorende gebruikersaccount gemarkeerd als uitgeschakeld. Met deze functie u het gebruikersaccount herstellen of verwijderen.

Het gebruikersaccount blijft uitgeschakeld in het beheerde Azure AD DS-domein, zelfs als u opnieuw een gebruikersaccount maakt met dezelfde UPN in de Azure AD-map. Als u het gebruikersaccount wilt verwijderen uit het beheerde Azure AD DS-domein, moet u het met geweld verwijderen uit de Azure AD-tenant.

Als u een gebruikersaccount volledig wilt verwijderen uit een door Azure AD DS beheerd domein, verwijdert u de `-RemoveFromRecycleBin` gebruiker permanent uit uw Azure AD-tenant met behulp van de cmdlet [Remove-MsolUser][Remove-MsolUser] PowerShell met de parameter.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen ondervindt, [opent u een Azure-ondersteuningsaanvraag][azure-support] voor extra hulp bij het oplossen van problemen.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
