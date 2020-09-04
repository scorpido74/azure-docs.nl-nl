---
title: Implementatiehandleiding voor FortiGate | Microsoft Docs
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en FortiGate SSL VPN.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 999e19ffad1d18e163881c844cbf30f8b7fef574
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88657054"
---
# <a name="fortigate-deployment-guide"></a>Implementatiehandleiding voor FortiGate

## <a name="contents"></a>Inhoud

- De FortiGate-licentie inwisselen
- Firmware downloaden
- De FortiGate-VM implementeren
   - Een statisch IP-adres instellen en een FQDN toewijzen
   - Een nieuwe regel voor inkomend verkeer voor een netwerkbeveiligingsgroep maken voor TCP-poort
- Een aangepaste Azure-app maken voor FortiGate
- Groepslidmaatschap instellen
   - Groepen maken voor gebruikers
- De FortiGate-VM configureren
   - De licentie installeren
   - Firmware bijwerken
   - De beheerpoort wijzigen in TCP
   - Het SAML-handtekeningcertificaat van Azure Active Directory uploaden
   - Een aangepast SSL-certificaat uploaden en configureren
   - Opdrachtregelconfiguratie uitvoeren
   - VPN-portals en firewallbeleid maken
- Aanmelden met Azure testen

## <a name="redeeming-the-fortigate-license"></a>De FortiGate-licentie inwisselen

Het product Fortinet FortiGate Next-Generation Firewall is beschikbaar als een virtuele machine in Azure IaaS. Er zijn twee licentiemodi voor deze virtuele machine:

- Betalen per gebruik (PAYG)
- Bring Your Own License (BYOL)

Als onderdeel van de samenwerking met Fortinet voor het bieden van adviezen voor SHA (Secure Hybrid Access), kan Fortinet licenties verstrekken aan leden van het Azure AD Get to Production SHA-team. In gevallen waarin geen licentie wordt afgegeven, werkt de implementatie met betalen per gebruik ook.

In gevallen waarin een licentie is verleend, verstrekt Fortinet een registratiecode die online moet worden ingewisseld.

![Fortigate SSL VPN](registration-code.png)

1. Registreer u op https://support.fortinet.com/.
2. Na de registratie, kunt u zich aanmelden op https://support.fortinet.com/.
3. Ga naar **Asset** - > **Register/Activate**.
4. Voer de registratiecode in die u hebt ontvangen van Fortinet.
5. Geef de registratiecode op, selecteer **The product will be used by a non-government user** en klik op **Next**.
6. Voer een productbeschrijving in (zoals FortiGate), stel de Fortinet-partner in als **Other** - > **Microsoft** en klik op **Next**.
7. Accepteer de **Fortinet Product Registration Agreement** en klik op **Next**.
8. Accepteer de **Terms** en klik op **Confirm**.
9. Klik op **License File Download** en sla de licentie op voor eventueel later gebruik.


## <a name="download-firmware"></a>Firmware downloaden

Op het moment van schrijven wordt de Fortinet FortiGate Azure-VM geleverd zonder de firmwareversie die nodig is voor SAML-verificatie. U kunt de nieuwste versie downloaden op de site van Fortinet.

1. Meld u aan op https://support.fortinet.com/.
2. Ga naar **Download** - > **Firmware Images**.
3. Klik op **Download** rechts van **Release Notes**.
4. Klik op **v6.**
5. Klik op **6.**
6. Klik op **6.4.**
7. Download **FGT_VM64_AZURE-v6-build1637-FORTINET.out** door in dezelfde rij op de link **HTTPS** te klikken.
8. Sla het bestand op voor later gebruik.


## <a name="deploy-the-fortigate-vm"></a>De FortiGate-VM implementeren

