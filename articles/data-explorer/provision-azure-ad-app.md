---
title: Een Azure AD-toepassing maken in Azure Data Explorer
description: Meer informatie over het maken van een Azure AD-toepassing in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 64e8637630675120fece1bbe1fa4a57d97f36eb5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550511"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Een Azure Active Directory-toepassingsregistratie maken in Azure Data Explorer

Azure Active Directory (Azure AD)-toepassingsverificatie wordt gebruikt voor toepassingen, zoals een onbeheerde service of een geplande stroom, die toegang moeten krijgen tot Azure Data Explorer zonder dat er een gebruiker aanwezig is. Als u verbinding maakt met een Azure Data Explorer-database met behulp van een toepassing, zoals een web-app, moet u verifiëren met behulp van serviceprincipal-verificatie. In dit artikel wordt beschreven hoe u een Azure AD-serviceprincipal maakt en registreert en deze vervolgens autoriseert om toegang te krijgen tot een Azure Data Explorer-database.

## <a name="create-azure-ad-application-registration"></a>Azure AD-toepassingsregistratie maken

Azure AD-toepassingsverificatie vereist het maken en registreren van een toepassing met Azure AD. Er wordt automatisch een serviceprincipal gemaakt wanneer de toepassingsregistratie wordt gemaakt in een Azure AD-tenant. 

