---
title: Azure Block Chain Workbench preview implementeren
description: Azure Block Chain Workbench preview implementeren
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 2ea18c784c6b5cf61013c131360d20349e67b1e5
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845279"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Azure Block Chain Workbench preview implementeren

De preview-versie van Azure Block Chain Workbench wordt geïmplementeerd met behulp van een oplossings sjabloon in azure Marketplace. De sjabloon vereenvoudigt de implementatie van onderdelen die nodig zijn voor het maken van blockchain-toepassingen. Eenmaal geïmplementeerd, biedt Blockchain Workbench toegang tot de client-apps om gebruikers en blockchain-toepassingen te maken en te beheren.

Zie [Azure Block Chain Workbench-architectuur](architecture.md)voor meer informatie over de onderdelen van Block Chain Workbench.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Voorbereiden voor implementatie

Met Blockchain Workbench kunt u een blockchain grootboek, samen met een set van relevante Azure-services voor het bouwen van een blockchain-gebaseerde toepassing, implementeren. De implementatie van Blockchain Workbench resulteert in de volgende Azure-services die binnen een resourcegroep in uw Azure-abonnement worden ingericht.

* App Service plan (Standard)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* SQL Database (Standard S0) + logische SQL-Server
* Azure Storage-account (standaard LRS)
* Schaalset voor virtuele machines met een capaciteit van 1
* Virtual Network resource groep (met Load Balancer, netwerk beveiligings groep, openbaar IP-adres, Virtual Network)
* Azure Block Chain-service. Als u een eerdere implementatie van Block Chain Workbench gebruikt, overweeg dan om Azure Block Chain Workbench te implementeren voor het gebruik van de Azure Block Chain-service.

Hier volgt een voor beeld van een implementatie die is gemaakt in de resource groep **myblockchain** .

![Voorbeeld implementatie](media/deploy/example-deployment.png)

