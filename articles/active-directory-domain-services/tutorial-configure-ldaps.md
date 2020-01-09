---
title: Zelf studie-LDAPS configureren voor Azure Active Directory Domain Services | Microsoft Docs
description: In deze zelf studie leert u hoe u Secure Lightweight Directory Access Protocol (LDAPS) kunt configureren voor een Azure Active Directory Domain Services beheerd domein.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: a8028cf4ece79fc31969532a358cca993c7ab948
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549445"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Zelf studie: secure LDAP configureren voor een Azure Active Directory Domain Services beheerd domein

Als u wilt communiceren met uw Azure Active Directory Domain Services (Azure AD DS) beheerde domein, wordt LDAP (Lightweight Directory Access Protocol) gebruikt. Het LDAP-verkeer wordt standaard niet versleuteld, wat een beveiligings probleem is voor veel omgevingen. Met Azure AD DS kunt u het beheerde domein configureren voor het gebruik van Secure Lightweight Directory Access Protocol (LDAPS). Wanneer u beveiligde LDAP gebruikt, wordt het verkeer versleuteld. Secure LDAP is ook bekend als LDAP via Secure Sockets Layer (SSL)/Transport Layer Security (TLS).

Deze zelf studie laat zien hoe u LDAPS kunt configureren voor een Azure AD DS beheerd domein.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een digitaal certificaat maken voor gebruik met Azure AD DS
> * Secure LDAP inschakelen voor Azure AD DS
> * Veilige LDAP configureren voor gebruik via het open bare Internet
> * Beveiligde LDAP binden en testen voor een door Azure AD DS beheerd domein

