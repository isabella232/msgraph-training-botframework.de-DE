---
ms.openlocfilehash: dc5816eb653053d63bfe3bf48413b3557583d109
ms.sourcegitcommit: e0d9b18d2d4cbeb4a48890f3420a47e6a90abc53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "49347787"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="6ffa2-101">In diesem Abschnitt verwenden Sie das Microsoft Graph-SDK, um dem Kalender des Benutzers ein Ereignis hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-101">In this section you'll use the Microsoft Graph SDK to add an event to the user's calendar.</span></span>

## <a name="implement-a-dialog"></a><span data-ttu-id="6ffa2-102">Implementieren eines Dialogs</span><span class="sxs-lookup"><span data-stu-id="6ffa2-102">Implement a dialog</span></span>

<span data-ttu-id="6ffa2-103">Erstellen Sie zunächst ein neues benutzerdefiniertes Dialogfeld, in dem der Benutzer aufgefordert wird, die zum Hinzufügen eines Ereignisses zum Kalender erforderlichen Werte einzugeben.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-103">Start by creating a new custom dialog to prompt the user for the values needed to add an event to their calendar.</span></span> <span data-ttu-id="6ffa2-104">In diesem Dialogfeld wird ein **WaterfallDialog** -Objekt verwendet, um die folgenden Schritte auszuführen.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-104">This dialog will use a **WaterfallDialog** to do the following steps.</span></span>

- <span data-ttu-id="6ffa2-105">Eingabeaufforderung für einen Betreff</span><span class="sxs-lookup"><span data-stu-id="6ffa2-105">Prompt for a subject</span></span>
- <span data-ttu-id="6ffa2-106">Fragen, ob der Benutzer Personen einladen möchte</span><span class="sxs-lookup"><span data-stu-id="6ffa2-106">Ask if the user wants to invite people</span></span>
- <span data-ttu-id="6ffa2-107">Aufforderung für Teilnehmer (wenn der Benutzer "Ja" zu vorherigem Schritt sagte)</span><span class="sxs-lookup"><span data-stu-id="6ffa2-107">Prompt for attendees (if user said yes to previous step)</span></span>
- <span data-ttu-id="6ffa2-108">Eingabeaufforderung für Startdatum und-Uhrzeit</span><span class="sxs-lookup"><span data-stu-id="6ffa2-108">Prompt for a start date and time</span></span>
- <span data-ttu-id="6ffa2-109">Auffordern eines Enddatums und einer Endzeit</span><span class="sxs-lookup"><span data-stu-id="6ffa2-109">Prompt for an end date and time</span></span>
- <span data-ttu-id="6ffa2-110">Alle gesammelten Werte anzeigen und Benutzer zum bestätigen bitten</span><span class="sxs-lookup"><span data-stu-id="6ffa2-110">Display all of the collected values and ask user to confirm</span></span>
- <span data-ttu-id="6ffa2-111">Wenn der Benutzer bestätigt, rufen Sie das Zugriffstoken von **OAuthPrompt** ab.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-111">If user confirms, get access token from **OAuthPrompt**</span></span>
- <span data-ttu-id="6ffa2-112">Erstellen des Ereignisses</span><span class="sxs-lookup"><span data-stu-id="6ffa2-112">Create the event</span></span>

1. <span data-ttu-id="6ffa2-113">Erstellen Sie eine neue Datei im **./Dialogs** -Verzeichnis mit dem Namen **NewEventDialog.cs** , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-113">Create a new file in the **./Dialogs** directory named **NewEventDialog.cs** and add the following code.</span></span>

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

    <span data-ttu-id="6ffa2-114">Dies ist die Shell eines neuen Dialogs, mit der der Benutzer aufgefordert wird, die zum Hinzufügen eines Ereignisses zum Kalender erforderlichen Werte einzugeben.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-114">This is the shell of a new dialog that will prompt the user for the values needed to add an event to their calendar.</span></span>

1. <span data-ttu-id="6ffa2-115">Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um den Benutzer zu einem Betreff aufzufordern.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-115">Add the following function to the **NewEventDialog** class to prompt the user for a subject.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForSubjectSnippet":::

1. <span data-ttu-id="6ffa2-116">Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um den Betreff zu speichern, den der Benutzer im vorherigen Schritt gegeben hat, und um zu Fragen, ob Teilnehmer hinzugefügt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-116">Add the following function to the **NewEventDialog** class to store the subject the user gave in the previous step, and to ask if they want to add attendees.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAddAttendeesSnippet":::

