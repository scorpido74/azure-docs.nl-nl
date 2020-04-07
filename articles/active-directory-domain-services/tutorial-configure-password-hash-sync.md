---
title: Wachtwoordhashsynchronisatie inschakelen voor Azure AD Domain Services | Microsoft Documenten
description: Lees in deze zelfstudie hoe u wachtwoordhashsynchronisatie inschakelt met Azure AD Connect met een beheerd Azure Active Directory Domain Services-domein.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 4bf85a8e38a3cfc46fe4dbaf86639899e7267178
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676611"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Zelfstudie: Wachtwoordsynchronisatie inschakelen in Azure Active Directory Domain Services voor hybride omgevingen

Voor hybride omgevingen kan een Azure Active Directory-tenant (Azure AD) worden geconfigureerd om te synchroniseren met een on-premises AD DS-omgeving (Active Directory Domain Services) met Azure AD Connect. Azure AD Connect synchroniseert standaard geen verouderde NT LAN Manager (NTLM) en Kerberos wachtwoordhashes die nodig zijn voor Azure Active Directory Domain Services (Azure AD DS).

Als u Azure AD DS wilt gebruiken met accounts die zijn gesynchroniseerd vanuit een on-premises AD DS-omgeving, moet u Azure AD Connect configureren om de wachtwoordhashes te synchroniseren die nodig zijn voor NTLM- en Kerberos-verificatie. Nadat Azure AD Connect is geconfigureerd, synchroniseert een on-premises gebeurtenis voor het maken van een account of wachtwoordwijziging ook de verouderde wachtwoordhashes met Azure AD.

U hoeft deze stappen niet uit te voeren als u alleen cloudaccounts gebruikt zonder on-premises AD DS-omgeving.

In deze zelfstudie leert u:

> [!div class="checklist"]
> * Waarom legacy NTLM en Kerberos wachtwoord hashes nodig zijn
> * Verouderde wachtwoordhashsynchronisatie configureren voor Azure AD Connect

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende bronnen nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement dat is gesynchroniseerd met een on-premises directory met Azure AD Connect.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
    * Schakel indien nodig [Azure AD Connect in voor wachtwoordhashsynchronisatie.][enable-azure-ad-connect]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Maak en configureer indien nodig [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Synchronisatie van wachtwoordhash met Azure AD Connect

Azure AD Connect wordt gebruikt om objecten zoals gebruikersaccounts en groepen vanuit een on-premises AD DS-omgeving te synchroniseren met een Azure AD-tenant. Als onderdeel van het proces stelt wachtwoordhashsynchronisatie accounts in staat om hetzelfde wachtwoord te gebruiken in de on-prem AD DS-omgeving en Azure AD.

Azure AD DS heeft wachtwoordhashes nodig in een indeling die geschikt is voor NTLM- en Kerberos-verificatie om gebruikers op het beheerde domein te verifiëren. Azure AD slaat geen wachtwoordhashes op in de indeling die vereist is voor NTLM- of Kerberos-verificatie totdat u Azure AD DS inschakelt voor uw tenant. Om veiligheidsredenen slaat Azure AD ook geen wachtwoordreferenties op in formulier clear-text. Daarom kan Azure AD deze NTLM- of Kerberos-wachtwoordhashes niet automatisch genereren op basis van de bestaande referenties van gebruikers.

Azure AD Connect kan worden geconfigureerd om de vereiste NTLM- of Kerberos-wachtwoordhashes voor Azure AD DS te synchroniseren. Controleer of u de stappen hebt voltooid om Azure AD Connect in te [schakelen voor wachtwoordhashsynchronisatie.][enable-azure-ad-connect] Als u een bestaand exemplaar van Azure AD Connect hebt gehad, [downloadt en updatet][azure-ad-connect-download] u naar de nieuwste versie om ervoor te zorgen dat u de verouderde wachtwoordhashes voor NTLM en Kerberos synchroniseren. Deze functionaliteit is niet beschikbaar in vroege versies van Azure AD Connect of met het oudere DirSync-hulpprogramma. Azure AD Connect-versie *1.1.614.0* of hoger is vereist.

> [!IMPORTANT]
> Azure AD Connect mag alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS-omgevingen. Azure AD Connect wordt niet geïnstalleerd in een door Azure AD DS beheerd domein om objecten te synchroniseren met Azure AD.

## <a name="enable-synchronization-of-password-hashes"></a>Synchronisatie van wachtwoordhashes inschakelen

Nu Azure AD Connect is geïnstalleerd en geconfigureerd om te synchroniseren met Azure AD, configureert u nu de verouderde wachtwoordhashsynchronisatie voor NTLM en Kerberos. Een PowerShell-script wordt gebruikt om de vereiste instellingen te configureren en vervolgens een volledige wachtwoordsynchronisatie naar Azure AD te starten. Wanneer dat Azure AD Connect-wachtwoordhashsynchronisatieproces is voltooid, kunnen gebruikers zich aanmelden bij toepassingen via Azure AD DS die gebruikmaken van verouderde NTLM- of Kerberos-wachtwoordhashes.

1. Open op de computer waarop Azure AD Connect is geïnstalleerd, in het menu Start de **Azure AD Connect->-synchronisatieservice**.
1. Selecteer het tabblad **Connectors.** De verbindingsgegevens die worden gebruikt om de synchronisatie tussen de on-premises AD DS-omgeving en Azure AD vast te stellen, worden weergegeven.

    Het **type** geeft *Windows Azure Active Directory (Microsoft)* aan voor de Azure AD-connector of *Active Directory Domain Services* voor de on-premises AD DS-connector. Noteer de connectornamen die in de volgende stap in het PowerShell-script moeten worden gebruikt.

    ![De verbindingsnamen weergeven in Synchronisatieservicebeheer](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    In dit voorbeeld worden de volgende connectors gebruikt:

    * De Azure AD-connector heeft de naam *contoso.onmicrosoft.com - AAD*
    * De on-premises AD DS-connector heet *onprem.contoso.com*

1. Kopieer en plak het volgende PowerShell-script op de computer waarop Azure AD Connect is geïnstalleerd. Het script activeert een volledige wachtwoordsynchronisatie met verouderde wachtwoordhashes. Werk `$azureadConnector` de `$adConnector` variabelen en variabelen bij met de connectornamen uit de vorige stap.

    Voer dit script uit op elk AD-forest om on-premises account NTLM- en Kerberos-wachtwoordhashes te synchroniseren met Azure AD.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
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

    Afhankelijk van de grootte van uw directory in termen van aantal accounts en groepen, kan synchronisatie van de verouderde wachtwoordhashes naar Azure AD enige tijd in beslag nemen. De wachtwoorden worden vervolgens gesynchroniseerd met het door Azure AD DS beheerde domein nadat ze zijn gesynchroniseerd met Azure AD.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heb je geleerd:

> [!div class="checklist"]
> * Waarom legacy NTLM en Kerberos wachtwoord hashes nodig zijn
> * Verouderde wachtwoordhashsynchronisatie configureren voor Azure AD Connect

> [!div class="nextstepaction"]
> [Meer informatie over hoe synchronisatie werkt in een beheerd Azure AD Domain Services-domein](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
