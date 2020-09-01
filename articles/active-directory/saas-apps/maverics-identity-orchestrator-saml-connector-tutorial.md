---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Maverics Identity Orchestrator SAML Connector | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Maverics Identity Orchestrator SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: ec5368427f50f548be965bb883683c859759bbf3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518925"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maverics-identity-orchestrator-saml-connector"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Maverics Identity Orchestrator SAML Connector

## <a name="introduction"></a>Inleiding

Strata biedt een eenvoudige manier om on-premises toepassingen te integreren met Azure AD voor verificatie en toegangsbeheer.

In deze handleiding wordt uitgelegd hoe u Maverics Identity Orchestrator&trade; configureert om:
* Gebruikers incrementeel te migreren van een on-premises identiteitssysteem naar Azure AD tijdens aanmelding bij een verouderde on-premises toepassing.
* Aanmeldingsaanvragen te routeren van een verouderd product voor webtoegangsbeheer, zoals CA SiteMinder of Oracle Access Manager, naar Azure AD.
* Gebruikers te verifiëren bij on-premises toepassingen die worden beveiligd met HTTP-headers of bedrijfseigen sessiecookies, nadat de gebruiker is geverifieerd bij Azure AD.

Strata biedt software die on-premises of in de cloud wordt geïmplementeerd, voor het ontdekken, verbinden en beheren van identiteitsproviders om gedistribueerd identiteitsbeheer te creëren voor hybride en multi-cloud ondernemingen.

In deze zelfstudie wordt uitgelegd hoe u een on-premises webtoepassing die momenteel wordt beveiligd door een verouderd product voor webtoegangsbeheer (CA SiteMinder), migreert om Azure AD te gebruiken voor verificatie en toegangsbeheer.
1. Maverics Identity Orchestrator installeren&trade;
2. Registreer uw bedrijfstoepassing bij Azure AD en configureer deze om de Maverics Azure AD SAML Zero Code Connector&trade; te gebruiken voor eenmalige aanmelding met SAML.
3. Integreer Maverics met SiteMinder en het LDAP-gebruikersarchief.
4. Stel Azure Key Vault in en configureer Maverics zodanig dat deze wordt gebruikt als de provider van geheimenbeheer.
5. Demonstreer gebruikersmigratie en sessieabstractie met behulp van Maverics om toegang tot een on-premises Java-webtoepassing te bieden.

Ga voor aanvullende installatie- en configuratie-instructies naar https://strata.io/docs

## <a name="prerequisites"></a>Vereisten

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
- Een abonnement op Maverics Identity Orchestrator SAML Connector waarvoor eenmalige aanmelding is ingesteld. Om de Maverics-software te verkrijgen, neemt u contact op met sales@strata.io

## <a name="install-maverics-identity-orchestratortrade"></a>Maverics Identity Orchestrator installeren&trade;

Om aan de slag te gaan met de installatie van Maverics Identity Orchestrator, raadpleegt u de installatie-instructies op https://strata.io/docs

## <a name="system-requirements"></a>Systeemvereisten
### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
* RHEL 7+
* CentOS 7+

### <a name="dependencies"></a>Afhankelijkheden
* systemd

## <a name="installation"></a>Installatie

1. Haal het nieuwste Maverics RPM-pakket op. Kopieer het pakket naar het systeem waarop u de Maverics-software wilt installeren.

2. Installeer het Maverics-pakket, waarbij u `maverics.rpm` vervangt door uw bestandsnaam.

    `sudo rpm -Uvf maverics.rpm`

3. Nadat u Maverics hebt geïnstalleerd, wordt dit als een service uitgevoerd onder `systemd`. Voer de volgende opdracht uit om te controleren of de service wordt uitgevoerd.

    `sudo systemctl status maverics`

Maverics wordt standaard geïnstalleerd in de map `/usr/local/bin`.

