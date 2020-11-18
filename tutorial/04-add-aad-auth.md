---
ms.openlocfilehash: 05b3223967bf2a6d321c00cca079cc5c5b8ff0db
ms.sourcegitcommit: e0d9b18d2d4cbeb4a48890f3420a47e6a90abc53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "49347807"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8caf2-101">In dieser Übung verwenden Sie die **OAuthPrompt** des bot-Frameworks zur Implementierung der Authentifizierung im bot und erwerben Zugriffstoken für den Aufruf der Microsoft Graph-API.</span><span class="sxs-lookup"><span data-stu-id="8caf2-101">In this exercise you will use the Bot Framework's **OAuthPrompt** to implement authentication in the bot, and acquire access tokens for calling the Microsoft Graph API.</span></span>

1. <span data-ttu-id="8caf2-102">Öffnen Sie **./appsettings.js** und nehmen Sie die folgenden Änderungen vor.</span><span class="sxs-lookup"><span data-stu-id="8caf2-102">Open **./appsettings.json** and make the following changes.</span></span>

    - <span data-ttu-id="8caf2-103">Ändern Sie den Wert von `MicrosoftAppId` in die Anwendungs-ID Ihrer **Graph Calendar bot** App-Registrierung.</span><span class="sxs-lookup"><span data-stu-id="8caf2-103">Change the value of `MicrosoftAppId` to the application ID of your **Graph Calendar Bot** app registration.</span></span>
    - <span data-ttu-id="8caf2-104">Ändern Sie den Wert von `MicrosoftAppPassword` in Ihren **Graph Calendar bot** -Client Secret.</span><span class="sxs-lookup"><span data-stu-id="8caf2-104">Change the value of `MicrosoftAppPassword` to your **Graph Calendar Bot** client secret.</span></span>
    - <span data-ttu-id="8caf2-105">Fügen Sie einen Wert `ConnectionName` mit dem Wert von hinzu `GraphBotAuth` .</span><span class="sxs-lookup"><span data-stu-id="8caf2-105">Add a value named `ConnectionName` with a value of `GraphBotAuth`.</span></span>

    :::code language="json" source="../demo/GraphCalendarBot/appsettings.example.json":::

    > [!NOTE]
    > <span data-ttu-id="8caf2-106">Wenn Sie einen anderen Wert als `GraphBotAuth` den Namen Ihres Eintrags in OAuth- **Verbindungseinstellungen** im Azure-Portal verwendet haben, verwenden Sie diesen Wert für den `ConnectionName` Eintrag.</span><span class="sxs-lookup"><span data-stu-id="8caf2-106">If you used a value other than `GraphBotAuth` for the name of your entry in **OAuth Connection Settings** in the Azure Portal, use that value for the `ConnectionName` entry.</span></span>

## <a name="implement-dialogs"></a><span data-ttu-id="8caf2-107">Implementieren von Dialogfeldern</span><span class="sxs-lookup"><span data-stu-id="8caf2-107">Implement dialogs</span></span>

1. <span data-ttu-id="8caf2-108">Erstellen Sie im Stamm des Projekts mit dem Namen **Dialogfelder** ein neues Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="8caf2-108">Create a new directory in the root of the project named **Dialogs**.</span></span> <span data-ttu-id="8caf2-109">Erstellen Sie eine neue Datei im **./Dialogs** -Verzeichnis mit dem Namen **LogoutDialog.cs** , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="8caf2-109">Create a new file in the **./Dialogs** directory named **LogoutDialog.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/LogoutDialog.cs" id="LogoutDialogSnippet":::

    <span data-ttu-id="8caf2-110">Dieses Dialogfeld stellt eine Basisklasse für alle anderen Dialogfelder im bot zur Verfügung, von denen abgeleitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="8caf2-110">This dialog provides a base class for all of the other dialogs in the bot to derive from.</span></span> <span data-ttu-id="8caf2-111">Dadurch kann sich der Benutzer unabhängig davon, wo er sich in den Dialogfeldern des bot befindet, abmelden.</span><span class="sxs-lookup"><span data-stu-id="8caf2-111">This allows the user to log out no matter where they are in the bot's dialogs.</span></span>

