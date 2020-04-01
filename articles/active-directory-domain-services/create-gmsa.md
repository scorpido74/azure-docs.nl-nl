---
title: Beheerde serviceaccounts voor groepen voor Azure AD-domeinservices | Microsoft Documenten
description: Meer informatie over het maken van een groepsbeheerserviceaccount (gMSA) voor gebruik met beheerde azure Directory Domain Services-domeinen
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 95269fbe38ae21d0b9761f67adee681ae896a8ab
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476341"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Een groepsbeheerserviceaccount (gMSA) maken in Azure AD Domain Services

Toepassingen en services hebben vaak een identiteit nodig om zichzelf te verifiëren met andere bronnen. Een webservice moet bijvoorbeeld worden geverifieerd met een databaseservice. Als een toepassing of service meerdere exemplaren heeft, zoals een webserverfarm, wordt het handmatig maken en configureren van de identiteiten voor die resources tijdrovend.

In plaats daarvan kan een groepsbeheerserviceaccount (gMSA) worden gemaakt in het azure active directory domain services (Azure AD DS) beheerde domein. Het Windows-besturingssysteem beheert automatisch de referenties voor een gMSA, wat het beheer van grote groepen resources vereenvoudigt.

In dit artikel ziet u hoe u een gMSA maakt in een door Azure AD DS beheerd domein met Azure PowerShell.

## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Vul indien nodig de zelfstudie in om [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance]te maken en te configureren.
* Een Windows Server-beheer-VM die is verbonden met het beheerde Azure AD DS-domein.
    * Vul indien nodig de zelfstudie in om [een beheer-vm][tutorial-create-management-vm]te maken.

## <a name="managed-service-accounts-overview"></a>Overzicht van beheerde serviceaccounts

Een standalone managed service account (sMSA) is een domeinaccount waarvan het wachtwoord automatisch wordt beheerd. Deze aanpak vereenvoudigt het beheer van de serviceprincipal name (SPN) en maakt gedelegeerd beheer mogelijk aan andere beheerders. U hoeft geen referenties voor het account handmatig te maken en te roteren.

Een groepsmanaged service account (gMSA) biedt dezelfde beheervereenvoudiging, maar voor meerdere servers in het domein. Met een gMSA kunnen alle exemplaren van een service die op een serverfarm worden gehost, dezelfde serviceprincipal gebruiken voor wederzijdse verificatieprotocollen om te werken. Wanneer een gMSA wordt gebruikt als serviceprincipal, beheert het Windows-besturingssysteem opnieuw het wachtwoord van het account in plaats van te vertrouwen op de beheerder.

Zie [het overzicht van groepsbeheeraccounts (gMSA) voor][gmsa-overview]meer informatie .

## <a name="using-service-accounts-in-azure-ad-ds"></a>Serviceaccounts gebruiken in Azure AD DS

Aangezien beheerde Azure AD DS-domeinen zijn vergrendeld en beheerd door Microsoft, zijn er enkele overwegingen bij het gebruik van serviceaccounts:

* Serviceaccounts maken in aangepaste organisatie-eenheden (OU) op het beheerde domein.
    * U geen serviceaccount maken in de ingebouwde *AADDC-gebruikers* of *AADDC-computers-gegevens.*
    * Maak in plaats daarvan [een aangepaste organisatie-eenheid][create-custom-ou] in het beheerde Azure AD DS-domein en maak vervolgens serviceaccounts in die aangepaste organisatie-eenheid.
* De hoofdtoets Key Distribution Services (KDS) is vooraf gemaakt.
    * De KDS-rootsleutel wordt gebruikt om wachtwoorden voor gMSA's te genereren en op te halen. In Azure AD DS wordt de KDS-root voor u gemaakt.
    * U hebt geen bevoegdheden om een andere te maken of de standaardkds-hoofdtoets weer te geven.

## <a name="create-a-gmsa"></a>Een gMSA maken

Maak eerst een aangepaste organisatie-eenheid met de cmdlet [Nieuw-ADOrganizationalUnit.][New-AdOrganizationalUnit] Zie Aangepaste GEGEVENS in Azure AD [DS][create-custom-ou]voor meer informatie over het maken en beheren van aangepaste gegevens.

> [!TIP]
> Als u deze stappen wilt uitvoeren om een gMSA te maken, [gebruikt u uw beheer-VM.][tutorial-create-management-vm] Deze beheer-VM moet al over de vereiste AD PowerShell-cmdlets en verbinding met het beheerde domein beschikken.

In het volgende voorbeeld wordt een aangepaste ou met de naam *myNewOU* gemaakt in het door Azure AD DS beheerde domein met de naam *aaddscontoso.com*. Gebruik je eigen ou en beheerde domeinnaam:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Maak nu een gMSA met de cmdlet [Nieuw-ADServiceAccount.][New-ADServiceAccount] De volgende voorbeeldparameters worden gedefinieerd:

* **-Naam** is ingesteld op *WebFarmSvc*
* **-De** parameter Pad geeft de aangepaste ou op voor de gMSA die in de vorige stap is gemaakt.
* DNS-vermeldingen en servicehoofdnamen zijn ingesteld voor *WebFarmSvc.aaddscontoso.com*
* Opdrachtgevers in *AADDSCONTOSO-SERVER$* mogen het wachtwoord ophalen met behulp van de identiteit.

Geef uw eigen namen en domeinnamen op.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

Toepassingen en services kunnen nu worden geconfigureerd om de gMSA te gebruiken als dat nodig is.

## <a name="next-steps"></a>Volgende stappen

Zie [Aan de slag met groepsbeheerserviceaccounts][gmsa-start]voor meer informatie over gMSA's .

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