1. <span data-ttu-id="6ffa2-117">Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um die Antwort des Benutzers aus dem vorherigen Schritt zu überprüfen und den Benutzer bei Bedarf zur Eingabe einer Liste von Teilnehmern aufzufordern.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-117">Add the following function to the **NewEventDialog** class to check the user's response from the previous step and prompt the user for a list of attendees if needed.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAttendeesSnippet":::

1. <span data-ttu-id="6ffa2-118">Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um die Liste der Teilnehmer aus dem vorherigen Schritt (sofern vorhanden) zu speichern und den Benutzer nach einem Startdatum und einer Startzeit aufzufordern.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-118">Add the following function to the **NewEventDialog** class to store the list of attendees from the previous step (if present) and prompt the user for a start date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForStartSnippet":::

1. <span data-ttu-id="6ffa2-119">Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um den Startwert aus dem vorherigen Schritt zu speichern und den Benutzer nach einem Enddatum und einer Endzeit aufzufordern.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-119">Add the following function to the **NewEventDialog** class to store the start value from the previous step and prompt the user for an end date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForEndSnippet":::

1. <span data-ttu-id="6ffa2-120">Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um den End-Wert aus dem vorherigen Schritt zu speichern, und fordern Sie den Benutzer auf, alle Eingaben zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-120">Add the following function to the **NewEventDialog** class to store the end value from the previous step and ask the user to confirm all of the inputs.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConfirmNewEventSnippet":::

1. <span data-ttu-id="6ffa2-121">Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um die Antwort des Benutzers im vorherigen Schritt zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-121">Add the following function to the **NewEventDialog** class to check the user's response from the previous step.</span></span> <span data-ttu-id="6ffa2-122">Wenn der Benutzer die Eingaben bestätigt, verwenden Sie die **OAuthPrompt** -Klasse, um ein Zugriffstoken abzurufen.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-122">If the user confirms the inputs, use the **OAuthPrompt** class to get an access token.</span></span> <span data-ttu-id="6ffa2-123">Beenden Sie andernfalls das Dialogfeld.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-123">Otherwise, end the dialog.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="GetTokenSnippet":::

1. <span data-ttu-id="6ffa2-124">Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um das Microsoft Graph-SDK zum Erstellen des neuen Ereignisses zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-124">Add the following function to the **NewEventDialog** class to use the Microsoft Graph SDK to create the new event.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AddEventSnippet":::

    <span data-ttu-id="6ffa2-125">Überprüfen Sie die Funktionsweise dieses Codes.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-125">Consider what this code does.</span></span>

    - <span data-ttu-id="6ffa2-126">Es ruft die **Mailbox Settings** des Benutzers ab, um die bevorzugte Zeitzone des Benutzers zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-126">It gets the user's **MailboxSettings** to determine the user's preferred time zone.</span></span>
    - <span data-ttu-id="6ffa2-127">Es wird ein **Event** -Objekt mit den vom Benutzer bereitgestellten Werten erstellt.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-127">It creates an **Event** object using the values provided by the user.</span></span> <span data-ttu-id="6ffa2-128">Beachten Sie, dass die Eigenschaften **Start** und **End** mit der Zeitzone des Benutzers festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-128">Note that the **Start** and **End** properties are set with the user's time zone.</span></span>
    - <span data-ttu-id="6ffa2-129">Das Ereignis wird im Kalender des Benutzers erstellt.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-129">It creates the event on the user's calendar.</span></span>

## <a name="add-validation"></a><span data-ttu-id="6ffa2-130">Validierung hinzufügen</span><span class="sxs-lookup"><span data-stu-id="6ffa2-130">Add validation</span></span>

<span data-ttu-id="6ffa2-131">Fügen Sie nun der Eingabe des Benutzers eine Validierung hinzu, um Fehler beim Erstellen des Ereignisses mit Microsoft Graph zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-131">Now add validation to the user's input to avoid errors when creating the event with Microsoft Graph.</span></span> <span data-ttu-id="6ffa2-132">Wir möchten sicherstellen, dass:</span><span class="sxs-lookup"><span data-stu-id="6ffa2-132">We want to make sure that:</span></span>

- <span data-ttu-id="6ffa2-133">Wenn der Benutzer eine Teilnehmerliste erhält, sollte dies eine durch Semikolons getrennte Liste gültiger e-Mail-Adressen sein.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-133">If the user gives an attendee list, it should be a semicolon-delimited list of valid email addresses.</span></span>
- <span data-ttu-id="6ffa2-134">Das Startdatum/die Uhrzeit sollte ein gültiges Datum und eine gültige Uhrzeit sein.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-134">The start date/time should be a valid date and time.</span></span>
- <span data-ttu-id="6ffa2-135">Das Enddatum/die Uhrzeit sollte ein gültiges Datum und eine gültige Uhrzeit sein und sollte später als der Anfang sein.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-135">The end date/time should be a valid date and time, and should be later than the start.</span></span>

