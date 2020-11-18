---
ms.openlocfilehash: dc5816eb653053d63bfe3bf48413b3557583d109
ms.sourcegitcommit: e0d9b18d2d4cbeb4a48890f3420a47e6a90abc53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "49347787"
---
<!-- markdownlint-disable MD002 MD041 -->

In diesem Abschnitt verwenden Sie das Microsoft Graph-SDK, um dem Kalender des Benutzers ein Ereignis hinzuzufügen.

## <a name="implement-a-dialog"></a>Implementieren eines Dialogs

Erstellen Sie zunächst ein neues benutzerdefiniertes Dialogfeld, in dem der Benutzer aufgefordert wird, die zum Hinzufügen eines Ereignisses zum Kalender erforderlichen Werte einzugeben. In diesem Dialogfeld wird ein **WaterfallDialog** -Objekt verwendet, um die folgenden Schritte auszuführen.

- Eingabeaufforderung für einen Betreff
- Fragen, ob der Benutzer Personen einladen möchte
- Aufforderung für Teilnehmer (wenn der Benutzer "Ja" zu vorherigem Schritt sagte)
- Eingabeaufforderung für Startdatum und-Uhrzeit
- Auffordern eines Enddatums und einer Endzeit
- Alle gesammelten Werte anzeigen und Benutzer zum bestätigen bitten
- Wenn der Benutzer bestätigt, rufen Sie das Zugriffstoken von **OAuthPrompt** ab.
- Erstellen des Ereignisses

