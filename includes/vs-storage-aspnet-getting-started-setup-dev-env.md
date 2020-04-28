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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67175936"
---
## <a name="set-up-the-development-environment"></a>De ontwikkelomgeving instellen

In deze sectie wordt uitgelegd hoe u de ontwikkel omgeving instelt. Dit omvat het maken van een ASP.NET MVC-app, het toevoegen van een verbonden services-verbinding, het toevoegen van een controller en het opgeven van de vereiste naam ruimte-instructies.

### <a name="create-an-aspnet-mvc-app-project"></a>Een ASP.NET MVC-app-project maken

1. Open Visual Studio.

1. Selecteer in het hoofd menu de optie **bestand** > **Nieuw** > **project**.

1. Selecteer in het dialoog venster **Nieuw project** **Web** > **ASP.net Web Application (.NET Framework)**. Geef **StorageAspNet**op in het veld **naam** . Selecteer **OK**.

    ![Schermafbeelding van het dialoogvenster New Project](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Selecteer **MVC**in het dialoog venster **nieuwe ASP.NET-webtoepassing** en selecteer vervolgens **OK**.

    ![Scherm afbeelding van het dialoog venster nieuwe ASP.NET-webtoepassing](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Verbonden services gebruiken om verbinding te maken met een Azure-opslag account

1. Klik in **Solution Explorer**met de rechter muisknop op het project.

1. Selecteer in het context menu de optie**verbonden service** **toevoegen** > .

1. Selecteer in het dialoog venster **verbonden services** de optie **Cloud opslag met Azure Storage**.

    ![Scherm opname van het dialoog venster verbonden services](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Selecteer in het dialoog venster **Azure Storage** het Azure-opslag account dat u wilt gebruiken voor deze zelf studie. Als u een nieuw Azure-opslag account wilt maken, selecteert u **een nieuw opslag account maken**en vult u het formulier in. Nadat u een bestaand opslag account hebt geselecteerd of een nieuwe hebt gemaakt, selecteert u **toevoegen**. Visual Studio installeert het NuGet-pakket voor Azure Storage en een opslag connection string op **Web. config**.

1. Klik in **Solution Explorer**met de rechter muisknop op **afhankelijkheden**, kies **NuGet-pakketten beheren**en voeg een NuGet-pakket verwijzing toe aan de nieuwste versie van micro soft. Azure. ConfigurationManager.

> [!TIP]
> Zie [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)voor meer informatie over het maken van een opslag account met de [Azure Portal](https://portal.azure.com).
>
> U kunt ook een opslag account maken met behulp van [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure cli](../articles/storage/common/storage-azure-cli.md)of [Azure Cloud shell](../articles/cloud-shell/overview.md).
