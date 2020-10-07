---
title: Implementatiehandleiding voor FortiGate | Microsoft Docs
description: De Fortinet FortiGate-firewall van de volgende generatie instellen en gebruiken.
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
ms.openlocfilehash: 357eb0a60e6246996de9ab75337ecc213d845ae7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273327"
---
# <a name="fortigate-deployment-guide"></a>Implementatiehandleiding voor FortiGate

Met deze implementatiehandleiding leert u de Fortinet FortiGate-firewall van de volgende generatie instellen en gebruiken.

## <a name="redeem-the-fortigate-license"></a>De FortiGate-licentie inwisselen

Het product Fortinet FortiGate Next-Generation Firewall is beschikbaar als een virtuele machine in Azure IaaS (infrastructuur als een dienst). Er zijn twee licentiemodi voor deze virtuele machine: betalen per gebruik en eigen licentie.

Fortinet kan leden van het team "Get to Production Secure Hybrid Access (SHA)" van Azure Active Directory (Azure AD) licenties verschaffen. In gevallen waarin geen licentie wordt afgegeven, werkt de implementatie met betalen per gebruik ook.

Als een licentie is verleend, verstrekt Fortinet een registratiecode die online moet worden ingewisseld.

![Schermopname van de FortiGate SSL VPN-registratiecode.](registration-code.png)

1. Registreer u op https://support.fortinet.com/.
2. Na de registratie, kunt u zich aanmelden op https://support.fortinet.com/.
3. Ga naar **Asset** > **Register/Activate**.
4. Voer de registratiecode in die u hebt ontvangen van Fortinet.
5. Geef de registratiecode op, selecteer **The product will be used by a non-government user** en selecteer **Next**.
6. Voer een productbeschrijving in (zoals FortiGate), stel de Fortinet-partner in als **Other** > **Microsoft**, en selecteer **Next**.
7. Accepteer de **Fortinet Product Registration Agreement** en selecteer **Next**.
8. Accepteer de **Terms** en selecteer **Confirm**.
9. Selecteer de **License File Download** en sla de licentie op voor eventueel later gebruik.


## <a name="download-firmware"></a>Firmware downloaden

De Fortinet FortiGate Azure-VM wordt momenteel geleverd zonder de firmwareversie die nodig is voor SAML-verificatie. U kunt de nieuwste versie downloaden op de site van Fortinet.

1. Meld u aan op https://support.fortinet.com/.
2. Ga naar **Download** > **Firmware Images**.
3. Selecteer **Download** rechts van **Release Notes**.
4. Selecteer **V6.** > **6.** > **6.4.** .
5. Download **FGT_VM64_AZURE-v6-build1637-FORTINET.out** door in dezelfde rij de link **HTTPS** te selecteren.
6. Sla het bestand op voor later.


## <a name="deploy-the-fortigate-vm"></a>De FortiGate-VM implementeren

1. Ga naar https://portal.azure.com en meld u aan bij het abonnement waarin de virtuele machine van FortiGate wilt implementeren.
2. Maak een nieuwe resourcegroep of open de resourcegroep waarin u de virtuele machine van FortiGate wilt implementeren.
3. Selecteer **Toevoegen**.
4. Voer *Forti* in bij **Marktplaats doorzoeken**. Selecteer **Fortinet FortiGate Next-Generation Firewall**
5. Selecteer het software-abonnement (eigen licentie indien u die hebt of anders betalen per gebruik). Selecteer **Maken**.
6. Vul de VM-configuratie in.

    ![Schermafbeelding van Een virtuele machine maken](virtual-machine.png)

7. Stel het **Authentication type** in op **Password** en geef beheerdersreferenties in voor de virtuele machine.
8. Selecteer **Beoordelen en maken** > **Maken**.
9. Wacht totdat de implementatie van de VM is voltooid.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Een statisch IP-adres instellen en een FQDN toewijzen

Voor een consistente gebruikerservaring  wijst u het openbare IP-adres voor de FortiGate-VM statisch toe. Wijs het daarnaast toe aan een FQDN (Fully Qualified Domain Name).

1. Ga naar https://portal.azure.com en open de instellingen voor de FortiGate-VM.
2. Selecteer op het scherm **Overview** op het openbare IP-adres.

    ![Schermopname van de Forti Gate SSL VPN.](public-ip-address.png)

3. Selecteer **Static** > **Save**.

Als u eigenaar bent van een openbaar routeerbare domeinnaam voor de omgeving waarin de FortiGate-VM wordt geïmplementeerd, maakt u een hostrecord (A-record) voor de VM. Deze record wordt toegewezen aan het voorafgaande openbare IP-adres dat statisch is toegewezen.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Een nieuwe regel voor inkomend verkeer voor een netwerkbeveiligingsgroep maken voor TCP-poort

