---
ms.openlocfilehash: 05b3223967bf2a6d321c00cca079cc5c5b8ff0db
ms.sourcegitcommit: e0d9b18d2d4cbeb4a48890f3420a47e6a90abc53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "49347807"
---
<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung verwenden Sie die **OAuthPrompt** des bot-Frameworks zur Implementierung der Authentifizierung im bot und erwerben Zugriffstoken für den Aufruf der Microsoft Graph-API.

1. Öffnen Sie **./appsettings.js** und nehmen Sie die folgenden Änderungen vor.

    - Ändern Sie den Wert von `MicrosoftAppId` in die Anwendungs-ID Ihrer **Graph Calendar bot** App-Registrierung.
    - Ändern Sie den Wert von `MicrosoftAppPassword` in Ihren **Graph Calendar bot** -Client Secret.
    - Fügen Sie einen Wert `ConnectionName` mit dem Wert von hinzu `GraphBotAuth` .

    :::code language="json" source="../demo/GraphCalendarBot/appsettings.example.json":::

    > [!NOTE]
    > Wenn Sie einen anderen Wert als `GraphBotAuth` den Namen Ihres Eintrags in OAuth- **Verbindungseinstellungen** im Azure-Portal verwendet haben, verwenden Sie diesen Wert für den `ConnectionName` Eintrag.

## <a name="implement-dialogs"></a>Implementieren von Dialogfeldern

1. Erstellen Sie im Stamm des Projekts mit dem Namen **Dialogfelder** ein neues Verzeichnis. Erstellen Sie eine neue Datei im **./Dialogs** -Verzeichnis mit dem Namen **LogoutDialog.cs** , und fügen Sie den folgenden Code hinzu.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/LogoutDialog.cs" id="LogoutDialogSnippet":::

    Dieses Dialogfeld stellt eine Basisklasse für alle anderen Dialogfelder im bot zur Verfügung, von denen abgeleitet werden soll. Dadurch kann sich der Benutzer unabhängig davon, wo er sich in den Dialogfeldern des bot befindet, abmelden.