1. Ga naar https://portal.azure.com en meld u aan bij het abonnement waarin de virtuele machine van FortiGate wilt implementeren.
2. Maak een nieuwe resourcegroep of open de resourcegroep waarin u de virtuele machine van FortiGate wilt implementeren.
3. Klik op **Toevoegen**
4. Typ 'Forti' in het zoekvak **Marketplace doorzoeken** en selecteer **Fortinet FortiGate Next-** **Generation Firewall**.
5. Selecteer een abonnement (BYOL als u een licentie hebt of anders Betalen per gebruik) en klik op **Maken**.
6. De VM-configuratie maken

    ![Fortigate SSL VPN](virtual-machine.png)

7. Stel het type verificatie in op **Wachtwoord** en geef beheerdersreferenties op voor de VM.
8. Klik op **Beoordelen en maken**.
9. Klik op **Maken**
10. Wacht totdat de implementatie van de VM is voltooid.


### <a name="set-a-statuc-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Een statisch IP-adres instellen en een FQDN toewijzen

Voor een consistente gebruikerservaring is het wenselijk om het openbare IP-adres voor de FortiGate-VM statisch toe te wijzen. Daarnaast is toewijzing van een FQDN ook handig om dezelfde redenen.

_Een statisch, openbaar IP-adres instellen_

1. Ga naar https://portal.azure.com en open de instellingen voor de FortiGate-VM.
2. Klik op het scherm **Overzicht** op het openbare IP-adres.

    ![Fortigate SSL VPN](public-ip-address.png)

3. Klik op **Statisch** en klik vervolgens op **Opslaan**.

_Een FQDN toewijzen_

Als u eigenaar bent van een openbaar routeerbare domeinnaam voor de omgeving waarin de FortiGate-VM wordt geïmplementeerd, maakt u een hostrecord (A-record) voor de VM die is toegewezen aan het openbare IP-adres dat hierboven statisch is toegewezen.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Een nieuwe regel voor inkomend verkeer voor een netwerkbeveiligingsgroep maken voor TCP-poort

1. Ga naar https://portal.azure.com en open de instellingen voor de FortiGate-VM.
2. Klik in het menu aan de linkerkant op **Netwerken**. De netwerkinterface wordt weergegeven evenals de regels voor binnenkomende poorten.
3. Klik op **Regel voor binnenkomende poort toevoegen**.
4. Een nieuwe regel voor binnenkomende poort maken voor TCP 8443

    ![Fortigate SSL VPN](port-rule.png)

5. Klik op **Toevoegen**


## <a name="create-a-custom-azure-app-for-fortigate"></a>Een aangepaste Azure-app maken voor FortiGate

1. Ga naar https://portal.azure.com en open de blade Azure Active Directory voor de tenant die de identiteit levert voor FortiGate-aanmeldingen.
2. Klik in het linkermenu op **Bedrijfstoepassingen**.
3. Klik op **Nieuwe toepassing**.
4. Klik op **Toepassing die niet in de galerij voorkomt**.
5. Geef een naam op (zoals FortiGate) en klik op **Toevoegen**.
6. Klik in het linkermenu op **Gebruikers en groepen**.
7. Voeg gebruikers toe die zich kunnen aanmelden en klik op **Toewijzen**.
8. Klik in het linkermenu op **Eenmalige aanmelding**.
9. Klik op **SAML**.
10. Klik in het gedeelte **Standaard SAML-configuratie** op het potlood om de configuratie te bewerken.
11. Configureren
    - Stel de id (entiteits-id) in op `https://<address>/remote/saml/metadata`.
    - Stel de antwoord-URL (URL voor Assertion Consumer Service) in op `https://<address>/remote/saml/login`.
    - Stel de URL voor afmelden in op `https://<address>/remote/saml/logout`.

    Waarbij `address` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

    Noteer deze URL's voor later gebruik:

    - Entiteits-id
    - Antwoord-URL
    - Afmeldings-URL
12. Klik op **Opslaan**.
13. Sluit het venster met de standaard SAML-configuratie.
14. Download onder **3: SAML-handtekeningcertificaat** het **certificaat (base64)** en sla het op voor later gebruik.
15. Kopieer onder **4: app-naam instellen** de URL voor Azure-aanmelding, de Azure AD-id en de URL voor afmelden bij Azure en sla deze op voor later gebruik.
    - URL voor aanmelden bij Azure
    - Azure AD-id
    - URL voor afmelden bij Azure
