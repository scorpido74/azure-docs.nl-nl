---
title: Azure AD Meerdere domeinen verbinden
description: In dit document wordt beschreven in het instellen en configureren van meerdere topleveldomeinen met O365 en Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18b5f19e3e994aa05fa99caf360d0c1be69ec7a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049773"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Ondersteuning voor meerdere domeinen voor federatie met Azure AD
In de volgende documentatie vindt u richtlijnen voor het gebruik van meerdere domeinen en subdomeinen op het hoogste niveau wanneer u zich bevindt met Office 365- of Azure AD-domeinen.

## <a name="multiple-top-level-domain-support"></a>Ondersteuning voor meerdere domeinen op het hoogste niveau
Voor het federatieen van meerdere domeinen op het hoogste niveau met Azure AD is een extra configuratie vereist die niet vereist is bij het federatieen met één domein op het hoogste niveau.

Wanneer een domein wordt gefedereerd met Azure AD, worden verschillende eigenschappen op het domein in Azure ingesteld.  Een belangrijke is IssuerUri.  Deze eigenschap is een URI die door Azure AD wordt gebruikt om het domein te identificeren waaraan het token is gekoppeld.  De URI hoeft niet op te lossen om iets, maar het moet een geldige URI.  Azure AD stelt de URI standaard in op de waarde van de federatieservice-id in uw on-premises AD FS-configuratie.

> [!NOTE]
> De federatieservice-id is een URI die op unieke wijze een federatieservice identificeert.  De federationservice is een instantie van AD FS die fungeert als de beveiligingstokenservice.
>
>

U de IssuerUri bekijken met `Get-MsolDomainFederationSettings -DomainName <your domain>`de opdracht PowerShell.

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Er doet zich een probleem voor wanneer u meer dan één domein op het hoogste niveau toevoegt.  Stel dat u een federatie hebt ingesteld tussen Azure AD en uw on-premises omgeving.  Voor dit document wordt het domein, bmcontoso.com gebruikt.  Nu is een tweede, top-level domein, bmfabrikam.com is toegevoegd.

![Domeinen](./media/how-to-connect-install-multiple-domains/domains.png)

Wanneer u probeert het bmfabrikam.com domein te converteren om te worden gefedereerd, treedt er een fout op.  De reden hiervoor is dat Azure AD een beperking heeft waardoor de eigenschap IssuerUri niet dezelfde waarde heeft voor meer dan één domein.  