De kosten van Blockchain Workbench is een optelling van de kosten van de onderliggende Azure-services. Prijs informatie voor Azure-Services kan worden berekend met behulp van de [prijs calculator](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Vereisten

Azure Block Chain Workbench vereist Azure AD-configuratie-en-toepassings registraties. U kunt ervoor kiezen om de Azure AD- [configuraties hand matig](#azure-ad-configuration) te doen voordat u de implementatie uitvoert of u voert een script na de implementatie uit. Als u Block Chain Workbench opnieuw wilt implementeren, raadpleegt u [Azure AD-configuratie](#azure-ad-configuration) om uw Azure AD-configuratie te controleren.

> [!IMPORTANT]
> Workbench hoeft niet te worden geïmplementeerd in dezelfde Tenant als het account dat u gebruikt om een Azure AD-toepassing te registreren. Workbench moet worden geïmplementeerd in een Tenant waarvoor u voldoende machtigingen hebt voor het implementeren van resources. Zie [een Active Directory Tenant verkrijgen](../../active-directory/develop/quickstart-create-new-tenant.md) en [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie over Azure AD-tenants.

## <a name="deploy-blockchain-workbench"></a>Block Chain Workbench implementeren

Als de vereiste stappen zijn voltooid, bent u klaar om de Blockchain Workbench te implementeren. De volgende secties beschrijven hoe het framework moet worden geïmplementeerd.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer uw account in de rechter bovenhoek en schakel over naar de gewenste Azure AD-Tenant waar u Azure Block Chain Workbench wilt implementeren.
3. Selecteer **Een resource maken** in het linkerdeelvenster. Zoek naar `Azure Blockchain Workbench` in de **Marketplace doorzoeken** zoekbalk. 

    ![Zoek balk voor Marketplace](media/deploy/marketplace-search-bar.png)

4. Selecteer **Azure Block Chain Workbench**.

    ![Zoek resultaten voor Marketplace](media/deploy/marketplace-search-results.png)

5. Selecteer **Maken**.
6. Vul de algemene instellingen in.

    ![Azure Block Chain Workbench maken](media/deploy/blockchain-workbench-settings-basic.png)

    | Instelling | Description  |
    |---------|--------------|
    | Resource voorvoegsel | Korte unieke id voor uw implementatie. Deze waarde wordt gebruikt als basis voor het benoemen van resources. |
    | VM-gebruikers naam | De gebruikers naam wordt gebruikt als beheerder voor alle virtuele machines (VM). |
    | Verificatietype | Selecteer of u een wachtwoord wilt gebruiken of een sleutel voor de verbinding met virtuele machines. |
    | Wachtwoord | Het wacht woord wordt gebruikt om verbinding te maken met Vm's. |
    | SSH | Gebruik een openbare RSA-sleutel in de indeling met één regel die begint met **ssh-rsa** of gebruik de PEM-indeling van meerdere regels. U kunt SSH-sleutels genereren met behulp van `ssh-keygen` op Linux en OS X of met PuTTYGen op Windows. Zie [SSH-sleutels gebruiken met Windows op Azure voor](../../virtual-machines/linux/ssh-from-windows.md)meer informatie over SSH-sleutels. |
    | Data Base-en block Chain-wacht woord | Geef het wachtwoord voor toegang tot de database die wordt gemaakt als onderdeel van de implementatie. Het wacht woord moet voldoen aan drie van de volgende vier vereisten: lengte moet tussen 12 & 72 tekens, 1 kleine letter, 1 hoofd letter, 1 cijfer en 1 speciaal teken zijn dat geen hekje (#), procent (%), komma (,), ster (*), terug-offerte (\`), dubbele aanhalings tekens ("), enkel aanhalings teken ('), streepje (-) en semicolumn (;) |
    | Implementatie regio | Geef op waar de Blockchain Workbench-resources worden geïmplementeerd. Voor beschikbaarheid wordt  aanbevolen om dit overeen te laten komen met de **locatie** instelling. |
    | Subscription | Geef het Azure-abonnement op dat u wilt gebruiken voor uw implementatie. |
    | Resourcegroepen | Maak een nieuwe resourcegroep door **nieuw** te selecteren en geef de naam op van een unieke resource-groep. |
    | Location | Geef de regio op waaar u het framework wilt implementeren. |

7. Selecteer **OK** om de configuratiesectie met basisinstellingen te voltooien.

8. Kies in **Geavanceerde instellingen**of u een nieuw Block chain-netwerk wilt maken of een bestaand Block chain-netwerk wilt gebruiken.

    Voor **nieuwe maken**:

    Met de optie *nieuwe maken* implementeert u een Azure Block Chain Service-quorum grootboek met de standaard-basis-SKU.

    ![Geavanceerde instellingen voor het nieuwe Block chain-netwerk](media/deploy/advanced-blockchain-settings-new.png)

    | Instelling | Description  |
    |---------|--------------|
    | Prijs categorie voor Azure Block Chain-Service | Kies **Basic** of **Standard** Azure Block Chain service tier die wordt gebruikt voor Block Chain workbench |
    | Azure Active Directory instellingen | Kies **later toevoegen**.</br>Opmerking: Als u ervoor hebt gekozen om [Azure AD vooraf te configureren of opnieuw](#azure-ad-configuration) te implementeren, kiest u *nu toevoegen*. |
    | VM selecteren | Selecteer voorkeurs opslag prestaties en VM-grootte voor uw Block chain-netwerk. Kies een kleinere VM-grootte, zoals *Standard ds1 v2* als u een abonnement hebt met lage service limieten, zoals de gratis laag van Azure. |

    Voor het **gebruik van bestaande**:

    Met de *gebruik bestaande* optie kunt u een netwerk van de blockchain Ethereum Proof-of-Authority (PoA) opgeven. Eind punten hebben de volgende vereisten.

   * Het eindpunt moet een Ethereum Proof-of-Authority (PoA) blockchain-netwerk zijn.
   * Het eindpunt moet openbaar toegankelijk zijn via het netwerk.
   * Het PoA Block chain-netwerk moet zo zijn geconfigureerd dat de prijs van een gas is ingesteld op nul.

     > [!NOTE]
     > Block Chain Workbench-accounts zijn niet gefinancierd. Als er fondsen zijn vereist, mislukken de trans acties.

     ![Geavanceerde instellingen voor bestaand Block chain-netwerk](media/deploy/advanced-blockchain-settings-existing.png)

     | Instelling | Description  |
     |---------|--------------|
     | RPC-eind punt Ethereum | Geef het RPC-eind punt op van een bestaand PoA Block chain-netwerk. Het eind punt begint met https://of http://en eindigt met een poort nummer. Bijvoorbeeld: `http<s>://<network-url>:<port>` |
     | Azure Active Directory instellingen | Kies **later toevoegen**.</br>Opmerking: Als u ervoor hebt gekozen om [Azure AD vooraf te configureren of opnieuw](#azure-ad-configuration) te implementeren, kiest u *nu toevoegen*. |
     | VM selecteren | Selecteer voorkeurs opslag prestaties en VM-grootte voor uw Block chain-netwerk. Kies een kleinere VM-grootte, zoals *Standard ds1 v2* als u een abonnement hebt met lage service limieten, zoals de gratis laag van Azure. |

9. Selecteer **OK** om de geavanceerde instellingen te volt ooien.

10. Bekijk de samenvatting om te controleren of uw parameters correct zijn.

    ![Samenvatting](media/deploy/blockchain-workbench-summary.png)

11. Selecteer **maken** om akkoord te gaan met de voorwaarden en uw Azure Blockchain Workbench te implementeren.

De implementatie kan Maxi maal 90 minuten duren. U kunt de Azure-portal gebruiken om de voortgang te bekijken. Selecteer in de zojuist gemaakte resource groep **implementaties > overzicht** om de status van de geïmplementeerde artefacten te bekijken.

> [!IMPORTANT]
> Na de implementatie moet u Active Directory instellingen volt ooien. Als u **later toevoegen**hebt gekozen, moet u het [Azure AD-configuratie script](#azure-ad-configuration-script)uitvoeren.  Als u **nu toevoegen**hebt gekozen, moet u [de antwoord-URL configureren](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Web-URL voor Block Chain workbench

Als de implementatie van de Blockchain Workbench is voltooid, bevat een nieuwe resourcegroep uw Blockchain Workbench-resources. Blockchain Workbench services zijn toegankelijk via een web-URL. De volgende stappen laten zien hoe u de web-URL van het geïmplementeerde framework kunt ophalen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **resource groepen** in het navigatie deel venster aan de linkerkant.
3. Kies de naam van de resource groep die u hebt opgegeven bij het implementeren van Block Chain Workbench.
4. Selecteer de kolomkop **type** om de lijst alfabetisch te sorteren op type.
5. Er zijn twee resources met het type **app service**. Selecteer de resource van het type **app service** *zonder* het achtervoegsel '-API '.

    ![Lijst met apps](media/deploy/resource-group-list.png)

6. In de App Service **Essentials** sectie, kopieer de **URL** -waarde voor de web-URL naar uw geïmplementeerde Blockchain Workbench.

    ![Essentiële informatie over app service](media/deploy/app-service.png)

Als u een aangepaste domeinnaam met Blockchain Workbench wilt koppelen, zie [configureren van een aangepaste domeinnaam voor een web-app in Azure App Service met Traffic Manager](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="azure-ad-configuration-script"></a>Azure AD-configuratie script

Azure AD moet worden geconfigureerd om uw Block Chain Workbench-implementatie te volt ooien. U gebruikt een Power shell-script om de configuratie uit te voeren.

1. Navigeer in een browser naar de [Web-URL van Block Chain Workbench](#blockchain-workbench-web-url).
2. U ziet instructies voor het instellen van Azure AD met behulp van Cloud Shell. Kopieer de opdracht en start Cloud Shell.

    ![AAD-script starten](media/deploy/launch-aad-script.png)

3. Kies de Azure AD-Tenant waar u Block Chain Workbench hebt geïmplementeerd.
4. Plak en voer de opdracht uit in Cloud Shell.
5. Wanneer u hierom wordt gevraagd, voert u de Azure AD-Tenant in die u wilt gebruiken voor Block Chain Workbench. Dit is de Tenant die de gebruikers voor Block Chain Workbench bevat.

    > [!IMPORTANT]
    > De geverifieerde gebruiker heeft machtigingen nodig voor het maken van Azure AD-toepassings registraties en het verlenen van gedelegeerde toepassings machtigingen in de Tenant. Mogelijk moet u een beheerder van de Tenant vragen om het Azure AD-configuratie script uit te voeren of een nieuwe Tenant te maken.

    ![Azure AD-Tenant invoeren](media/deploy/choose-tenant.png)

6. U wordt gevraagd om u te verifiëren bij de Azure AD-Tenant met behulp van een browser. Open de web-URL in een browser, voer de code in en authenticatie.

    ![Verifiëren met code](media/deploy/authenticate.png)

7. Het script voert diverse status berichten uit. U krijgt een **geslaagd** status bericht als de Tenant is ingericht.
8. Ga naar de URL van de Block Chain-Workbench. U wordt gevraagd om toestemming te geven om Lees machtigingen te verlenen aan de Directory. Hiermee wordt de Block Chain Workbench-web-app toegang tot de gebruikers in de Tenant. Als u de Tenant beheerder bent, kunt u ervoor kiezen om toestemming te geven voor de hele organisatie. Met deze optie wordt de toestemming geaccepteerd voor alle gebruikers in de Tenant. Anders wordt elke gebruiker gevraagd toestemming te geven bij het eerste gebruik van de Block Chain Workbench-webtoepassing.
9. Selecteer **accepteren** om toestemming te geven.

     ![Toestemming voor het lezen van gebruikers profielen](media/deploy/graph-permission-consent.png)

10. Na toestemming kunt u de Block Chain Workbench-web-app gebruiken.

## <a name="azure-ad-configuration"></a>Azure AD-configuratie

Als u ervoor kiest om de Azure AD-instellingen voor de implementatie hand matig te configureren of te verifiëren, voltooit u alle stappen in deze sectie. Als u de Azure AD-instellingen liever automatisch wilt configureren, gebruikt u het [configuratie script van Azure AD](#azure-ad-configuration-script) nadat u Block Chain Workbench hebt geïmplementeerd.

### <a name="blockchain-workbench-api-app-registration"></a>Registratie van API-app voor Blockchain Workbench

Voor de implementatie van Block Chain Workbench is registratie van een Azure AD-toepassing vereist. U hebt een Azure Active Director-tenant  (Azure AD) nodig om de app te registreren. U kunt een bestaande Tenant gebruiken of een nieuwe Tenant maken. Als u een bestaande Azure AD-Tenant gebruikt, hebt u voldoende machtigingen nodig voor het registreren van toepassingen, het verlenen van Graph API machtigingen en het toestaan van gast toegang binnen een Azure AD-Tenant. Als u niet gemachtigd bent in een bestaande Azure AD-tenant maakt u een nieuwe tenant.


1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer uw account in de rechter bovenhoek en schakel over naar de gewenste Azure AD-Tenant. De tenant moet de tenant zijn van de abonnementsbeheerder van het abonnement waar Workbench is geïmplementeerd en waar u voldoende machtigingen hebt om toepassingen te registreren.
3. Selecteer in het navigatiedeelvenster aan de linkerkant de **Azure Active Directory**-service. Selecteer **app-registraties** > **nieuwe toepassings registratie**.

    ![App-registratie](media/deploy/app-registration.png)

4. Geef een **naam** en **aanmeldings-URL** op voor de toepassing. U kunt waarden van tijdelijke aanduidingen gebruiken omdat de waarden zijn gewijzigd tijdens de implementatie. 

    ![App-registratie maken](media/deploy/app-registration-create.png)

    |Instelling  | Waarde  |
    |---------|---------|
    |Name | `Blockchain API` |
    |Toepassingstype |Web-app/-API|
    |Aanmeldings-URL | `https://blockchainapi` |

5. Selecteer **maken** om de Azure AD-toepassing te registreren.

### <a name="modify-manifest"></a>Manifest wijzigen

Vervolgens moet u het manifest wijzigen om toepassings rollen binnen Azure AD te gebruiken om Block Chain Workbench-beheerders op te geven.  Zie voor meer informatie over de toepassingsmanifesten [Azure Active Directory-toepassingsmanifest](../../active-directory/develop/reference-app-manifest.md).

1. Selecteer in het deel venster geregistreerde toepassings Details het **manifest** voor de toepassing die u hebt geregistreerd.
2. Genereer een GUID. U kunt een GUID genereren met behulp van de Power shell-opdracht [GUID]:: NewGuid () of de nieuwe GUID-cmdlet. Een andere mogelijkheid is om een GUID-Generator website te gebruiken.
3. U gaat het **appRoles** gedeelte van het manifest bijwerken. Selecteer in het manifest bewerkingsvenster **bewerken** en vervang `"appRoles": []` door de opgegeven JSON. Vervang de waarde voor de **id** veld door de GUID die u hebt gegenereerd. 

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
    > De waarde **beheerder** is nodig voor het identificeren van Blockchain Workbench-beheerders.

4. In het manifest wijzigt u ook de waarde **Oauth2AllowImplicitFlow** in **True**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. Selecteer **Opslaan** om de wijzigingen in het manifest op te slaan.

### <a name="add-graph-api-required-permissions"></a>Graph API vereiste machtigingen toevoegen

De API-toepassing moet de gebruiker om toestemming voor toegang tot de map vragen. Stel de volgende vereiste machtiging in voor de API-App:

1. Selecteer in de registratie van de Block Chain-API-app **instellingen > vereiste machtigingen > Selecteer een API > Microsoft Graph**.

    ![Een API selecteren](media/deploy/client-app-select-api.png)

    Klik op **Selecteren**.

2. Kies in **toegang inschakelen** onder **gedelegeerde machtigingen** **de optie alle basis profielen van alle gebruikers lezen**.

    ![Toegang inschakelen](media/deploy/client-app-read-perms.png)

    Selecteer **Opslaan** en selecteer vervolgens **gereed**.

3. Selecteer in **vereiste machtigingen**de optie **verlenen machtigingen** en selecteer vervolgens **Ja** bij de verificatie prompt.

   ![Machtigingen verlenen](media/deploy/client-app-grant-permissions.png)

   Door deze machtiging te verlenen krijgt Blockchain Workbench toegang tot gebruikers in de directory. De leesmachtiging is vereist voor het zoeken en toevoegen van leden in Blockchain Workbench.

### <a name="get-application-id"></a>Toepassings-ID ophalen

De toepassings-ID en Tenant gegevens zijn vereist voor de implementatie. Verzamel en bewaar de gegevens voor gebruik tijdens de implementatie.

1. Selecteer **instellingen** > **eigenschappen** voor de toepassing die u hebt geregistreerd.
2. In het **eigenschappen** deelvenster, kopieer en bewaar de volgende waarden voor later gebruik tijdens de implementatie.

    ![Eigenschappen van API-app](media/deploy/app-properties.png)

    | Instelling voor opslaan  | Gebruiken in implementatie |
    |------------------|-------------------|
    | Toepassings-id | Azure Active Directory instellen >-toepassings-ID |

### <a name="get-tenant-domain-name"></a>Domein naam van Tenant ophalen

Verzamel en bewaar de domeinnaam van de Active Directory-tenant waar de toepassingen zijn geregistreerd. 

Selecteer in het navigatiedeelvenster aan de linkerkant de **Azure Active Directory**-service. Selecteer **Namen van aangepaste domeinen**. Kopieer en bewaar de domeinnaam.

![Domeinnaam](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Gast gebruikers instellingen

Als u gast gebruikers in uw Azure AD-Tenant hebt, volgt u de extra stappen om ervoor te zorgen dat de gebruikers toewijzing en het beheer van Block Chain Workbench goed werken.

1. Schakel over naar uw Azure AD-Tenant en selecteer **Azure Active Directory > gebruikers instellingen > externe instellingen voor samen werking beheren**.
2. Stel **gast gebruikers machtigingen** in op **Nee**.
    ![Instellingen voor externe samen werking](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>De antwoord-URL configureren

Zodra Azure Block Chain Workbench is geïmplementeerd, moet u de **antwoord-URL** voor de Azure Active Directory (Azure AD)-client toepassing configureren van de geïmplementeerde Block Chain Workbench web-URL.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Controleer of u in de tenant bent waar u de Azure AD-clienttoepassing hebt geregistreerd.
3. Selecteer in het navigatiedeelvenster aan de linkerkant de **Azure Active Directory**-service. Selecteer **App-registraties**.
4. Selecteer de Azure AD-client toepassing die u hebt geregistreerd in de sectie vereisten.
5. Selecteer **instellingen > antwoord-url's**.
6. Geef de web-URL op van de Azure Block Chain Workbench-implementatie die u hebt opgehaald in het gedeelte **de web-URL van Azure Block Chain Workbench ophalen** . De antwoord-URL wordt voorafgegaan door `https://`. Bijvoorbeeld: `https://myblockchain2-7v75.azurewebsites.net`

    ![Antwoord-URL's](media/deploy/configure-reply-url.png)

7. Selecteer **opslaan** om de clientregistratie bij te werken.

## <a name="remove-a-deployment"></a>Een implementatie verwijderen

Wanneer u een implementatie niet meer nodig hebt, kunt u een implementatie verwijderen door de Blockchain Workbench-resourcegroep te verwijderen.

1. In de Azure-portal, gaat u naar **resourcegroep** in het navigatiedeelvenster links en selecteert u de resourcegroep die u wilt verwijderen. 
2. Selecteer **Resourcegroep verwijderen**. Bevestig de verwijdering door de naam van de resourcegroep in te voeren en selecteer **verwijderen**.

    ![Resourcegroep verwijderen](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel met instructies hebt u Azure Blockchain Workbench geïmplementeerd. Ga door naar het volgende artikel voor meer informatie over het maken van een blockchain-toepassing.

> [!div class="nextstepaction"]
> [Een Block Chain-toepassing maken in azure Block Chain workbench](create-app.md)