16. Klik onder **2 – Gebruikerskenmerken en claims** op het potlood om de configuratie te bewerken.
17. Klik op **Nieuwe claim toevoegen**.
18. Geef de naam **username** op.
19. Stel Bronkenmerk in op **user.userprincipalname**.
20. Klik op **Opslaan**.
21. Klik op **Een groepsclaim toevoegen**.
22. Selecteer **Alle groepen**.
23. Selecteer **De naam van de groepsclaim aanpassen**.
24. Geef de naam **group** op.
25. Klik op **Opslaan**.


## <a name="prepare-for-group-matching"></a>Groepslidmaatschap instellen

FortiGate maakt het mogelijk om op basis van groepslidmaatschap verschillende gebruikerservaringen voor de portal aan te bieden na aanmelding. Zo kan er een ervaring zijn voor de groep Marketing en een andere voor de groep Finance.

Configureer dit als volgt.

### <a name="create-groups-for-users"></a>Groepen maken voor gebruikers

1. Ga naar https://portal.azure.com en open de blade Azure Active Directory voor de tenant die de identiteit levert voor FortiGate-aanmeldingen.
2. Klik op **Groepen**.
3. Klik op **Nieuwe groep**.
4. Maak een groep met deze instellingen:
    - Groepstype = Beveiliging
    - Groepsnaam = `a meaningful name`
    - Groepsbeschrijving = `a meaningful description for the group`
    - Type lidmaatschap = Toegewezen
    - Leden = `users for the user experience that will map to this group`
5. Herhaal stap 3 en 4 voor eventuele extra gebruikerservaringen.
6. Als de groepen zijn gemaakt, selecteert u elke groep en noteert u de object-id voor elke groep.
7. Sla deze object-id's en de groepsnamen op voor later gebruik.


## <a name="configure-the-fortigate-vm"></a>De FortiGate-VM configureren

### <a name="install-the-license"></a>De licentie installeren

1. Ga naar `https://<address>`

    waarbij `address` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Als de implementatie het BYOL-model gebruikt, ziet u een prompt voor het uploaden van een licentie. Selecteer het licentiebestand dat eerder is en upload het. Klik op **OK** en start de FortiGate-VM opnieuw op.

    ![Fortigate SSL VPN](license.png)

5. Nadat de VM opnieuw is opgestart, meldt u zich opnieuw aan met de referenties van de beheerder om de licentie te valideren

### <a name="update-firmware"></a>Firmware bijwerken

1. Ga naar `https://<address>`

    waarbij `address` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Klik op **System** in het menu aan de linkerkant.
5. Klik op **Firmware** in het linkermenu onder System.
6. Klik op de pagina Firmware Management op **Browse** en selecteer het firmwarebestand dat u eerder hebt gedownload.
7. Negeer de waarschuwing en klik op **Backup config and upgrade**.

    ![Fortigate SSL VPN](backup-configure-upgrade.png)

8. Klik op **Continue**.
9. Wanneer u wordt gevraagd om de FortiGate-configuratie op te slaan (als een .conf-bestand), klikt u op **Save**.
10. Wacht tot de firmware is geüpload, is toegepast en de FortiGate-VM opnieuw is opgestart.
11. Als de FortiGate-VM opnieuw is opgestart, meldt u zich opnieuw aan met de referenties van de beheerder.
12. Wanneer u wordt gevraagd om het dashboard in te stellen, klikt u op **Later**.
13. Wanneer de video start, klikt u op **OK**.

### <a name="change-the-management-port-to-tcp"></a>De beheerpoort wijzigen in TCP

1. Ga naar `https://<address>`

    waarbij `address` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Klik op **System** in het menu aan de linkerkant.
5. Wijzig onder Administration Settings de HTTPS-poort in **8443**.
6. Klik op **Toepassen**.
7. Als de wijziging is toegepast, probeert de browser de pagina Administration Settings opnieuw te laden maar dit lukt niet. Het adres van de beheerpagina is vanaf nu namelijk `https://<address>`.

    ![Fortigate SSL VPN](certificate.png)

