---
title: Polling Langlopende operaties | Microsoft Documenten
description: Azure Media Services biedt API's die aanvragen naar Media Services verzenden om bewerkingen te starten (bijvoorbeeld een kanaal maken, starten, stoppen of verwijderen), deze bewerkingen zijn langlopend. In dit onderwerp wordt uitgelegd hoe u langlopende bewerkingen peilen.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 43d9a6adc935010eab6e5e52d73f2019c8afcf5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887155"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Live streaming leveren met Azure Media Services

## <a name="overview"></a>Overzicht

Microsoft Azure Media Services biedt API's die aanvragen naar Media Services verzenden om bewerkingen te starten (bijvoorbeeld: een kanaal maken, starten, stoppen of verwijderen). Deze operaties zijn langlopend.

De Media Services .NET SDK biedt API's die het verzoek verzenden en wachten tot de bewerking is voltooid (intern worden de API's met bepaalde tussenpozen gepeild voor de voortgang van de bewerking). Bijvoorbeeld wanneer u kanaal belt. Start(), de methode keert terug nadat het kanaal is gestart. U ook gebruik maken van de asynchrone versie: wachten kanaal. StartAsync() (zie [TAP](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)voor informatie over asynchrone patroon op basis van taken). API's die een bewerkingsverzoek verzenden en vervolgens de status peilen totdat de bewerking is voltooid, worden "pollingmethoden" genoemd. Deze methoden (met name de Async-versie) worden aanbevolen voor uitgebreide clienttoepassingen en/of stateful services.

Er zijn scenario's waarin een toepassing niet kan wachten op een langlopende http-aanvraag en handmatig wil peilen voor de voortgang van de bewerking. Een typisch voorbeeld hiervan is een browser die interactie heeft met een stateloze webservice: wanneer de browser een kanaal aanvraagt, start de webservice een langdurige bewerking en retourneert de bewerkings-id naar de browser. De browser kan vervolgens de webservice vragen om de bedrijfsstatus op basis van de ID te krijgen. De Media Services .NET SDK biedt API's die nuttig zijn voor dit scenario. Deze API's worden "niet-polling methoden" genoemd.
De 'niet-pollingmethoden' hebben het volgende naamgevingspatroon:*OperationName-bewerking*verzenden (bijvoorbeeld SendCreateOperation). Bewerkingsmethoden*voor bewerking*van Bewerking verzenden, retourneer het **iOperation-object.** het geretourneerde object bevat informatie die kan worden gebruikt om de bewerking bij te houden. De*bewerkingsnaambewerkingsmethoden*verzenden, **retourneertaak\<IOperation->**.

Momenteel ondersteunen de volgende klassen niet-pollingmethoden: **Kanaal,** **StreamingEndpoint**en **Program**.

Als u wilt peilen naar de bewerkingsstatus, gebruikt u de methode **GetOperation** in de klasse **OperationBaseCollection.** Gebruik de volgende intervallen om de bedrijfsstatus te controleren: gebruik voor **kanaal-** en **streamingendpuntbewerkingen** 30 seconden; Voor **programmabewerkingen** gebruikt u 10 seconden.

## <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkelomgeving in en vul het app.config-bestand in met verbindingsgegevens, zoals beschreven in [de ontwikkeling van Media Services met .NET](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Voorbeeld

In het volgende voorbeeld wordt een klasse gedefinieerd die **ChannelOperations wordt genoemd**. Deze klassendefinitie kan een startpunt zijn voor de definitie van uw webserviceklasse. Voor de eenvoud, de volgende voorbeelden gebruik maken van de niet-async versies van methoden.

In het voorbeeld wordt ook uitgelegd hoe de client deze klasse kan gebruiken.

### <a name="channeloperations-class-definition"></a>Definitie van de klasse ChannelOperations

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
{
    // Read values from the App.config file.
    private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
    private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
    private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
    private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelPreview CreateChannelPreview()
    {
        return new ChannelPreview
        {
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>De clientcode

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

