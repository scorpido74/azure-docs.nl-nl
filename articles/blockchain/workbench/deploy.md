---
title: Azure Blockchain Workbench Preview implementeren
description: Azure Blockchain Workbench Preview implementeren
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 141bb8825e47eb2309f9f551990a2976e8f4e209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943208"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Azure Blockchain Workbench Preview implementeren

Azure Blockchain Workbench Preview wordt geïmplementeerd met behulp van een oplossingssjabloon in de Azure Marketplace. De sjabloon vereenvoudigt de implementatie van componenten die nodig zijn om blockchain-toepassingen te maken. Eenmaal geïmplementeerd biedt Blockchain Workbench toegang tot client-apps om gebruikers en blockchain-toepassingen te maken en te beheren.

Zie [Azure Blockchain Workbench-architectuur](architecture.md)voor meer informatie over de componenten van Blockchain Workbench.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Implementatie voorbereiden

Blockchain Workbench stelt u in staat om een blockchain-grootboek te implementeren, samen met een reeks relevante Azure-services die het vaakst worden gebruikt om een op blockchain gebaseerde toepassing te bouwen. Het implementeren van Blockchain Workbench resulteert in de volgende Azure-services die worden ingericht binnen een brongroep in uw Azure-abonnement.

* App-serviceabonnement (standaard)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* SQL-database (Standaard S0) + SQL Logische Server
* Azure Storage-account (Standaard LRS)
* Virtuele machineschaalset met een capaciteit van 1
* Brongroep voor virtuele netwerken (met Load Balancer, Network Security Group, Openbaar IP-adres, virtueel netwerk)
* Azure Blockchain-service. Als u een eerdere Blockchain Workbench-implementatie gebruikt, u Azure Blockchain Workbench opnieuw implementeren om Azure Blockchain-service te gebruiken.

Het volgende is een voorbeeldimplementatie die is gemaakt in **de myblockchain-resourcegroep.**

![Voorbeeldimplementatie](media/deploy/example-deployment.png)

