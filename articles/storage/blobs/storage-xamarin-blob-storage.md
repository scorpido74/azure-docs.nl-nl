---
title: Het gebruik van object (BLOB)-opslag van Xamarin | Microsoft Docs
description: Met de Azure Storage-client bibliotheek voor Xamarin kunnen ontwikkel aars iOS-, Android-en Windows Store-apps maken met hun eigen gebruikers interface. Deze zelf studie laat zien hoe u Xamarin kunt gebruiken om een toepassing te maken die gebruikmaakt van Azure Blob-opslag.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: a724cf309ef0677b79636f6789de9a6c8f9ba248
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465504"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Blob Storage gebruiken in Xamarin

Xamarin stelt ontwikkel aars in staat om een gedeelde C#-code basis te gebruiken voor het maken van iOS-, Android-en Windows Store-apps met hun eigen gebruikers interface. In deze zelf studie wordt uitgelegd hoe u Azure Blob Storage gebruikt met een Xamarin-toepassing. Als u meer wilt weten over Azure Storage, raadpleeg dan eerst [Inleiding tot Microsoft Azure Storage](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Een nieuwe Xamarin-toepassing maken

Voor deze zelf studie maken we een app die is gericht op Android, iOS en Windows. Met deze app maakt u gewoon een container en uploadt u een BLOB in deze container. We gebruiken Visual Studio in Windows, maar dezelfde informatie kan worden toegepast bij het maken van een app met behulp van Xamarin Studio op macOS.

Voer de volgende stappen uit om uw toepassing te maken:

1. Als u dat nog niet hebt gedaan, downloadt en installeert u [Xamarin voor Visual Studio](https://www.xamarin.com/download).
2. Open Visual Studio en maak een lege app (native Portable): **bestand > nieuw > Project > platformoverschrijdende > lege app (native Portable)**.
3. Klik met de rechter muisknop op uw oplossing in het deel venster Solution Explorer en selecteer **NuGet-pakketten beheren voor oplossing**. Zoek naar **WindowsAzure. Storage** en installeer de nieuwste stabiele versie voor alle projecten in uw oplossing.
4. Uw project bouwen en uitvoeren.

U hebt nu een toepassing waarmee u kunt klikken op een knop waarmee een item wordt verhoogd.

## <a name="create-container-and-upload-blob"></a>Container maken en BLOB uploaden

Vervolgens `(Portable)` voegt u onder uw project code toe aan `MyClass.cs` . Met deze code wordt een container gemaakt en wordt een BLOB geüpload naar deze container. `MyClass.cs`moet er als volgt uitzien:

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

Zorg ervoor dat u "your_account_name_here" en "your_account_key_here" vervangt door uw werkelijke account naam en de account sleutel.

Uw iOS-, Android-en Windows Phone-projecten hebben allemaal verwijzingen naar uw draag bare project. Dit betekent dat u al uw gedeelde code op één plek kunt schrijven en gebruiken in al uw projecten. U kunt nu de volgende regel code aan elk project toevoegen om te beginnen met het gebruik van:`MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp. Droid > MainActivity.cs

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

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp. iOS > ViewController.cs

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

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp. WinPhone > MainPage. xaml > MainPage.xaml.cs

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

U kunt deze toepassing nu uitvoeren in een Android-of Windows Phone-Emulator. U kunt deze toepassing ook uitvoeren in een iOS-emulator, maar hiervoor is wel een Mac vereist. Raadpleeg de documentatie voor het [verbinden van Visual Studio met een Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/) voor specifieke instructies over hoe u dit doet.

Wanneer u uw app hebt uitgevoerd, wordt de container `mycontainer` in uw opslag account gemaakt. Het moet de BLOB bevatten, `myblob` , die de tekst bevat `Hello, world!` . U kunt dit controleren met behulp van de [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een platformoverschrijdende toepassing maakt in Xamarin die gebruikmaakt van Azure Storage, die specifiek gericht is op één scenario in Blob Storage. U kunt echter veel meer doen met niet alleen Blob Storage, maar ook met tabel, bestand en Queue Storage. Raadpleeg de volgende artikelen voor meer informatie:

* [Aan de slag met Azure Blob Storage met .NET](storage-dotnet-how-to-use-blobs.md)
* [Kennismaking met Azure Files](../files/storage-files-introduction.md)
* [Ontwikkelen voor Azure Files met .NET](../files/storage-dotnet-how-to-use-files.md)
* [Aan de slag met Azure Table Storage met .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Aan de slag met Azure Queue Storage met .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
