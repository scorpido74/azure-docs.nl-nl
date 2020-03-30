---
title: Noodherstel implementeren met behulp van back-up en herstel in API-beheer
titleSuffix: Azure API Management
description: Meer informatie over het gebruik van back-ups en herstel om noodherstel uit te voeren in Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/03/2020
ms.author: apimpm
ms.openlocfilehash: f9758678708338a284a35b45f7f9dd43b9a9017c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335963"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Noodherstel implementeren met back-up en herstellen van services in Azure API Management

Door uw API's te publiceren en te beheren via Azure API Management, profiteert u van fouttolerantie en infrastructuurmogelijkheden die u anders handmatig zou ontwerpen, implementeren en beheren. Het Azure-platform vermindert een groot deel van potentiële fouten tegen een fractie van de kosten.

Als u wilt herstellen van beschikbaarheidsproblemen die van invloed zijn op de regio die uw API-beheerservice host, moet u op elk gewenst moment uw service in een andere regio opnieuw maken. Afhankelijk van uw hersteltijddoelstelling wilt u misschien een stand-byservice in een of meer regio's behouden. U ook proberen hun configuratie en inhoud synchroon te houden met de actieve service volgens uw doelstelling voor herstelpunten. De service back-up en herstel functies biedt de nodige bouwstenen voor de uitvoering van disaster recovery strategie.

Back-up- en herstelbewerkingen kunnen ook worden gebruikt voor het repliceren van API Management-serviceconfiguratie tussen operationele omgevingen, bijvoorbeeld ontwikkeling en fasering. Pas op dat runtime-gegevens zoals gebruikers en abonnementen ook worden gekopieerd, wat misschien niet altijd wenselijk is.

In deze handleiding ziet u hoe u back-up- en herstelbewerkingen automatiseren en hoe u zorgen voor een succesvolle verificatie van back-up- en herstelaanvragen door Azure Resource Manager.

> [!IMPORTANT]
> Herstelbewerking wijzigt de aangepaste hostnaamconfiguratie van de doelservice niet. We raden u aan om dezelfde aangepaste hostname en TLS-certificaat te gebruiken voor zowel actieve als stand-byservices, zodat het verkeer na het voltooien van de herstelbewerking kan worden omgeleid naar de stand-by-instantie door een eenvoudige DNS CNAME-wijziging.
>
> Back-upbewerking legt geen vooraf geaggregeerde logboekgegevens vast die worden gebruikt in rapporten die worden weergegeven op het Analytics-blad in de Azure-portal.

> [!WARNING]
> Elke back-up verloopt na 30 dagen. Als u probeert een back-up te herstellen nadat de vervaldatum van 30 dagen is verstreken, mislukt het herstel met een `Cannot restore: backup expired` bericht.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Azure Resource Manager-aanvragen verifiëren

> [!IMPORTANT]
> De REST API voor back-up en herstel maakt gebruik van Azure Resource Manager en heeft een ander verificatiemechanisme dan de REST API's voor het beheren van uw API Management-entiteiten. In de stappen in deze sectie wordt beschreven hoe u Azure Resource Manager-aanvragen verifiëren. Zie [Azure Resource Manager-aanvragen verifiëren](/rest/api/index)voor meer informatie.

Alle taken die u uitvoert op resources met Azure Resource Manager, moeten met De volgende stappen worden geverifieerd met Azure Active Directory:

-   Voeg een toepassing toe aan de Azure Active Directory-tenant.
-   Machtigingen instellen voor de toepassing die u hebt toegevoegd.
-   Download het token voor het verifiëren van aanvragen naar Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Een Azure Active Directory-toepassing maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Navigeer met het abonnement dat uw API Management-serviceinstantie bevat naar het tabblad **App-registraties** in **Azure Active Directory** (Azure Active Directory > Beheer/App-registraties).

    > [!NOTE]
    > Als de standaardmap van Azure Active Directory niet zichtbaar is voor uw account, neemt u contact op met de beheerder van het Azure-abonnement om de vereiste machtigingen voor uw account toe te kennen.

3. Klik op **Nieuwe toepassing registreren**.

    Het venster **Maken** wordt aan de rechterkant weergegeven. Daar voert u relevante informatie in in de AAD-app.