1. <span data-ttu-id="8caf2-112">Erstellen Sie eine neue Datei im **./Dialogs** -Verzeichnis mit dem Namen **MainDialog.cs** , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="8caf2-112">Create a new file in the **./Dialogs** directory named **MainDialog.cs** and add the following code.</span></span>

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

    <span data-ttu-id="8caf2-113">Nehmen Sie sich einen Moment Zeit, diesen Code zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="8caf2-113">Take a moment to review this code.</span></span>

    - <span data-ttu-id="8caf2-114">Im Konstruktor wird ein [WaterfallDialog](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-waterfall-dialogs?view=azure-bot-service-4.0) mit einer Reihe von Schritten eingerichtet, die in der richtigen Reihenfolge ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="8caf2-114">In the constructor, it sets up a [WaterfallDialog](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-waterfall-dialogs?view=azure-bot-service-4.0) with a set of steps that occur in order.</span></span>
        - <span data-ttu-id="8caf2-115">In `LoginPromptStepAsync` wird ein **OAuthPrompt** gesendet.</span><span class="sxs-lookup"><span data-stu-id="8caf2-115">In `LoginPromptStepAsync` it sends an **OAuthPrompt**.</span></span> <span data-ttu-id="8caf2-116">Wenn der Benutzer nicht angemeldet ist, wird der Benutzer eine Benutzeroberflächen Ansage senden.</span><span class="sxs-lookup"><span data-stu-id="8caf2-116">If the user isn't logged in, this will send a UI prompt to the user.</span></span>
        - <span data-ttu-id="8caf2-117">In `ProcessLoginStepAsync` IT wird überprüft, ob die Anmeldung erfolgreich war, und es wird eine Bestätigung gesendet.</span><span class="sxs-lookup"><span data-stu-id="8caf2-117">In `ProcessLoginStepAsync` it checks if the login was successful and sends a confirmation.</span></span>
        - <span data-ttu-id="8caf2-118">In `PromptUserStepAsync` wird ein **ChoicePrompt** mit den verfügbaren Befehlen gesendet.</span><span class="sxs-lookup"><span data-stu-id="8caf2-118">In `PromptUserStepAsync` it sends a **ChoicePrompt** with the available commands.</span></span>
        - <span data-ttu-id="8caf2-119">`CommandStepAsync`Dadurch wird die Auswahl des Benutzers gespeichert, und dann wird ein **OAuthPrompt** erneut gesendet.</span><span class="sxs-lookup"><span data-stu-id="8caf2-119">In `CommandStepAsync` it saves the user's choice, then resends an **OAuthPrompt**.</span></span>
        - <span data-ttu-id="8caf2-120">In `ProcessStepAsync` wird basierend auf dem empfangenen Befehl eine Aktion durch.</span><span class="sxs-lookup"><span data-stu-id="8caf2-120">In `ProcessStepAsync` it takes action based on the command received.</span></span>
        - <span data-ttu-id="8caf2-121">In `ReturnToPromptStepAsync` beginnt der Wasserfall, übergibt jedoch eine Kennzeichnung, um die anfängliche Benutzeranmeldung zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="8caf2-121">In `ReturnToPromptStepAsync` it starts the waterfall over, but passes a flag to skip the initial user login.</span></span>

## <a name="update-calendarbot"></a><span data-ttu-id="8caf2-122">CalendarBot aktualisieren</span><span class="sxs-lookup"><span data-stu-id="8caf2-122">Update CalendarBot</span></span>

<span data-ttu-id="8caf2-123">Der nächste Schritt besteht darin, **CalendarBot** so zu aktualisieren, dass diese neuen Dialogfelder verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8caf2-123">The next step is to update **CalendarBot** to use these new dialogs.</span></span>

1. <span data-ttu-id="8caf2-124">Öffnen Sie **./Bots/CalendarBot.cs** , und ersetzen Sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="8caf2-124">Open **./Bots/CalendarBot.cs** and replace its entire contents with the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Bots/CalendarBot.cs" id="CalendarBotSnippet":::

    <span data-ttu-id="8caf2-125">Im folgenden finden Sie eine kurze Zusammenfassung der Änderungen.</span><span class="sxs-lookup"><span data-stu-id="8caf2-125">Here's a brief summary of the changes.</span></span>

    - <span data-ttu-id="8caf2-126">Die **CalendarBot** -Klasse wurde in eine Vorlagenklasse geändert, wobei ein **Dialog Feld** empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="8caf2-126">Changed the **CalendarBot** class to be a template class, receiving a **Dialog**.</span></span>
    - <span data-ttu-id="8caf2-127">Die **CalendarBot** -Klasse wurde so geändert, dass Sie **TeamsActivityHandler** erweitert, sodass Sie sich in Microsoft Teams anmelden kann.</span><span class="sxs-lookup"><span data-stu-id="8caf2-127">Changed the **CalendarBot** class to extend **TeamsActivityHandler**, allowing it to sign in in Microsoft Teams.</span></span>
    - <span data-ttu-id="8caf2-128">Zusätzliche Methodenüberschreibungen zur Aktivierung der Authentifizierung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8caf2-128">Added additional method overrides to enable authentication.</span></span>