Nadat u Maverics hebt geïnstalleerd, wordt het standaardbestand `maverics.yaml` gemaakt in de map `/etc/maverics`. Voordat u uw configuratie bewerkt om `workflows` en `connectors` te bevatten, ziet uw configuratiebestand er als volgt uit:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="config-options"></a>Configuratieopties
### <a name="version"></a>Versie
Het veld `version` geeft aan welke versie van het configuratiebestand wordt gebruikt. Als dit niet is opgegeven, wordt de meest recente versie van het configuratiebestand gebruikt.

```yaml
version: 0.1
```
### <a name="listen-address"></a>Luisteradres
`listenAddress` geeft aan op welk adres Orchestrator luistert. Als het hostgedeelte van het adres leeg is, luistert Orchestrator op alle beschikbare unicast- en anycast-IP-adressen van het lokale systeem. Als het poortgedeelte van het adres leeg is, wordt er automatisch een poortnummer gekozen.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

Het veld `tls` toont een kaart van Transport Layer Security-objecten. De TLS-objecten kunnen worden gebruikt door connectors evenals de Orchestrator-server. Raadpleeg de pakketdocumentatie van `transport` voor alle beschikbare TLS-opties.

Microsoft Azure vereist communicatie via TLS wanneer eenmalige aanmelding met SAML wordt gebruikt. U kunt [hier](https://letsencrypt.org/getting-started/) meer informatie vinden om uw certificaten te genereren.

De sleutel `maverics` is gereserveerd voor de Orchestrator-server. Alle andere sleutels zijn beschikbaar en kunnen worden gebruikt om een TLS-object in te voeren in een opgegeven connector.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Bestanden opnemen

`connectors` en `workflows` kunnen worden gedefinieerd in hun eigen, afzonderlijke configuratiebestanden en er kan naar worden verwezen door `maverics.yaml` met behulp van `includeFiles` zoals in het volgende voorbeeld.
```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

In deze zelfstudie wordt één `maverics.yaml`-configuratiebestand gebruikt.

## <a name="using-azure-key-vault-as-your-secrets-provider"></a>Azure Key Vault gebruiken als uw provider van geheimenbeheer

### <a name="secret-management"></a>Geheimenbeheer

Maverics kan integreren met verschillende geheimenbeheeroplossingen om geheimen te laden. De huidige integraties omvatten een bestand, Hashicorp Vault en Azure Key Vault. Als er geen geheimenbeheeroplossing is opgegeven, laadt Maverics geheimen standaard als tekst zonder opmaak vanuit `maverics.yaml`.
Om een waarde als een geheim te declareren in een `maverics.yaml`-configuratiebestand, zet u haakjes rondom het geheim:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="file"></a>Bestand

Om geheimen te laden vanuit een bestand, voegt u de omgevingsvariabele `MAVERICS_SECRET_PROVIDER` toe in het bestand `/etc/maverics/maverics.env`, met:

`MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

Vervolgens start u de Maverics-service opnieuw op: `sudo systemctl restart maverics`

De inhoud van het `secrets.yaml`-bestand kan worden gevuld met een willekeurig aantal `secrets`.
```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="azure-key-vault"></a>Azure Key Vault

De volgende stappen tonen hoe u een Azure Key Vault instelt via de [Azure-portal](https://portal.azure.com) of [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest):

1. [Meld u aan](https://portal.azure.com) met behulp van de Azure-portal of de CLI-opdracht:
    ```shell
    az login
    ```

2. [Maak een nieuwe Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault), of met behulp van de CLI-opdracht:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

3. [Voeg de geheimen toe aan Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault), of met behulp van een CLI-opdracht:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

4. [Registreer een toepassing met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application), of met behulp van een CLI-opdracht:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

5. [Autoriseer een toepassing om een geheim te gebruiken](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault), of met behulp van een CLI-opdracht:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

Om geheimen te laden vanuit Azure Key Vault, stelt u de omgevingsvariabele `MAVERICS_SECRET_PROVIDER` in het bestand `/etc/maverics/maverics.env` in, met de referenties die u in het bestand azure-credentials.json vindt, met het volgende patroon: `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

Vervolgens start u de Maverics-service opnieuw op: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Uw toepassing configureren in Azure AD voor eenmalige aanmelding met SAML

1. Navigeer in uw Azure Active Directory-tenant naar `Enterprise applications`, zoek naar `Maverics Identity Orchestrator SAML Connector` en selecteer deze.

2. Op de pagina 'Maverics Identity Orchestrator SAML Connector' | Eigenschappen stelt u `User assignment required?` in op Nee om de toepassing te laten werken voor pas gemigreerde gebruikers.

3. Op de pagina 'Maverics Identity Orchestrator SAML Connector' | Overzicht selecteert u `Setup single sign-on` en vervolgens `SAML`.

4. Op de pagina 'Maverics Identity Orchestrator SAML Connector' | Eenmalige aanmelding met SAML bewerkt u de Standaard SAML-configuratie.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

5. Stel de `Entity ID` in door een URL te typen met het volgende patroon: `https://<SUBDOMAIN>.maverics.org`. De `Entity ID` moet uniek zijn voor alle apps in de tenant. Sla de hier ingevoerde waarde op om deze op te nemen in de configuratie van Maverics.

6. Stel de Antwoord-URL in met het volgende patroon: `https://<AZURECOMPANY.COM>/<MY_APP>/`. 

7. Stel de Aanmeldings-URL in met het volgende patroon: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>`. Klik op Opslaan.

8. Ga naar de sectie SAML-handtekeningcertificaat, klik op de kopieerknop om de URL voor federatieve metagegevens van de app te kopiëren, en sla deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

## <a name="maverics-identity-orchestrator-azure-ad-saml-connector-configuration"></a>Configuratie van Maverics Identity Orchestrator Azure AD SAML Connector

De Maverics Identity Orchestrator Azure AD Connector ondersteunt: 
- OpenID Connect
- SAML Connect 

1. Stel `authType: saml` in om eenmalige aanmelding met SAML in te schakelen.

1. Maak de waarde voor `samlMetadataURL`: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`

1. Definieer nu de URL waar Azure gebruikers naar terug zal leiden in uw app nadat ze zich hebben aangemeld met hun Azure-referenties.
`samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`

1. Kopieer de waarde van de Entiteits-id die hierboven is geconfigureerd: `samlEntityID: https://<SUBDOMAIN>.maverics.org`

1. Kopieer de waarde van de Antwoord-URL die Azure AD zal gebruiken om het SAML-antwoord te POSTEN.
`samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. Genereer een JWT-ondertekeningssleutel, die wordt gebruikt om de Maverics Identity Orchestrator&trade;-sessiegegevens te beveiligen, met behulp van het [OpenSSL-hulpprogramma](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Kopieer het antwoord naar de `jwtSigningKey`-configuratie-eigenschap: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`

## <a name="attributes-and-attribute-mapping"></a>Kenmerken en kenmerktoewijzing
Kenmerktoewijzing wordt gebruikt om de toewijzing van gebruikerskenmerken uit een on-premises gebruikerslijst aan Azure AD te definiëren wanneer gebruikers worden ingericht.

Kenmerken bepalen de gebruikersgegevens die kunnen worden geretourneerd aan een toepassing in een claim, doorgegeven in sessiecookies of doorgegeven aan de toepassing in HTTP-headervariabelen.

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector-yaml"></a>Maverics Identity Orchestrator Azure AD SAML Connector-yaml configureren

Uw configuratie van Maverics Identity Orchestrator Azure AD Connector ziet er als volgt uit:
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-azure-ad"></a>Gebruikers migreren naar Azure AD

Volg deze configuratie om gebruikers incrementeel te migreren vanuit een product voor webtoegangsbeheer zoals CA SiteMinder, Oracle Access Manager of IBM Tivoli, een LDAP-adreslijst of een SQL-database.

## <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Uw toepassingsmachtigingen configureren in Azure AD om gebruikers te maken

1. Navigeer in uw Azure Active Directory-tenant naar `App registrations` en selecteer de toepassing 'Maverics Identity Orchestrator SAML Connector'.

2. Op de pagina 'Maverics Identity Orchestrator SAML Connector' | Certificaten en geheimen selecteert u `New client secret` en vervolgens een vervaloptie. Klik op de kopieerknop om het geheim te kopiëren en sla het op uw computer op.

3. Op de pagina 'Maverics Identity Orchestrator SAML Connector' | API-machtigingen selecteert u `Add permission` en bij ‘API-machtigingen aanvragen’ selecteert u `Microsoft Graph` en vervolgens `Application permissions`. Op het volgende scherm selecteert u de `User.ReadWrite.All` en vervolgens `Add permissions`. U wordt dan teruggeleid naar ‘API-machtigingen’. Selecteer daar `Grant admin consent`.


## <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-for-user-migration"></a>De Maverics Identity Orchestrator SAML Connector-yaml configureren voor gebruikersmigratie

Voeg de volgende aanvullende eigenschappen aan het configuratiebestand toe om de werkstroom voor gebruikersmigratie in te schakelen:
1. Stel de URL voor Azure Graph in: `graphURL: https://graph.microsoft.com`
1. Stel de URL voor het OAuth-token in met het volgende patroon: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`
1. Stel het Clientgeheim in dat hierboven is gegenereerd: `oauthClientSecret: <CLIENT SECRET>`


Uw uiteindelijke configuratie van Maverics Identity Orchestrator Azure AD Connector ziet er als volgt uit:
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-siteminder"></a>De Maverics Zero Code Connector&trade; configureren voor SiteMinder

De SiteMinder-connector wordt gebruikt om gebruikers te migreren naar Azure AD en om gebruikers aan te melden bij verouderde on-premises toepassingen die worden beveiligd door SiteMinder, met behulp van de pas gemaakte Azure AD-identiteiten en -referenties.

Voor deze zelfstudie is SiteMinder geconfigureerd om de verouderde toepassing te beveiligen met op formulieren gebaseerde verificatie en met behulp van de cookie `SMSESSION`. Om te integreren met een app die verificatie en sessies via HTTP-headers gebruikt, moet u de header-emulatieconfiguratie aan de connector toevoegen.

In dit voorbeeld wordt het kenmerk `username` toegewezen aan de HTTP-header `SM_USER`:
```yaml
  headers:
    SM_USER: username
```

Stel de `proxyPass` in op de locatie waarnaar aanvragen worden geproxied. Dit is meestal de host van de beveiligde toepassing.

`loginPage` moet overeenkomen met de URL van het aanmeldingsformulier dat momenteel door SiteMinder wordt gebruikt wanneer gebruikers worden omgeleid voor verificatie.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-ldap"></a>De Maverics Zero Code Connector&trade; configureren voor LDAP

Wanneer toepassingen worden beveiligd door een WAM-product zoals SiteMinder, worden gebruikersidentiteiten en kenmerken meestal opgeslagen in een LDAP-adreslijst.

Deze connectorconfiguratie demonstreert hoe u verbinding maakt met de LDAP-adreslijst die is geconfigureerd als het gebruikersarchief voor SiteMinder, zodat de juiste gebruikersprofielgegevens kunnen worden verzameld tijdens de migratiewerkstroom en een bijbehorende gebruiker kan worden gemaakt in Azure AD.

* `baseDN` specificeert de locatie in de adreslijst waarop de LDAP-zoekopdracht moet worden uitgevoerd.

* `url` is het adres en de poort van de LDAP-server waarmee verbinding moet worden gemaakt.

* `serviceAccountUsername` is de gebruikersnaam die wordt gebruikt om verbinding te maken met de LDAP-server, meestal uitgedrukt als een bindings-DN, bijvoorbeeld `CN=Directory Manager`.

* `serviceAccountPassword` is het wachtwoord dat wordt gebruikt om verbinding te maken met de LDAP-server. Deze waarde wordt opgeslagen in het Azure Key Vault-exemplaar dat eerder is geconfigureerd.  

* `userAttributes` definieert de lijst met gebruikergerelateerde kenmerken waarnaar moet worden gezocht. Deze kenmerken worden later toegewezen aan bijbehorende Azure AD-kenmerken.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

## <a name="configure-the-migration-workflow"></a>De migratiewerkstroom configureren

De configuratie van de migratiewerkstroom bepaalt hoe Maverics gebruikers zal migreren van SiteMinder/LDAP naar Azure AD.

Deze werkstroom:
- Maakt gebruik van de SiteMinder-connector om de SiteMinder-aanmelding via een proxy uit te voeren. Gebruikersreferenties worden gevalideerd via SiteMinder-verificatie en vervolgens doorgegeven aan volgende stappen van de werkstroom.
- Haalt gebruikersprofielkenmerken op uit het SiteMinder-gebruikersarchief.
- Dient een aanvraag in bij de Microsoft Graph-API om de gebruiker te maken in uw Azure AD-tenant.

Stappen:
1. Geef de werkstroom een naam, bijvoorbeeld Migratie van SiteMinder naar Azure AD.
2. Geef het `endpoint` op. Dit is een HTTP-pad waarop de werkstroom beschikbaar wordt gemaakt, waardoor de `actions` van die werkstroom worden geactiveerd in reactie op aanvragen. Het `endpoint` komt meestal overeen met de app die wordt geproxied, bijvoorbeeld `/my_app`. De waarde moet zowel de voorloop- als volgslash bevatten.
3. Voeg de toepasselijke `actions` aan de werkstroom toe.
    - Definieer de `login`-methode voor de SiteMinder-connector. De connectorwaarde moet overeenkomen met de naamwaarde in de connectorconfiguratie.
     - Definieer de `getprofile`-methode voor de LDAP-connector.
     - Definieer de `createuser`-methode voor de Azure AD-connector.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>De migratiewerkstroom verifiëren

1. Als de Maverics-service nog niet wordt uitgevoerd, start u deze door de volgende opdracht uit te voeren: `sudo systemctl start maverics`

2. Navigeer naar de geproxiede aanmeldings-URL: `http://host.company.com/my_app`.
3. Geef gebruikersreferenties op die worden gebruikt voor aanmelding bij de toepassing terwijl deze wordt beveiligd door SiteMinder.
4. Navigeer naar Start > Gebruikers | Alle gebruikers om te verifiëren dat uw gebruiker is gemaakt in de Azure AD-tenant.  

## <a name="configure-the-session-abstraction-workflow"></a>De werkstroom voor sessieabstractie configureren

Met de werkstroom voor sessieabstractie worden verificatie en toegangsbeheer voor de verouderde on-premises webtoepassing verplaatst naar Azure AD.

De Azure-connector maakt gebruik van de `login`-methode om de gebruiker naar de aanmeldings-URL om te leiden, ervan uitgaande dat er geen sessie bestaat.

Na verificatie wordt het sessietoken dat als gevolg is gemaakt, doorgegeven aan Maverics en wordt de `emulate`-methode van de SiteMinder-connector gebruikt om de cookiegebaseerde sessie en/of headergebaseerde sessie te emuleren en vervolgens de aanvraag te voorzien van aanvullende kenmerken die de toepassing vereist.

1. Geef de werkstroom een naam, bijvoorbeeld Sessieabstractie SiteMinder.
2. Geeft het `endpoint` op, dat overeenkomt met de app die wordt geproxied. De waarde moet zowel de voorloop- als volgslash bevatten, bijvoorbeeld `/my_app/`.
3. Voeg de toepasselijke `actions` aan de werkstroom toe.
    - Definieer de `login`-methode voor de Azure-connector. De `connector`-waarde moet overeenkomen met de `name`-waarde in de connectorconfiguratie.
    - Definieer de `emulate`-methode voor de SiteMinder-connector.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>De werkstroom voor sessieabstractie verifiëren

1. Navigeer naar de geproxiede toepassings-URL: `https://<AZURECOMPANY.COM>/<MY_APP>`. De gebruiker wordt omgeleid naar de geproxiede aanmeldingspagina.
2. Voer de Azure AD-gebruikersreferenties in.
3. De gebruiker moet naar de toepassing worden omgeleid alsof hij direct door SiteMinder is geverifieerd.
