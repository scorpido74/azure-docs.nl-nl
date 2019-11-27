---
title: Wachtwoord-hash-synchronisatie inschakelen voor Azure AD Domain Services | Microsoft Docs
description: In deze zelf studie leert u hoe u wachtwoord-hash synchronisatie kunt inschakelen met Azure AD Connect naar een Azure Active Directory Domain Services beheerd domein.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 3a1d99ad282190c61f652179dd08a810c9444064
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481163"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Zelf studie: wachtwoord synchronisatie inschakelen in Azure Active Directory Domain Services voor hybride omgevingen

Voor hybride omgevingen kan een Azure Active Directory-Tenant (Azure AD) worden geconfigureerd om te synchroniseren met een on-premises Active Directory Domain Services-omgeving (AD DS) met behulp van Azure AD Connect. Standaard synchroniseert Azure AD Connect geen oudere NT LAN Manager (NTLM)-en Kerberos-wachtwoord hashes die nodig zijn voor Azure Active Directory Domain Services (Azure AD DS).

Als u Azure AD DS wilt gebruiken met accounts die zijn gesynchroniseerd vanuit een on-premises AD DS omgeving, moet u Azure AD Connect configureren om de wachtwoord-hashes te synchroniseren die zijn vereist voor NTLM-en Kerberos-verificatie. Nadat Azure AD Connect is geconfigureerd, worden de verouderde wacht woord-hashes ook gesynchroniseerd met Azure AD wanneer er een on-premises account wordt gemaakt of een wachtwoord wijziging

U hoeft deze stappen niet uit te voeren als u alleen Cloud accounts gebruikt zonder lokale AD DS omgeving.

In deze zelf studie leert u het volgende:

> [!div class="checklist"]
> * Waarom zijn oudere NTLM-en Kerberos-wachtwoord-hashes vereist
> * Verouderde wachtwoord-hash synchronisatie voor Azure AD Connect configureren

Als u nog geen abonnement op Azure hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie hebt u de volgende resources nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-Tenant die is gekoppeld aan uw abonnement dat is gesynchroniseerd met een on-premises Directory met behulp van Azure AD Connect.
    * Als dat nodig is, [maakt u een Azure Active Directory-Tenant][create-azure-ad-tenant] of [koppelt u een Azure-abonnement aan uw account][associate-azure-ad-tenant].
    * Schakel, indien nodig, [Azure AD Connect in voor synchronisatie van wacht woord-hashes][enable-azure-ad-connect].
* Een Azure Active Directory Domain Services beheerd domein ingeschakeld en geconfigureerd in uw Azure AD-Tenant.
    * Als dat nodig is, kunt [u een Azure Active Directory Domain Services-exemplaar maken en configureren][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Wachtwoord hash synchroniseren met Azure AD Connect

Azure AD Connect wordt gebruikt voor het synchroniseren van objecten zoals gebruikers accounts en-groepen van een on-premises AD DS omgeving in een Azure AD-Tenant. Als onderdeel van het proces kunnen met wacht woord-hash-synchronisatie accounts hetzelfde wacht woord gebruiken in de on-premises AD DS omgeving en Azure AD.

Voor het verifiëren van gebruikers in het beheerde domein heeft Azure AD DS wacht woord-hashes nodig in een indeling die geschikt is voor NTLM-en Kerberos-verificatie. Azure AD slaat geen wacht woord-hashes in de vereiste indeling voor NTLM-of Kerberos-verificatie op totdat u Azure AD DS voor uw Tenant inschakelt. Uit veiligheids overwegingen slaat Azure AD ook geen wachtwoord referenties op in een normale tekst vorm. Daarom kan Azure AD deze NTLM-of Kerberos-wachtwoord hashes niet automatisch genereren op basis van de bestaande referenties van gebruikers.

Azure AD Connect kunnen worden geconfigureerd voor het synchroniseren van de vereiste NTLM-of Kerberos-wachtwoord hashes voor Azure AD DS. Zorg ervoor dat u de stappen hebt voltooid om [Azure AD Connect in te scha kelen voor de synchronisatie van wacht woord-hashes][enable-azure-ad-connect]. Als u een bestaand exemplaar van Azure AD Connect hebt, [downloadt en bijwerkt u de nieuwste versie][azure-ad-connect-download] om ervoor te zorgen dat u de verouderde wachtwoord hashes voor NTLM en Kerberos kunt synchroniseren. Deze functionaliteit is niet beschikbaar in vroege releases van Azure AD Connect of met het hulp programma legacy DirSync. Azure AD Connect versie *1.1.614.0* of hoger is vereist.

> [!IMPORTANT]
> Azure AD Connect mag alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS omgevingen. Het is niet mogelijk om Azure AD Connect te installeren in een beheerd domein van Azure AD DS om objecten terug te synchroniseren naar Azure AD.

## <a name="enable-synchronization-of-password-hashes"></a>Synchronisatie van wacht woord-hashes inschakelen

Als Azure AD Connect is geïnstalleerd en geconfigureerd om te synchroniseren met Azure AD, configureert u nu de verouderde hash-synchronisatie voor wacht woord voor NTLM en Kerberos. Er wordt een Power shell-script gebruikt voor het configureren van de vereiste instellingen en het starten van een volledige wachtwoord synchronisatie met Azure AD. Wanneer Azure AD Connect het synchronisatie proces voor wacht woord-hash is voltooid, kunnen gebruikers zich aanmelden bij toepassingen via Azure AD DS die gebruikmaken van verouderde NTLM-of Kerberos-wachtwoord-hashes.

1. Open op de computer waarop Azure AD Connect is geïnstalleerd, in het menu Start de **Azure AD Connect >-synchronisatie service**.
1. Selecteer het tabblad **connectors** . De verbindings gegevens die worden gebruikt om de synchronisatie tussen de on-premises AD DS omgeving en Azure AD tot stand te brengen, worden weer gegeven.

    Het **type** geeft *Windows Azure Active Directory (micro soft)* aan voor de Azure AD-connector of *Active Directory Domain Services* voor de on-premises AD DS connector. Noteer de namen van connectors die in de volgende stap in het Power shell-script moeten worden gebruikt.

    ![De namen van connectors weer geven in Sync Service Manager](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    In dit voor beeld worden de volgende connectors gebruikt:

    * De Azure AD-connector heet *contoso.onmicrosoft.com-Aad*
    * De on-premises AD DS connector bevindt zich in de naam *onprem.contoso.com*

1. Kopieer en plak het volgende Power shell-script naar de computer waarop Azure AD Connect is geïnstalleerd. Met het script wordt een volledige wachtwoord synchronisatie geactiveerd met verouderde wacht woord-hashes. Werk de `$azureadConnector`-en `$adConnector` variabelen bij met de namen van de connectors uit de vorige stap.

    Voer dit script uit in elk AD-forest om on-premises account NTLM-en Kerberos-wachtwoord-hashes te synchroniseren met Azure AD.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    Afhankelijk van de grootte van uw map in termen van het aantal accounts en groepen, kan de synchronisatie van de verouderde wacht woord-hashes naar Azure AD enige tijd duren. De wacht woorden worden vervolgens gesynchroniseerd met het beheerde domein van Azure AD DS nadat het is gesynchroniseerd met Azure AD.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Waarom zijn oudere NTLM-en Kerberos-wachtwoord-hashes vereist
> * Verouderde wachtwoord-hash synchronisatie voor Azure AD Connect configureren

> [!div class="nextstepaction"]
> [Meer informatie over hoe synchronisatie werkt in een Azure AD Domain Services beheerd domein](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