1. Erstellen Sie eine neue Datei im **./Dialogs** -Verzeichnis mit dem Namen **MainDialog.cs** , und fügen Sie den folgenden Code hinzu.

    ```csharp
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Dialogs.Choices;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;

    namespace CalendarBot.Dialogs
    {
        public class MainDialog : LogoutDialog
        {
            const string NO_PROMPT = "no-prompt";
            protected readonly ILogger _logger;

            public MainDialog(IConfiguration configuration, ILogger<MainDialog> logger)
                : base(nameof(MainDialog), configuration["ConnectionName"])
            {
                _logger = logger;

                // OAuthPrompt dialog handles the authentication and token
                // acquisition
                AddDialog(new OAuthPrompt(
                    nameof(OAuthPrompt),
                    new OAuthPromptSettings
                    {
                        ConnectionName = ConnectionName,
                        Text = "Please login",
                        Title = "Login",
                        Timeout = 300000, // User has 5 minutes to login
                    }));

                AddDialog(new ChoicePrompt(nameof(ChoicePrompt)));

                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    LoginPromptStepAsync,
                    ProcessLoginStepAsync,
                    PromptUserStepAsync,
                    CommandStepAsync,
                    ProcessStepAsync,
                    ReturnToPromptStepAsync
                }));

                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }

            private async Task<DialogTurnResult> LoginPromptStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // If we're going through the waterfall a second time, don't do an extra OAuthPrompt
                var options = stepContext.Options?.ToString();
                if (options == NO_PROMPT)
                {
                    return await stepContext.NextAsync(cancellationToken: cancellationToken);
                }

                return await stepContext.BeginDialogAsync(nameof(OAuthPrompt), null, cancellationToken);
            }

            private async Task<DialogTurnResult> ProcessLoginStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // If we're going through the waterfall a second time, don't do an extra OAuthPrompt
                var options = stepContext.Options?.ToString();
                if (options == NO_PROMPT)
                {
                    return await stepContext.NextAsync(cancellationToken: cancellationToken);
                }

                // Get the token from the previous step. If it's there, login was successful
                if (stepContext.Result != null)
                {
                    var tokenResponse = stepContext.Result as TokenResponse;
                    if (!string.IsNullOrEmpty(tokenResponse?.Token))
                    {
                        await stepContext.Context.SendActivityAsync(
                            MessageFactory.Text("You are now logged in."), cancellationToken);
                        return await stepContext.NextAsync(null, cancellationToken);
                    }
                }

                await stepContext.Context.SendActivityAsync(
                    MessageFactory.Text("Login was not successful please try again."), cancellationToken);
                return await stepContext.EndDialogAsync();
            }

            private async Task<DialogTurnResult> PromptUserStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                var options = new PromptOptions
                {
                    Prompt = MessageFactory.Text("Please choose an option below"),
                    Choices = new List<Choice> {
                        new Choice { Value = "Show token" },
                        new Choice { Value = "Show me" },
                        new Choice { Value = "Show calendar" },
                        new Choice { Value = "Add event" },
                        new Choice { Value = "Log out" },
                    }
                };

                return await stepContext.PromptAsync(
                    nameof(ChoicePrompt),
                    options,
                    cancellationToken);
            }

            private async Task<DialogTurnResult> CommandStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // Save the command the user entered so we can get it back after
                // the OAuthPrompt completes
                var foundChoice = stepContext.Result as FoundChoice;
                // Result could be a FoundChoice (if user selected a choice button)
                // or a string (if user just typed something)
                stepContext.Values["command"] = foundChoice?.Value ?? stepContext.Result;

                // There is no reason to store the token locally in the bot because we can always just call
                // the OAuth prompt to get the token or get a new token if needed. The prompt completes silently
                // if the user is already signed in.
                return await stepContext.BeginDialogAsync(nameof(OAuthPrompt), null, cancellationToken);
            }

            private async Task<DialogTurnResult> ProcessStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                if (stepContext.Result != null)
                {
                    var tokenResponse = stepContext.Result as TokenResponse;

                    // If we have the token use the user is authenticated so we may use it to make API calls.
                    if (tokenResponse?.Token != null)
                    {
                        var command = ((string)stepContext.Values["command"] ?? string.Empty).ToLowerInvariant();

                        if (command.StartsWith("show token"))
                        {
                            // Show the user's token - for testing and troubleshooting
                            // Generally production apps should not display access tokens
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text($"Your token is: {tokenResponse.Token}"),
                                cancellationToken);
                        }
                        else if (command.StartsWith("show me"))
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I don't know how to do this yet!"),
                                cancellationToken);
                        }
                        else if (command.StartsWith("show calendar"))
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I don't know how to do this yet!"),
                                cancellationToken);
                        }
                        else if (command.StartsWith("add event"))
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I don't know how to do this yet!"),
                                cancellationToken);
                        }
                        else
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I'm sorry, I didn't understand. Please try again."),
                                cancellationToken);
                        }
                    }
                }
                else
                {
                    await stepContext.Context.SendActivityAsync(
                        MessageFactory.Text("We couldn't log you in. Please try again later."),
                        cancellationToken);
                    return await stepContext.EndDialogAsync(cancellationToken: cancellationToken);
                }

                // Go to the next step
                return await stepContext.NextAsync(cancellationToken: cancellationToken);
            }

            private async Task<DialogTurnResult> ReturnToPromptStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // Restart the dialog, but skip the initial login prompt
                return await stepContext.ReplaceDialogAsync(InitialDialogId, NO_PROMPT, cancellationToken);
            }
        }
    }
    ```

    Nehmen Sie sich einen Moment Zeit, diesen Code zu überprüfen.

    - Im Konstruktor wird ein [WaterfallDialog](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-waterfall-dialogs?view=azure-bot-service-4.0) mit einer Reihe von Schritten eingerichtet, die in der richtigen Reihenfolge ausgeführt werden.
        - In `LoginPromptStepAsync` wird ein **OAuthPrompt** gesendet. Wenn der Benutzer nicht angemeldet ist, wird der Benutzer eine Benutzeroberflächen Ansage senden.
        - In `ProcessLoginStepAsync` IT wird überprüft, ob die Anmeldung erfolgreich war, und es wird eine Bestätigung gesendet.
        - In `PromptUserStepAsync` wird ein **ChoicePrompt** mit den verfügbaren Befehlen gesendet.
        - `CommandStepAsync`Dadurch wird die Auswahl des Benutzers gespeichert, und dann wird ein **OAuthPrompt** erneut gesendet.
        - In `ProcessStepAsync` wird basierend auf dem empfangenen Befehl eine Aktion durch.
        - In `ReturnToPromptStepAsync` beginnt der Wasserfall, übergibt jedoch eine Kennzeichnung, um die anfängliche Benutzeranmeldung zu überspringen.

## <a name="update-calendarbot"></a>CalendarBot aktualisieren

Der nächste Schritt besteht darin, **CalendarBot** so zu aktualisieren, dass diese neuen Dialogfelder verwendet werden.

