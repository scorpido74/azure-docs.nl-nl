---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176541"
---
1. Klik in de oplossingsweergave (of **Solution Explorer** in Visual Studio) met de rechtermuisknop op de map **Componenten,** klik op **Meer componenten opvragen...**, zoek naar de component Google Cloud **Messaging-client** en voeg deze toe aan het project.
2. Open het ToDoActivity.cs projectbestand en voeg de volgende instructie toe aan de klasse:

    ```csharp
    using Gcm.Client;
    ```

3. Voeg in de klasse **ToDoActiviteit** de volgende nieuwe code toe: 

    ```csharp
    // Create a new instance field for this activity.
    static ToDoActivity instance = new ToDoActivity();

    // Return the current activity instance.
    public static ToDoActivity CurrentActivity
    {
        get
        {
            return instance;
        }
    }
    // Return the Mobile Services client.
    public MobileServiceClient CurrentClient
    {
        get
        {
            return client;
        }
    }
    ```

    Hierdoor hebt u toegang tot de mobiele clientinstance vanuit het push handler-serviceproces.
4. Voeg de volgende code toe aan de **methode OnCreate** nadat de **MobileServiceClient** is gemaakt:

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

Uw **ToDoActivity** is nu voorbereid op het toevoegen van pushmeldingen.