1. Ga naar https://portal.azure.com en open de instellingen voor de FortiGate-VM.
2. Selecteer **Networking** in het menu aan de linkerkant. De netwerkinterface wordt weergegeven evenals de regels voor binnenkomende poorten.
3. Selecteer **Add inbound port rule**.
4. Maak een nieuwe regel voor binnenkomende poort TCP 8443.

    ![Schermafbeelding van Add inbound security rule.](port-rule.png)

5. Selecteer **Toevoegen**.


## <a name="create-a-custom-azure-app-for-fortigate"></a>Een aangepaste Azure-app maken voor FortiGate

1. Ga naar https://portal.azure.com en open het venster Azure AD voor de tenant die de identiteit levert voor FortiGate-aanmeldingen.
2. Selecteer in het linkermenu **Enterprise-toepassingen**.
3. Selecteer **New Application** > **Non-gallery application**.
4. Voer een naam in (bijvoorbeeld Forti Gate) en selecteer **Add**.
5. Selecteer **Users and groups** in het menu.
6. Voeg gebruikers toe die zich kunnen aanmelden en selecteer **Toewijzen**.
7. Selecteer **Single sign-on** in het linkermenu.
8. Selecteer **SAML**.
9. Selecteer in het gedeelte **Standaard SAML-configuratie** het potloodpictogram om de configuratie te bewerken.
10. Configureer het volgende:
    - **Identifier (Entity ID)** moet `https://<address>/remote/saml/metadata` zijn.
    - **Reply URL (Assertion Consumer Service URL)** moet  `https://<address>/remote/saml/login` zijn.
    - **Logout URL** moet `https://<address>/remote/saml/logout` zijn.

    `<address>` is de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

11. Noteer deze URL's voor later gebruik: Entiteits-id, antwoord-URL en afmeldings-URL.
12. Selecteer **Save**, and sluit **Basic SAML Configuration**.
13. Download onder **3 – SAML Signing Certificate** het **Certificate (base64)** en sla het op voor later gebruik.
14. Kopieer onder **4 – Set up (App Name)** de URL voor Azure-aanmelding, de Azure AD-id en de URL voor afmelden bij Azure en sla deze op voor later gebruik.
15. Klik onder **2 – User Attributes and Claims** op het potloodpictogram om de configuratie te bewerken.
16. Selecteer **Add new claim**en stel de naam in op **username**.
17. Stel het bronkenmerk in op **user.userprincipalname**.
18. Selecteer **Save** > **Add a group claim** > **All groups**.
19. Selecteer **Customize the name of the group claim**, en stel de naam in op **group**.
20. Selecteer **Opslaan**.


## <a name="prepare-for-group-matching"></a>Matchen van groepen voorbereiden

FortiGate maakt het mogelijk om op basis van groepslidmaatschap verschillende gebruikerservaringen voor de portal aan te bieden na aanmelding. Zo kan er een ervaring zijn voor de groep Marketing en een andere voor de groep Finance. Zo maakt u groepen voor gebruikers:

1. Ga naar https://portal.azure.com en open het venster Azure AD voor de tenant die de identiteit levert voor FortiGate-aanmeldingen.
2. Selecteer **Groups** > **New Group**.
3. Maak een groep met de volgende details:
    - Groepstype = Beveiliging
    - Groepsnaam = `a meaningful name`
    - Groepsbeschrijving = `a meaningful description for the group`
    - Type lidmaatschap = Toegewezen
    - Leden = `users for the user experience that will map to this group`
4. Herhaal stap 3 en 4 voor eventuele extra gebruikerservaringen.
5. Nadat u de groepen hebt gemaakt, selecteert u elke groep en noteert u de **Object-id** voor elke groep.
6. Sla deze object-cd's en de groepsnamen op voor later gebruik.


## <a name="configure-the-fortigate-vm"></a>De FortiGate-VM configureren

De volgende secties helpen u bij het instellen van de FortiGate-VM.

### <a name="install-the-license"></a>De licentie installeren

1. Ga naar `https://<address>`. waarbij `<address>` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Als voor de implementatie een eigen licentie nodig is, wordt u gevraagd een licentie te uploaden. Selecteer het licentiebestand dat u eerder hebt gemaakt en upload het. Selecteer **OK** en start de FortiGate-VM opnieuw op.

    ![Schermopname van de FortiGate VM-licentie](license.png)