### <a name="upload-the-azure-active-directory-saml-signing-certificate"></a>Het SAML-handtekeningcertificaat van Azure Active Directory uploaden

1. Ga naar `https://<address>`

    waarbij `address` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Klik op **System** in het menu aan de linkerkant.
5. Klik onder System op **Certificates**.
6. Klik op **Import** - > **Remote Certificate**.
7. Blader naar het certificaat dat u hebt gedownload uit de implementatie van de aangepaste FortiGate-app in de Azure-tenant, selecteer het certificaat en klik op **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Een aangepast SSL-certificaat uploaden en configureren

U kunt de FortiGate-VM configureren met uw eigen SSL-certificaat dat ondersteuning biedt voor de FQDN die u gebruikt. Als u toegang hebt tot een SSL-certificaat dat is verpakt met de persoonlijke sleutel in PFX-indeling, kan dit voor dit doel worden gebruikt.

1. Ga naar `https://<address>`

    waarbij `address` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Klik op **System** in het menu aan de linkerkant.
5. Klik onder System op **Certificates**.
6. Klik op **Import** - > **Local Certificate**.
7. Klik op **PKCS #12 Certificate**.
8. Blader naar het PFX-bestand met het SSL-certificaat en de persoonlijke sleutel.
9. Geef het wachtwoord voor het PFX-bestand op.
10. Geef een beschrijvende naam op voor het certificaat.
11. Klik op **OK**
12. Klik op **System** in het menu aan de linkerkant.
13. Klik onder System op **Settings**.
14. Open onder Administration Settings de vervolgkeuzelijst naast HTTPS server certificate en selecteer het SSL-certificaat dat u hierboven hebt geïmporteerd.
15. Klik op **Toepassen**.
16. Sluit het browservenster en ga weer naar `https://<address>`.
17. Meld u aan met de beheerdersreferenties voor FortiGate en kijk of het juiste SSL-certificaat wordt gebruikt.


### <a name="perform-command-line-configuration"></a>Opdrachtregelconfiguratie uitvoeren

_Opdrachtregelconfiguratie uitvoeren voor SAML-verificatie_

1. Ga naar https://portal.azure.com en open de instellingen voor de FortiGate-VM.
2. Klik in het menu aan de linkerkant op **Serial Console**.
3. Meld u aan bij de console met de beheerdersreferenties voor de FortiGate-VM.

    Voor de volgende stap zijn de URL's nodig die u eerder hebt genoteerd. Te weten:

    - Entiteits-id
    - Antwoord-URL
    - Afmeldings-URL
    - URL voor aanmelden bij Azure
    - Azure AD-id
    - URL voor afmelden bij Azure
4. Voer de volgende opdrachten uit in de console:

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > De afmeldings-URL voor Azure bevat een vraagteken (?). U moet een speciale toetsencombinatie gebruiken om dit teken goed in te voeren in de console van FortiGate. De URL is doorgaans

    `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`

    Als u dit wilt opgeven in de console, typt u het volgende:

    ```
    set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
    ```
    Gevolgd door CTRL + V.

    Plak daarna de rest van de URL om de regel te voltooien.

    ```
    set idp-single-logout-url
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
    ```

5. Controleer de configuratie met deze opdracht:

    ```
    show user saml
    ```

_Opdrachtregelconfiguratie uitvoeren voor groepslidmaatschap_

1. Ga naar https://portal.azure.com en open de instellingen voor de FortiGate-VM.
2. Klik in het menu aan de linkerkant op **Serial Console**.
3. Meld u aan bij de console met de beheerdersreferenties voor de FortiGate-VM.
4. Voer de volgende opdrachten uit in de console:

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    Herhaal de opdracht edit `group 1 name` voor elke extra groep die een andere portalervaring moet hebben in FortiGate.

_Opdrachtregelconfiguratie uitvoeren voor verificatie-time-out_

