---
title: Toegang tot Azure Storage met een beheerde identiteit met Windows VM-systeem | Microsoft Documenten
description: Een zelfstudie die u helpt bij het doorlopen van het proces voor het gebruiken van een door het Windows-VM-systeem toegewezen beheerde identiteit om toegang te krijgen tot Azure Storage.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 182bf02bfaad598a447304cc9f2ed42f6221176d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75971964"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Zelfstudie: een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Storage

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u toegang krijgt tot Azure Storage met een door het systeem toegewezen beheerde identiteit voor een virtuele Windows-machine (VM). Procedures voor:

> [!div class="checklist"]
> * Een blobcontainer in een opslagaccount maken
> * Door het systeem toegewezen beheerde entiteit van de virtuele Windows-machine toegang verlenen tot een opslagaccount
> * Een toegangstoken ophalen en daarmee Azure Storage aanroepen

> [!NOTE]
> Azure Active Directory-verificatie voor Azure Storage is beschikbaar als openbare preview.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]



## <a name="enable"></a>Inschakelen

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Toegang verlenen


### <a name="create-storage-account"></a>Een opslagaccount maken

In deze sectie maakt u een opslagaccount.

1. Klik op de knop **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Storage** en vervolgens **Opslagaccount - blob, bestand, tabel, wachtrij**.
3. Geef onder **Naam** een naam voor het opslagaccount op.
4. **Implementatiemodel** en **Soort account** moeten respectievelijk worden ingesteld op **Resource Manager** en **Storage (algemeen gebruik v1)**.
5. Zorg ervoor dat de waarden van **Abonnement** en **Resourcegroep** overeenkomen met de waarden die u hebt opgegeven bij het maken van de virtuele machine in de vorige stap.
6. Klik **op Maken**.

    ![Nieuw opslagaccount maken](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

### <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Een blobcontainer maken en een bestand naar het opslagaccount uploaden

Voor bestanden is blobopslag nodig, dus moeten we een blobcontainer maken waarin het bestand kan worden opgeslagen. Vervolgens uploadt u een bestand naar de blobcontainer in het nieuwe opslagaccount.

1. Navigeer terug naar het zojuist gemaakte opslagaccount.
2. Klik onder **Blob Service** op **Containers**.
3. Klik op **+ Container** boven aan de pagina.
4. Voer onder **Nieuwe container** een naam in voor de container en laat **Niveau openbare toegang** ingesteld staan op de standaardwaarde.

    ![Opslagcontainer maken](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Maak met behulp van een editor naar keuze een bestand met de naam *hello_world.txt* op uw lokale computer. Open het bestand en voeg de tekst ‘Hello world! :)’ toe (zonder de aanhalingstekens) en sla het bestand vervolgens op.
6. Upload het bestand naar de zojuist gemaakte container door op de naam van de container te klikken, en vervolgens op **Uploaden**
7. Klik in het deelvenster **Blob uploaden** onder **Bestanden** op het mappictogram en blader naar het bestand **hello_world.txt** op uw lokale computer, selecteer het bestand en klik vervolgens op **Uploaden**.
    ![Tekstbestand uploaden](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

### <a name="grant-access"></a>Toegang verlenen

In deze sectie ziet u hoe u uw VM-toegang verleent tot een Azure Storage-container. U kunt de door het systeem toegewezen beheerde identiteit van de virtuele machine gebruiken om de gegevens in de Azure Storage-blob op te halen.

1. Navigeer terug naar het zojuist gemaakte opslagaccount.
2. Klik op de koppeling **Toegangsbeheer (IAM)** in het linkerpaneel.
3. Klik **op + Roltoewijzing toevoegen** boven aan de pagina om een nieuwe roltoewijzing voor uw vm toe te voegen.
4. Selecteer **onder Rol**in de vervolgkeuzelijst De optie **Opslagblob-gegevenslezer**.
5. In de volgende vervolgkeuzelijst, onder **Toegang toewijzen aan**, kiest u **Virtuele machine**.
6. Controleer vervolgens of het juiste abonnement wordt weergegeven in de vervolgkeuzelijst **Abonnement**, en stel **Resourcegroep** in op **Alle resourcegroepen**.
7. Kies onder **Selecteren** uw virtuele machine en klik vervolgens op **Opslaan**.

    ![Machtigingen toewijzen](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="access-data"></a>Toegang tot gegevens 

Azure Storage biedt systeemeigen ondersteuning voor Microsoft Azure AD-verificatie, zodat toegangstokens die zijn verkregen met behulp van een beheerde identiteit direct kunnen worden geaccepteerd. Dit maakt deel uit van de integratie van Azure Storage met Azure AD en wijkt af van het opgeven van referenties in de verbindingsreeks.

Hier vindt u een voorbeeld van een .NET-code van het openen van een verbinding met Azure Storage met behulp van een toegangstoken en vervolgens het lezen van de inhoud van het bestand dat u eerder hebt gemaakt. Deze code moet worden uitgevoerd op de virtuele machine om toegang te krijgen tot het eindpunt van de beheerde identiteit van de virtuele machine. .NET Framework 4.6 of hoger is vereist om de methode voor toegangstoken te gebruiken. Vervang de waarde van `<URI to blob file>` dienovereenkomstig. U kunt deze waarde verkrijgen door te navigeren naar het bestand dat u hebt gemaakt en geüpload naar de blobopslag en de **URL** onder **Eigenschappen** op de pagina **Overzicht** te kopiëren.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");

            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);

            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }
    }
}
```

Het antwoord bevat de inhoud van het bestand:

`Hello world! :)`


## <a name="disable"></a>Uitschakelen

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd een door het Windows-VM-systeem toegewezen identiteit in te schakelen om toegang tot Azure Storage te krijgen.  Zie voor meer informatie over Azure Storage:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)
