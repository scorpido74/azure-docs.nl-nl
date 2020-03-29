---
title: Certificaatverlenging voor Office 365- en Azure AD-gebruikers | Microsoft Documenten
description: In dit artikel worden Office 365-gebruikers uitgelegd hoe ze problemen met e-mails kunnen oplossen die hen informeren over het vernieuwen van een certificaat.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d98a1aabef2de505e66b2127226b9e89cd791e20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60244840"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Federatiecertificaten voor Office 365 en Azure Active Directory verlengen
## <a name="overview"></a>Overzicht
Voor een succesvolle federatie tussen Azure Active Directory (Azure AD) en Active Directory Federation Services (AD FS) moeten de certificaten die DOOR AD FS worden gebruikt om beveiligingstokens aan Azure AD te ondertekenen, overeenkomen met wat is geconfigureerd in Azure AD. Elke mismatch kan leiden tot gebroken vertrouwen. Azure AD zorgt ervoor dat deze informatie gesynchroniseerd wordt gehouden wanneer u AD FS en Web Application Proxy implementeert (voor extranettoegang).

In dit artikel vindt u aanvullende informatie om uw tokenondertekeningscertificaten te beheren en deze in de volgende gevallen synchroon te houden met Azure AD:

* U implementeert de proxy van de webtoepassing niet en daarom zijn de federatiemetagegevens niet beschikbaar in het extranet.
* U gebruikt de standaardconfiguratie van AD FS niet voor tokenondertekeningscertificaten.
* U gebruikt een externe identiteitsprovider.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Standaardconfiguratie van AD FS voor tokenondertekeningscertificaten
De tokensigning- en tokendecrypting-certificaten zijn meestal zelfondertekende certificaten en zijn goed voor een jaar. Ad FS bevat standaard een automatisch vernieuwingsproces met de naam **AutoCertificateRollover**. Als u AD FS 2.0 of hoger gebruikt, worden uw certificaat automatisch bijgewerkt in Office 365 en Azure AD voordat het verloopt.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Meldingen vernieuwen vanuit het Microsoft 365-beheercentrum of een e-mail
> [!NOTE]
> Zie Wijzigingen beheren in certificaten voor [tokenondertekenen](#managecerts) als u een e-mail of een portalmelding hebt ontvangen waarin u wordt gevraagd uw certificaat voor Office te vernieuwen. Microsoft is op de hoogte van een mogelijk probleem dat kan leiden tot meldingen voor certificaatverlenging, zelfs als er geen actie vereist is.
>
>

Azure AD probeert de federatiemetagegevens te controleren en de tokenondertekeningscertificaten bij te werken zoals aangegeven met deze metagegevens. 30 dagen voor het verstrijken van de tokenondertekeningscertificaten controleert Azure AD of er nieuwe certificaten beschikbaar zijn door de metagegevens van de federatie te peilen.

* Als de federatie metagegevens met succes kan worden gepeild en de nieuwe certificaten kan ophalen, wordt er geen e-mailmelding of waarschuwing in het Microsoft 365-beheercentrum aan de gebruiker uitgegeven.
* Als de nieuwe tokenondertekeningscertificaten niet kunnen worden opgehaald, omdat de metagegevens van de federatie niet bereikbaar zijn of automatische certificaatrollover niet is ingeschakeld, geeft Azure AD een e-mailmelding en een waarschuwing uit in het Microsoft 365-beheercentrum.

![Office 365-portalmelding](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Als u AD FS gebruikt om de bedrijfscontinuïteit te garanderen, controleert u of uw servers de volgende updates hebben, zodat verificatiefouten voor bekende problemen niet optreden. Dit vermindert bekende AD FS-proxyserverproblemen voor deze verlengings- en toekomstige verlengingsperioden:
>
> Server 2012 R2 - [Rollup van Windows Server mei 2014](https://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 en 2012 - [Verificatie via proxy mislukt in Windows Server 2012 of Windows 2008 R2 SP1](https://support.microsoft.com/kb/3094446)
>
>

## <a name="check-if-the-certificates-need-to-be-updated"></a>Controleren of de certificaten moeten worden bijgewerkt<a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Stap 1: De status AutoCertificateRollover controleren
Open PowerShell op uw AD FS-server. Controleer of de waarde AutoCertificateRollover is ingesteld op True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Als u AD FS 2.0 gebruikt, voert u eerst Add-Pssnapin Microsoft.Adfs.Powershell uit.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Stap 2: Bevestigen dat AD FS en Azure AD gesynchroniseerd zijn
Open op uw AD FS-server de MSOnline PowerShell-prompt en maak verbinding met Azure AD.

> [!NOTE]
> MSOL-Cmdlets maken deel uit van de MSOnline PowerShell module.
> U de MSOnline PowerShell Module rechtstreeks downloaden vanuit de PowerShell Gallery.
> 
>

    Install-Module MSOnline

Maak verbinding met Azure AD met de MSOnline PowerShell-module.

    Import-Module MSOnline
    Connect-MsolService

Controleer de certificaten die zijn geconfigureerd in AD FS- en Azure AD-vertrouwenseigenschappen voor het opgegeven domein.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Als de duimafdrukken in beide uitvoerresultaten overeenkomen, worden uw certificaten gesynchroniseerd met Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Stap 3: Controleren of uw certificaat bijna verloopt
Controleer in de uitvoer van Get-MsolFederationProperty of Get-AdfsCertificate de datum onder 'Niet na'. Als de datum minder dan 30 dagen weg is, moet u actie ondernemen.

| AutoCertificateRollover | Certificaten die synchroon zijn met Azure AD | Federatiemetagegevens zijn openbaar toegankelijk | Geldigheid | Actie |
|:---:|:---:|:---:|:---:|:---:|
| Ja |Ja |Ja |- |Geen actie nodig. Zie [certificaat voor tokenondertekening automatisch verlengen](#autorenew). |
| Ja |Nee |- |Minder dan 15 dagen |Onmiddellijk verlengen. Zie [Certificaat voor tokenondertekening handmatig verlengen](#manualrenew). |
| Nee |- |- |Minder dan 30 dagen |Onmiddellijk verlengen. Zie [Certificaat voor tokenondertekening handmatig verlengen](#manualrenew). |

\[-] Maakt niet uit

## <a name="renew-the-token-signing-certificate-automatically-recommended"></a>Het certificaat voor het ondertekenen van certificaten automatisch verlengen (aanbevolen)<a name="autorenew"></a>
U hoeft geen handmatige stappen uit te voeren als beide van de volgende stappen waar zijn:

* U hebt Web Application Proxy geïmplementeerd, waarmee toegang tot de federatiemetagegevens van het extranet kan worden ingeschakeld.
* U gebruikt de standaardconfiguratie van AD FS (AutoCertificateRollover is ingeschakeld).

Controleer het volgende om te controleren of het certificaat automatisch kan worden bijgewerkt.

**1. De ad fs-eigenschap AutoCertificateRollover moet zijn ingesteld op True.** Dit geeft aan dat AD FS automatisch nieuwe certificaten voor tokenondertekening en tokendecryptie genereert, voordat de oude verlopen.

**2. De metadata van de AD FS-federatie is openbaar toegankelijk.** Controleer of uw federatiemetagegevens openbaar toegankelijk zijn door vanaf een computer op het openbare internet naar de volgende URL te navigeren (buiten het bedrijfsnetwerk):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

waar `(your_FS_name)` wordt vervangen door de hostnaam van de federatieservice die uw organisatie gebruikt, zoals fs.contoso.com.  Als u beide instellingen met succes verifiëren, hoeft u niets anders te doen.  

Voorbeeld: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## <a name="renew-the-token-signing-certificate-manually"></a>Het certificaat voor tokenondertekening handmatig verlengen<a name="manualrenew"></a>
U ervoor kiezen om de tokenondertekeningscertificaten handmatig te verlengen. De volgende scenario's werken bijvoorbeeld beter voor handmatige vernieuwing:

* Tokenondertekeningscertificaten zijn geen zelfondertekende certificaten. De meest voorkomende reden hiervoor is dat uw organisatie AD FS-certificaten beheert die zijn ingeschreven bij een certificeringsinstantie voor een organisatiecertificaat.
* Netwerkbeveiliging staat niet toe dat de federatiemetagegevens openbaar beschikbaar zijn.

In deze scenario's moet u elke keer dat u de tokenondertekeningscertificaten bijwerkt, ook uw Office 365-domein bijwerken met behulp van de powershell-opdracht Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Stap 1: Ervoor zorgen dat AD FS nieuwe tokenondertekeningscertificaten heeft
**Niet-standaardconfiguratie**

Als u een niet-standaardconfiguratie van AD FS gebruikt (waarbij **AutoCertificateRollover** is ingesteld op **False),** gebruikt u waarschijnlijk aangepaste certificaten (niet zelf ondertekend). Zie Richtlijnen voor klanten die zelfondertekende certificaten van AD FS niet gebruiken voor meer informatie over het verlengen van de ad [FS-tokenondertekeningscertificaten.](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert)

**Federatiemetagegevens zijn niet openbaar beschikbaar**

Als **AutoCertificateRollover daarentegen** is ingesteld op **True,** maar uw federatiemetagegevens niet openbaar toegankelijk zijn, moet u er eerst voor zorgen dat er nieuwe tokenondertekeningscertificaten zijn gegenereerd door AD FS. Bevestig dat u nieuwe tokenondertekeningscertificaten hebt door de volgende stappen te nemen:

1. Controleer of u bent aangemeld bij de primaire AD FS-server.
2. Controleer de huidige ondertekeningscertificaten in AD FS door een PowerShell-opdrachtvenster te openen en de volgende opdracht uit te voeren:

    PS C:\>Get-ADFSCertificate –CertificateType-tokenondertekenen

   > [!NOTE]
   > Als u AD FS 2.0 gebruikt, moet u eerst Add-Pssnapin Microsoft.Adfs.Powershell uitvoeren.
   >
   >
3. Bekijk de opdrachtuitvoer bij alle vermelde certificaten. Als AD FS een nieuw certificaat heeft gegenereerd, ziet u twee certificaten in de uitvoer: een waarvoor de **IsPrimary-waarde** **True** is en de **NotAfter-datum** binnen 5 dagen is en een certificaat waarvoor **IsPrimary** **Onwaar** is en **NotAfter** ongeveer een jaar in de toekomst.
4. Als u slechts één certificaat ziet en de **NotAfter-datum** binnen 5 dagen is, moet u een nieuw certificaat genereren.
5. Als u een nieuw certificaat wilt genereren, voert `PS C:\>Update-ADFSCertificate –CertificateType token-signing`u de volgende opdracht uit bij een PowerShell-opdrachtprompt: .
6. Controleer de update door opnieuw de volgende\>opdracht uit te voeren: PS C: Get-ADFSCertificate –CertificateType-tokenondertekenen

Er moeten nu twee certificaten worden vermeld, waarvan er één een **NotAfter-datum** heeft van ongeveer een jaar in de toekomst en waarvoor de **IsPrimary-waarde** **onwaar**is.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Stap 2: De nieuwe tokenondertekeningscertificaten bijwerken voor de Office 365-vertrouwensrelatie
Werk Office 365 als volgt bij met de nieuwe tokenondertekeningscertificaten die voor de vertrouwensrelatie moeten worden gebruikt.

1. Open de Microsoft Azure Active Directory Module voor Windows PowerShell.
2. Voer $cred=Get-Credential uit. Wanneer deze cmdlet u om referenties vraagt, typt u uw accountreferenties voor cloudservicebeheerders.
3. Voer Connect-MsolService –Credential $cred uit. Deze cmdlet verbindt u met de cloudservice. Het maken van een context die u verbindt met de cloudservice is vereist voordat een van de extra cmdlets die door het hulpprogramma zijn geïnstalleerd, wordt uitgevoerd.
4. Als u deze opdrachten uitvoert op een computer die niet de primaire federatieserver van AD &lt;FS is,&gt;voert &lt;u de&gt; primaire server Set-MSOLAdfscontext -Computer AD FS uit, waarbij de primaire AD FS-server de interne FQDN-naam van de primaire AD FS-server is. Deze cmdlet creëert een context die u verbindt met AD FS.
5. Uitvoeren Update-MSOLFederatedDomain –DomainName-domein &lt;&gt;. Deze cmdlet werkt de instellingen van AD FS bij in de cloudservice en configureert de vertrouwensrelatie tussen de twee.

> [!NOTE]
> Als u meerdere domeinen op het hoogste niveau moet ondersteunen, zoals contoso.com en fabrikam.com, moet u de **supportmultipledomain-schakelaar** gebruiken met alle cmdlets. Zie [Ondersteuning voor meerdere topleveldomeinen voor](how-to-connect-install-multiple-domains.md)meer informatie .
>


## <a name="repair-azure-ad-trust-by-using-azure-ad-connect"></a>Azure AD-vertrouwensrelatie herstellen met Azure AD Connect<a name="connectrenew"></a>
Als u uw AD FS-farm en Azure AD-vertrouwensrelatie hebt geconfigureerd met Azure AD Connect, u Azure AD Connect gebruiken om te detecteren of u actie moet ondernemen voor uw tokenondertekeningscertificaten. Als u de certificaten moet vernieuwen, u Azure AD Connect gebruiken om dit te doen.

Zie [Het vertrouwen herstellen voor](how-to-connect-fed-management.md)meer informatie.

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Updatestappen AD FS en Azure AD-certificaatupdate
Tokenondertekeningscertificaten zijn standaard X509-certificaten die worden gebruikt om alle tokens die de federatieserver uitgeeft, veilig te ondertekenen. Tokendecryptiecertificaten zijn standaard X509-certificaten die worden gebruikt om binnenkomende tokens te decoderen. 

Ad FS is standaard geconfigureerd om certificaten voor tokenondertekening en tokendecryptie automatisch te genereren, zowel op het beginmoment als wanneer de certificaten hun vervaldatum naderen.

Azure AD probeert 30 dagen voor het verstrijken van het huidige certificaat een nieuw certificaat uit de metagegevens van uw federatieservice op te halen. Als er op dat moment geen nieuw certificaat beschikbaar is, blijft Azure AD de metagegevens regelmatig dagelijks controleren. Zodra het nieuwe certificaat beschikbaar is in de metagegevens, worden de federatie-instellingen voor het domein bijgewerkt met de nieuwe certificaatgegevens. U kunt `Get-MsolDomainFederationSettings` controleren of u het nieuwe certificaat ziet in het NextSigningCertificate / SigningCertificate.

Zie Certificaten voor [tokenondertekening en tokendecryptie voor AD FS verkrijgen en configureren voor](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs) meer informatie over tokenondertekeningscertificaten in AD FS