## <a name="update-startupcs"></a><span data-ttu-id="8caf2-129">Startup.cs aktualisieren</span><span class="sxs-lookup"><span data-stu-id="8caf2-129">Update Startup.cs</span></span>

<span data-ttu-id="8caf2-130">Der letzte Schritt besteht darin, die `ConfigureServices` Methode so zu aktualisieren, dass die für die Authentifizierung erforderlichen Dienste und das neue Dialogfeld hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="8caf2-130">The final step is to update the `ConfigureServices` method to add the services needed for authentication and the new dialog.</span></span>

1. <span data-ttu-id="8caf2-131">Öffnen Sie **./Startup.cs** , und entfernen Sie die `services.AddTransient<IBot, Bots.CalendarBot>();` Verbindung aus der `ConfigureServices` -Methode.</span><span class="sxs-lookup"><span data-stu-id="8caf2-131">Open **./Startup.cs** and remove the `services.AddTransient<IBot, Bots.CalendarBot>();` line from the `ConfigureServices` method.</span></span>

1. <span data-ttu-id="8caf2-132">Fügen Sie am Ende der Methode den folgenden Code ein `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8caf2-132">Insert the following code at the end of the `ConfigureServices` method.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="ConfigureServiceSnippet":::

## <a name="test-authentication"></a><span data-ttu-id="8caf2-133">Testen der Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="8caf2-133">Test authentication</span></span>

1. <span data-ttu-id="8caf2-134">Speichern Sie alle Änderungen, und starten Sie den bot mit `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="8caf2-134">Save all of your changes and start the bot with `dotnet run`.</span></span>

1. <span data-ttu-id="8caf2-135">Öffnen Sie den bot Framework-Emulator.</span><span class="sxs-lookup"><span data-stu-id="8caf2-135">Open the Bot Framework Emulator.</span></span> <span data-ttu-id="8caf2-136">Wählen Sie das Menü **Datei** und dann **neue bot-Konfiguration...** aus.</span><span class="sxs-lookup"><span data-stu-id="8caf2-136">Select the **File** menu, then **New Bot Configuration...**.</span></span>

1. <span data-ttu-id="8caf2-137">Füllen Sie die Felder wie folgt aus.</span><span class="sxs-lookup"><span data-stu-id="8caf2-137">Fill in the fields as follows.</span></span>

    - <span data-ttu-id="8caf2-138">**Name des bot:**`CalendarBot`</span><span class="sxs-lookup"><span data-stu-id="8caf2-138">**Bot name:** `CalendarBot`</span></span>
    - <span data-ttu-id="8caf2-139">**Endpunkt-URL:**`https://localhost:3978/api/messages`</span><span class="sxs-lookup"><span data-stu-id="8caf2-139">**Endpoint URL:** `https://localhost:3978/api/messages`</span></span>
    - <span data-ttu-id="8caf2-140">**Microsoft App-ID:** die Anwendungs-ID Ihrer **Graph Calendar bot** App-Registrierung</span><span class="sxs-lookup"><span data-stu-id="8caf2-140">**Microsoft App ID:** the application ID of your **Graph Calendar Bot** app registration</span></span>
    - <span data-ttu-id="8caf2-141">**Microsoft-App-Kennwort:** Ihr **grafischer Kalender bot** -Client Geheimnis</span><span class="sxs-lookup"><span data-stu-id="8caf2-141">**Microsoft App password:** your **Graph Calendar Bot** client secret</span></span>
    - <span data-ttu-id="8caf2-142">**In ihrer bot-Konfiguration gespeicherte Schlüssel verschlüsseln:** Aktiviert</span><span class="sxs-lookup"><span data-stu-id="8caf2-142">**Encrypt keys stored in your bot configuration:** Enabled</span></span>

    ![Screenshot des Dialogfelds "neue bot-Konfiguration"](images/new-bot-config.png)

1. <span data-ttu-id="8caf2-144">Wählen Sie **Speichern und verbinden** aus.</span><span class="sxs-lookup"><span data-stu-id="8caf2-144">Select **Save and connect**.</span></span> <span data-ttu-id="8caf2-145">Nachdem der Emulator eine Verbindung hergestellt hat, sollte Folgendes angezeigt werden: `Welcome to Microsoft Graph CalendarBot. Type anything to get started.`</span><span class="sxs-lookup"><span data-stu-id="8caf2-145">After the emulator connects, you should see `Welcome to Microsoft Graph CalendarBot. Type anything to get started.`</span></span>