4. Voer een naam in voor de toepassing.
5. Selecteer **Native**voor het toepassingstype .
6. Voer een tijdelijke aanduiding-URL in, zoals `http://resources` voor het **URI-omleiden,** omdat dit een vereist veld is, maar de waarde wordt later niet meer gebruikt. Klik op het selectievakje om de toepassing op te slaan.
7. Klik **op Maken**.

### <a name="add-an-application"></a>Een toepassing toevoegen

1. Zodra de toepassing is gemaakt, klikt u op **API-machtigingen**.
2. Klik op **+ Een machtiging toevoegen**.
4. Druk op **Selecteer Microsoft-API's**.
5. Kies **Azure Service Management**.
6. Druk op **Selecteren**.

    ![Machtigingen toevoegen](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Klik op **Gedelegeerde machtigingen** naast de nieuw toegevoegde toepassing en schakel het selectievakje in voor **Access Azure Service Management (voorbeeld).**
8. Druk op **Selecteren**.
9. Klik **op Machtigingen verlenen**.

### <a name="configuring-your-app"></a>Uw app configureren

Voordat u de API's belt die de back-up genereren en deze herstellen, moet u een token krijgen. In het volgende voorbeeld wordt het pakket [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet gebruikt om het token op te halen.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Vervangen `{tenant id}` `{application id}`, `{redirect uri}` en met behulp van de volgende instructies:

1. Vervang `{tenant id}` de tenant-id van de Azure Active Directory-toepassing die u hebt gemaakt. U de ID openen door op **Eindpunten voor app-registraties** -> **te**klikken.

    ![Eindpunten][api-management-endpoint]

2. Vervang `{application id}` de waarde die u krijgt door naar de pagina **Instellingen** te navigeren.
3. Vervang `{redirect uri}` de waarde door de waarde op het tabblad **URI's omleiden** van uw Azure Active Directory-toepassing.

    Zodra de waarden zijn opgegeven, moet het codevoorbeeld een token retourneren dat vergelijkbaar is met het volgende voorbeeld:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > Het token kan na een bepaalde periode verlopen. Voer het codevoorbeeld opnieuw uit om een nieuw token te genereren.

## <a name="calling-the-backup-and-restore-operations"></a>De back-up- en herstelbewerkingen aanroepen

De REST API's zijn [Api Management Service - Backup](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) en Api Management Service - [Restore](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore).

Voordat u de bewerkingen 'back-up en herstel' aanroept die in de volgende secties zijn beschreven, stelt u de koptekst voor autorisatieaanvragen in voor uw REST-aanroep.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Een back-up maken van een API-beheerservice

Als u een back-up van een API Management-serviceprobleem wilt maken, gaat u als volgt op het volgende HTTP-verzoek:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

Hierbij

-   `subscriptionId`- ID van het abonnement met de API Management-service die u probeert te back-upsmaken
-   `resourceGroupName`- naam van de brongroep van uw Azure API Management-service
-   `serviceName`- de naam van de API Management-service waarvan u een back-up maakt op het moment dat u wordt gemaakt
-   `api-version`- vervangen door`2018-06-01-preview`

Geef in de hoofdtekst van het verzoek de naam van het doelAzure-opslagaccount, de toegangssleutel, de naam van blobcontainer en de back-upnaam op:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Stel de waarde `Content-Type` van `application/json`de aanknopingskop in op .

Back-up is een langdurige bewerking die meer dan een minuut in beslag kan nemen. Als de aanvraag is geslaagd en het `202 Accepted` back-upproces `Location` is gestart, ontvangt u een antwoordstatuscode met een koptekst. Doe 'GET'-verzoeken naar `Location` de URL in de koptekst om de status van de bewerking te achterhalen. Terwijl de back-up aan de gang is, ontvangt u nog steeds een statuscode '202 Geaccepteerd'. Een reactiecode `200 OK` van geeft aan dat de back-upbewerking succesvol is voltooid.

Let op de volgende beperkingen bij het maken van een back-up- of herstelverzoek:

-   **De** in de aanvraaginstantie gespecificeerde recipiënt **moet bestaan**.
-   Terwijl er een back-up wordt gemaakt, **vermijdt u wijzigingen in het beheer van de service,** zoals een upgrade of downgrade van SKU, wijziging van de domeinnaam en meer.
-   Herstel van een **back-up is slechts 30 dagen sinds** het moment van de oprichting gegarandeerd.
-   **Gebruiksgegevens** die worden gebruikt voor het maken van **analyserapporten, worden niet opgenomen** in de back-up. Gebruik [Azure API Management REST API][azure api management rest api] om periodiek analyserapporten op te halen voor bewaring.
-   Bovendien maken de volgende items geen deel uit van de back-upgegevens: aangepaste DOMEIN TLS/SSL-certificaten en tussentijdse of rootcertificaten die zijn geüpload door de inhoud van de klant, ontwikkelaarsportalen en instellingen voor virtuele netwerkintegratie.
-   De frequentie waarmee u serviceback-ups uitvoert, is van invloed op de doelstelling van uw herstelpunt. Om dit tot een minimum te beperken, raden we u aan regelmatig back-ups te implementeren en on-demand back-ups uit te voeren nadat u wijzigingen hebt aangebracht in uw API Management-service.
-   **Wijzigingen in** de serviceconfiguratie (bijvoorbeeld API's, beleidsregels en het verschijnen van ontwikkelaarsportalen) terwijl de back-upbewerking in werking **is, kunnen worden uitgesloten van de back-up en worden verloren.**
-   **Geef** toegang van het besturingsvlak tot Azure Storage Account, als de [firewall][azure-storage-ip-firewall] is ingeschakeld. De klant moet de set [IP-adressen][control-plane-ip-address] van Azure API Management Control Plane openen op zijn opslagaccount voor back-up naar of herstellen van. 

> [!NOTE]
> Als u probeert back-ups/herstelte van/naar een API-beheerservice met behulp van een opslagaccount dat [firewall][azure-storage-ip-firewall] heeft ingeschakeld, in dezelfde Azure-regio, te gebruiken, dan werkt dit niet. Dit komt omdat de aanvragen voor Azure Storage niet zijn gesnoeerd met een openbaar IP-adres van Compute > (Azure Api Management Control Plane). De opslagaanvraag cross-regio wordt snated.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Een API-beheerservice herstellen

Als u een API Management-service wilt herstellen vanuit een eerder gemaakte back-up, gaat u het volgende HTTP-verzoek in:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

Hierbij

-   `subscriptionId`- ID van het abonnement dat de API Management-service bevat waar u een back-up in herstelt
-   `resourceGroupName`- naam van de brongroep met de Azure API Management-service waaru een back-up in herstelt
-   `serviceName`- de naam van de API Management-service die wordt hersteld in gespecificeerd tijdens de creatie
-   `api-version`- vervangen door`2018-06-01-preview`

Geef in de hoofdtekst van het verzoek de locatie van het back-upbestand op. Dat wil zeggen, voeg de naam van het Azure-opslagaccount, de toegangssleutel, de naam van blobcontainers en de naam van de back-up toe:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Stel de waarde `Content-Type` van `application/json`de aanknopingskop in op .

Herstellen is een langdurige bewerking die tot 30 minuten of meer minuten kan duren. Als de aanvraag is geslaagd en het `202 Accepted` herstelproces is `Location` gestart, ontvangt u een antwoordstatuscode met een koptekst. Doe 'GET'-verzoeken naar `Location` de URL in de koptekst om de status van de bewerking te achterhalen. Terwijl het herstel aan de gang is, blijft u de statuscode '202 Geaccepteerd' ontvangen. Een antwoordcode `200 OK` van geeft aan dat de herstelbewerking succesvol is voltooid.

> [!IMPORTANT]
> **De SKU** van de service die wordt hersteld **in moet overeenkomen met** de SKU van de back-up service wordt hersteld.
>
> **Wijzigingen die** zijn aangebracht in de serviceconfiguratie (bijvoorbeeld API's, beleidsregels, het verschijnen van ontwikkelaarsportalen) terwijl de herstelbewerking wordt **uitgevoerd, kunnen worden overschreven.**

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Back-up- en herstelbewerkingen kunnen ook worden uitgevoerd met respectievelijk PowerShell [_Backup-AzApiManagement-_](/powershell/module/az.apimanagement/backup-azapimanagement) en [_Restore-AzApiManagement-opdrachten._](/powershell/module/az.apimanagement/restore-azapimanagement)

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende bronnen voor verschillende walkthroughs van het back-up-/herstelproces.

-   [Azure API-beheeraccounts repliceren](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Back-up maken en terugzetten in API Management automatiseren met Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: back-ups maken en configuratie](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    herstellen_De door Stuart beschreven aanpak komt niet overeen met de officiële richtlijnen, maar is wel interessant._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
