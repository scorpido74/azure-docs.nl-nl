---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a7c696870e22e1692ca5ed778e47f8e4cc00615a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175936"
---
## <a name="set-up-the-development-environment"></a>De ontwikkelomgeving instellen

Deze sectie loopt door het opzetten van de ontwikkelomgeving. Dit omvat het maken van een ASP.NET MVC-app, het toevoegen van een verbinding met verbonden services, het toevoegen van een controller en het opgeven van de vereiste naamruimterichtlijnen.

### <a name="create-an-aspnet-mvc-app-project"></a>Een ASP.NET MVC-appproject maken

1. Open Visual Studio.

1. Selecteer **Bestand** > **nieuw** > **project**in het hoofdmenu .

1. Selecteer **webASP.NET** > **webtoepassing (.NET Framework)** in het dialoogvenster **Nieuw project.** Geef **storageAspNet**op in het veld **Naam** . Selecteer **OK**.

    ![Schermafbeelding van het dialoogvenster New Project](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Selecteer **MVC**in het dialoogvenster **Nieuwe ASP.NET webtoepassing** en selecteer **OK**.

    ![Schermafbeelding van het dialoogvenster Nieuwe ASP.NET webtoepassing](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Verbonden services gebruiken om verbinding te maken met een Azure-opslagaccount

1. Klik in **Solution Explorer**met de rechtermuisknop op het project.

1. Selecteer**Verbonden service** **toevoegen** > in het contextmenu .

1. Selecteer **cloudopslag met Azure Storage**in het dialoogvenster **Verbonden services.**

    ![Schermafbeelding van het dialoogvenster Verbonden services](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Selecteer in het dialoogvenster **Azure Storage** het Azure-opslagaccount dat voor deze zelfstudie moet worden gebruikt. Als u een nieuw Azure-opslagaccount wilt maken, selecteert u **Een nieuw opslagaccount maken**en vult u het formulier in. Nadat u een bestaand opslagaccount hebt geselecteerd of een nieuw opslagaccount hebt gemaakt, selecteert u **Toevoegen**. Visual Studio installeert het NuGet-pakket voor Azure Storage en een tekenreeks voor opslagverbindingen met **Web.config**.

1. Klik in **Solution Explorer**met de rechtermuisknop op **Afhankelijkheden,** kies **NuGet-pakketten beheren**en voeg een NuGet-pakketverwijzing toe naar de nieuwste versie van Microsoft.Azure.ConfigurationManager.

> [!TIP]
> Zie [Een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)voor meer informatie over het maken van een opslagaccount met de [Azure-portal.](https://portal.azure.com)
>
> U ook een opslagaccount maken met [Azure PowerShell,](../articles/storage/common/storage-powershell-guide-full.md) [Azure CLI](../articles/storage/common/storage-azure-cli.md)of Azure [Cloud Shell.](../articles/cloud-shell/overview.md)