1. Inloggen op [Azure-portal](https://portal.azure.com) `Azure Active Directory` en het blad openen

    ![Azure Active Directory selecteren in het portalmenu](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. Selecteer het **mes app-registraties** en selecteer **Nieuwe registratie**

    ![Een nieuwe app-registratie starten](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. Vul de volgende gegevens in: 

    * **Naam** 
    * **Ondersteunde accounttypen**
    * **URI-web** > **omleiden**
        > [!IMPORTANT] 
        > Het toepassingstype moet **web**zijn. De URI is optioneel en wordt in dit geval leeg gelaten.
    * Selecteer **Registreren**

    ![Nieuwe app-registratie registreren](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. Selecteer het **blad Overzicht** en kopieer de **toepassings-id**.

    > [!NOTE]
    > U hebt de toepassings-id nodig om de serviceprincipal te machtigen om toegang te krijgen tot de database.

    ![App-registratie-id kopiëren](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. Selecteer in het **& geheimenblad** **Nieuw clientgeheim**

    ![Start creatie van klantgeheim](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > In dit artikel wordt beschreven met behulp van een clientgeheim voor de referenties van de toepassing.  U ook een X509-certificaat gebruiken om uw aanvraag te verifiëren. Selecteer **Certificaat uploaden** en volg de instructies om het openbare gedeelte van het certificaat te uploaden.

1. Voer een beschrijving in, verloopt en selecteer **Toevoegen**

    ![Clientgeheime parameters invoeren](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. Kopieer de sleutelwaarde.

    > [!NOTE]
    > Wanneer u deze pagina verlaat, is de sleutelwaarde niet toegankelijk.  U hebt de sleutel nodig om clientreferenties naar de database te configureren.

    ![Clientgeheime sleutelwaarde kopiëren](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

Uw toepassing wordt gemaakt. Als u alleen toegang nodig hebt tot een geautoriseerde Azure Data Explorer-bron, zoals in het onderstaande programmatische voorbeeld, slaat u de volgende sectie over. Zie [gedelegeerde machtigingen configureren voor de toepassingsregistratie voor](#configure-delegated-permissions-for-the-application-registration)ondersteuning voor gedelegeerde machtigingen .

## <a name="configure-delegated-permissions-for-the-application-registration"></a>Gedelegeerde machtigingen voor de toepassingsregistratie configureren

Als uw toepassing toegang moet krijgen tot Azure Data Explorer met behulp van de referenties van de aanroepende gebruiker, configureert u gedelegeerde machtigingen voor uw toepassingsregistratie. Als u bijvoorbeeld een web-API bouwt om toegang te krijgen tot Azure Data Explorer en u wilt verifiëren met behulp van de referenties van de gebruiker die uw API *aanroept.*  

1. Selecteer in het **beheer van API-machtigingen** de optie **Een machtiging toevoegen**.
1. Selecteer **API's die mijn organisatie gebruikt**. Zoeken naar en selecteer **Azure Data Explorer**.

    ![Api-machtigingen voor Azure Data Explorer toevoegen](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. Selecteer **in Gedelegeerde machtigingen**het vak **user_impersonation** en **Machtigingen toevoegen**

    ![Gedelegeerde machtigingen selecteren met gebruikersimitatie](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>De serviceprincipal toegang verlenen tot een Azure Data Explorer-database

Nu uw serviceprincipal-toepassingsregistratie is gemaakt, moet u de bijbehorende serviceprincipaltoegang verlenen tot uw Azure Data Explorer-database. 

1. Maak in de [webgebruikersinterface](https://dataexplorer.azure.com/)verbinding met uw database en open een querytabblad.

1. Voer de volgende opdracht uit:

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    Bijvoorbeeld:
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    De laatste parameter is een tekenreeks die wordt weergegeven als notities wanneer u de rollen opvraagt die aan een database zijn gekoppeld.
    
    > [!NOTE]
    > Na het maken van de toepassingsregistratie kan er een vertraging van enkele minuten optreden totdat er naar Azure Data Explorer kan worden verwezen. Als u bij het uitvoeren van de opdracht een foutmelding ontvangt dat de toepassing niet is gevonden, wacht u en probeert u het opnieuw.

Zie beheerders van [beveiligingsrollen](/azure/kusto/management/security-roles) en [innamemachtigingen](/azure/kusto/api/netfx/kusto-ingest-client-permissions.md)voor meer informatie.  

## <a name="using-application-credentials-to-access-a-database"></a>Toepassingsreferenties gebruiken om toegang te krijgen tot een database

Gebruik de toepassingsreferenties om programmatisch toegang te krijgen tot uw database met behulp van de [Azure Data Explorer-clientbibliotheek.](/azure/kusto/api/netfx/about-kusto-data.md)

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > Geef de toepassings-id en de sleutel van de eerder gemaakte toepassingsregistratie (serviceprincipal) op.

Zie [Authenticeren met Azure AD voor Azure Data Explorer-toegang](/azure/kusto/management/access-control/how-to-authenticate-with-aad) en [gebruik Azure Key Vault met .NET Core-webapp](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app)voor meer informatie.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="invalid-resource-error"></a>Ongeldige resourcefout

Als uw toepassing wordt gebruikt om gebruikers of toepassingen voor Azure Data Explorer-toegang te verifiëren, moet u gedelegeerde machtigingen instellen voor azure Data Explorer-servicetoepassing. Uw toepassing declareren kan gebruikers of toepassingen verifiëren voor Azure Data Explorer-toegang. Als u dit niet doet, wordt een fout gemaakt die vergelijkbaar is met de volgende fout wanneer een verificatiepoging wordt gedaan:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

U moet de instructies volgen voor [het instellen van gedelegeerde machtigingen voor Azure Data Explorer-servicetoepassing.](#configure-delegated-permissions-for-the-application-registration)

### <a name="enable-user-consent-error"></a>Fout in de toestemming van de gebruiker inschakelen

Uw Azure AD-tenantbeheerder kan een beleid voeren dat voorkomt dat tenantgebruikers toestemming geven voor toepassingen. Deze situatie zal resulteren in een fout vergelijkbaar met de volgende, wanneer een gebruiker probeert in te loggen op uw toepassing:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

U moet contact opnemen met uw Azure AD-beheerder om toestemming te verlenen aan alle gebruikers in de tenant of toestemming van de gebruiker voor uw specifieke toepassing inschakelen.

## <a name="next-steps"></a>Volgende stappen

* Zie [Kusto-verbindingstekenreeksen](/azure/kusto/api/connection-strings/kusto.md) voor een lijst met ondersteunde verbindingstekenreeksen.
