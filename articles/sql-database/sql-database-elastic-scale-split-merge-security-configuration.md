---
title: Beveiligingsconfiguratie splitsen
description: Stel x409-certificaten in voor versleuteling met de split/merge-service voor elastische schaal.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: a5ea0fd252d1792d4c40cc6d7869f4ba57edc1ad
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011358"
---
# <a name="split-merge-security-configuration"></a>Beveiligingsconfiguratie splitsen

Als u de split/merge-service wilt gebruiken, moet u de beveiliging correct configureren. De service maakt deel uit van de functie Elastische schaal van Microsoft Azure SQL Database. Zie Hiervoor zie [Hiervoor uitgesplitste onderdelen en samenvoegservicezelfstudie voor](sql-database-elastic-scale-configure-deploy-split-and-merge.md)meer informatie .

## <a name="configuring-certificates"></a>Certificaten configureren

Certificaten worden op twee manieren geconfigureerd. 

1. [Het TLS/SSL-certificaat configureren](#to-configure-the-tlsssl-certificate)
2. [Clientcertificaten configureren](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Om certificaten te verkrijgen

Certificaten kunnen worden verkregen bij openbare certificeringsinstanties (CV's) of bij de [Windows Certificate Service.](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx) Dit zijn de voorkeursmethoden om certificaten te verkrijgen.

Als deze opties niet beschikbaar zijn, u **zelfondertekende certificaten**genereren.

## <a name="tools-to-generate-certificates"></a>Hulpprogramma's voor het genereren van certificaten

* [makecert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>De gereedschappen uitvoeren

* Zie [Opdrachtprompt](https://msdn.microsoft.com/library/ms229859.aspx) voor Visual Studio's in een opdracht voor ontwikkelaars voor Visuele studio's 
  
    Ga bij installatie naar:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Haal de WDK uit [Windows 8.1: Download kits en tools](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-tlsssl-certificate"></a>Het TLS/SSL-certificaat configureren

Een TLS/SSL-certificaat is vereist om de communicatie te versleutelen en de server te verifiëren. Kies de meest toepasselijke van de drie onderstaande scenario's en voer al zijn stappen uit:

### <a name="create-a-new-self-signed-certificate"></a>Een nieuw zelfondertekend certificaat maken

1. [Een zelfondertekend certificaat maken](#create-a-self-signed-certificate)
2. [PFX-bestand maken voor zelfondertekend TLS/SSL-certificaat](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [TLS/SSL-certificaat uploaden naar cloudservice](#upload-tlsssl-certificate-to-cloud-service)
4. [TLS/SSL-certificaat bijwerken in serviceconfiguratiebestand](#update-tlsssl-certificate-in-service-configuration-file)
5. [TLS/SSL-certificeringsinstantie importeren](#import-tlsssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Een bestaand certificaat gebruiken in het certificaatarchief
1. [TLS/SSL-certificaat exporteren vanuit certificate store](#export-tlsssl-certificate-from-certificate-store)
2. [TLS/SSL-certificaat uploaden naar cloudservice](#upload-tlsssl-certificate-to-cloud-service)
3. [TLS/SSL-certificaat bijwerken in serviceconfiguratiebestand](#update-tlsssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Een bestaand certificaat in een PFX-bestand gebruiken
1. [TLS/SSL-certificaat uploaden naar cloudservice](#upload-tlsssl-certificate-to-cloud-service)
2. [TLS/SSL-certificaat bijwerken in serviceconfiguratiebestand](#update-tlsssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Clientcertificaten configureren
Clientcertificaten zijn vereist om aanvragen voor de service te verifiëren. Kies de meest toepasselijke van de drie onderstaande scenario's en voer al zijn stappen uit:

### <a name="turn-off-client-certificates"></a>Clientcertificaten uitschakelen
1. [Verificatie op basis van clientcertificaat uitschakelen](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Nieuwe zelfondertekende clientcertificaten afgeven
1. [Een zelfondertekende certificeringsinstantie maken](#create-a-self-signed-certification-authority)
2. [CA-certificaat uploaden naar cloudservice](#upload-ca-certificate-to-cloud-service)
3. [CA-certificaat in serviceconfiguratiebestand bijwerken](#update-ca-certificate-in-service-configuration-file)
4. [Clientcertificaten uitgeven](#issue-client-certificates)
5. [PFX-bestanden maken voor clientcertificaten](#create-pfx-files-for-client-certificates)
6. [Clientcertificaat importeren](#import-client-certificate)
7. [Duimafdrukken van clientcertificaat kopiëren](#copy-client-certificate-thumbprints)
8. [Toegestane clients configureren in het serviceconfiguratiebestand](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Bestaande clientcertificaten gebruiken
1. [Openbare SLEUTEL VAN CA zoeken](#find-ca-public-key)
2. [CA-certificaat uploaden naar cloudservice](#upload-ca-certificate-to-cloud-service)
3. [CA-certificaat in serviceconfiguratiebestand bijwerken](#update-ca-certificate-in-service-configuration-file)
4. [Duimafdrukken van clientcertificaat kopiëren](#copy-client-certificate-thumbprints)
5. [Toegestane clients configureren in het serviceconfiguratiebestand](#configure-allowed-clients-in-the-service-configuration-file)
6. [Herroepingscontrole clientcertificaat configureren](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Toegestane IP-adressen
Toegang tot de serviceeindpunten kan worden beperkt tot specifieke bereiken van IP-adressen.

## <a name="to-configure-encryption-for-the-store"></a>Versleuteling voor de winkel configureren
Er is een certificaat vereist om de referenties te versleutelen die zijn opgeslagen in het metagegevensarchief. Kies de meest toepasselijke van de drie onderstaande scenario's en voer al zijn stappen uit:

### <a name="use-a-new-self-signed-certificate"></a>Een nieuw zelfondertekend certificaat gebruiken
1. [Een zelfondertekend certificaat maken](#create-a-self-signed-certificate)
2. [PFX-bestand maken voor zelfondertekend versleutelingscertificaat](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Versleutelingscertificaat uploaden naar cloudservice](#upload-encryption-certificate-to-cloud-service)
4. [Versleutelingscertificaat bijwerken in serviceconfiguratiebestand](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Een bestaand certificaat gebruiken in het certificaatarchief
1. [Versleutelingscertificaat exporteren uit certificaatarchief](#export-encryption-certificate-from-certificate-store)
2. [Versleutelingscertificaat uploaden naar cloudservice](#upload-encryption-certificate-to-cloud-service)
3. [Versleutelingscertificaat bijwerken in serviceconfiguratiebestand](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Een bestaand certificaat gebruiken in een PFX-bestand
1. [Versleutelingscertificaat uploaden naar cloudservice](#upload-encryption-certificate-to-cloud-service)
2. [Versleutelingscertificaat bijwerken in serviceconfiguratiebestand](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>De standaardconfiguratie
De standaardconfiguratie weigert alle toegang tot het HTTP-eindpunt. Dit is de aanbevolen instelling, omdat de aanvragen voor deze eindpunten gevoelige informatie kunnen bevatten, zoals databasereferenties.
De standaardconfiguratie geeft alle toegang tot het HTTPS-eindpunt. Deze instelling kan verder worden beperkt.

### <a name="changing-the-configuration"></a>De configuratie wijzigen
De groep regels voor toegangscontrole die van toepassing zijn op en eindpunt, wordt geconfigureerd in de ** \<sectie EndpointAcls>** in het **serviceconfiguratiebestand**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

De regels in een toegangscontrolegroep zijn \<geconfigureerd in een accesscontrol-naam="> gedeelte van het serviceconfiguratiebestand. 

De indeling wordt uitgelegd in de documentatie van Network Access Control Lists.
Als u bijvoorbeeld alleen IP's in het bereik 100.100.0 tot 100.100.255.255 toestaat om toegang te krijgen tot het HTTPS-eindpunt, zien de regels er als volgt uit:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Denial of service preventie
Er zijn twee verschillende mechanismen die worden ondersteund om Denial of Service-aanvallen te detecteren en te voorkomen:

* Aantal gelijktijdige aanvragen per externe host beperken (standaard uitgeschakeld)
* Toegangssnelheid per externe host beperken (standaard ingeschakeld)

Deze zijn gebaseerd op de functies die verder zijn gedocumenteerd in Dynamic IP Security in IIS. Let bij het wijzigen van deze configuratie op de volgende factoren:

* Het gedrag van proxy's en netwerkadresvertaalapparaten via de externe hostgegevens
* Elke aanvraag voor een resource in de webrol wordt overwogen (bijvoorbeeld het laden van scripts, afbeeldingen, enz.)

## <a name="restricting-number-of-concurrent-accesses"></a>Aantal gelijktijdige toegangen beperken
De instellingen die dit gedrag configureren zijn:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

DynamicIpRestrictionDenyByConcurrentRequests wijzigen in true om deze beveiliging in te schakelen.

## <a name="restricting-rate-of-access"></a>Beperking van de toegangssnelheid
De instellingen die dit gedrag configureren zijn:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Het antwoord op een geweigerd verzoek configureren
Met de volgende instelling configureert u het antwoord op een geweigerdverzoek:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Raadpleeg de documentatie voor Dynamische IP-beveiliging in IIS voor andere ondersteunde waarden.

## <a name="operations-for-configuring-service-certificates"></a>Bewerkingen voor het configureren van servicecertificaten
Dit onderwerp is alleen ter referentie. Volg de configuratiestappen die zijn beschreven in:

* Het TLS/SSL-certificaat configureren
* Clientcertificaten configureren

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken
Uitvoeren:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Aanpassen:

* -n met de service-URL. Jokertekens ("CN=*.cloudapp.net") en alternatieve namen ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") worden ondersteund.
* -e met de vervaldatum van het certificaat Maak een sterk wachtwoord en geef dit op wanneer dit wordt gevraagd.

## <a name="create-pfx-file-for-self-signed-tlsssl-certificate"></a>PFX-bestand maken voor zelfondertekend TLS/SSL-certificaat
Uitvoeren:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Voer het wachtwoord in en exporteer vervolgens het certificaat met de volgende opties:

* Ja, exporteer de privésleutel
* Alle uitgebreide eigenschappen exporteren

## <a name="export-tlsssl-certificate-from-certificate-store"></a>TLS/SSL-certificaat exporteren vanuit certificaatarchief
* Certificaat zoeken
* Klik op Acties -> Alle taken -> exporteren...
* Uitvoercertificaat in een . PFX-bestand met de volgende opties:
  * Ja, exporteer de privésleutel
  * Alle certificaten opnemen in het certificeringspad indien mogelijk *Alle uitgebreide eigenschappen exporteren

## <a name="upload-tlsssl-certificate-to-cloud-service"></a>TLS/SSL-certificaat uploaden naar cloudservice
Uploadcertificaat met het bestaande of gegenereerde . PFX-bestand met het TLS-sleutelpaar:

* Voer het wachtwoord in dat de persoonlijke sleutelgegevens beschermt

## <a name="update-tlsssl-certificate-in-service-configuration-file"></a>TLS/SSL-certificaat bijwerken in serviceconfiguratiebestand
Werk de duimafdrukwaarde van de volgende instelling in het serviceconfiguratiebestand bij met de duimafdruk van het certificaat dat naar de cloudservice is geüpload:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-tlsssl-certification-authority"></a>TLS/SSL-certificeringsinstantie importeren
Volg deze stappen in alle account/machine die met de service communiceert:

* Dubbelklik op de . CER-bestand in Windows Verkenner
* Klik in het dialoogvenster Certificaat op Certificaat installeren...
* Certificaat importeren in het opgeslagen vertrouwde basiscertificeringsinstanties

## <a name="turn-off-client-certificate-based-authentication"></a>Verificatie op basis van clientcertificaat uitschakelen
Alleen clientcertificaatgebaseerde verificatie wordt ondersteund en het uitschakelen ervan biedt openbare toegang tot de serviceeindpunten, tenzij er andere mechanismen zijn (bijvoorbeeld Microsoft Azure Virtual Network).

Wijzig deze instellingen in false in het serviceconfiguratiebestand om de functie uit te schakelen:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Kopieer vervolgens dezelfde duimafdruk als het TLS/SSL-certificaat in de CA-certificaatinstelling:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Een zelfondertekende certificeringsinstantie maken
Voer de volgende stappen uit om een zelfondertekend certificaat te maken om op te treden als certificeringsinstantie:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Aanpassen

* -e met de vervaldatum van de certificering

## <a name="find-ca-public-key"></a>Openbare CA-sleutel zoeken
Alle clientcertificaten moeten zijn uitgegeven door een certificeringsinstantie die door de service wordt vertrouwd. Zoek de openbare sleutel van de certificeringsinstantie die de clientcertificaten heeft uitgegeven die worden gebruikt voor verificatie om deze te uploaden naar de cloudservice.

Als het bestand met de openbare sleutel niet beschikbaar is, exporteert u het uit het certificaatarchief:

* Certificaat zoeken
  * Zoeken naar een clientcertificaat uitgegeven door dezelfde certificeringsinstantie
* Dubbelklik op het certificaat.
* Selecteer het tabblad Certificaatpad in het dialoogvenster Certificaat.
* Dubbelklik op de CA-vermelding in het pad.
* Maak aantekeningen van de certificaateigenschappen.
* Sluit het dialoogvenster **Certificaat.**
* Certificaat zoeken
  * Zoek naar de CA hierboven vermeld.
* Klik op Acties -> Alle taken -> exporteren...
* Uitvoercertificaat in een . CER met de volgende opties:
  * **Nee, exporteer de privésleutel niet**
  * Neem indien mogelijk alle certificaten op in het certificeringspad.
  * Exporteer alle uitgebreide eigenschappen.

## <a name="upload-ca-certificate-to-cloud-service"></a>CA-certificaat uploaden naar cloudservice
Uploadcertificaat met het bestaande of gegenereerde . CER-bestand met de openbare SLEUTEL CA.

## <a name="update-ca-certificate-in-service-configuration-file"></a>CA-certificaat bijwerken in serviceconfiguratiebestand
Werk de duimafdrukwaarde van de volgende instelling in het serviceconfiguratiebestand bij met de duimafdruk van het certificaat dat naar de cloudservice is geüpload:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Werk de waarde van de volgende instelling bij met dezelfde duimafdruk:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Clientcertificaten uitgeven
Elke persoon die gemachtigd is om toegang te krijgen tot de service moet een clientcertificaat hebben uitgegeven voor exclusief gebruik en moet zijn eigen sterke wachtwoord kiezen om zijn privésleutel te beschermen. 

De volgende stappen moeten worden uitgevoerd in dezelfde machine waar het zelfondertekende CA-certificaat is gegenereerd en opgeslagen:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Aanpassen:

* -n met een ID voor de client die met dit certificaat wordt geverifieerd
* -e met de vervaldatum van het certificaat
* MyID.pvk en MyID.cer met unieke bestandsnamen voor dit clientcertificaat

Met deze opdracht wordt gevraagd om een wachtwoord aan te maken en vervolgens één keer te gebruiken. Gebruik een sterk wachtwoord.

## <a name="create-pfx-files-for-client-certificates"></a>PFX-bestanden maken voor clientcertificaten
Voer voor elk gegenereerd clientcertificaat het:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Aanpassen:

    MyID.pvk and MyID.cer with the filename for the client certificate

Voer het wachtwoord in en exporteer vervolgens het certificaat met de volgende opties:

* Ja, exporteer de privésleutel
* Alle uitgebreide eigenschappen exporteren
* De persoon aan wie dit certificaat wordt uitgegeven, moet het exportwachtwoord kiezen

## <a name="import-client-certificate"></a>Clientcertificaat importeren
Elke persoon voor wie een clientcertificaat is afgegeven, moet het sleutelpaar importeren in de machines die ze zullen gebruiken om met de service te communiceren:

* Dubbelklik op de . PFX-bestand in Windows Verkenner
* Importcertificaat in de Persoonlijke winkel met ten minste deze optie:
  * Alle uitgebreide eigenschappen opnemen die zijn aangevinkt

## <a name="copy-client-certificate-thumbprints"></a>Duimafdrukken voor clientcertificaat kopiëren
Elke persoon voor wie een clientcertificaat is uitgegeven, moet deze stappen volgen om de duimafdruk van zijn certificaat te verkrijgen, die aan het serviceconfiguratiebestand wordt toegevoegd:

* Voer certmgr.exe uit
* Het tabblad Persoonlijk selecteren
* Dubbelklik op het clientcertificaat dat moet worden gebruikt voor verificatie
* Selecteer in het dialoogvenster Certificaat dat wordt geopend het tabblad Details
* Controleer of Alles weergeven wordt weergegeven
* Het veld met de naam Duimafdruk in de lijst selecteren
* De waarde van de duimafdruk kopiëren
  * Niet-zichtbare Unicode-tekens verwijderen voor het eerste cijfer
  * Alle spaties verwijderen

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Toegestane clients configureren in het serviceconfiguratiebestand
Werk de waarde van de volgende instelling in het serviceconfiguratiebestand bij met een door komma's gescheiden lijst met de duimafdrukken van de clientcertificaten die toegang tot de service hebben gekregen:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Intrekkingscontrole clientcertificaat configureren
De standaardinstelling controleert niet bij de certificeringsinstantie voor de intrekkingsstatus van clientcertificaat. Als u de controles wilt inschakelen, wijzigt u de volgende instelling met een van de waarden die zijn gedefinieerd in de Enumeration X509RevocationMode:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>PFX-bestand maken voor zelfondertekende versleutelingscertificaten
Voer voor een versleutelingscertificaat het:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Aanpassen:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Voer het wachtwoord in en exporteer vervolgens het certificaat met de volgende opties:

* Ja, exporteer de privésleutel
* Alle uitgebreide eigenschappen exporteren
* U hebt het wachtwoord nodig bij het uploaden van het certificaat naar de cloudservice.

## <a name="export-encryption-certificate-from-certificate-store"></a>Versleutelingscertificaat exporteren vanuit certificaatarchief
* Certificaat zoeken
* Klik op Acties -> Alle taken -> exporteren...
* Uitvoercertificaat in een . PFX-bestand met de volgende opties: 
  * Ja, exporteer de privésleutel
  * Neem indien mogelijk alle certificaten op in het certificeringspad 
* Alle uitgebreide eigenschappen exporteren

## <a name="upload-encryption-certificate-to-cloud-service"></a>Versleutelingscertificaat uploaden naar cloudservice
Uploadcertificaat met het bestaande of gegenereerde . PFX-bestand met het sleutelpaar voor versleuteling:

* Voer het wachtwoord in dat de persoonlijke sleutelgegevens beschermt

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Versleutelingscertificaat bijwerken in serviceconfiguratiebestand
Werk de duimafdrukwaarde van de volgende instellingen in het serviceconfiguratiebestand bij met de duimafdruk van het certificaat dat naar de cloudservice is geüpload:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Algemene certificaatbewerkingen
* Het TLS/SSL-certificaat configureren
* Clientcertificaten configureren

## <a name="find-certificate"></a>Certificaat zoeken
Volg deze stappen:

1. Voer mmc.exe uit.
2. Bestand -> Module toevoegen/verwijderen...
3. Selecteer **Certificaten**.
4. Klik op**toevoegen**.
5. Kies de locatie van het certificaatarchief.
6. Klik op **Voltooien**.
7. Klik op **OK**.
8. **Certificaten**uitvouwen .
9. Het certificaatwinkelknooppunt uitbreiden.
10. Het onderliggende certificaat uitvouwen.
11. Selecteer een certificaat in de lijst.

## <a name="export-certificate"></a>Certificaat exporteren
In de **wizard Certificaat export:**

1. Klik op **Volgende**.
2. Selecteer **Ja**en **exporteer vervolgens de privésleutel**.
3. Klik op **Volgende**.
4. Selecteer de gewenste uitvoerbestandsindeling.
5. Controleer de gewenste opties.
6. Wachtwoord **controleren**.
7. Voer een sterk wachtwoord in en bevestig het.
8. Klik op **Volgende**.
9. Typ of blader door een bestandsnaam waar u het certificaat moet opslaan (gebruik een . PFX-extensie).
10. Klik op **Volgende**.
11. Klik op **Voltooien**.
12. Klik op **OK**.

## <a name="import-certificate"></a>Certificaat importeren
Ga als een van de drie keer naar de wizard Certificaat importeren:

1. Selecteer de winkellocatie.
   
   * Selecteer **Huidige gebruiker** als alleen processen die onder de huidige gebruiker worden uitgevoerd, toegang krijgen tot de service
   * Selecteer **Lokale machine** als andere processen in deze computer toegang krijgen tot de service
2. Klik op **Volgende**.
3. Als u uit een bestand importeert, bevestigt u het bestandspad.
4. Als u een . PFX-bestand:
   1. Voer het wachtwoord in dat de privésleutel beschermt
   2. Importopties selecteren
5. Selecteer certificaten 'Plaatsen' in het volgende archief
6. Klik op **Bladeren**.
7. Selecteer de gewenste winkel.
8. Klik op **Voltooien**.
   
   * Als het opgeslagen trusted root certification authority is gekozen, klikt u op **Ja**.
9. Klik op **OK** op alle dialoogvensters.

## <a name="upload-certificate"></a>Certificaat uploaden
In [Azure Portal](https://portal.azure.com/)

1. Selecteer **Cloudservices**.
2. Selecteer de cloudservice.
3. Klik in het bovenste menu op **Certificaten**.
4. Klik op de onderste balk op **Uploaden**.
5. Selecteer het certificaatbestand.
6. Als het een. PFX-bestand, voer het wachtwoord voor de privésleutel in.
7. Zodra u klaar bent, kopieert u de duimafdruk van het certificaat uit het nieuwe item in de lijst.

## <a name="other-security-considerations"></a>Andere veiligheidsoverwegingen
De TLS-instellingen die in dit document worden beschreven, versleutelen de communicatie tussen de service en haar clients wanneer het HTTPS-eindpunt wordt gebruikt. Dit is belangrijk omdat referenties voor databasetoegang en mogelijk andere gevoelige informatie in de communicatie zijn opgenomen. Houd er echter rekening mee dat de service de interne status, inclusief referenties, behoudt in de interne tabellen in de Microsoft Azure SQL-database die u hebt opgegeven voor metagegevensopslag in uw Microsoft Azure-abonnement. Die database is gedefinieerd als onderdeel van de volgende instelling in uw serviceconfiguratiebestand (. CSCFG-bestand): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Referenties die in deze database zijn opgeslagen, worden versleuteld. Als aanbevolen procedure moet u er echter voor zorgen dat zowel web- als werknemersrollen van uw service-implementaties up-to-date en veilig worden gehouden, omdat ze beide toegang hebben tot de metagegevensdatabase en het certificaat dat wordt gebruikt voor versleuteling en decryptie van opgeslagen referenties. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