![Federatiefout](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain Parameter
Om deze beperking te omzeilen, moet u een andere IssuerUri `-SupportMultipleDomain` toevoegen, wat kan worden gedaan met behulp van de parameter.  Deze parameter wordt gebruikt met de volgende cmdlets:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Met deze parameter configureert Azure AD de IssuerUri zodat deze is gebaseerd op de naam van het domein.  De IssuerUri zal uniek zijn in verschillende mappen in Azure AD.  Als u de parameter gebruikt, kan de powershell-opdracht met succes worden voltooid.

![Federatiefout](./media/how-to-connect-install-multiple-domains/convert.png)

Als u de instellingen voor het bmfabrikam.com-domein bekijkt, ziet u het volgende:

![Federatiefout](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain`wijzigt de andere eindpunten niet, die nog steeds zijn geconfigureerd om te wijzen op de federatieservice op adfs.bmcontoso.com.

Een ander `-SupportMultipleDomain` ding dat dat doet is dat het ervoor zorgt dat het AD FS-systeem de juiste uitgeverwaarde bevat in tokens die zijn uitgegeven voor Azure AD. Deze waarde wordt ingesteld door het domeingedeelte van de gebruikers UPN in te nemen en in te stellen als domein in de IssuerUri, d.w.z. https://{upn suffix}/adfs/services/trust.

Tijdens de verificatie naar Azure AD of Office 365 wordt het element IssuerUri in het token van de gebruiker dus gebruikt om het domein in Azure AD te vinden.  Als er geen overeenkomst kan worden gevonden, mislukt de verificatie.

Als het UPN van een bsimon@bmcontoso.comgebruiker bijvoorbeeld het element IssuerUri in het token, `http://bmcontoso.com/adfs/services/trust`AD FS-emissies, is ingesteld op . Dit element komt overeen met de Azure AD-configuratie en verificatie wordt geslaagd.

Het volgende is de aangepaste claimregel die deze logica implementeert:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> Als u de schakelaar -SupportMultipleDomain wilt gebruiken wanneer u nieuwe of reeds bestaande domeinen probeert toe te voegen of converteert, moet uw federatieve vertrouwensrelatie al zijn ingesteld om deze te ondersteunen.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>De vertrouwensrelatie tussen AD FS en Azure AD bijwerken
Als u de federatieve vertrouwensrelatie tussen AD FS en uw exemplaar van Azure AD niet hebt ingesteld, moet u deze vertrouwensrelatie mogelijk opnieuw maken.  De reden hiervoor is, wanneer het `-SupportMultipleDomain` oorspronkelijk is ingesteld zonder de parameter, de IssuerUri is ingesteld met de standaardwaarde.  In de screenshot hieronder zie je dat `https://adfs.bmcontoso.com/adfs/services/trust`de IssuerUri is ingesteld op .

Als u een nieuw domein hebt toegevoegd aan de Azure `Convert-MsolDomaintoFederated -DomainName <your domain>`AD-portal en vervolgens probeert het te converteren met behulp van, krijgt u de volgende fout.

![Federatiefout](./media/how-to-connect-install-multiple-domains/trust1.png)

Als u de `-SupportMultipleDomain` schakelaar probeert toe te voegen, ontvangt u de volgende fout:

![Federatiefout](./media/how-to-connect-install-multiple-domains/trust2.png)

Gewoon proberen `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` te draaien op het oorspronkelijke domein zal ook resulteren in een fout.

![Federatiefout](./media/how-to-connect-install-multiple-domains/trust3.png)

Gebruik de onderstaande stappen om een extra domein op het hoogste niveau toe te voegen.  Als u al een domein hebt toegevoegd `-SupportMultipleDomain` en de parameter niet hebt gebruikt, begint u met de stappen voor het verwijderen en bijwerken van uw oorspronkelijke domein.  Als u nog geen domein op het hoogste niveau hebt toegevoegd, u beginnen met de stappen voor het toevoegen van een domein met PowerShell van Azure AD Connect.

Gebruik de volgende stappen om de microsoft online-vertrouwensrelatie te verwijderen en uw oorspronkelijke domein bij te werken.

1. Open **AD FS-beheer** op uw AD FS-federatieserver.
2. Aan de linkerkant **vertrouwensrelaties** en **vertrouwensrelaties van relying party** uitbreiden
3. Verwijder aan de rechterkant de vermelding van het **Microsoft Office 365 Identity Platform.**
   ![Microsoft Online verwijderen](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Op een machine waarop [Azure Active Directory Module voor Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) is geïnstalleerd, wordt het volgende uitgevoerd: `$cred=Get-Credential`.  
5. Voer de gebruikersnaam en het wachtwoord in van een globale beheerder voor het Azure AD-domein waarmee u zich bevindt.
6. Voer in PowerShell`Connect-MsolService -Credential $cred`
7. Voer in PowerShell . `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`  Deze update is voor het oorspronkelijke domein.  Dus met behulp van de bovenstaande domeinen zou het zijn:`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

De volgende stappen gebruiken om het nieuwe domein op het hoogste niveau toe te voegen met PowerShell

1. Op een machine waarop [Azure Active Directory Module voor Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) is geïnstalleerd, wordt het volgende uitgevoerd: `$cred=Get-Credential`.  
2. Voer de gebruikersnaam en het wachtwoord in van een globale beheerder voor het Azure AD-domein waarmee u zich bevindt
3. Voer in PowerShell`Connect-MsolService -Credential $cred`
4. Voer in PowerShell`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Gebruik de volgende stappen om het nieuwe domein op het hoogste niveau toe te voegen met Azure AD Connect.

1. Azure AD Connect starten vanaf het bureaublad of het startmenu
2. Kies 'Een extra Azure ![AD-domein toevoegen' Een extra Azure AD-domein toevoegen](./media/how-to-connect-install-multiple-domains/add1.png)
3. Voer uw Azure AD- en Active Directory-referenties in
4. Selecteer het tweede domein dat u wilt configureren voor federatie.
   ![Een extra Azure AD-domein toevoegen](./media/how-to-connect-install-multiple-domains/add2.png)
5. Klik op Installeren

### <a name="verify-the-new-top-level-domain"></a>Het nieuwe domein op het hoogste niveau verifiëren
Met de opdracht `Get-MsolDomainFederationSettings -DomainName <your domain>`PowerShell u de bijgewerkte IssuerUri bekijken.  De screenshot hieronder toont de federatie-instellingen zijn bijgewerkt op het oorspronkelijke domein`http://bmcontoso.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

En de IssuerUri op het nieuwe domein is ingesteld op`https://bmfabrikam.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Ondersteuning voor subdomeinen
Wanneer u een subdomein toevoegt, vanwege de manier waarop Azure AD domeinen heeft verwerkt, worden de instellingen van de bovenliggende overnemende tekst overgenomen.  Dus, de IssuerUri, moet overeenkomen met de ouders.

Laten we bijvoorbeeld zeggen dat ik bmcontoso.com en dan corp.bmcontoso.com toevoegen.  De IssuerUri voor een gebruiker uit corp.bmcontoso.com moet ** http://bmcontoso.com/adfs/services/trust.**  De bovenstaande standaardregel voor Azure AD genereert echter een token met een uitgever als ** http://corp.bmcontoso.com/adfs/services/trust.** die niet overeenkomt met de vereiste waarde van het domein en verificatie zal mislukken.

### <a name="how-to-enable-support-for-subdomains"></a>Ondersteuning voor subdomeinen inschakelen
Om dit gedrag te omzeilen, moet het vertrouwen van de AD FS-partij voor Microsoft Online worden bijgewerkt.  Hiervoor moet u een aangepaste claimregel configureren, zodat alle subdomeinen worden verwijderd van het UPN-achtervoegsel van de gebruiker bij het samenstellen van de aangepaste uitgeverwaarde.

De volgende claim zal dit doen:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Het laatste getal in de reguliere expressieset is het aantal bovenliggende domeinen dat er in uw hoofddomein zijn. Hier wordt bmcontoso.com gebruikt, dus twee bovenliggende domeinen zijn noodzakelijk. Als drie bovenliggende domeinen zouden worden bewaard (d.w.z. corp.bmcontoso.com), dan zou het aantal drie geweest zijn. Uiteindelijk kan een bereik worden aangegeven, de match zal altijd worden gemaakt om het maximum van domeinen te evenaren. "{2,3}" komt overeen met twee tot drie domeinen (d.w.z. bmfabrikam.com en corp.bmcontoso.com).

Gebruik de volgende stappen om een aangepaste claim toe te voegen om subdomeinen te ondersteunen.

1. AD FS-beheer openen
2. Klik met de rechtermuisknop op de vertrouwensrelatie van Microsoft Online RP en kies Claimregels bewerken
3. Selecteer de derde claimregel ![en vervang claim bewerken](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Vervang de huidige claim:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Claim vervangen](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Klik op Ok.  Klik op Toepassen.  Klik op Ok.  Sluit AD FS-beheer.

## <a name="next-steps"></a>Volgende stappen
Nu u Azure AD Connect geïnstalleerd hebt, kunt u [de installatie verifiëren en licenties toewijzen](how-to-connect-post-installation.md).

Lees meer over deze functies, die tijdens de installatie zijn ingeschakeld: [Automatische upgrade](how-to-connect-install-automatic-upgrade.md), [Onopzettelijk verwijderen voorkomen](how-to-connect-sync-feature-prevent-accidental-deletes.md) en [Azure AD Connect Health](how-to-connect-health-sync.md).

Lees meer over deze veelvoorkomende onderwerpen: [Scheduler en het activeren van de synchronisatie](how-to-connect-sync-feature-scheduler.md).

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