Als u nog geen abonnement op Azure hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources en bevoegdheden nodig om deze zelf studie te volt ooien:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory Tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
    * Als dat nodig is, [maakt u een Azure Active Directory-Tenant][create-azure-ad-tenant] of [koppelt u een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een Azure Active Directory Domain Services beheerd domein ingeschakeld en geconfigureerd in uw Azure AD-Tenant.
    * Als dat nodig is, kunt [u een Azure Active Directory Domain Services-exemplaar maken en configureren][create-azure-ad-ds-instance].
* Het hulp programma *Ldp. exe* is geïnstalleerd op de computer.
    * Installeer, indien nodig, [de Remote Server Administration Tools (RSAT)][rsat] voor *Active Directory Domain Services en LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelf studie configureert u Secure LDAP voor het met Azure AD DS beheerde domein met behulp van de Azure Portal. Meld u eerst aan bij de [Azure Portal](https://portal.azure.com)om aan de slag te gaan.

## <a name="create-a-certificate-for-secure-ldap"></a>Een certificaat maken voor beveiligde LDAP

Als u beveiligde LDAP wilt gebruiken, wordt er een digitaal certificaat gebruikt om de communicatie te versleutelen. Dit digitale certificaat wordt toegepast op uw door Azure AD DS beheerd domein en laat hulpprogram ma's zoals *Ldp. exe* beveiligde versleutelde communicatie gebruiken bij het opvragen van gegevens. Er zijn twee manieren om een certificaat voor beveiligde LDAP-toegang tot het beheerde domein te maken:

* Een certificaat van een open bare certificerings instantie (CA) of een CA voor ondernemingen.
    * Als uw organisatie certificaten van een open bare certificerings instantie krijgt, haalt u het beveiligde LDAP-certificaat van die open bare certificerings instantie op. Als u een ondernemings-CA in uw organisatie gebruikt, haalt u het beveiligde LDAP-certificaat op van de CA voor ondernemingen.
    * Een open bare certificerings instantie werkt alleen wanneer u een aangepaste DNS-naam gebruikt met uw Azure AD DS beheerde domein. Als de DNS-domein naam van uw beheerde domein eindigt op *. onmicrosoft.com*, kunt u geen digitaal certificaat maken om de verbinding met dit standaard domein te beveiligen. Micro soft is eigenaar van het *onmicrosoft.com* -domein, waardoor een open bare certificerings instantie geen certificaat kan uitgeven. In dit scenario maakt u een zelfondertekend certificaat en gebruikt u dat voor het configureren van secure LDAP.
* Een zelfondertekend certificaat dat u zelf maakt.
    * Deze methode is geschikt voor test doeleinden en is wat deze zelf studie laat zien.

Het certificaat dat u wilt aanvragen of maken, moet voldoen aan de volgende vereisten. Het beheerde domein detecteert problemen als u beveiligde LDAP inschakelt met een ongeldig certificaat:

* **Vertrouwde uitgever** : het certificaat moet worden uitgegeven door een certificerings instantie die wordt vertrouwd door computers die verbinding maken met het beheerde domein met behulp van secure LDAP. Deze instantie kan een open bare CERTIFICERINGs instantie of een bedrijfs certificerings instantie zijn die door deze computers wordt vertrouwd.
* **Levens duur** : het certificaat moet ten minste de volgende 3-6 maanden geldig zijn. Secure LDAP toegang tot uw beheerde domein wordt verstoord wanneer het certificaat is verlopen.
* **Onderwerpnaam** : de onderwerpnaam van het certificaat moet uw beheerde domein zijn. Als uw domein bijvoorbeeld de naam *aadds.contoso.com*heeft, moet de onderwerpnaam van het certificaat * *. aadds.contoso.com*zijn.
    * De alternatieve naam van de DNS-naam of het onderwerp van het certificaat moet een Joker certificaat zijn om ervoor te zorgen dat de beveiligde LDAP goed werkt met de Azure AD Domain Services. Domein controllers gebruiken wille keurige namen en kunnen worden verwijderd of toegevoegd om ervoor te zorgen dat de service beschikbaar blijft.
* **Sleutel gebruik** : het certificaat moet worden geconfigureerd voor *digitale hand tekeningen* en *sleutel codering*.
* **Certificaat doeleinde** -het certificaat moet geldig zijn voor SSL-server authenticatie.

In deze zelf studie maakt u een zelfondertekend certificaat voor secure LDAP met de cmdlet [New-SelfSignedCertificate][New-SelfSignedCertificate] . Open een Power shell-venster als **beheerder** en voer de volgende opdrachten uit. Vervang de *$dnsName* variabele door de DNS-naam die wordt gebruikt door uw eigen beheerde domein, zoals *aadds.contoso.com*:

```powershell
# Define your own DNS name used by your Azure AD DS managed domain
$dnsName="aadds.contoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

In de volgende voorbeeld uitvoer ziet u dat het certificaat is gegenereerd en is opgeslagen in het lokale certificaat archief (*LocalMachine\MY*):

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aadds.contoso.com
```

## <a name="understand-and-export-required-certificates"></a>Vereiste certificaten begrijpen en exporteren

Als u beveiligde LDAP wilt gebruiken, wordt het netwerk verkeer versleuteld met behulp van open bare-sleutel infrastructuur (PKI).

* Een **persoonlijke** sleutel wordt toegepast op het door Azure AD DS beheerde domein.
    * Deze persoonlijke sleutel wordt gebruikt voor het *ontsleutelen* van het beveiligde LDAP-verkeer. De persoonlijke sleutel moet alleen worden toegepast op het beheerde domein van Azure AD DS en niet algemeen naar client computers worden gedistribueerd.
    * Een certificaat dat de persoonlijke sleutel bevat, maakt gebruik van de *. PFX* -bestands indeling.
* Een **open bare** sleutel wordt toegepast op de client computers.
    * Deze open bare sleutel wordt gebruikt voor het *versleutelen* van het beveiligde LDAP-verkeer. De open bare sleutel kan worden gedistribueerd naar client computers.
    * Certificaten zonder de persoonlijke sleutel gebruiken de *. CER* -bestands indeling.

Deze twee sleutels, de *persoonlijke* en *open bare* sleutels, zorgen ervoor dat alleen de juiste computers met elkaar kunnen communiceren. Als u een open bare CA of een CA voor ondernemingen gebruikt, wordt u verleend met een certificaat dat de persoonlijke sleutel bevat en kan worden toegepast op een beheerd domein van Azure AD DS. De open bare sleutel moet al bekend zijn en worden vertrouwd door client computers. In deze zelf studie hebt u een zelfondertekend certificaat gemaakt met de persoonlijke sleutel, dus u moet de juiste persoonlijke en open bare onderdelen exporteren.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Een certificaat voor Azure AD DS exporteren

Voordat u het digitale certificaat dat u in de vorige stap hebt gemaakt, kunt gebruiken met uw Azure AD DS beheerde domein, exporteert u het certificaat naar een *. PFX* -certificaat bestand dat de persoonlijke sleutel bevat.

1. Selecteer de **Windows** -en **R** -sleutels om het dialoog venster *uitvoeren* te openen.
1. Open de micro soft Management Console (MMC) door **MMC** in te voeren in het dialoog venster *uitvoeren* en selecteer vervolgens **OK**.
1. Klik in het dialoog venster **Gebruikersaccountbeheer** op **Ja** om MMC als Administrator te starten.
1. Klik in het menu **bestand** op **module toevoegen/verwijderen...**
1. Kies **computer account**in de **module Certificaten** en selecteer vervolgens **volgende**.
1. Kies op de pagina **computer selecteren** de optie **lokale computer: (de computer waarop deze console wordt uitgevoerd)** en selecteer vervolgens **volt ooien**.
1. Klik in het dialoog venster **modules toevoegen of verwijderen** op **OK** om de module Certificaten aan MMC toe te voegen.
1. Vouw in het MMC-venster **console basis**uit. Selecteer **certificaten (lokale computer)** , vouw het **persoonlijke** knoop punt uit, gevolgd door het knoop punt **certificaten** .

    ![Open het archief met persoonlijke certificaten in de micro soft Management Console](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Het zelfondertekende certificaat dat u in de vorige stap hebt gemaakt, wordt weer gegeven, zoals *aadds.contoso.com*. Klik met de rechter muisknop op dit certificaat en kies **alle taken > exporteren...**

    ![Certificaat exporteren in micro soft Management Console](./media/tutorial-configure-ldaps/export-cert.png)

1. Selecteer in de **wizard Certificaat exporteren**de optie **volgende**.
1. De persoonlijke sleutel voor het certificaat moet worden geëxporteerd. Als de persoonlijke sleutel niet is opgenomen in het geëxporteerde certificaat, mislukt de actie voor het inschakelen van beveiligde LDAP voor uw beheerde domein.

    Kies op de pagina **persoonlijke sleutel exporteren** **de optie Ja, de persoonlijke sleutel exporteren**en vervolgens **volgende**selecteren.
1. Beheerde domeinen van Azure AD DS ondersteunen alleen de *. PFX* -certificaat bestands indeling die de persoonlijke sleutel bevat. Exporteer het certificaat niet als *. CER* -certificaat bestand indeling zonder de persoonlijke sleutel.

    Selecteer op de pagina **bestands indeling voor export** de optie **Personal Information Exchange-PKCS #12 (. PFX)** als de bestands indeling voor het geëxporteerde certificaat. Schakel indien mogelijk het selectie vakje *alle certificaten in het certificeringspad insluiten in*:

    ![Kies de optie voor het exporteren van het certificaat in de PKCS 12-(. PFX-bestand indeling](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Aangezien dit certificaat wordt gebruikt om gegevens te ontsleutelen, moet u de toegang nauw keurig beheren. Een wacht woord kan worden gebruikt om het gebruik van het certificaat te beveiligen. Zonder het juiste wacht woord kan het certificaat niet worden toegepast op een service.

    Kies op de pagina **beveiliging** de optie voor **wacht woord** om de te beveiligen *. PFX* -certificaat bestand. Voer een wacht woord in en bevestig dit en selecteer vervolgens **volgende**. Dit wacht woord wordt in de volgende sectie gebruikt om secure LDAP in te scha kelen voor uw Azure AD DS beheerde domein.
1. Geef op de pagina **te exporteren bestand** de bestands naam en de locatie op waarnaar u het certificaat wilt exporteren, bijvoorbeeld *C:\Users\accountname\azure-AD-DS.pfx*.
1. Selecteer op de pagina controleren de optie **volt ooien** om het certificaat te exporteren naar een *. PFX* -certificaat bestand. Er wordt een bevestigings dialoogvenster weer gegeven wanneer het certificaat is geëxporteerd.
1. Wijzig de MMC niet voor gebruik in de volgende sectie.

### <a name="export-a-certificate-for-client-computers"></a>Een certificaat voor client computers exporteren

Client computers moeten de verlener van het beveiligde LDAP-certificaat vertrouwen om met behulp van LDAPS verbinding te kunnen maken met het beheerde domein. Op de client computers is een certificaat vereist om gegevens te versleutelen die door Azure AD DS worden ontsleuteld. Als u een open bare certificerings instantie gebruikt, moet de computer deze certificaat verleners automatisch vertrouwen en een bijbehorend certificaat hebben. In deze zelf studie gebruikt u een zelfondertekend certificaat en hebt u een certificaat gegenereerd dat de persoonlijke sleutel bevat in de vorige stap. Nu gaan we het zelfondertekende certificaat exporteren en installeren in het vertrouwde certificaat archief op de client computer:

1. Ga terug naar de MMC voor *certificaten (lokale computer) > Archief met persoonlijke > certificaten* . Het zelfondertekende certificaat dat in een vorige stap is gemaakt, wordt weer gegeven, zoals *aadds.contoso.com*. Klik met de rechter muisknop op dit certificaat en kies **alle taken > exporteren...**
1. Selecteer in de **wizard Certificaat exporteren**de optie **volgende**.
1. Als u de persoonlijke sleutel voor clients niet nodig hebt, kiest u op de pagina **persoonlijke sleutel exporteren** de optie **Nee, de persoonlijke sleutel niet exporteren**en selecteert u **volgende**.
1. Selecteer op de pagina **bestands indeling voor export** de optie **Base-64 Encoded X. 509 (. CER)** als de bestands indeling voor het geëxporteerde certificaat:

    ![Kies de optie voor het exporteren van het certificaat in de base-64 Encoded X. 509 (. CER)-bestands indeling](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Geef op de pagina **te exporteren bestand** de bestands naam en de locatie op waarnaar u het certificaat wilt exporteren, bijvoorbeeld *C:\Users\accountname\azure-AD-DS-client.CER*.
1. Selecteer op de pagina controleren de optie **volt ooien** om het certificaat te exporteren naar een *. CER* -certificaat bestand. Er wordt een bevestigings dialoogvenster weer gegeven wanneer het certificaat is geëxporteerd.

De *. CER* -certificaat bestand kan nu worden gedistribueerd naar client computers die de beveiligde LDAP-verbinding moeten vertrouwen met het door Azure AD DS beheerde domein. Laten we het certificaat installeren op de lokale computer.

1. Open bestanden Verkenner en blader naar de locatie waar u de hebt opgeslagen *. CER* -certificaat bestand, zoals *C:\Users\accountname\azure-AD-DS-client.CER*.
1. Klik met de rechter muisknop op *. CER* -certificaat bestand en kies **certificaat installeren**.
1. Kies in de **wizard Certificaat importeren**om het certificaat op te slaan op de *lokale computer*en selecteer vervolgens **volgende**:

    ![Selecteer de optie voor het importeren van het certificaat in het archief van de lokale computer](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Wanneer u hierom wordt gevraagd, kiest u **Ja** om de computer toe te staan wijzigingen aan te brengen.
1. Kies **automatisch het certificaat archief selecteren op basis van het type certificaat**en selecteer vervolgens **volgende**.
1. Selecteer op de pagina controleren de optie **volt ooien** om de te importeren *. CER* -certificaat. Er wordt een bevestigings dialoogvenster weer gegeven wanneer het certificaat is geïmporteerd.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Secure LDAP inschakelen voor Azure AD DS

Als er een digitaal certificaat is gemaakt en geëxporteerd dat de persoonlijke sleutel bevat, en de client computer is ingesteld om de verbinding te vertrouwen, schakelt u nu beveiligde LDAP in op uw Azure AD DS beheerde domein. Voer de volgende configuratie stappen uit om beveiligde LDAP in te scha kelen op een beheerd domein van Azure AD DS:

1. Voer in het [Azure Portal](https://portal.azure.com) *domein Services* in het vak **Zoek resources** in. Selecteer **Azure AD Domain Services** in het Zoek resultaat.

    ![Zoek en selecteer uw door Azure AD DS beheerde domein in het Azure Portal](./media/tutorial-configure-ldaps/search-for-domain-services.png)

1. Kies uw beheerde domein, zoals *aadds.contoso.com*.
1. Kies aan de linkerkant van het Azure AD DS-venster **secure LDAP**.
1. Beveiligde LDAP-toegang tot uw beheerde domein is standaard uitgeschakeld. Schakel **secure LDAP** in om in te **scha kelen**.
1. Secure LDAP toegang tot uw beheerde domein via internet is standaard uitgeschakeld. Wanneer u open bare beveiligde toegang tot LDAP inschakelt, is uw domein gevoelig voor wachtwoord beveiliging tegen aanvallen via internet. In de volgende stap wordt een netwerk beveiligings groep geconfigureerd om de toegang tot de vereiste bron-IP-adresbereiken te vergren delen.

    Schakel **beveiligde LDAP-toegang via internet** in of uit om in te **scha kelen**.

1. Selecteer het mappictogram naast **. PFX-bestand met beveiligd LDAP-certificaat**. Blader naar het pad van de *. PFX* -bestand en selecteer vervolgens het certificaat dat u in een vorige stap hebt gemaakt en dat de persoonlijke sleutel bevat.

    Zoals vermeld in de vorige sectie over certificaat vereisten, kunt u geen certificaat van een open bare certificerings instantie gebruiken met het domein default *. onmicrosoft.com* . Micro soft is eigenaar van het *onmicrosoft.com* -domein, waardoor een open bare certificerings instantie geen certificaat kan uitgeven. Zorg ervoor dat uw certificaat de juiste indeling heeft. Als dat niet het geval is, genereert het Azure-platform certificaat validatie fouten wanneer u Secure LDAP inschakelt.

1. Voer het **wacht woord in om te ontsleutelen. PFX-bestand** dat in een vorige stap is ingesteld toen het certificaat werd geëxporteerd naar een *. PFX* -bestand.
1. Selecteer **Opslaan** om beveiligd LDAP in te scha kelen.

    ![Schakel secure LDAP in voor een door Azure AD DS beheerd domein in het Azure Portal](./media/tutorial-configure-ldaps/enable-ldaps.png)

Er wordt een melding weer gegeven dat beveiligde LDAP wordt geconfigureerd voor het beheerde domein. U kunt geen andere instellingen voor het beheerde domein wijzigen totdat deze bewerking is voltooid.

Het duurt enkele minuten om de beveiliging van LDAP voor uw beheerde domein in te scha kelen. Als het beveiligde LDAP-certificaat dat u opgeeft niet voldoet aan de vereiste criteria, mislukt de actie voor het inschakelen van beveiligde LDAP voor het beheerde domein. Enkele veelvoorkomende redenen voor fouten zijn als de domein naam onjuist is of het certificaat binnenkort verloopt of al is verlopen. U kunt het certificaat opnieuw maken met geldige para meters en vervolgens secure LDAP inschakelen met dit bijgewerkte certificaat.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Beveiligde LDAP-toegang via internet vergren delen

Wanneer u beveiligde LDAP-toegang via internet naar uw Azure AD DS beheerde domein inschakelt, wordt er een beveiligings risico gegenereerd. Het beheerde domein is bereikbaar vanaf internet op TCP-poort 636. Het is raadzaam de toegang tot het beheerde domein te beperken tot specifieke IP-adressen voor uw omgeving. Een regel voor een Azure-netwerk beveiligings groep kan worden gebruikt om de toegang tot de beveiliging van LDAP te beperken.

We gaan een regel maken om binnenkomende beveiligde LDAP-toegang via TCP-poort 636 uit een opgegeven reeks IP-adressen toe te staan. Een standaard *DenyAll* -regel met een lagere prioriteit is van toepassing op alle andere inkomende verkeer van het Internet, zodat alleen de opgegeven adressen uw Azure AD DS beheerde domein kunnen bereiken met behulp van secure LDAP.

1. Selecteer in de Azure Portal *resource groepen* aan de linkerkant navigatie.
1. Kies een resource groep, zoals *myResourceGroup*, en selecteer vervolgens uw netwerk beveiligings groep, zoals *aaads-NSG*.
1. De lijst met bestaande binnenkomende en uitgaande beveiligings regels wordt weer gegeven. Kies aan de linkerkant van de netwerk beveiligings groep Windows **instellingen > regels voor binnenkomende beveiliging**.
1. Selecteer **toevoegen**en maak een regel om *TCP* -poort *636*toe te staan. Voor een betere beveiliging kiest u de bron als *IP-adressen* en geeft u vervolgens uw eigen geldige IP-adres of bereik op voor uw organisatie.

    | Instelling                           | Waarde        |
    |-----------------------------------|--------------|
    | Bron                            | IP-adressen |
    | IP-adressen van bron/CIDR-bereiken | Een geldig IP-adres of-bereik voor uw omgeving |
    | Poortbereiken van bron                | *            |
    | Bestemming                       | Alle          |
    | Poortbereiken van doel           | 636          |
    | Protocol                          | TCP          |
    | Actie                            | Toestaan        |
    | Prioriteit                          | 401          |
    | Name                              | AllowLDAPS   |

1. Wanneer u klaar bent, selecteert u **toevoegen** om de regel op te slaan en toe te passen.

    ![Een regel voor de netwerk beveiligings groep maken om LDAPS-toegang via internet te beveiligen](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>DNS-zone voor externe toegang configureren

Wanneer beveiligde LDAP-toegang via internet is ingeschakeld, werkt u de DNS-zone bij zodat client computers dit beheerde domein kunnen vinden. Het *secure LDAP externe IP-adres* wordt weer gegeven op het tabblad **Eigenschappen** voor uw door Azure AD DS beheerde domein:

![Bekijk het beveiligde externe IP-adres van LDAP voor uw door Azure AD DS beheerd domein in het Azure Portal](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Configureer uw externe DNS-provider om een hostrecord, zoals *LDAPS*, te maken om dit externe IP-adres op te lossen. Als u eerst lokaal op uw computer wilt testen, kunt u een vermelding in het Windows-hosts-bestand maken. Als u het hosts-bestand op uw lokale computer wilt bewerken, opent u *Klad blok* als beheerder en opent u het bestand *C:\Windows\System32\drivers\etc*

In het volgende voor beeld van een DNS-vermelding, met uw externe DNS-provider of in het lokale hosts-bestand, wordt verkeer voor *LDAPS.aadds.contoso.com* naar het externe IP-adres van *40.121.19.239*opgelost:

```
40.121.19.239    ldaps.aadds.contoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Query's naar het beheerde domein testen

Gebruik het hulp programma *Ldp. exe* om verbinding te maken en te verbinden met uw Azure AD DS beheerde domein en om te zoeken in LDAP. Dit hulp programma is opgenomen in het Remote Server Administration Tools-pakket (RSAT). Zie [install Remote Server Administration Tools][rsat](Engelstalig) voor meer informatie.

1. Open *Ldp. exe* en maak verbinding met het beheerde domein. Selecteer **verbinding**en kies vervolgens **verbinding maken...** .
1. Voer de naam van het beveiligde LDAP-DNS-domein in van uw beheerde domein dat in de vorige stap is gemaakt, zoals *LDAPS.aadds.contoso.com*. Als u beveiligde LDAP wilt gebruiken, stelt u **poort** in op *636*en schakelt u het selectie vakje in voor **SSL**.
1. Selecteer **OK** om verbinding te maken met het beheerde domein.

Maak vervolgens een binding met uw door Azure AD DS beheerd domein. Gebruikers (en service accounts) kunnen geen LDAP-eenvoudige bindingen uitvoeren als NTLM-wachtwoord synchronisatie is uitgeschakeld op uw Azure AD DS-exemplaar. Zie [Secure Your Azure AD DS Managed Domain][secure-domain](Engelstalig) voor meer informatie over het uitschakelen van NTLM-wachtwoord hash-synchronisatie.

1. Selecteer de menu optie **verbinding** en kies vervolgens **binden...** .
1. Geef de referenties op van een gebruikers account dat deel uitmaakt van de groep *Aad DC-Administrators* , zoals *contosoadmin*. Voer het wacht woord van het gebruikers account in en voer vervolgens uw domein in, bijvoorbeeld *aadds.contoso.com*.
1. Kies voor **bindings type**de optie voor *binden met referenties*.
1. Selecteer **OK** om een binding te maken met uw Azure AD DS beheerde domein.

Als u de objecten wilt zien die zijn opgeslagen in uw Azure AD DS beheerde domein:

1. Selecteer de menu optie **weer gave** en kies vervolgens **boom structuur**.
1. Laat het veld *gebaseerd op* leeg en selecteer **OK**.
1. Kies een container, zoals *AADDC gebruikers*, klik met de rechter muisknop op de container en kies **zoeken**.
1. Behoud de vooraf ingevulde velden en selecteer vervolgens **uitvoeren**. De resultaten van de query worden weer gegeven in het rechterdeel venster.

    ![Zoeken naar objecten in uw Azure AD DS beheerde domein met behulp van LDP. exe](./media/tutorial-configure-ldaps/ldp-query.png)

Als u rechtstreeks een query wilt uitvoeren op een specifieke container, kunt u in het menu **> structuur van weer gave** een **basis** opgeven zoals *OE = AADDC gebruikers, DC = CONTOSO, DC = com* of *OE = AADDC computers, DC = CONTOSO, DC = com*. Zie [basis beginselen van LDAP-query's][ldap-query-basics]voor meer informatie over het opmaken en maken van query's.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een DNS-vermelding hebt toegevoegd aan het lokale hosts-bestand van uw computer om de connectiviteit voor deze zelf studie te testen, verwijdert u deze vermelding en voegt u een formeel record toe aan uw DNS-zone. Voer de volgende stappen uit om de vermelding uit het lokale hosts-bestand te verwijderen:

1. Open *Klad blok* op uw lokale computer als beheerder
1. Blader naar en open het bestand *C:\Windows\System32\drivers\etc*
1. Verwijder de regel voor de record die u hebt toegevoegd, zoals `40.121.19.239    ldaps.aadds.contoso.com`

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een digitaal certificaat maken voor gebruik met Azure AD DS
> * Secure LDAP inschakelen voor Azure AD DS
> * Veilige LDAP configureren voor gebruik via het open bare Internet
> * Beveiligde LDAP binden en testen voor een door Azure AD DS beheerd domein

> [!div class="nextstepaction"]
> [Wachtwoord-hash-synchronisatie configureren voor een hybride Azure AD-omgeving](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