1. Erstellen Sie eine neue Datei im **./Dialogs** -Verzeichnis mit dem Namen **NewEventDialog.cs** , und fügen Sie den folgenden Code hinzu.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.Graph;
    using CalendarBot.Graph;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    using TimexTypes = Microsoft.Recognizers.Text.DataTypes.TimexExpression.Constants.TimexTypes;

    namespace CalendarBot.Dialogs
    {
        public class NewEventDialog : LogoutDialog
        {
            protected readonly ILogger _logger;
            private readonly IGraphClientService _graphClientService;

            public NewEventDialog(
                IConfiguration configuration,
                IGraphClientService graphClientService)
                : base(nameof(NewEventDialog), configuration["ConnectionName"])
            {

            }

            // Generate a DateTime from the list of
            // DateTimeResolutions provided by the DateTimePrompt
            private static DateTime GetDateTimeFromResolutions(IList<DateTimeResolution> resolutions)
            {
                var timex = new TimexProperty(resolutions[0].Timex);

                // Handle the "now" case
                if (timex.Now ?? false)
                {
                    return DateTime.Now;
                }

                // Otherwise generate a DateTime
                return TimexHelpers.DateFromTimex(timex);
            }
        }
    }
    ```

    Dies ist die Shell eines neuen Dialogs, mit der der Benutzer aufgefordert wird, die zum Hinzufügen eines Ereignisses zum Kalender erforderlichen Werte einzugeben.

1. Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um den Benutzer zu einem Betreff aufzufordern.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForSubjectSnippet":::

1. Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um den Betreff zu speichern, den der Benutzer im vorherigen Schritt gegeben hat, und um zu Fragen, ob Teilnehmer hinzugefügt werden sollen.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAddAttendeesSnippet":::

1. Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um die Antwort des Benutzers aus dem vorherigen Schritt zu überprüfen und den Benutzer bei Bedarf zur Eingabe einer Liste von Teilnehmern aufzufordern.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAttendeesSnippet":::

1. Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um die Liste der Teilnehmer aus dem vorherigen Schritt (sofern vorhanden) zu speichern und den Benutzer nach einem Startdatum und einer Startzeit aufzufordern.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForStartSnippet":::

1. Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um den Startwert aus dem vorherigen Schritt zu speichern und den Benutzer nach einem Enddatum und einer Endzeit aufzufordern.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForEndSnippet":::

1. Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um den End-Wert aus dem vorherigen Schritt zu speichern, und fordern Sie den Benutzer auf, alle Eingaben zu bestätigen.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConfirmNewEventSnippet":::

1. Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um die Antwort des Benutzers im vorherigen Schritt zu überprüfen. Wenn der Benutzer die Eingaben bestätigt, verwenden Sie die **OAuthPrompt** -Klasse, um ein Zugriffstoken abzurufen. Beenden Sie andernfalls das Dialogfeld.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="GetTokenSnippet":::

1. Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um das Microsoft Graph-SDK zum Erstellen des neuen Ereignisses zu verwenden.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AddEventSnippet":::

    Überprüfen Sie die Funktionsweise dieses Codes.

    - Es ruft die **Mailbox Settings** des Benutzers ab, um die bevorzugte Zeitzone des Benutzers zu bestimmen.
    - Es wird ein **Event** -Objekt mit den vom Benutzer bereitgestellten Werten erstellt. Beachten Sie, dass die Eigenschaften **Start** und **End** mit der Zeitzone des Benutzers festgelegt werden.
    - Das Ereignis wird im Kalender des Benutzers erstellt.

## <a name="add-validation"></a>Validierung hinzufügen

Fügen Sie nun der Eingabe des Benutzers eine Validierung hinzu, um Fehler beim Erstellen des Ereignisses mit Microsoft Graph zu vermeiden. Wir möchten sicherstellen, dass:

- Wenn der Benutzer eine Teilnehmerliste erhält, sollte dies eine durch Semikolons getrennte Liste gültiger e-Mail-Adressen sein.
- Das Startdatum/die Uhrzeit sollte ein gültiges Datum und eine gültige Uhrzeit sein.
- Das Enddatum/die Uhrzeit sollte ein gültiges Datum und eine gültige Uhrzeit sein und sollte später als der Anfang sein.

1. Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um den Eintrag des Benutzers für die Teilnehmer zu überprüfen.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AttendeesValidatorSnippet":::

1. Fügen Sie der **NewEventDialog** -Klasse die folgenden Funktionen hinzu, um den Eintrag des Benutzers für Startdatum und-Uhrzeit zu überprüfen.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="StartValidatorSnippet":::

1. Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um den Eintrag des Benutzers für Enddatum und-Uhrzeit zu überprüfen.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="EndValidatorSnippet":::

## <a name="add-steps-to-waterfalldialog"></a>Hinzufügen von Schritten zu WaterfallDialog

Da Sie nun alle "Schritte" für das Dialogfeld haben, besteht der letzte Schritt darin, Sie zu einem **WaterfallDialog** im Konstruktor hinzuzufügen und dann die **NewEventDialog** zum **MainDialog** hinzuzufügen.

1. Suchen Sie den **NewEventDialog** -Konstruktor, und fügen Sie den folgenden Code hinzu.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConstructorSnippet":::

    Dadurch werden alle verwendeten Dialogfelder hinzugefügt, und alle Funktionen, die Sie als Schritte in der **WaterfallDialog** implementiert haben, werden hinzugefügt.

1. Öffnen Sie **/Dialogs/MainDialog.cs** , und fügen Sie dem Konstruktor die folgende Verbindung hinzu.

    ```csharp
    AddDialog(new NewEventDialog(configuration, graphClientService));
    ```

1. Ersetzen Sie den Code innerhalb des `else if (command.StartsWith("add event"))` Blocks in `ProcessStepAsync` durch Folgendes.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="AddEventSnippet" highlight="3":::

1. Speichern Sie alle Änderungen, und starten Sie den bot neu.

1. Verwenden Sie den bot Framework-Emulator, um eine Verbindung mit dem bot herzustellen und sich anzumelden. Klicken Sie auf die Schaltfläche **Ereignis hinzufügen** .

1. Antworten Sie auf die Eingabeaufforderungen, um ein neues Ereignis zu erstellen. Beachten Sie Folgendes: Wenn Sie zur Eingabe von Start-und Endwerten aufgefordert werden, können Sie Ausdrücke wie "heute um 15.00 Uhr" oder "jetzt" verwenden.

    ![Screenshot des Dialogfelds "Ereignis hinzufügen" im bot Framework-Emulator](images/add-event.png)