De kosten van Blockchain Workbench zijn een totaal van de kosten van de onderliggende Azure-services. Prijsinformatie voor Azure-services kan worden berekend met behulp van de [prijscalculator](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Vereisten

Azure Blockchain Workbench vereist Azure AD-configuratie- en toepassingsregistraties. U ervoor kiezen om de Azure [AD-configuraties handmatig](#azure-ad-configuration) uit te voeren voordat u de implementatie uitvoert of een scriptpost-implementatie uitvoert. Als u Blockchain Workbench opnieuw implementeert, raadpleegt u [de Azure AD-configuratie](#azure-ad-configuration) om uw Azure AD-configuratie te verifiëren.

> [!IMPORTANT]
> Workbench hoeft niet te worden geïmplementeerd in dezelfde tenant als degene die u gebruikt om een Azure AD-toepassing te registreren. Workbench moet worden geïmplementeerd in een tenant waar u voldoende machtigingen hebt om resources te implementeren. Zie [Een Active Directory-tenant](../../active-directory/develop/quickstart-create-new-tenant.md) en [Toepassingen voor het integreren van toepassingen met Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie over Azure AD-tenants.

## <a name="deploy-blockchain-workbench"></a>Blockchain Workbench implementeren

Zodra de vereiste stappen zijn voltooid, bent u klaar om de Blockchain Workbench te implementeren. In de volgende secties wordt beschreven hoe u het framework implementeert.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer uw account in de rechterbovenhoek en schakel over naar de gewenste Azure AD-tenant waar u Azure Blockchain Workbench wilt implementeren.
1. Selecteer in de linkerbovenhoek van Azure Portal **Een resource maken**.
1. Selecteer **Blockchain** > **Azure Blockchain Workbench (voorbeeld)**.

    ![Azure Blockchain-werkbank maken](media/deploy/blockchain-workbench-settings-basic.png)

    | Instelling | Beschrijving  |
    |---------|--------------|
    | Resourcevoorvoegsel | Korte unieke id voor uw implementatie. Deze waarde wordt gebruikt als basis voor het benoemen van resources. |
    | VM-gebruikersnaam | De gebruikersnaam wordt gebruikt als beheerder voor alle virtuele machines (VM). |
    | Verificatietype | Selecteer of u een wachtwoord of sleutel wilt gebruiken voor verbinding maken met VM's. |
    | Wachtwoord | Het wachtwoord wordt gebruikt voor het maken van verbinding met VM's. |
    | SSH | Gebruik een RSA-public key in de single-line-indeling vanaf **ssh-rsa** of gebruik het multi-line PEM-formaat. U SSH-sleutels genereren met Behulp van `ssh-keygen` Linux en OS X, of met Behulp van PuTTYGen op Windows. Meer informatie over SSH-sleutels, zie [SSH-sleutels gebruiken met Windows op Azure.](../../virtual-machines/linux/ssh-from-windows.md) |
    | Database- en Blockchain-wachtwoord | Geef het wachtwoord op dat moet worden gebruikt voor toegang tot de database die is gemaakt als onderdeel van de implementatie. Het wachtwoord moet voldoen aan drie van de volgende vier vereisten: de lengte moet tussen 12 & 72 tekens, 1 kleine lettersteken, 1 hoofdletterteken, 1 getal en 1\`speciaal teken dat geen getalteken is(#), procent(), komma(,), ster(*), terugquote( ), dubbele aanhaling("), enkele aanhaling('), dash(-) en semicolumn(;) |
    | Implementatieregio | Geef op waar u Blockchain Workbench-resources implementeren. Voor de beste beschikbaarheid moet dit overeenkomen met de **instelling Locatie.** |
    | Abonnement | Geef het Azure-abonnement op dat u wilt gebruiken voor uw implementatie. |
    | Resourcegroepen | Maak een nieuwe resourcegroep door **Nieuw maken te** selecteren en een unieke naam van de resourcegroep op te geven. |
    | Locatie | Geef aan welk gebied u het framework wilt implementeren. |

1. Selecteer **OK** om de configuratiesectie voor basisinstellingen te voltooien.

1. Kies in **Geavanceerde instellingen**of u een nieuw blockchain-netwerk wilt maken of een bestaand proof-of-authority blockchain-netwerk wilt gebruiken.

    Voor **Nieuw maken:**

    Met *de nieuwe* optie maken wordt een Azure Blockchain Service Quorum-grootboek met de standaardbasissku geïmplementeerd.

    ![Geavanceerde instellingen voor nieuw blockchain-netwerk](media/deploy/advanced-blockchain-settings-new.png)

    | Instelling | Beschrijving  |
    |---------|--------------|
    | Prijscategorie Azure Blockchain Service | Kies **Basic** of **Standard** Azure Blockchain Service-laag die wordt gebruikt voor Blockchain Workbench |
    | Azure Active Directory-instellingen | Kies **Later toevoegen**.</br>Opmerking: Als u ervoor hebt gekozen azure [AD vooraf](#azure-ad-configuration) te configureren of opnieuw te implementeren, kiest u *Nu toevoegen*. |
    | VM-selectie | Selecteer voorkeursopslagprestaties en VM-grootte voor uw blockchain-netwerk. Kies een kleiner VM-formaat, zoals *Standaard DS1 v2* als u een abonnement hebt met lage servicelimieten zoals de gratis laag azure. |

    Voor **gebruik bestaande**:

    Met *de bestaande* optie voor gebruik u een Ethereum Proof-of-Authority (PoA) blockchain-netwerk opgeven. Eindpunten hebben de volgende vereisten.

   * Het eindpunt moet een Ethereum Proof-of-Authority (PoA) blockchain-netwerk zijn.
   * Het eindpunt moet openbaar toegankelijk zijn via het netwerk.
   * Het PoA blockchain-netwerk moet worden geconfigureerd om de gasprijs op nul te zetten.

     > [!NOTE]
     > Blockchain Workbench-accounts worden niet gefinancierd. Als er geld nodig is, mislukken de transacties.

     ![Geavanceerde instellingen voor bestaand blockchain-netwerk](media/deploy/advanced-blockchain-settings-existing.png)

     | Instelling | Beschrijving  |
     |---------|--------------|
     | Ethereum RPC-eindpunt | Geef het RPC-eindpunt van een bestaand PoA blockchain-netwerk. Het eindpunt begint met https:// of http:// en eindigt met een poortnummer. Bijvoorbeeld: `http<s>://<network-url>:<port>` |
     | Azure Active Directory-instellingen | Kies **Later toevoegen**.</br>Opmerking: Als u ervoor hebt gekozen azure [AD vooraf](#azure-ad-configuration) te configureren of opnieuw te implementeren, kiest u *Nu toevoegen*. |
     | VM-selectie | Selecteer voorkeursopslagprestaties en VM-grootte voor uw blockchain-netwerk. Kies een kleiner VM-formaat, zoals *Standaard DS1 v2* als u een abonnement hebt met lage servicelimieten zoals de gratis laag azure. |

1. Selecteer **OK** om geavanceerde instellingen te voltooien.

1. Bekijk het overzicht om te controleren of uw parameters juist zijn.

    ![Samenvatting](media/deploy/blockchain-workbench-summary.png)

1. Selecteer **Maken** om akkoord te gaan met de voorwaarden en implementeer uw Azure Blockchain Workbench.

De implementatie kan tot 90 minuten duren. U de Azure-portal gebruiken om de voortgang te controleren. Selecteer in de nieuw gemaakte resourcegroep **Implementaties > overzicht** om de status van de geïmplementeerde artefacten weer te geven.

> [!IMPORTANT]
> Na implementatie moet u Active Directory-instellingen voltooien. Als u **Later toevoegen**hebt gekozen, moet u het Azure [AD-configuratiescript](#azure-ad-configuration-script)uitvoeren.  Als u **Nu toevoegen**hebt gekozen, moet u de URL van het antwoord [configureren](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench-web-URL

Zodra de implementatie van de Blockchain Workbench is voltooid, bevat een nieuwe resourcegroep uw Blockchain Workbench-bronnen. Blockchain Workbench-services zijn toegankelijk via een web-URL. In de volgende stappen ziet u hoe u de web-URL van het geïmplementeerde framework ophalen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen**in het navigatiedeelvenster aan de linkerkant .
1. Kies de naam van de resourcegroep die u hebt opgegeven bij het implementeren van Blockchain Workbench.
1. Selecteer de kolomkop **TYPE** om de lijst alfabetisch te sorteren op type.
1. Er zijn twee bronnen met type **App Service**. Selecteer de bron van het type **App-service** *zonder* het achtervoegsel 'api'.

    ![Lijst met app-service](media/deploy/resource-group-list.png)

1. Kopieer in het **overzicht**van de appservice de **URL-waarde,** die de web-URL vertegenwoordigt naar uw geïmplementeerde Blockchain Workbench.

    ![App-serviceessentials](media/deploy/app-service.png)

Als u een aangepaste domeinnaam wilt koppelen aan Blockchain Workbench, raadpleegt u [het configureren van een aangepaste domeinnaam voor een web-app in Azure App Service met Traffic Manager](../../app-service/configure-domain-traffic-manager.md).

## <a name="azure-ad-configuration-script"></a>Azure AD-configuratiescript

Azure AD moet zijn geconfigureerd om uw Blockchain Workbench-implementatie te voltooien. U gebruikt een PowerShell-script om de configuratie uit te stellen.

1. Navigeer in een browser naar de URL van het [Blockchain Workbench-web.](#blockchain-workbench-web-url)
1. U ziet instructies voor het instellen van Azure AD met Cloud Shell. Kopieer de opdracht en start Cloud Shell.

    ![Aad-script starten](media/deploy/launch-aad-script.png)

1. Kies de Azure AD-tenant waar u Blockchain Workbench hebt geïmplementeerd.
1. Plak en voer de opdracht uit in de Cloud Shell PowerShell-omgeving.
1. Wanneer u daarom wordt gevraagd, voert u de Azure AD-tenant in die u wilt gebruiken voor Blockchain Workbench. Dit zal de huurder zijn met de gebruikers voor Blockchain Workbench.

    > [!IMPORTANT]
    > De geverifieerde gebruiker heeft machtigingen nodig om Azure AD-toepassingsregistraties te maken en gedelegeerde toepassingsmachtigingen in de tenant toe te kennen. Mogelijk moet u een beheerder van de tenant vragen om het Azure AD-configuratiescript uit te voeren of een nieuwe tenant te maken.

    ![Azure AD-tenant invoeren](media/deploy/choose-tenant.png)

1. U wordt gevraagd om met een browser te verifiëren aan de Azure AD-tenant. Open de web-URL in een browser, voer de code in en verifieer.

    ![Verifiëren met code](media/deploy/authenticate.png)

1. Het script levert verschillende statusberichten op. U krijgt een bericht over de **status SUCCES** als de tenant is ingericht.
1. Navigeer naar de URL van Blockchain Workbench. U wordt gevraagd toestemming te geven om leesmachtigingen te verlenen aan de directory. Hierdoor heeft de Blockchain Workbench web-app toegang tot de gebruikers in de tenant. Als u de tenantbeheerder bent, u ervoor kiezen om toestemming te geven voor de hele organisatie. Deze optie accepteert toestemming voor alle gebruikers in de tenant. Anders wordt elke gebruiker om toestemming gevraagd bij het eerste gebruik van de Blockchain Workbench-webtoepassing.
1. Selecteer **Accepteren** om toestemming te geven.

     ![Toestemming om gebruikersprofielen te lezen](media/deploy/graph-permission-consent.png)

1. Na toestemming kan de Blockchain Workbench webapp worden gebruikt.

U hebt uw Azure Blockchain Workbench-implementatie voltooid. Zie [Volgende stappen](#next-steps) voor suggesties om aan de slag te gaan met uw implementatie.

## <a name="azure-ad-configuration"></a>Azure AD-configuratie

Als u ervoor kiest om de A2-instellingen voor azure ad handmatig te configureren of te verifiëren voordat u deze geïmplementeerd, voert u alle stappen in deze sectie uit. Als u de voorkeur geeft aan het automatisch configureren van Azure AD-instellingen, gebruikt u [Azure AD-configuratiescript](#azure-ad-configuration-script) nadat u Blockchain Workbench hebt geïmplementeerd.

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API-app-registratie

Blockchain Workbench-implementatie vereist registratie van een Azure AD-toepassing. U hebt een Azure Active Directory-tenant (Azure AD) nodig om de app te registreren. U een bestaande tenant gebruiken of een nieuwe tenant maken. Als u een bestaande Azure AD-tenant gebruikt, hebt u voldoende machtigingen nodig om toepassingen te registreren, API-machtigingen voor grafieken toe te kennen en gasttoegang toe te staan binnen een Azure AD-tenant. Als u niet over voldoende machtigingen beschikt in een bestaande Azure AD-tenant, maakt u een nieuwe tenant.


1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer uw account in de rechterbovenhoek en schakel over naar de gewenste Azure AD-tenant. De tenant moet de huurder van de abonnementsbeheerder zijn van het abonnement waarbij Azure Blockchain Workbench wordt geïmplementeerd en u over voldoende machtigingen beschikt om toepassingen te registreren.
1. Selecteer in het navigatiedeelvenster aan de linkerkant de **Azure Active Directory**-service. Selecteer **App-registraties** > **Nieuwe registratie**.

    ![App-registratie](media/deploy/app-registration.png)

1. Geef een **weergavenaam** op en kies **alleen Accounts in deze organisatiemap**.

    ![Een app-registratie maken](media/deploy/app-registration-create.png)

1. Selecteer **Registreren** om de Azure AD-toepassing te registreren.

### <a name="modify-manifest"></a>Manifest wijzigen

Vervolgens moet u het manifest wijzigen om toepassingsrollen binnen Azure AD te gebruiken om Blockchain Workbench-beheerders op te geven.  Zie [Azure Active Directory-toepassingsmanifest](../../active-directory/develop/reference-app-manifest.md)voor meer informatie over toepassingsmanifesten.


1. Een GUID is vereist voor het manifest. U een GUID genereren `[guid]::NewGuid()` met `New-GUID` de opdracht PowerShell of de cmdlet. Een andere optie is het gebruik van een GUID generator website.
1. Selecteer **Manifest** voor de aanmeldingsaanvraag in de sectie **Beheren.**
1. Werk vervolgens het gedeelte **appRollen** van het manifest bij. Vervang `"appRoles": []` door de meegeleverde JSON. Zorg ervoor dat u de waarde voor het **id-veld** vervangt door de GUID die u hebt gegenereerd. 

    ![Manifest bewerken](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > De **waardebeheerder** is nodig om Blockchain Workbench-beheerders te identificeren.

1. In het manifest wijzigt u ook de **Waarde Oauth2AllowImplicitFlow** in **true**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. Selecteer **Opslaan** om de manifestwijzigingen op te slaan.

### <a name="add-graph-api-required-permissions"></a>Vereiste machtigingen voor Grafiek-API toevoegen

De API-toepassing moet toestemming vragen aan de gebruiker om toegang te krijgen tot de map. Stel de volgende vereiste machtiging in voor de API-toepassing:

1. Selecteer **API-machtigingen**in de registratie van de *Blockchain API-app.* Standaard wordt de Graph **API-gebruiker.Lees-toestemming** toegevoegd.
1. De Workbench-toepassing vereist leestoegang tot de basisprofielinformatie van gebruikers. Selecteer In *Geconfigureerde machtigingen*de optie **Een machtiging toevoegen**. Selecteer **Microsoft Graph**in **Microsoft API's**.
1. Aangezien de Workbench-toepassing de geverifieerde gebruikersreferenties gebruikt, selecteert **u Gedelegeerde machtigingen**.
1. Kies in de categorie *Gebruiker* de optie **Gebruiker.ReadBasic.All-machtigingen.**

    ![Configuratie van azure AD-app-app met de toevoeging van de Microsoft Graph-gebruiker.ReadBasic.Alle gedelegeerde machtiging](media/deploy/add-graph-user-permission.png)

    Selecteer **Machtigingen toevoegen**.

1. Selecteer *In Geconfigureerde machtigingen*de optie Toestemming voor beheerders **verlenen** voor het domein en selecteert u **Ja** voor de verificatieprompt.

   ![Machtigingen verlenen](media/deploy/client-app-grant-permissions.png)

   Door toestemming te verlenen, heeft Blockchain Workbench toegang tot gebruikers in de directory. De leestoestemming is vereist om leden te zoeken en toe te voegen aan Blockchain Workbench.

### <a name="get-application-id"></a>Toepassings-id ophalen

De toepassings-ID en tenantgegevens zijn vereist voor implementatie. Verzamel en bewaar de informatie voor gebruik tijdens de implementatie.

1. Selecteer **Overzicht**voor de aanmelding die u hebt geregistreerd.
1. Kopieer en bewaar de waarde **van de toepassings-id** voor later gebruik tijdens de implementatie.

    ![Eigenschappen van API-apps](media/deploy/app-properties.png)

    | Instellen om op te slaan  | Gebruiken in implementatie |
    |------------------|-------------------|
    | (Client-)id van de app | Azure Active Directory-instelling > toepassings-id |

### <a name="get-tenant-domain-name"></a>Tenant-domeinnaam aanschaffen

Verzamel en bewaar de Active Directory-tenantdomeinnaam waar de toepassingen zijn geregistreerd. 

Selecteer in het navigatiedeelvenster aan de linkerkant de **Azure Active Directory**-service. Selecteer **Namen van aangepaste domeinen**. Kopieer en bewaar de domeinnaam.

![Domeinnaam](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Instellingen voor gastgebruikers

Als u gastgebruikers in uw Azure AD-tenant hebt, volgt u de aanvullende stappen om ervoor te zorgen dat de toewijzing en beheer van de Blockchain Workbench-gebruiker naar behoren werkt.

1. Schakel uw Azure AD-tenant over en selecteer **Azure Active Directory > Gebruikersinstellingen > Instellingen voor externe samenwerking beheren**.
1. Machtigingen **voor gastgebruikers** instellen zijn beperkt tot **Nee**.
    ![Instellingen voor externe samenwerking](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>De antwoord-URL configureren

Zodra de Azure Blockchain Workbench is geïmplementeerd, moet u de Azure Active Directory -clientclient-client-url **van de** geïmplementeerde Web-URL van Blockchain Workbench configureren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Controleer of u zich in de tenant bevindt waar u de Azure AD-clienttoepassing hebt geregistreerd.
1. Selecteer in het navigatiedeelvenster aan de linkerkant de **Azure Active Directory**-service. Selecteer **App-registraties**.
1. Selecteer de Azure AD-clienttoepassing die u hebt geregistreerd in de sectie vereiste.
1. Selecteer **Verificatie**.
1. Geef de belangrijkste web-URL op van de Azure Blockchain Workbench-implementatie die u hebt opgehaald in de sectie [Blockchain Workbench-web-URL.](#blockchain-workbench-web-url) De URL van het `https://`antwoord is vooraf gekoppeld aan . Bijvoorbeeld: `https://myblockchain2-7v75.azurewebsites.net`

    ![URL's voor verificatieantwoord](media/deploy/configure-reply-url.png)

1. Controleer in de sectie **Geavanceerde instelling** **Access-tokens** en **ID-tokens**.

    ![Geavanceerde verificatieinstellingen](media/deploy/authentication-advanced-settings.png)

1. Selecteer **Opslaan** om de clientregistratie bij te werken.

## <a name="remove-a-deployment"></a>Een implementatie verwijderen

Wanneer een implementatie niet langer nodig is, u een implementatie verwijderen door de Blockchain Workbench-brongroep te verwijderen.

1. Navigeer in de Azure-portal naar **de groep Resource** in het linkernavigatiedeelvenster en selecteer de resourcegroep die u wilt verwijderen. 
1. Selecteer **Resourcegroep verwijderen**. Verwijder het verwijderen door de naam van de brongroep in te voeren en selecteer **Verwijderen**.

    ![Resourcegroep verwijderen](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel met how-to hebt u Azure Blockchain Workbench geïmplementeerd. Ga door naar het volgende how-to-artikel om te leren hoe je een blockchain-toepassing maakt.

> [!div class="nextstepaction"]
> [Een blockchain-toepassing maken in Azure Blockchain Workbench](create-app.md)