5. Nadat de VM opnieuw is opgestart, meldt u zich opnieuw aan met de referenties van de beheerder om de licentie te valideren

### <a name="update-firmware"></a>Firmware bijwerken

1. Ga naar `https://<address>`. waarbij `<address>` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Selecteer **System** > **Firmware** in het menu aan de linkerkant.
5. Selecteer op de pagina **Firmware Management** op **Browse** en selecteer het firmwarebestand dat u eerder hebt gedownload.
6. Negeer de waarschuwing en selecteer **Backup config and upgrade**.

    ![Schermafbeelding van Firmware Management.](backup-configure-upgrade.png)

7. Selecteer **Doorgaan**.
8. Wanneer u wordt gevraagd om de FortiGate-configuratie op te slaan (als een .conf-bestand), selecteert u **Save**.
9. Wacht tot de firmware is geüpload en toegepast. Wacht totdat de FortiGate-VM opnieuw is opgestart.
10. Als de FortiGate-VM opnieuw is opgestart, meldt u zich opnieuw aan met de referenties van de beheerder.
11. Wanneer u wordt gevraagd het dashboard in te stellen, selecteert u **Later**.
12. Wanneer de video start, selecteert u **OK**.

### <a name="change-the-management-port-to-tcp"></a>De beheerpoort wijzigen in TCP

