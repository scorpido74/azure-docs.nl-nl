---
title: Zelfstudie - LDAPS configureren voor Azure Active Directory Domain Services | Microsoft Documenten
description: In deze zelfstudie leert u hoe u het LDAPS (Secure Lightweight Directory Access Protocol) configureert voor een beheerd Azure Active Directory Domain Services-domein.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 6db2c907abc495ca3c88e1e73e885043a8f19997
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79481531"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Zelfstudie: Beveiligde LDAP configureren voor een beheerd Azure Active Directory Domain Services-domein

Als u wilt communiceren met uw azure active directory domain services (Azure AD DS)-domein, wordt het LDAP (Lightweight Directory Access Protocol) gebruikt. Standaard is het LDAP-verkeer niet versleuteld, wat een beveiligingsprobleem is voor veel omgevingen. Met Azure AD DS u het beheerde domein configureren om het beveiligde Lightweight Directory Access Protocol (LDAPS) te gebruiken. Wanneer u veilige LDAP gebruikt, wordt het verkeer versleuteld. Secure LDAP is ook bekend als LDAP over Secure Sockets Layer (SSL) / Transport Layer Security (TLS).

In deze zelfstudie ziet u hoe u LDAPS configureert voor een door Azure AD DS beheerd domein.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een digitaal certificaat maken voor gebruik met Azure AD DS
> * Beveiligde LDAP inschakelen voor Azure AD DS
> * Veilige LDAP configureren voor gebruik via het openbare internet
> * Bind en test beveiligde LDAP voor een door Azure AD DS beheerd domein

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Maak en configureer indien nodig [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance].
* Het *ldp.exe-gereedschap* dat op uw computer is geïnstalleerd.
    * Installeer indien nodig [de RSAT (Remote Server Administration Tools)][rsat] voor *Active Directory Domain Services en LDAP.*

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelfstudie configureert u beveiligde LDAP voor het door Azure AD DS beheerde domein met behulp van de Azure-portal. Meld u eerst aan bij de [Azure-portal](https://portal.azure.com)om aan de slag te gaan.

## <a name="create-a-certificate-for-secure-ldap"></a>Een certificaat maken voor beveiligde LDAP

Om veilige LDAP te gebruiken, wordt een digitaal certificaat gebruikt om de communicatie te versleutelen. Dit digitale certificaat wordt toegepast op uw door Azure AD DS beheerde domein en laat hulpprogramma's zoals *LDP.exe* veilige versleutelde communicatie gebruiken bij het opvragen van gegevens. Er zijn twee manieren om een certificaat te maken voor beveiligde LDAP-toegang tot het beheerde domein:

* Een certificaat van een openbare certificeringsinstantie (CA) of een bedrijfs-CA.
    * Als uw organisatie certificaten van een openbare CA krijgt, krijgt u het beveiligde LDAP-certificaat van die openbare CA. Als u een enterprise CA in uw organisatie gebruikt, krijgt u het beveiligde LDAP-certificaat van de enterprise CA.
    * Een openbare CA werkt alleen wanneer u een aangepaste DNS-naam gebruikt met uw door Azure AD DS beheerde domein. Als de DNS-domeinnaam van uw beheerde domein eindigt in *.onmicrosoft.com,* u geen digitaal certificaat maken om de verbinding met dit standaarddomein te beveiligen. Microsoft is eigenaar van het onmicrosoft.com domein *.onmicrosoft.com,* zodat een openbare CA geen certificaat afgeeft. Maak in dit scenario een zelfondertekend certificaat en gebruik dat om beveiligde LDAP te configureren.
* Een zelfondertekend certificaat dat u zelf maakt.
    * Deze aanpak is goed voor het testen van doeleinden, en is wat deze tutorial laat zien.

Het certificaat dat u aanvraagt of maakt, moet aan de volgende vereisten voldoen. Uw beheerde domein ondervindt problemen als u beveiligde LDAP inschakelt met een ongeldig certificaat:

* **Vertrouwde uitgever** - Het certificaat moet worden uitgegeven door een autoriteit die wordt vertrouwd door computers die verbinding maken met het beheerde domein met behulp van beveiligde LDAP. Deze autoriteit kan een openbare CA of een Enterprise CA zijn die door deze computers wordt vertrouwd.
* **Levensduur** - Het certificaat moet ten minste de komende 3-6 maanden geldig zijn. Beveiligde LDAP-toegang tot uw beheerde domein wordt verstoord wanneer het certificaat verloopt.
* **Onderwerpnaam** - De onderwerpnaam op het certificaat moet uw beheerde domein zijn. Als uw domein bijvoorbeeld *aaddscontoso.com*de naam heeft, moet de onderwerpnaam van het certificaat **.aaddscontoso.com*zijn.
    * De DNS-naam of de alternatieve onderwerpnaam van het certificaat moet een wildcardcertificaat zijn om ervoor te zorgen dat de beveiligde LDAP naar behoren werkt met de Azure AD Domain Services. Domeincontrollers gebruiken willekeurige namen en kunnen worden verwijderd of toegevoegd om ervoor te zorgen dat de service beschikbaar blijft.
* **Sleutelgebruik** - Het certificaat moet zijn geconfigureerd voor *digitale handtekeningen* en *sleutelcodering.*
* **Certificaatdoel** - Het certificaat moet geldig zijn voor TLS-serververificatie.

Er zijn verschillende tools beschikbaar om zelfondertekende certificaten te maken, zoals OpenSSL, Keytool, MakeCert, [New-SelfSignedCertificate][New-SelfSignedCertificate] cmdlet etc. Laten we in deze zelfstudie een zelfondertekend certificaat maken voor beveiligde LDAP met de cmdlet [Nieuw-SelfSignedCertificate.][New-SelfSignedCertificate] Open een PowerShell-venster als **beheerder** en voer de volgende opdrachten uit. Vervang de *$dnsName* variabele door de DNS-naam die wordt gebruikt door uw eigen beheerde domein, zoals *aaddscontoso.com:*

```powershell
# Define your own DNS name used by your Azure AD DS managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

In de volgende voorbeelduitvoerwordt weergegeven dat het certificaat is gegenereerd en wordt opgeslagen in het lokale certificaatarchief (*LocalMachine\MY):*

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>Vereiste certificaten begrijpen en exporteren

Om veilige LDAP te gebruiken, wordt het netwerkverkeer versleuteld met behulp van de openbare sleutelinfrastructuur (PKI).

* Er wordt een **privésleutel** toegepast op het beheerde Azure AD DS-domein.
    * Deze privésleutel wordt gebruikt om het beveiligde LDAP-verkeer te *decoderen.* De privésleutel mag alleen worden toegepast op het beheerde Azure AD DS-domein en niet op grote schaal worden gedistribueerd naar clientcomputers.
    * Een certificaat met de privésleutel maakt gebruik van de *. PFX-bestandsindeling.*
* Er wordt een **openbare** sleutel toegepast op de clientcomputers.
    * Deze openbare sleutel wordt gebruikt om het beveiligde LDAP-verkeer te *versleutelen.* De openbare sleutel kan worden gedistribueerd naar clientcomputers.
    * Certificaten zonder de privésleutel gebruiken de *. *CER-bestandsindeling.

Deze twee sleutels, de *privé-* en *openbare* sleutels, zorgen ervoor dat alleen de juiste computers met elkaar kunnen communiceren. Als u een openbare CA of enterprise CA gebruikt, krijgt u een certificaat met de privésleutel en kan deze worden toegepast op een door Azure AD DS beheerd domein. De openbare sleutel moet al bekend en vertrouwd zijn door clientcomputers. In deze zelfstudie hebt u een zelfondertekend certificaat gemaakt met de privésleutel, zodat u de juiste privé- en openbare componenten moet exporteren.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Een certificaat exporteren voor Azure AD DS

Voordat u het digitale certificaat gebruiken dat in de vorige stap met uw door Azure AD DS beheerde domein is gemaakt, exporteert u het certificaat naar een *. PFX-certificaatbestand* met de privésleutel.

1. Als u het dialoogvenster *Uitvoeren* wilt openen, selecteert u de **Windows-** en **R-toetsen.**
1. Open de Microsoft Management Console (MMC) door **mmc** in te voeren in het dialoogvenster *Uitvoeren* en selecteer **OK**.
1. Klik op de prompt **gebruikersaccountbeheer** op **Ja** om MMC als beheerder te starten.
1. Klik **in** het menu Bestand op **Module toevoegen/verwijderen...**
1. Kies **in** de wizard Certificaten de optie **Computeraccount**en selecteer **Volgende**.
1. Kies **op** de pagina Computer selecteren de optie **Lokale computer: (de computer waarop deze console wordt uitgevoerd)** en selecteer **Voltooien**.
1. Klik in het dialoogvenster **Inlijnen toevoegen of verwijderen** op **OK** om de module certificaten toe te voegen aan MMC.
1. Vouw **consoleroot**uit in het MMC-venster. Selecteer **Certificaten (lokale computer)** en vouw vervolgens het **persoonlijke** knooppunt uit, gevolgd door het knooppunt **Certificaten.**

    ![Het archief voor persoonlijke certificaten openen in de Microsoft Management Console](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Het zelfondertekende certificaat dat in de vorige stap is gemaakt, wordt weergegeven, zoals *aaddscontoso.com*. Selecteer dit certificaat met de rechtermuisknop en kies **Alle taken > exporteren...**

    ![Certificaat exporteren in de Microsoft Management Console](./media/tutorial-configure-ldaps/export-cert.png)

1. Selecteer **Volgende**in de **wizard Certificaat exporteren**.
1. De privésleutel voor het certificaat moet worden geëxporteerd. Als de privésleutel niet is opgenomen in het geëxporteerde certificaat, mislukt de actie om beveiligde LDAP in te schakelen voor uw beheerde domein.

    Kies op de pagina **Privésleutel exporteren** de optie **Ja, exporteer de privésleutel**en selecteer **Volgende**.
1. Azure AD DS-beheerde domeinen ondersteunen alleen de *. PFX-certificaatbestandsindeling* die de privésleutel bevat. Exporteer het certificaat niet als *. BESTANDSindeling van het CER-certificaat* zonder de privésleutel.

    Selecteer op de pagina **Bestandsindeling exporteren** de optie **Persoonlijke gegevensuitwisseling - PKCS-#12 (. PFX)** als bestandsformaat voor het geëxporteerde certificaat. Schakel het selectievakje Voor *Het opnemen van alle certificaten in het certificeringspad indien mogelijk:*

    ![Kies de optie om het certificaat te exporteren in de PKCS 12 (. PFX)-bestandsindeling](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Aangezien dit certificaat wordt gebruikt om gegevens te decoderen, moet u de toegang zorgvuldig beheren. Een wachtwoord kan worden gebruikt om het gebruik van het certificaat te beschermen. Zonder het juiste wachtwoord kan het certificaat niet worden toegepast op een service.

    Kies **op** de pagina Beveiliging de optie **Wachtwoord** om de *. PFX-certificaatbestand.* Voer een wachtwoord in en bevestig deze en selecteer **Volgende**. Dit wachtwoord wordt in de volgende sectie gebruikt om beveiligde LDAP in te schakelen voor uw door Azure AD DS beheerde domein.
1. Geef op de pagina **Bestand tot exporteren** de bestandsnaam en locatie op waar u het certificaat wilt exporteren, zoals *C:\Users\accountname\azure-ad-ds.pfx*. Houd een notitie bij van het wachtwoord en de locatie van de *. PFX-bestand* als deze informatie nodig zou zijn in de volgende stappen.
1. Selecteer op de controlepagina **Voltooien** om het certificaat te exporteren naar een *. PFX-certificaatbestand.* Er wordt een bevestigingsdialoogvenster weergegeven wanneer het certificaat is geëxporteerd.
1. Laat de MMC open voor gebruik in de volgende sectie.

### <a name="export-a-certificate-for-client-computers"></a>Een certificaat exporteren voor clientcomputers

Clientcomputers moeten erop vertrouwen dat de uitgever van het beveiligde LDAP-certificaat met LDAPS verbinding kan maken met het beheerde domein. De clientcomputers hebben een certificaat nodig om gegevens die door Azure AD DS zijn gedecodeerd, met succes te versleutelen. Als u een openbare CA gebruikt, moet de computer deze certificaatuitgevers automatisch vertrouwen en een bijbehorend certificaat hebben. In deze zelfstudie gebruikt u een zelfondertekend certificaat en genereert u een certificaat met de privésleutel in de vorige stap. Laten we nu het zelfondertekende certificaat exporteren en vervolgens installeren in het vertrouwde certificaatarchief op de clientcomputer:

1. Ga terug naar het MMC for *Certificates (Local Computer) > Personal > Certificates* store. Het zelfondertekende certificaat dat in een vorige stap is gemaakt, wordt weergegeven, zoals *aaddscontoso.com*. Selecteer dit certificaat met de rechtermuisknop en kies **Alle taken > exporteren...**
1. Selecteer **Volgende**in de **wizard Certificaat exporteren**.
1. Omdat u de privésleutel voor clients niet nodig hebt, kiest u op de pagina **Privésleutel exporteren** **nee, exporteer de privésleutel niet**en selecteert u **Volgende**.
1. Selecteer op de pagina **Bestandsindeling exporteren** de optie **Basis-64 gecodeerde X.509 (. CER)** als bestandsformaat voor het geëxporteerde certificaat:

    ![Kies de optie om het certificaat te exporteren in de Basis-64 gecodeerde X.509 (. CER) bestandsindeling](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Geef **op** de pagina Bestand tot exporteren de bestandsnaam en locatie op waar u het certificaat wilt exporteren, zoals *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Selecteer op de controlepagina **Voltooien** om het certificaat te exporteren naar een *. *CER-certificaatbestand. Er wordt een bevestigingsdialoogvenster weergegeven wanneer het certificaat is geëxporteerd.

De *. CER-certificaatbestand* kan nu worden gedistribueerd naar clientcomputers die de beveiligde LDAP-verbinding met het beheerde Azure AD DS-domein moeten vertrouwen. Laten we het certificaat installeren op de lokale computer.

1. Open Verkenner en blader naar de locatie waar u de *. *CER-certificaatbestand, zoals *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Selecteer de *rechtermuisknop. CER-certificaatbestand* en kies **vervolgens Certificaat installeren**.
1. Kies in de **wizard Certificaat importeren**de optie om het certificaat op te slaan in de lokale *machine*en selecteer **Volgende:**

    ![De optie kiezen om het certificaat te importeren in het lokale machinearchief](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Kies **Ja** om de computer wijzigingen aan te laten brengen wanneer u daarom wordt gevraagd.
1. Kies om **het certificaatarchief automatisch**te selecteren op basis van het type certificaat en selecteer **Volgende**.
1. Selecteer op de controlepagina **Voltooien** om de *. CER-certificaat.* bestand Er wordt een bevestigingsdialoogvenster weergegeven wanneer het certificaat is geïmporteerd.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Beveiligde LDAP inschakelen voor Azure AD DS

Met een digitaal certificaat dat is gemaakt en geëxporteerd dat de privésleutel bevat en de clientcomputer die is ingesteld om de verbinding te vertrouwen, u nu beveiligde LDAP inschakelen op uw door Azure AD DS beheerde domein. Voer de volgende configuratiestappen uit om beveiligde LDAP in te schakelen op een door Azure AD DS beheerd domein:

1. Voer in de [Azure-portal](https://portal.azure.com) *domeinservices* in in het vak **Zoekbronnen.** Selecteer **Azure AD Domain Services** in het zoekresultaat.

    ![Uw door Azure AD DS beheerde domein zoeken en selecteren in de Azure-portal](./media/tutorial-configure-ldaps/search-for-domain-services.png)

1. Kies uw beheerde domein, zoals *aaddscontoso.com*.
1. Kies aan de linkerkant van het Azure AD DS-venster De optie **Beveiligde LDAP**.
1. Standaard is beveiligde LDAP-toegang tot uw beheerde domein uitgeschakeld. Schakel **Secure LDAP** in om in te schakelen om in te **schakelen.**
1. Beveiligde LDAP-toegang tot uw beheerde domein via internet is standaard uitgeschakeld. Wanneer u openbare beveiligde LDAP-toegang inschakelt, is uw domein gevoelig voor brute force-aanvallen met wachtwoorden via internet. In de volgende stap wordt een netwerkbeveiligingsgroep geconfigureerd om de toegang tot alleen de vereiste bron-IP-adresbereiken te vergrendelen.

    Schakel **beveiligde LDAP-toegang via internet** mogelijk toe om in te schakelen om in te **schakelen.**

1. Selecteer het mappictogram naast **. PFX-bestand met veilig LDAP-certificaat**. Blader naar het pad van de *. PFX-bestand* selecteert u vervolgens het certificaat dat is gemaakt in een vorige stap met de privésleutel.

    Zoals in de vorige sectie over certificaatvereisten is opgemerkt, u een certificaat van een openbare CA niet gebruiken met het standaard *domein .onmicrosoft.com.* Microsoft is eigenaar van het onmicrosoft.com domein *.onmicrosoft.com,* zodat een openbare CA geen certificaat afgeeft. Zorg ervoor dat uw certificaat in de juiste indeling is. Als dit niet het is, genereert het Azure-platform certificaatvalidatiefouten wanneer u beveiligde LDAP inschakelt.

1. Voer het **wachtwoord in om te decoderen . PFX-bestand** dat in een vorige stap werd ingesteld toen het certificaat werd geëxporteerd naar een *. PFX-bestand.*
1. Selecteer **Opslaan** om beveiligde LDAP in te schakelen.

    ![Beveiligde LDAP inschakelen voor een door Azure AD DS beheerd domein in de Azure-portal](./media/tutorial-configure-ldaps/enable-ldaps.png)

Er wordt een melding weergegeven dat beveiligde LDAP wordt geconfigureerd voor het beheerde domein. U andere instellingen voor het beheerde domein pas wijzigen als deze bewerking is voltooid.

Het duurt een paar minuten om beveiligde LDAP in te schakelen voor uw beheerde domein. Als het beveiligde LDAP-certificaat dat u opgeeft niet voldoet aan de vereiste criteria, mislukt de actie om beveiligde LDAP in te schakelen voor het beheerde domein. Enkele veelvoorkomende redenen voor het mislukken zijn als de domeinnaam onjuist is, of het certificaat verloopt binnenkort of is al verlopen. U het certificaat opnieuw maken met geldige parameters en vervolgens beveiligde LDAP inschakelen met behulp van dit bijgewerkte certificaat.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Veilige LDAP-toegang via internet vergrendelen

Wanneer u beveiligde LDAP-toegang via internet inschakelt tot uw door Azure AD DS beheerde domein, wordt een beveiligingsbedreiging veroorzaakt. Het beheerde domein is bereikbaar vanaf het internet op TCP-poort 636. Het wordt aanbevolen om de toegang tot het beheerde domein te beperken tot specifieke bekende IP-adressen voor uw omgeving. Een Azure-netwerkbeveiligingsgroepregel kan worden gebruikt om de toegang tot beveiligde LDAP te beperken.

Laten we een regel maken om inkomende beveiligde LDAP-toegang via TCP-poort 636 toe te staan vanaf een bepaalde set IP-adressen. Een standaard *DenyAll-regel* met een lagere prioriteit is van toepassing op alle andere binnenkomende verkeer van het internet, zodat alleen de opgegeven adressen uw door Azure AD DS beheerde domein kunnen bereiken met behulp van beveiligde LDAP.

1. Selecteer *resourcegroepen* aan de linkerkant in de Azure-portal.
1. Kies u resourcegroep, zoals *myResourceGroup,* en selecteer vervolgens uw netwerkbeveiligingsgroep, zoals *aaads-nsg*.
1. De lijst met bestaande inkomende en uitgaande beveiligingsregels wordt weergegeven. Kies **instellingen > binnenkomende beveiligingsregels**aan de linkerkant van de venstervensters van de netwerkbeveiligingsgroep .
1. Selecteer **Toevoegen**en maak vervolgens een regel om *TCP-poort* *636*toe te staan . Voor een betere beveiliging kiest u de bron als *IP-adressen* en geeft u vervolgens uw eigen geldige IP-adres of -bereik voor uw organisatie op.

    | Instelling                           | Waarde        |
    |-----------------------------------|--------------|
    | Bron                            | IP-adressen |
    | Bron-IP-adressen / CIDR-bereiken | Een geldig IP-adres of bereik voor uw omgeving |
    | Poortbereiken van bron                | *            |
    | Doel                       | Alle          |
    | Poortbereiken van doel           | 636          |
    | Protocol                          | TCP          |
    | Actie                            | Toestaan        |
    | Prioriteit                          | 401          |
    | Name                              | AllowLDAPS   |

1. Wanneer u klaar bent, selecteert u **Toevoegen** om de regel op te slaan en toe te passen.

    ![Een netwerkbeveiligingsgroepregel maken om LDAPS-toegang via internet te beveiligen](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>DNS-zone configureren voor externe toegang

Als beveiligde LDAP-toegang via internet is ingeschakeld, werkt u de DNS-zone bij zodat clientcomputers dit beheerde domein kunnen vinden. Het *externe IP-adres Secure LDAP* wordt weergegeven op het tabblad **Eigenschappen** voor uw door Azure AD DS beheerde domein:

![Het beveiligde LDAP-externe IP-adres voor uw door Azure AD DS beheerde domein weergeven in de Azure-portal](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Configureer uw externe DNS-provider om een hostrecord te maken, zoals *ldaps,* om dit externe IP-adres op te lossen. Als u eerst lokaal wilt testen op uw machine, u een vermelding maken in het Windows-hosts-bestand. Als u het hosts-bestand op uw lokale machine wilt bewerken, opent u *Kladblok* als beheerder en opent u het bestand *C:\Windows\System32\drivers\etc*

In het volgende voorbeeld DNS-vermelding, met uw externe DNS-provider of in het lokale hosts-bestand, wordt het verkeer voor *ldaps.aaddscontoso.com* naar het externe IP-adres van *40.121.19.239*opgelost:

```
40.121.19.239    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Query's testen naar het beheerde domein

Als u verbinding wilt maken en binden aan uw door Azure AD DS beheerde domein en via LDAP wilt zoeken, gebruikt u het *ldp.exe-hulpprogramma.* Deze tool is opgenomen in het RSAT-pakket (Remote Server Administration Tools). Zie [Hulpprogramma's voor extern serverbeheer installeren][rsat]voor meer informatie.

1. Open *LDP.exe* en maak verbinding met het beheerde domein. Selecteer **Verbinding**en kies **Verbinding maken...**.
1. Voer de beveiligde LDAP DNS-domeinnaam in van uw beheerde domein dat in de vorige stap is gemaakt, zoals *ldaps.aaddscontoso.com*. Als u veilige LDAP wilt gebruiken, stelt u **Poort** in op *636*en schakelt u het selectievakje in voor **SSL**.
1. Selecteer **OK** om verbinding te maken met het beheerde domein.

Bind vervolgens aan uw door Azure AD DS beheerde domein. Gebruikers (en serviceaccounts) kunnen geen eenvoudige LDAP-bindingen uitvoeren als u ntlm-wachtwoordhashsynchronisatie hebt uitgeschakeld op uw Azure AD DS-exemplaar. Zie [Uw azure AD DS-beheerde domein beveiligen][secure-domain]voor meer informatie over het uitschakelen van ntlm-wachtwoordhashsynchronisatie.

1. Selecteer de **menuoptie Verbinding** en kies **Binden...**.
1. Geef de referenties op van een gebruikersaccount van de groep *AAD DC Administrators,* zoals *contosoadmin*. Voer het wachtwoord van het gebruikersaccount in en voer vervolgens uw domein in, zoals *aaddscontoso.com.*
1. Kies **bij Binden type**de optie binden met *referenties*.
1. Selecteer **OK** om te binden aan uw door Azure AD DS beheerde domein.

Ga als een opgaover naar de objecten die zijn opgeslagen in uw door Azure AD DS beheerde domein:

1. Selecteer de **menuoptie Weergave** en kies **Vervolgens Structuur**.
1. Laat het *veld BaseDN* leeg en selecteer **OK**.
1. Kies een container, zoals *AADDC-gebruikers,* selecteer de container vervolgens met de rechtermuisknop en kies **Zoeken**.
1. Verlaat de vooraf ingevulde velden set en selecteer **Uitvoeren**. De resultaten van de query worden weergegeven in het rechtervenster.

    ![Zoeken naar objecten in uw door Azure AD DS beheerde domein met LDP.exe](./media/tutorial-configure-ldaps/ldp-query.png)

Als u een specifieke container rechtstreeks wilt opvragen, u in het menu **View > Tree** een **BaseDN** opgeven, zoals *OU=AADDC-gebruikers, DC=AADDSCONTOSO, DC=COM* of *OU=AADDC-computers,DC=AADDSCONTOSO, DC=COM*. Zie [basisbeginselen van LDAP-query's][ldap-query-basics]voor meer informatie over het opmaken en maken van query's.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een DNS-vermelding hebt toegevoegd aan het lokale hosts-bestand van uw computer om de connectiviteit voor deze zelfstudie te testen, verwijdert u dit item en voegt u een formele record toe aan uw DNS-zone. Voer de volgende stappen uit om de vermelding uit het lokale hosts-bestand te verwijderen:

1. Open *Notepad op* uw lokale machine als beheerder
1. Bladeren naar en open het bestand *C:\Windows\System32\drivers\etc*
1. Verwijder de regel voor de record die u hebt toegevoegd, zoals`40.121.19.239    ldaps.aaddscontoso.com`

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een digitaal certificaat maken voor gebruik met Azure AD DS
> * Beveiligde LDAP inschakelen voor Azure AD DS
> * Veilige LDAP configureren voor gebruik via het openbare internet
> * Bind en test beveiligde LDAP voor een door Azure AD DS beheerd domein

> [!div class="nextstepaction"]
> [Synchronisatie van wachtwoordhash configureren voor een hybride Azure AD-omgeving](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
