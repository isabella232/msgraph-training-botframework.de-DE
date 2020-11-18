---
ms.openlocfilehash: ced0e75c32d26aed43afa61d498f2f0c438bf2d0
ms.sourcegitcommit: e0d9b18d2d4cbeb4a48890f3420a47e6a90abc53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "49347832"
---
<!-- markdownlint-disable MD002 MD041 -->

In diesem Abschnitt verwenden Sie das Microsoft Graph-SDK, um den angemeldeten Benutzer abzurufen.

## <a name="create-a-graph-service"></a>Erstellen eines Graph-Diensts

Beginnen Sie mit der Implementierung eines Diensts, der vom bot verwendet werden kann, um ein **GraphServiceClient** aus dem Microsoft Graph-SDK zu erhalten und diesen Dienst dann für den bot über Dependency Injection verfügbar zu machen.

1. Erstellen Sie ein neues Verzeichnis im Stamm des Projekts mit dem Namen **Graph**. Erstellen Sie eine neue Datei im **./Graph** -Verzeichnis mit dem Namen **IGraphClientService.cs** , und fügen Sie den folgenden Code hinzu.

    :::code language="csharp" source="../demo/GraphCalendarBot/Graph/IGraphClientService.cs" id="IGraphClientServiceSnippet":::

1. Erstellen Sie eine neue Datei im **./Graph** -Verzeichnis mit dem Namen **GraphClientService.cs** , und fügen Sie den folgenden Code hinzu.

    :::code language="csharp" source="../demo/GraphCalendarBot/Graph/GraphClientService.cs" id="GraphClientServiceSnippet":::

1. Öffnen Sie **./Startup.cs** , und fügen Sie den folgenden Code am Ende der `ConfigureServices` Funktion hinzu.

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="AddGraphServiceSnippet":::

1. Öffnen Sie **./Dialogs/MainDialog.cs**. Fügen Sie am `using` Anfang der Datei die folgenden Anweisungen hinzu.

    ```csharp
    using System;
    using System.IO;
    using CalendarBot.Graph;
    using AdaptiveCards;
    using Microsoft.Graph;
    ```

1. Fügen Sie der **MainDialog** -Klasse die folgende Eigenschaft hinzu.

    ```csharp
    private readonly IGraphClientService _graphClientService;
    ```

1. Suchen Sie den Konstruktor für die **MainDialog** -Klasse, und aktualisieren Sie seine Signatur, um einen **IGraphServiceClient** -Parameter zu übernehmen.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ConstructorSignatureSnippet" highlight="4":::

1. Fügen Sie den folgenden Code zum Konstruktor hinzu.

    ```csharp
    _graphClientService = graphClientService;
    ```

## <a name="get-the-logged-on-user"></a>Den angemeldeten Benutzer abrufen

In diesem Abschnitt verwenden Sie Microsoft Graph, um den Namen, die e-Mail-Adresse und das Foto des Benutzers abzurufen. Anschließend erstellen Sie eine Adaptive Karte, um die Informationen anzuzeigen.

1. Erstellen Sie eine neue Datei im Stamm des Projekts mit dem Namen **CardHelper.cs**. Fügen Sie den folgenden Code in die Datei ein:

    ```csharp
    using AdaptiveCards;
    using Microsoft.Graph;
    using System;
    using System.IO;

    namespace CalendarBot
    {
        public class CardHelper
        {
            public static AdaptiveCard GetUserCard(User user, Stream photo)
            {
              // Create an Adaptive Card to display the user
                // See https://adaptivecards.io/designer/ for possibilities
                var userCard = new AdaptiveCard("1.2");

                var columns = new AdaptiveColumnSet();
                userCard.Body.Add(columns);

                var userPhotoColumn = new AdaptiveColumn { Width = AdaptiveColumnWidth.Auto };
                columns.Columns.Add(userPhotoColumn);

                userPhotoColumn.Items.Add(new AdaptiveImage {
                    Style = AdaptiveImageStyle.Person,
                    Size = AdaptiveImageSize.Small,
                    Url = GetDataUriFromPhoto(photo)
                });

                var userInfoColumn = new AdaptiveColumn {Width = AdaptiveColumnWidth.Stretch };
                columns.Columns.Add(userInfoColumn);

                userInfoColumn.Items.Add(new AdaptiveTextBlock {
                    Weight = AdaptiveTextWeight.Bolder,
                    Wrap = true,
                    Text = user.DisplayName
                });

                userInfoColumn.Items.Add(new AdaptiveTextBlock {
                    Spacing = AdaptiveSpacing.None,
                    IsSubtle = true,
                    Wrap = true,
                    Text = user.Mail ?? user.UserPrincipalName
                });

                return userCard;
            }

            private static Uri GetDataUriFromPhoto(Stream photo)
            {
                // Copy to a MemoryStream to get access to bytes
                var photoStream = new MemoryStream();
                photo.CopyTo(photoStream);

                var photoBytes = photoStream.ToArray();

                return new Uri($"data:image/png;base64,{Convert.ToBase64String(photoBytes)}");
            }
        }
    }
    ```

    In diesem Code wird das **AdaptiveCard** -NuGet-Paket verwendet, um eine Adaptive Karte zum Anzeigen des Benutzers zu erstellen.

1. Fügen Sie der **MainDialog** -Klasse die folgende Funktion hinzu.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="DisplayLoggedInUserSnippet":::

    Überprüfen Sie die Funktionsweise dieses Codes.

    - Er verwendet das **graphClient** , um [den angemeldeten Benutzer abzurufen](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0).
        - Es verwendet die `Select` -Methode, um die zurückgegebenen Felder zu begrenzen.
    - Es verwendet die **graphClient** , um [das Foto des Benutzers abzurufen](https://docs.microsoft.com/graph/api/profilephoto-get?view=graph-rest-1.0)und fordert die kleinste unterstützte Größe von 48x48 Pixeln an.
    - Es verwendet die **CardHelper** -Klasse, um eine Adaptive Karte zu erstellen und die Karte als Anlage zu senden.

1. Ersetzen Sie den Code innerhalb des `else if (command.StartsWith("show me"))` Blocks in `ProcessStepAsync` durch Folgendes.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ShowMeSnippet" highlight="3":::

1. Speichern Sie alle Änderungen, und starten Sie den bot neu.

1. Verwenden Sie den bot Framework-Emulator, um eine Verbindung mit dem bot herzustellen und sich anzumelden. Wählen Sie die Schaltfläche " **anzeigen** " aus, um den angemeldeten Benutzer anzuzeigen.

    ![Ein Screenshot der adaptiven Karte, die den Benutzer zeigt](images/user-card.png)