1. Ga naar `https://<address>`. waarbij `<address>` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Selecteer **System** in het menu aan de linkerkant.
5. Wijzig onder **Administration Settings** de HTTPS-poort in **8443** en selecteer **Apply**.
6. Als de wijziging is toegepast, probeert de browser de beheerpagina opnieuw te laden maar dit lukt niet. Het adres van de beheerpagina is vanaf nu namelijk `https://<address>`.

    ![Schermafbeelding van Upload Remote Certificate.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Het SAML-handtekeningcertificaat van Azure AD uploaden

1. Ga naar `https://<address>`. waarbij `<address>` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Selecteer **System** > **Certificates** in het menu aan de linkerkant
5. Selecteer **Import** > **Remote Certificate**.
6. Blader naar het certificaat dat u hebt gedownload uit de implementatie van de aangepaste FortiGate-app in de Azure-tenant. Selecteer het en vervolgens **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Een aangepast SSL-certificaat uploaden en configureren

U kunt de FortiGate-VM configureren met uw eigen SSL-certificaat dat ondersteuning biedt voor de FQDN die u gebruikt. Als u toegang hebt tot een SSL-certificaat dat is verpakt met de persoonlijke sleutel in PFX-indeling, kan dit voor dit doel worden gebruikt.

1. Ga naar `https://<address>`. waarbij `<address>` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Selecteer **System** > **Certificates** in het menu aan de linkerkant
5. Selecteer **Import** > **Local Certificate** > **PKCS #12 Certificate**.
6. Blader naar het PFX-bestand dat het SSL-certificaat en de persoonlijke sleutel bevat.
7. Geef het .PFX-wachtwoord en een beschrijvende naam voor het certificaat op. Selecteer vervolgens **OK**.
8. Selecteer **System** > **Settings** in het menu aan de linkerkant.
9. Open onder **Administration Settings** de lijst naast **HTTPS server certificate** en selecteer het SSL-certificaat dat u eerder hebt geïmporteerd.
10. Selecteer **Toepassen**.
11. Sluit het browservenster en ga naar `https://<address>`.
12. Meld u aan met de FortiGate-beheerdersreferenties. U ziet nu het juiste SSL-certificaat dat wordt gebruikt.


### <a name="perform-command-line-configuration"></a>Opdrachtregelconfiguratie uitvoeren

In de volgende secties vindt u de stappen voor verschillende configuraties met behulp van de opdracht regel.

#### <a name="for-saml-authentication"></a>Voor SAML-verificatie

1. Ga naar https://portal.azure.com en open de instellingen voor de FortiGate-VM.
2. Selecteer **Serial Console** in het menu aan de linkerkant.
3. Meld u aan bij de console met de beheerdersreferenties voor de FortiGate-VM. Voor de volgende stap zijn de URL's nodig die u eerder hebt genoteerd.
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
    > De Azure-afmeldings-URL bevat een `?`-teken. U moet een speciale toetsencombinatie gebruiken om dit teken goed in te voeren in de console van FortiGate. De URL is doorgaans `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`. Als u dit wilt opgeven in de console, typt u:
        ```
        set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
        ```.
    Typ vervolgens CTRL+V en plak de rest van de URL om de regel te voltooien: 
        ```
        set idp-single-logout-url
        https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
        ```

5. Om de configuratie te bevestingen, moet u het volgende uitvoeren:

    ```
    show user saml
    ```

#### <a name="for-group-matching"></a>Om groepen te matchen

1. Ga naar https://portal.azure.com en open de instellingen voor de FortiGate-VM.
2. Selecteer **Serial Console** in het menu aan de linkerkant.
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

    Herhaal deze opdrachten (te beginnen vanaf de tweede regel van de code) voor elke extra groep die een andere portaalervaring moet hebben in FortiGate.

#### <a name="for-authentication-timeout"></a>Voor time-out van de verificatie

1. Ga naar https://portal.azure.com en open de instellingen voor de FortiGate-VM.
2. Selecteer **Serial Console** in het menu aan de linkerkant.
3. Meld u aan bij de console met de beheerdersreferenties voor de FortiGate-VM.
4. Voer de volgende opdrachten uit in de console:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>VPN-portals en firewallbeleid maken

1. Ga naar `https://<address>`. waarbij `<address>` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
3. Selecteer in het linkermenu **VPN** > **SSL-VPN Portals** > **Create New**.
6. Geef een naam op (meestal de naam van de Azure-groep die wordt gebruikt voor het bieden van de aangepaste portalervaring).
7. Selecteer het plusteken ( **+** ) naast **Source IP Pools**, selecteer de standaardpool en klik op **Close**.
8. Pas de ervaring voor deze groep aan. Voor testdoeleinden kan dit een aanpassing zijn van het portalbericht en het thema. Hier kunt u ook aangepaste bladwijzers maken die gebruikers naar interne bronnen sturen.
9. Selecteer **OK**.
10. Herhaal stap 5-9 voor elke Azure-groep die een aangepaste portalervaring moet krijgen.
11. Selecteer onder VPN **SSL-VPN Settings**.
12. Selecteer het plusteken ( **+** ) naast **Listen on Interfaces**, selecteer **Port1** en vervolgens **Close**.
14. Als u eerder een aangepast SSL-certificaat hebt geïnstalleerd, selecteert u dit certificaat in de vervolgkeuzelijst **Server Certificate**.
15. Selecteer onder **Authentication/Portal Mapping** op **Create New**. Kies de eerste Azure-groep en koppel deze aan het portaal met dezelfde naam. Selecteer vervolgens **OK**.
18. Herhaal stappen 15-17 voor alle Azure-groepen die u aan een portaal wilt koppelen.
19. Bewerk **All Other Users/Groups** onder **Authentication/Portal Mapping**.
20. Stel het portaal in op **full-access** en selecteer **OK** > **Apply**.
23. Ga naar het begin van de pagina **SSL-VPN Setting** en selecteer **No SSL-VPN policies exist. Click here to create a new SSL-VPN policy using these settings.**
24. Geef een naam op, zoals **VPN Grp**. Stel **Outgoing Interface** vervolgens i op **port** en selecteer **Source**.
27. Selecteer **All** onder **Address**.
28. Selecteer onder **User** de eerste Azure-groep.
29. Selecteer **Close** > **Destination**. Onder **Address** bevindt zich meestal het interne netwerk. Selecteer **login.microsoft.com** voor testdoeleinden.
32. Selecteer **Close** > **Service** > **All**. Selecteer vervolgens **Close** > **OK**.
37. Selecteer in het menu aan de linkerzijde **Policy & Objects** > **Firewall Policy**.
39. Vouw **SSL-VPN tunnel interface (ssl.root) -> port** > **port** uit.
40. Klik met de rechtermuisknop op het VPN-beleid dat u eerder hebt gemaakt (**VPN Grp 1**) en selecteer **Copy**.
41. Klik met de rechtermuisknop onder het VPN-beleid en selecteer **Paste** > **Below**.
42. Bewerk het nieuwe beleid en geeft het een andere naam (bijvoorbeeld **VPN Grp2**). Wijzig ook de groep waarop het van toepassing is (naar een andere Azure-groep).
43. Klik met de rechtermuisknop op het nieuwe beleid en stel de status in op **Enabled**.


## <a name="test-sign-in-by-using-azure"></a>Aanmelden met Azure testen

1. Gebruik een InPrivate-sessie in uw browser om naar `https://<address>` te gaan.  
2. De aanmelding moet worden omgeleid naar Azure AD.
3. Nadat u referenties hebt opgegeven voor een gebruiker die is toegewezen aan de FortiGate-app in de Azure-tenant, moet het juiste gebruikersportaal verschijnen.

    ![Schermafbeelding van FortiGate SSL VPN](test-sign-in.png)
