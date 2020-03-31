---
title: Objectopslag (Blob) gebruiken vanuit Xamarin | Microsoft Documenten
description: Met de Azure Storage-clientbibliotheek voor Xamarin kunnen ontwikkelaars iOS-, Android- en Windows Store-apps maken met hun eigen gebruikersinterfaces. In deze zelfstudie ziet u hoe u Xamarin gebruikt om een toepassing te maken die Azure Blob-opslag gebruikt.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8a1c91c8a8a59af26386e70e68e7c4fd93f5eaa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726342"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Blob-opslag van Xamarin gebruiken

Xamarin stelt ontwikkelaars in staat om een gedeelde C#-codebase te gebruiken om iOS-, Android- en Windows Store-apps te maken met hun eigen gebruikersinterfaces. In deze zelfstudie ziet u hoe u Azure Blob-opslag gebruiken met een Xamarin-toepassing. Zie [Inleiding tot Microsoft Azure Storage](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)als u meer wilt weten over Azure Storage voordat u in de code duikt.

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Een nieuwe Xamarin-toepassing maken

Voor deze zelfstudie maken we een app die zich richt op Android, iOS en Windows. Deze app maakt gewoon een container en uploadt een blob in deze container. We gebruiken Visual Studio op Windows, maar dezelfde lessen kunnen worden toegepast bij het maken van een app met Xamarin Studio op macOS.

Volg de volgende stappen om uw toepassing te maken:

1. Als u [xamarin](https://www.xamarin.com/download)nog niet hebt gedownload en geïnstalleerd voor Visual Studio.
2. Open Visual Studio en maak een Lege App (Native Portable): **Bestand > Nieuwe > Project > Cross-Platform > Blank App (Native Portable)**.
3. Klik met de rechtermuisknop op uw oplossing in het deelvenster Solution Explorer en selecteer **NuGet-pakketten voor oplossing beheren.** Zoek naar **WindowsAzure.Storage** en installeer de nieuwste stabiele versie voor alle projecten in uw oplossing.
4. Bouw en voer uw project uit.

U moet nu een toepassing die u toelaat om een knop die een teller stappen klikken.

## <a name="create-container-and-upload-blob"></a>Container maken en blob uploaden

Vervolgens voegt `(Portable)` u onder uw project wat `MyClass.cs`code toe aan . Met deze code wordt een container geüpload en wordt een blob in deze container geüpload. `MyClass.cs`er als volgt uitzien:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Zorg ervoor dat u 'your_account_name_here' en 'your_account_key_here' vervangt door uw werkelijke accountnaam en accountsleutel.

Uw iOS-, Android- en Windows Phone-projecten hebben allemaal verwijzingen naar uw Portable-project- wat betekent dat u al uw gedeelde code op één plaats schrijven en deze gebruiken voor al uw projecten. U nu de volgende coderegel aan elk project toevoegen om te beginnen met profiteren:`MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

U deze toepassing nu uitvoeren in een Android- of Windows Phone-emulator. Je deze toepassing ook uitvoeren in een iOS-emulator, maar hiervoor is een Mac nodig. Lees de documentatie voor het verbinden van [Visual Studio met een Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/) voor specifieke instructies over hoe u dit doen.

Zodra u uw app uitvoert, `mycontainer` wordt de container in uw opslagaccount gemaakt. Het moet de `myblob`blob bevatten, die `Hello, world!`de tekst heeft, . U dit verifiëren met de [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een cross-platform toepassing in Xamarin maakt die Azure Storage gebruikt, waarbij u zich specifiek richt op één scenario in Blob Storage. U echter veel meer doen met niet alleen Blob Storage, maar ook met tabel-, bestands- en wachtrijopslag. Bekijk de volgende artikelen voor meer informatie:

* [Aan de slag met Azure Blob Storage met .NET](storage-dotnet-how-to-use-blobs.md)
* [Kennismaking met Azure Files](../files/storage-files-introduction.md)
* [Ontwikkelen voor Azure Files met .NET](../files/storage-dotnet-how-to-use-files.md)
* [Aan de slag met Azure Table Storage met .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Aan de slag met Azure Queue-opslag met .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