1. Ga naar https://portal.azure.com en open de instellingen voor de FortiGate-VM.
2. Klik in het menu aan de linkerkant op **Serial Console**.
3. Meld u aan bij de console met de beheerdersreferenties voor de FortiGate-VM.
4. Voer de volgende opdrachten uit in de console:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>VPN-portals en firewallbeleid maken

1. Ga naar `https://<address>`

    waarbij `address` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
3. Klik op **VPN** in het menu aan de linkerkant.
4. Klik onder VPN op **SSL-VPN Portals**.
5. Klik op **Create New**.
6. Geef een naam op (meestal de naam van de Azure-groep die wordt gebruikt voor het bieden van de aangepaste portalervaring).
7. Klik op het plusteken ( **+** ) naast Source IP Pools, selecteer de standaardpool en klik op **Close**.
8. Pas de ervaring voor deze groep aan. Voor testdoeleinden kan dit een aanpassing zijn van het portalbericht en het thema. Hier kunt u ook aangepaste bladwijzers maken die gebruikers naar interne bronnen sturen.
9. Klik op **OK**
10. Herhaal stap 5 tot en met 9 voor elke Azure-groep die een aangepaste portalervaring moet krijgen.
11. Klik onder VPN op **SSL-VPN Settings**.
12. Klik op het plusteken ( **+** ) naast Listen on Interfaces.
13. Selecteer **Port1** en klik op **Close**.


14. Als er eerder een aangepast SSL-certificaat is geïnstalleerd, selecteert u dit certificaat in de vervolgkeuzelijst Server Certificate.
15. Klik onder Authentication/Portal Mapping op **Create New**.
16. Kies de eerste Azure-groep en koppel deze aan de portal met dezelfde naam.
17. Klik op **OK**
18. Herhaal stap 15 tot en met 17 voor elke koppeling van Azure-groep en portal.
19. Bewerk **All Other Users/Groups** onder Authentication/Portal Mapping.
20. Stel de portal in op **full-access**.
21. Klik op **OK**
22. Klik op **Toepassen**.
23. Ga naar het begin van de pagina SSL-VPN Settings en klik op de waarschuwing **No SSL-VPN policies**
    **exist. Click here to create a new SSL-VPN policy using these settings**.
24. Geef een naam op, zoals **VPN Grp**.
25. Stel Outgoing Interface in op **port**.
26. Klik op **Source**.
27. Selecteer **All** onder Address.
28. Selecteer onder User de eerste Azure-groep.
29. Klik op **Close**.
30. Klik op **Destination**.
31. Onder Address zal dit meestal het interne netwerk zijn. Selecteer login.microsoft.com voor testdoeleinden.
32. Klik op **Close**.
33. Klik op **Service**.
34. Klik op **All**.
35. Klik op **Close**.
36. Klik op **OK**
37. Klik op **Policy & Objects** in het menu aan de linkerkant.
38. Klik onder Policy & Objects op **Firewall Policy**.
39. Vouw **SSL-VPN tunnel interface (ssl.root) -> port** uit.
40. Klik met de rechtermuisknop op het VPN-beleid dat u eerder hebt gemaakt (**VPN Grp 1**) en selecteer **Copy**.
41. Klik met de rechtermuisknop onder het VPN-beleid en selecteer **Paste** - > **Below**.
42. Bewerk het nieuwe beleid door een andere naam op te geven (bijvoorbeeld **VPN Grp2**) en de groep te wijzigen waarop het beleid van toepassing is (een andere Azure-groep).
43. Klik met de rechtermuisknop op het nieuwe beleid en stel de status in op **Enabled**.


## <a name="test-sign-in-using-azure"></a>Aanmelden met Azure testen

1. Gebruik een InPrivate-sessie in uw browser om naar `https://<address>` te gaan. 
2. De aanmelding moet worden omgeleid naar Azure Active Directory.
3. Nadat u referenties hebt opgegeven voor een gebruiker die is toegewezen aan de FortiGate-app in de Azure-tenant, moet de juiste gebruikersportal worden weergegeven.

    ![Fortigate SSL VPN](test-sign-in.png)
