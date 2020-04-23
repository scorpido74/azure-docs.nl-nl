---
title: Verificatie van een beheerde identiteit met Azure Active Directory
description: In dit artikel vindt u informatie over het verifiëren van een beheerde identiteit met Azure Active Directory om toegang te krijgen tot Azure Event Hubs-bronnen
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: dfc60fbc03021e72dccc0f60a7ac34d204ef6df9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025183"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot gebeurtenishubsbronnen
Azure Event Hubs ondersteunt Azure Active Directory (Azure AD)-verificatie met [beheerde identiteiten voor Azure-resources.](../active-directory/managed-identities-azure-resources/overview.md) Beheerde identiteiten voor Azure-resources kunnen toegang tot Gebeurtenishubs-bronnen autoriseren met Azure AD-referenties van toepassingen die worden uitgevoerd in Azure Virtual Machines (VM's), functie-apps, virtuele machineschaalsets en andere services. Door beheerde identiteiten voor Azure-resources samen met Azure AD-verificatie te gebruiken, u voorkomen dat referenties worden opgeslagen in uw toepassingen die in de cloud worden uitgevoerd.

In dit artikel ziet u hoe u de toegang tot een gebeurtenishub autoriseert met behulp van een beheerde identiteit van een Azure VM.

## <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten inschakelen op een virtuele machine
Voordat u beheerde identiteiten voor Azure Resources gebruiken om gebeurtenishubsbronnen van uw VM te autoriseren, moet u eerst beheerde identiteiten voor Azure Resources op de VM inschakelen. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure Resources:

- [Azure Portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-clientbibliotheken](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Machtigingen verlenen aan een beheerde identiteit in Azure AD
Als u een aanvraag voor gebeurtenishubsservice wilt autoriseren vanuit een beheerde identiteit in uw toepassing, configureert u eerst RBAC-instellingen (Role-based access control) voor die beheerde identiteit. Azure Event Hubs definieert RBAC-rollen die machtigingen bevatten voor het verzenden en lezen vanuit gebeurtenishubs. Wanneer de RBAC-rol is toegewezen aan een beheerde identiteit, krijgt de beheerde identiteit toegang tot eventhubsgegevens op het juiste bereik.

Zie [Verifiëren met Azure Active Directory voor toegang tot Gebeurtenishubs-bronnen voor](authorize-access-azure-active-directory.md)meer informatie over het toewijzen van RBAC-rollen.

## <a name="use-event-hubs-with-managed-identities"></a>Event Hubs gebruiken met beheerde identiteiten
Als u gebeurtenishubs met beheerde identiteit wilt gebruiken, moet u de identiteit van de rol en het juiste bereik toewijzen. De procedure in deze sectie maakt gebruik van een eenvoudige toepassing die wordt uitgevoerd onder een beheerde identiteit en toegang heeft tot bronnen van Gebeurtenishubs.

Hier gebruiken we een voorbeeldvan een webtoepassing die wordt gehost in [Azure App Service.](https://azure.microsoft.com/services/app-service/) Zie [Een web-app voor ASP.NET Core maken in Azure](../app-service/app-service-web-get-started-dotnet.md) voor stapsgewijze instructies voor het maken van een webtoepassing.

Voer de volgende stappen uit nadat de toepassing is gemaakt: 

1. Ga naar **Instellingen** en selecteer **Identiteit**. 
1. Selecteer de **status** aan **.** 
1. Selecteer **Opslaan** om de instelling op te slaan. 

    ![Beheerde identiteit voor een web-app](./media/authenticate-managed-identity/identity-web-app.png)

Nadat u deze instelling hebt ingeschakeld, wordt een nieuwe service-identiteit gemaakt in uw Azure Active Directory (Azure AD) en geconfigureerd in de App Service-host.

Wijs deze serviceidentiteit nu toe aan een rol in het vereiste bereik in de bronnen van Event Hubs.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>RBAC-rollen toewijzen met de Azure-portal
Als u een rol wilt toewijzen aan resources voor gebeurtenishubs, navigeert u naar die bron in de Azure-portal. Geef de Instellingen voor Toegangsbeheer (IAM) voor de resource weer en volg deze instructies om roltoewijzingen te beheren:

> [!NOTE]
> In de volgende stappen wordt een functie voor service-identiteit aan de naamruimten van uw gebeurtenishubs toegeschreven. U dezelfde stappen volgen om een rol toe te wijzen die is toegewezen aan een gebeurtenishubsbron. 

1. Navigeer in de Azure-portal naar de naamruimte van uw gebeurtenishubs en geef het **overzicht** voor de naamruimte weer. 
1. Selecteer **Toegangsbeheer (IAM)** in het linkermenu om toegangsbeheerinstellingen voor de gebeurtenishub weer te geven.
1.  Selecteer het tabblad **Toewijzingen van rollen** om de lijst met roltoewijzingen weer te geven.
3.  Selecteer **Toevoegen** om een nieuwe rol toe te voegen.
4.  Selecteer op de pagina **Roltoewijzing toevoegen** de rollen van gebeurtenishubs die u wilt toewijzen. Zoek vervolgens naar de serviceidentiteit die u had geregistreerd om de rol toe te wijzen.
    
    ![Pagina Roltoewijzing toevoegen](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Selecteer **Opslaan**. De identiteit aan wie u de rol hebt toegewezen, wordt weergegeven onder die rol. De volgende afbeelding laat bijvoorbeeld zien dat de service-identiteit de eigenaar van gebeurtenishubsgegevens heeft.
    
    ![Identiteit toegewezen aan een rol](./media/authenticate-managed-identity/role-assigned.png)

Zodra u de rol hebt toegewezen, heeft de webtoepassing toegang tot de bronnen Van gebeurtenishubs onder het gedefinieerde bereik. 

### <a name="test-the-web-application"></a>De webtoepassing testen
1. Maak een naamruimte voor gebeurtenishubs en een gebeurtenishub. 
2. Implementeer de web-app naar Azure. Zie de volgende sectie met tabbladen voor koppelingen naar de webtoepassing op GitHub. 
3. Controleer of verzendenontvangen.aspx is ingesteld als het standaarddocument voor de web-app. 
3. Identiteit **inschakelen** voor de web-app. 
4. Wijs deze identiteit toe aan de rol **Van de eigenaar van gebeurtenishubs** op naamruimteniveau of gebeurtenishubniveau. 
5. Voer de webtoepassing uit, voer de naam van de naamruimte en de naam van de gebeurtenishub, een bericht in en selecteer **Verzenden**. Als u het evenement wilt ontvangen, selecteert u **Ontvangen**. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.Messaging.EventHubs (laatste)](#tab/latest)
U nu uw webtoepassing starten en uw browser naar de voorbeeldaspx-pagina wijzen. U de voorbeeldwebtoepassing vinden die gegevens verzendt en ontvangt van bronnen van Gebeurtenishubs in de [GitHub-repo.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

Installeer het nieuwste pakket van [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)en begin met het verzenden van evenementen naar Event Hubs met **EventHubProducerClient** en het ontvangen van evenementen met **EventHubConsumerClient.** 

> [!NOTE]
> Zie [Gebeurtenissen publiceren met Azure-identiteit op GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)voor een Java-voorbeeld dat een beheerde identiteit gebruikt om gebeurtenissen naar een gebeurtenishub te publiceren.

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (verouderd)](#tab/old)
U nu uw webtoepassing starten en uw browser naar de voorbeeldaspx-pagina wijzen. U de voorbeeldwebtoepassing vinden die gegevens verzendt en ontvangt van bronnen van Gebeurtenishubs in de [GitHub-repo.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)

Installeer het nieuwste pakket van [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)en begin met het verzenden naar en ontvangen van gegevens van Event hubs met behulp van de EventHubClient zoals weergegeven in de volgende code: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Event Hubs voor Kafka
U Apache Kafka-toepassingen gebruiken om berichten te verzenden naar en berichten te ontvangen van Azure Event Hubs met behulp van beheerde identiteit OAuth. Zie het volgende voorbeeld op GitHub: [Event Hubs voor Kafka - berichten verzenden en ontvangen met behulp van beheerde identiteit OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Voorbeelden
- **Azure.Messaging.EventHubs-voorbeelden**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Voorbeelden van Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Deze voorbeelden maken gebruik van de oude **Microsoft.Azure.EventHubs-bibliotheek,** maar u deze eenvoudig bijwerken naar de nieuwste **Azure.Messaging.EventHubs-bibliotheek.** Zie de [handleiding voor het migreren van Microsoft.Azure.EventHubs naar Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)als u het voorbeeld wilt verplaatsen van het gebruik van de oude bibliotheek naar de nieuwe bibliotheek.
    Dit voorbeeld is bijgewerkt om de nieuwste **Azure.Messaging.EventHubs-bibliotheek** te gebruiken.
- [Event Hubs voor Kafka - berichten verzenden en ontvangen met behulp van beheerde identiteit OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Volgende stappen
- Zie het volgende artikel voor meer informatie over beheerde identiteiten voor Azure-resources: [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)
- Zie de volgende gerelateerde artikelen:
    - [Aanvragen voor Azure Event Hubs verifiëren vanuit een toepassing met Azure Active Directory](authenticate-application.md)
    - [Aanvragen voor Azure Event Hubs verifiëren met behulp van gedeelde toegangshandtekeningen](authenticate-shared-access-signature.md)
    - [Toegang tot gebeurtenishubsresources autoriseren met Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Toegang tot bronnen van gebeurtenishubs autoriseren met behulp van gedeelde toegangshandtekeningen](authorize-access-shared-access-signature.md)