1. Öffnen Sie **./Bots/CalendarBot.cs** , und ersetzen Sie den gesamten Inhalt durch den folgenden Code.

    :::code language="csharp" source="../demo/GraphCalendarBot/Bots/CalendarBot.cs" id="CalendarBotSnippet":::

    Im folgenden finden Sie eine kurze Zusammenfassung der Änderungen.

    - Die **CalendarBot** -Klasse wurde in eine Vorlagenklasse geändert, wobei ein **Dialog Feld** empfangen wurde.
    - Die **CalendarBot** -Klasse wurde so geändert, dass Sie **TeamsActivityHandler** erweitert, sodass Sie sich in Microsoft Teams anmelden kann.
    - Zusätzliche Methodenüberschreibungen zur Aktivierung der Authentifizierung hinzugefügt.

## <a name="update-startupcs"></a>Startup.cs aktualisieren

Der letzte Schritt besteht darin, die `ConfigureServices` Methode so zu aktualisieren, dass die für die Authentifizierung erforderlichen Dienste und das neue Dialogfeld hinzugefügt werden.

1. Öffnen Sie **./Startup.cs** , und entfernen Sie die `services.AddTransient<IBot, Bots.CalendarBot>();` Verbindung aus der `ConfigureServices` -Methode.

1. Fügen Sie am Ende der Methode den folgenden Code ein `ConfigureServices` .

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="ConfigureServiceSnippet":::

## <a name="test-authentication"></a>Testen der Authentifizierung

1. Speichern Sie alle Änderungen, und starten Sie den bot mit `dotnet run` .

1. Öffnen Sie den bot Framework-Emulator. Wählen Sie das Menü **Datei** und dann **neue bot-Konfiguration...** aus.

1. Füllen Sie die Felder wie folgt aus.

    - **Name des bot:**`CalendarBot`
    - **Endpunkt-URL:**`https://localhost:3978/api/messages`
    - **Microsoft App-ID:** die Anwendungs-ID Ihrer **Graph Calendar bot** App-Registrierung
    - **Microsoft-App-Kennwort:** Ihr **grafischer Kalender bot** -Client Geheimnis
    - **In ihrer bot-Konfiguration gespeicherte Schlüssel verschlüsseln:** Aktiviert

    ![Screenshot des Dialogfelds "neue bot-Konfiguration"](images/new-bot-config.png)

1. Wählen Sie **Speichern und verbinden** aus. Nachdem der Emulator eine Verbindung hergestellt hat, sollte Folgendes angezeigt werden: `Welcome to Microsoft Graph CalendarBot. Type anything to get started.`

1. Geben Sie Text ein, und senden Sie ihn an den bot. Der bot antwortet mit einer Anmeldeaufforderung.

1. Klicken Sie auf die Schaltfläche **Anmelden** . Der Emulator fordert Sie auf, die URL zu bestätigen, die mit beginnt `oauthlink://https://token.botframeworkcom` . Wählen Sie **bestätigen** aus, um den Vorgang fortzusetzen.

1. Melden Sie sich im Popupfenster mit Ihrem Microsoft 365-Konto an. Überprüfen Sie die angeforderten Berechtigungen und akzeptieren Sie.

1. Sobald die Authentifizierung und Zustimmung abgeschlossen ist, enthält das Popupfenster einen Validierungscode. Kopieren Sie den Code, und schließen Sie das Fenster.

    ![Ein Screenshot des bot-Framework-Emulator-Validierungscodes](images/validation-code.png)

1. Geben Sie den Überprüfungscode im Chatfenster ein, um die Anmeldung abzuschließen.

    ![Ein Screenshot der Anmelde Unterhaltung mit dem Beispiel-bot](images/bot-login.png)

1. Wenn Sie die Schaltfläche " **Token anzeigen** " (oder "Typ" `show token` ) auswählen, zeigt der bot das Zugriffstoken an. Die Schaltfläche **Abmelden** (oder eingeben `log out` ) wird Sie abmelden.

> [!TIP]
> Möglicherweise wird die folgende Fehlermeldung im bot Framework-Emulator angezeigt, wenn eine Unterhaltung mit dem bot gestartet wird.
>
> ```text
> Failed to generate an actual sign-in link: Error: Failed to connect to ngrok instance for OAuth postback URL:
> FetchError: request to http://127.0.0.1:4041/api/tunnels failed, reason: connect ECONNREFUSED 127.0.0.1:4041
> ```
>
> Schließen Sie in diesem Fall den Emulator, und starten Sie ihn neu.