1. <span data-ttu-id="6ffa2-136">Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um den Eintrag des Benutzers für die Teilnehmer zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-136">Add the following function to the **NewEventDialog** class to validate the user's entry for attendees.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AttendeesValidatorSnippet":::

1. <span data-ttu-id="6ffa2-137">Fügen Sie der **NewEventDialog** -Klasse die folgenden Funktionen hinzu, um den Eintrag des Benutzers für Startdatum und-Uhrzeit zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-137">Add the following functions to the **NewEventDialog** class to validate the user's entry for start date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="StartValidatorSnippet":::

1. <span data-ttu-id="6ffa2-138">Fügen Sie die folgende Funktion zur **NewEventDialog** -Klasse hinzu, um den Eintrag des Benutzers für Enddatum und-Uhrzeit zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-138">Add the following function to the **NewEventDialog** class to validate the user's entry for end date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="EndValidatorSnippet":::

## <a name="add-steps-to-waterfalldialog"></a><span data-ttu-id="6ffa2-139">Hinzufügen von Schritten zu WaterfallDialog</span><span class="sxs-lookup"><span data-stu-id="6ffa2-139">Add steps to WaterfallDialog</span></span>

<span data-ttu-id="6ffa2-140">Da Sie nun alle "Schritte" für das Dialogfeld haben, besteht der letzte Schritt darin, Sie zu einem **WaterfallDialog** im Konstruktor hinzuzufügen und dann die **NewEventDialog** zum **MainDialog** hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-140">Now that you have all of the "steps" for the dialog, the last step is to add them to a **WaterfallDialog** in the constructor, then add the **NewEventDialog** to the **MainDialog**.</span></span>

1. <span data-ttu-id="6ffa2-141">Suchen Sie den **NewEventDialog** -Konstruktor, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-141">Locate the **NewEventDialog** constructor and add the following code to it.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConstructorSnippet":::

    <span data-ttu-id="6ffa2-142">Dadurch werden alle verwendeten Dialogfelder hinzugefügt, und alle Funktionen, die Sie als Schritte in der **WaterfallDialog** implementiert haben, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-142">This adds all of the dialogs that are used, and adds all of the functions you implemented as steps in the **WaterfallDialog**.</span></span>

1. <span data-ttu-id="6ffa2-143">Öffnen Sie **/Dialogs/MainDialog.cs** , und fügen Sie dem Konstruktor die folgende Verbindung hinzu.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-143">Open **./Dialogs/MainDialog.cs** and add the following line to the constructor.</span></span>

    ```csharp
    AddDialog(new NewEventDialog(configuration, graphClientService));
    ```

1. <span data-ttu-id="6ffa2-144">Ersetzen Sie den Code innerhalb des `else if (command.StartsWith("add event"))` Blocks in `ProcessStepAsync` durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-144">Replace the code inside the `else if (command.StartsWith("add event"))` block in `ProcessStepAsync` with the following.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="AddEventSnippet" highlight="3":::

1. <span data-ttu-id="6ffa2-145">Speichern Sie alle Änderungen, und starten Sie den bot neu.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-145">Save all of your changes and restart the bot.</span></span>

1. <span data-ttu-id="6ffa2-146">Verwenden Sie den bot Framework-Emulator, um eine Verbindung mit dem bot herzustellen und sich anzumelden.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-146">Use the Bot Framework Emulator to connect to the bot and log in.</span></span> <span data-ttu-id="6ffa2-147">Klicken Sie auf die Schaltfläche **Ereignis hinzufügen** .</span><span class="sxs-lookup"><span data-stu-id="6ffa2-147">Select the **Add event** button.</span></span>

1. <span data-ttu-id="6ffa2-148">Antworten Sie auf die Eingabeaufforderungen, um ein neues Ereignis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-148">Respond to the prompts to create a new event.</span></span> <span data-ttu-id="6ffa2-149">Beachten Sie Folgendes: Wenn Sie zur Eingabe von Start-und Endwerten aufgefordert werden, können Sie Ausdrücke wie "heute um 15.00 Uhr" oder "jetzt" verwenden.</span><span class="sxs-lookup"><span data-stu-id="6ffa2-149">Note that when prompted for start and end values, you can use phrases like "today at 3PM", or "now".</span></span>

    ![Screenshot des Dialogfelds "Ereignis hinzufügen" im bot Framework-Emulator](images/add-event.png)