1. <span data-ttu-id="8caf2-146">Geben Sie Text ein, und senden Sie ihn an den bot.</span><span class="sxs-lookup"><span data-stu-id="8caf2-146">Type some text and send it to the bot.</span></span> <span data-ttu-id="8caf2-147">Der bot antwortet mit einer Anmeldeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="8caf2-147">The bot responds with a login prompt.</span></span>

1. <span data-ttu-id="8caf2-148">Klicken Sie auf die Schaltfläche **Anmelden** .</span><span class="sxs-lookup"><span data-stu-id="8caf2-148">Select the **Login** button.</span></span> <span data-ttu-id="8caf2-149">Der Emulator fordert Sie auf, die URL zu bestätigen, die mit beginnt `oauthlink://https://token.botframeworkcom` .</span><span class="sxs-lookup"><span data-stu-id="8caf2-149">The emulator prompts you to confirm the URL that starts with `oauthlink://https://token.botframeworkcom`.</span></span> <span data-ttu-id="8caf2-150">Wählen Sie **bestätigen** aus, um den Vorgang fortzusetzen.</span><span class="sxs-lookup"><span data-stu-id="8caf2-150">Select **Confirm** to continue.</span></span>

1. <span data-ttu-id="8caf2-151">Melden Sie sich im Popupfenster mit Ihrem Microsoft 365-Konto an.</span><span class="sxs-lookup"><span data-stu-id="8caf2-151">In the pop-up window, login with your Microsoft 365 account.</span></span> <span data-ttu-id="8caf2-152">Überprüfen Sie die angeforderten Berechtigungen und akzeptieren Sie.</span><span class="sxs-lookup"><span data-stu-id="8caf2-152">Review the requested permissions and accept.</span></span>

1. <span data-ttu-id="8caf2-153">Sobald die Authentifizierung und Zustimmung abgeschlossen ist, enthält das Popupfenster einen Validierungscode.</span><span class="sxs-lookup"><span data-stu-id="8caf2-153">Once authentication and consent are complete, the pop-up window provides a validation code.</span></span> <span data-ttu-id="8caf2-154">Kopieren Sie den Code, und schließen Sie das Fenster.</span><span class="sxs-lookup"><span data-stu-id="8caf2-154">Copy the code and close the window.</span></span>

    ![Ein Screenshot des bot-Framework-Emulator-Validierungscodes](images/validation-code.png)

1. <span data-ttu-id="8caf2-156">Geben Sie den Überprüfungscode im Chatfenster ein, um die Anmeldung abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="8caf2-156">Enter the validation code in the chat window to complete the login.</span></span>

    ![Ein Screenshot der Anmelde Unterhaltung mit dem Beispiel-bot](images/bot-login.png)

1. <span data-ttu-id="8caf2-158">Wenn Sie die Schaltfläche " **Token anzeigen** " (oder "Typ" `show token` ) auswählen, zeigt der bot das Zugriffstoken an.</span><span class="sxs-lookup"><span data-stu-id="8caf2-158">If you select the **Show token** button (or type `show token`), the bot displays the access token.</span></span> <span data-ttu-id="8caf2-159">Die Schaltfläche **Abmelden** (oder eingeben `log out` ) wird Sie abmelden.</span><span class="sxs-lookup"><span data-stu-id="8caf2-159">The **Log out** button (or typing `log out`) will log you out.</span></span>

> [!TIP]
> <span data-ttu-id="8caf2-160">Möglicherweise wird die folgende Fehlermeldung im bot Framework-Emulator angezeigt, wenn eine Unterhaltung mit dem bot gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="8caf2-160">You may receive the following error message in the Bot Framework Emulator when starting a conversation with the bot.</span></span>
>
> ```text
> Failed to generate an actual sign-in link: Error: Failed to connect to ngrok instance for OAuth postback URL:
> FetchError: request to http://127.0.0.1:4041/api/tunnels failed, reason: connect ECONNREFUSED 127.0.0.1:4041
> ```
>
> <span data-ttu-id="8caf2-161">Schließen Sie in diesem Fall den Emulator, und starten Sie ihn neu.</span><span class="sxs-lookup"><span data-stu-id="8caf2-161">If this happens, close the emulator and restart it.</span></span>
