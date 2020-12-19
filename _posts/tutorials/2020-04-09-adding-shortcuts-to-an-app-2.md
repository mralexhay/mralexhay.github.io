---
layout: post
title: Adding Shortcuts To An App - Part Two
description: How to add a shortcut action with parameters that accept multiple values, custom file types, can be shown conditionally or present values from enums and calculated lists.
category: tutorials
summary: How to add a shortcut action with parameters that accept multiple values, custom file types, can be shown conditionally or present values from enums and calculated lists.
---

In the [last post]({% post_url /tutorials/2020-04-04-adding-shortcuts-to-an-app-1 %}) we looked at starting from scratch with a blank Xcode project and adding a basic Shortcuts action that made text uppercase.

In this post we’re going to dive deeper and look at different types of parameters including handling arrays, enums, nested parameters and calculated options.

We’re going to be creating a new Shortcuts action that will accept multiple files of any type and rename them with a formatted date (either prepended or appended). We’ll also optionally offer to change the case and then output the files back into Shortcuts.

This project will be updated on [GitHub](https://github.com/mralexhay/ShortcutsExample) with each post.

Let’s get started!    

---

## Step One

Navigate to  the **Intents.intentdefinition** file, hit the **+** and let’s add a new intent called **RenameFiles**.

![Screenshot 1](/assets/tut2_1.png)

## Step Two

Add a description for the intent and add our first parameter. We’ll call it **files**.

Set the type to **File** and since we want to be able to rename multiple files at once, tick **Supports multiple values**.

Under file type, we could leave it on Text or Image if we only wanted to support those types of files but we want to accept any files so we’ll select **Custom**.

Now we can define the UTIs [(uniform type identifiers](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/UniformTypeIdentifier.html)) of files we want to accept in our Files parameter.

UTIs are strings that Apple use to identify file types. If we wanted to allow only PDFs, we could add **com.adobe.pdf** or if we want to broadly support movie files we could use **public.movie**.

![Screenshot 2](/assets/tut2_2.png)

We want to support all file types so we’ll use the base type that encompasses all other UTIs: **public.item**.

Now let’s add a prompt and a validation error and untick **Intent is eligible for Siri Suggestions** as per the previous post.

![Screenshot 3](/assets/tut2_3.png)

## Step Three

Next we’ll add a new parameter called **dateFormat**. This will be a pre-populated list of calculated values so we’ll tick **Valid values are provided dynamically** and add a validation error code.

![Screenshot 4](/assets/tut2_4.png)

## Step Four

We’re going to give the user the option to change the case of the title when renaming. Let’s add a new Boolean parameter called **changeCase**. Add a prompt and check the default value is **false**.

![Screenshot 5](/assets/tut2_5.png)

## Step Five

If the user elects to change the case then we want to give them the option of either uppercase or lowercase. 

To do that, we’re going to add a new parameter called **newCase** that will be only be shown when our **changeCase** paratmeter is set to **true**. 

Under **type** select **Add new enum…**. We’ll call our enum **RenameCase** and set the display name to **Case**. This will show in the title of the window when the user is choosing which case. Add **lowercase** and **uppercase** as our options.

![Screenshot 6](/assets/tut2_6.png)

## Step Six
Head back to our Intent and notice the type is now **Case**.  We can now set a default value to **uppercase**.

To make sure our new parameter only shows when **changeCase** is **true**, we’ll select **parent parameter**, **has exact value** and **true**.

![Screenshot 7](/assets/tut2_7.png)

It’s worth noting you can only have *one level* of nesting parent/child parameters. We couldn’t now have a new parameter that only shows if the **“newCase”** is **“uppercase”**, for example.

---

## Step Seven

We’ll add a final parameter which allows the user to choose where the date is positioned in the new filename. We’ll call the parameter **position** and set the type to a new **RenamePosition** enum, containing **prepend** and **append**.

We’ll capitalise the display names since these are going to be the first words in our action’s summary.

Set the default value to **Prepend**.

![Screenshot 8](/assets/tut2_8.png)

## Step Eight

Set the input and key parameters to **files** since the previous action is likely to be providing the files to rename to this one, so it makes sense to automatically populate the parameter.

In the intent, notice our summary has two supported combinations. This is because we have a parent parameter. We can now choose different summaries depending on whether **changeCase** toggled on.

We’ll use the same description for both summaries. We’ll leave the **changeCase** and **case** parameters out of the summary since they’re optional. They’ll show in the Shortcut action under  a **show more** twirl-down.

![Screenshot 9](/assets/tut2_9.gif)
![Screenshot 10](/assets/tut2_10.gif){:.small-img}

## Step Nine

Now we’ll add a response. For our result make sure the type is File since that’s what we’re outputting. 

We’ll set the display name to **Renamed Files**, tick **Supports multiple values** since we’ll be outputting more than one and select **result** in the output.

![Screenshot 11](/assets/tut2_11.png)

Add an error property in case we need to show any errors to the user. 

![Screenshot 12](/assets/tut2_12.png)

## Step Ten

Now let’s write our intent handler. Create a new Swift file called **renameFilesHandler.swift**. I’ve put it in a group folder called **Intents** to keep them together.

Define our new **RenameFilesIntentHandler** class that inherits from NSObject and conforms to the automatically created **RenameFilesIntentHandling** protocol. Xcode can auto-populate our protocol stubs - we have a function to validate each parameter, one to provide options for our **provideDate** drop-down list and one to handle the intent.

![Screenshot 13](/assets/tut2_13.png)

## Step Eleven

When validating a parameter that accepts multiple values, we need to return an array of resolution results. 

In this code we’re creating an empty array of **RenameFilesFilesResolutionResult**.

If our **Files** parameter is empty then we return a single **unsupported** resolution result in our array which will show the error we defined.

If there *are* input files, we append a successful resolution result to the array for each one.

Note that files in Intents are described by objects called **INFiles**.

```swift
func resolveFiles(for intent: RenameFilesIntent, with completion: @escaping ([RenameFilesFilesResolutionResult]) -> Void) {
    var resultArray = [RenameFilesFilesResolutionResult]()
    let files = intent.files ?? []
    if files.isEmpty {
        resultArray.append(RenameFilesFilesResolutionResult.unsupported(forReason: .noFiles))
    } else {
        for file in files {
            resultArray.append(RenameFilesFilesResolutionResult.success(with: file))
        }
    }
    completion(resultArray)
}
```

---

## Step Twelve

We’ll provide options for our **dateFormat** parameter by creating an array of strings with today’s date in three different formats (eg: *2020-04-01, 2020-04 & 2020*).

We’ll then validate that the user has picked one of the options or we’ll show an error.

```swift
func provideDateFormatOptions(for intent: RenameFilesIntent, with completion: @escaping ([String]?, Error?) -> Void) {
    let dateFormatter = DateFormatter()
    dateFormatter.locale = Locale.current
    dateFormatter.calendar = Calendar.current
    dateFormatter.dateFormat = “yyyy-MM-dd”

    let fullDate = dateFormatter.string(from: Date())
    let yearsAndMonths = String(fullDate.dropLast(3))
    let yearOnly = String(fullDate.dropLast(6))
    
    let optionsArray: [String] = [fullDate, yearsAndMonths, yearOnly]
    
    completion(optionsArray, nil)
 }

func resolveDateFormat(for intent: RenameFilesIntent, with completion: @escaping (RenameFilesDateFormatResolutionResult) -> Void) {
    if let dateFormat = intent.dateFormat {
        completion(RenameFilesDateFormatResolutionResult.success(with: dateFormat))
    } else {
        completion(RenameFilesDateFormatResolutionResult.unsupported(forReason: .empty))
    }
}
```

---

## Step Thirteen

It’s easy to resolve our non-Optional enum values.

```swift
func resolveNewCase(for intent: RenameFilesIntent, with completion: @escaping (RenameCaseResolutionResult) -> Void) {
    let newCase = intent.newCase
    completion(RenameCaseResolutionResult.success(with: newCase))
}

func resolvePosition(for intent: RenameFilesIntent, with completion: @escaping (RenamePositionResolutionResult) -> Void) {
    let position = intent.position
    completion(RenamePositionResolutionResult.success(with: position))
}
```

---

## Step Fourteen

With our **changeCase** Bool parameter, we’ll default to **false** if the input is ambiguous.

```swift
func resolveChangeCase(for intent: RenameFilesIntent, with completion: @escaping (INBooleanResolutionResult) -> Void) {
    let changeCase = intent.changeCase?.boolValue ?? false
    completion(INBooleanResolutionResult.success(with: changeCase))
}
```

---

## Step Fifteen

Now we’ll write the code to handle the main logic intent: renaming the files and outputting the results. 

We’ll use all of our validated parameters to return an array of identical **INFiles** with new names, or we’ll display an error if there’s a problem. 

```swift
func handle(intent: RenameFilesIntent, completion: @escaping (RenameFilesIntentResponse) -> Void) {
    let files = intent.files ?? []
    let position = intent.position
    let changeCase = intent.changeCase?.boolValue ?? false
    guard let dateFormat = intent.dateFormat else {
        completion(RenameFilesIntentResponse.failure(error: “Please choose a valid date format”))
        return
    }
    
    var outputArray = [INFile]()
    
    for file in files {
        var newName = file.filename
        
        if changeCase {
            let newCase = intent.newCase
            switch newCase {
            case .lowercase:
                newName = newName.lowercased()
            case .uppercase:
                newName = newName.uppercased()
            default:
                completion(RenameFilesIntentResponse.failure(error: “An invalid case was selected”))
                return
            }
        }

        switch position {
        case .append:
            guard let fileURL = file.fileURL else {
                completion(RenameFilesIntentResponse.failure(error: “Couldn’t get file URL of \(file.filename)”))
                return
            }
            let filePath = fileURL.deletingPathExtension().lastPathComponent
            let nameNoExt = FileManager.default.displayName(atPath: filePath)
            let ext = fileURL.pathExtension
            newName = “\(nameNoExt)_\(dateFormat).\(ext)”
        case .prepend:
            newName = “\(dateFormat)_\(newName)”
        default:
            completion(RenameFilesIntentResponse.failure(error: “An invalid position was selected”))
            return
        }
        
        let renamedFile = INFile(data: file.data, filename: newName, typeIdentifier: file.typeIdentifier)
        outputArray.append(renamedFile)
    }
    completion(RenameFilesIntentResponse.success(result: outputArray))
}
```

Here’s the complete, commented intent handler:

```swift
import Intents

class RenameFilesIntentHandler: NSObject, RenameFilesIntentHandling {
    
    func resolveFiles(for intent: RenameFilesIntent, with completion: @escaping ([RenameFilesFilesResolutionResult]) -> Void) {
        // For paramters that accept multiple files, we need to pass an array of Resolution Results to the completion handler
        var resultArray = [RenameFilesFilesResolutionResult]()
        let files = intent.files ?? []
        if files.isEmpty {
            resultArray.append(RenameFilesFilesResolutionResult.unsupported(forReason: .noFiles))
        } else {
            for file in files {
                resultArray.append(RenameFilesFilesResolutionResult.success(with: file))
            }
        }
        completion(resultArray)
    }
    
    // this function will provide the drop-down list of options to choose from when tapping the “Date Format parameter in Shortcuts”
    func provideDateFormatOptions(for intent: RenameFilesIntent, with completion: @escaping ([String]?, Error?) -> Void) {
        let dateFormatter = DateFormatter()
        dateFormatter.locale = Locale.current
        dateFormatter.calendar = Calendar.current
        dateFormatter.dateFormat = “yyyy-MM-dd”

        let fullDate = dateFormatter.string(from: Date())
        let yearsAndMonths = String(fullDate.dropLast(3))
        let yearOnly = String(fullDate.dropLast(6))
        
        let optionsArray: [String] = [fullDate, yearsAndMonths, yearOnly]
        
        completion(optionsArray, nil)
     }

    func resolveDateFormat(for intent: RenameFilesIntent, with completion: @escaping (RenameFilesDateFormatResolutionResult) -> Void) {
        if let dateFormat = intent.dateFormat {
            completion(RenameFilesDateFormatResolutionResult.success(with: dateFormat))
        } else {
            completion(RenameFilesDateFormatResolutionResult.unsupported(forReason: .empty))
        }
    }
    
    func resolveNewCase(for intent: RenameFilesIntent, with completion: @escaping (RenameCaseResolutionResult) -> Void) {
        let newCase = intent.newCase
        completion(RenameCaseResolutionResult.success(with: newCase))
    }

    func resolvePosition(for intent: RenameFilesIntent, with completion: @escaping (RenamePositionResolutionResult) -> Void) {
        let position = intent.position
        completion(RenamePositionResolutionResult.success(with: position))
    }
    
    func resolveChangeCase(for intent: RenameFilesIntent, with completion: @escaping (INBooleanResolutionResult) -> Void) {
        let changeCase = intent.changeCase?.boolValue ?? false
        completion(INBooleanResolutionResult.success(with: changeCase))
    }
    
    func handle(intent: RenameFilesIntent, completion: @escaping (RenameFilesIntentResponse) -> Void) {
        let files = intent.files ?? []
        let position = intent.position
        let changeCase = intent.changeCase?.boolValue ?? false
        guard let dateFormat = intent.dateFormat else {
            // We can display errors to the user when problems occur
            completion(RenameFilesIntentResponse.failure(error: “Please choose a valid date format”))
            return
        }
        
        // The intent response expects an array of INFiles
        var outputArray = [INFile]()
        
        for file in files {
            var newName = file.filename
            
            // change the case of the filename if selected
            if changeCase {
                let newCase = intent.newCase
                switch newCase {
                case .lowercase:
                    newName = newName.lowercased()
                case .uppercase:
                    newName = newName.uppercased()
                default:
                    completion(RenameFilesIntentResponse.failure(error: “An invalid case was selected”))
                    return
                }
            }
            
            // append or prepend the selected date value
            switch position {
            case .append:
                // if appending the date, we need to split the extension from the name first
                guard let fileURL = file.fileURL else {
                    completion(RenameFilesIntentResponse.failure(error: “Couldn’t get file URL of \(file.filename)”))
                    return
                }
                let filePath = fileURL.deletingPathExtension().lastPathComponent
                let nameNoExt = FileManager.default.displayName(atPath: filePath)
                let ext = fileURL.pathExtension
                newName = “\(nameNoExt)_\(dateFormat).\(ext)”
            case .prepend:
                newName = “\(dateFormat)_\(newName)”
            default:
                // We’ll show an error if for some reason one of our enum values hasn’t been selected
                completion(RenameFilesIntentResponse.failure(error: “An invalid position was selected”))
                return
            }
            
            // construct a new INFile with identical data and type identifier and the new file name
            let renamedFile = INFile(data: file.data, filename: newName, typeIdentifier: file.typeIdentifier)
            outputArray.append(renamedFile)
        }
        completion(RenameFilesIntentResponse.success(result: outputArray))
    }
}
```

---

## Step Sixteen

Now let’s make sure our **IntentHandler** is called when the shortcut is run.

In **IntentHandler.swift** we’ll add a new case for **RenameFilesIntent** to our switch statement:

```swift
import Intents

class IntentHandler: INExtension {
    
    // When shortcuts are run, the relevant intent handler should to be returned
    override func handler(for intent: INIntent) -> Any {
        switch intent {
        case is MakeUppercaseIntent:
            return MakeUppercaseIntentHandler()
        case is RenameFilesIntent:
            return RenameFilesIntentHandler()
        default:
            // No intents should be unhandled so we’ll throw an error by default
            fatalError(“No handler for this intent”)
        }
    }
}
```

---

## Step Seventeen

Let’s build and run, jump into the Shortcuts app and try our new action out!

![Screenshot 13](/assets/tut2_13.png)

## Summary
In this post we’ve looked at parameters that:
* accept multiple values
* accept custom file types 
* are enums
* are conditional with a child/parent relationship 
* provide a calculated list at run time

In the next post we’ll be looking at in-app intent handling, the visual list API, outputting custom types and supporting iOS 14's new SwiftUI App protocol.

---

These are the other posts in the series:

* [Part 1]({% post_url /tutorials/2020-04-04-adding-shortcuts-to-an-app-1 %}): _Creating a project and adding the first action_
* Part 2: _Exploring parameters: arrays, enums, calculated lists and files_
* [Part 3]({% post_url /tutorials/2020-10-21-adding-shortcuts-to-an-app-3 %}): _In-app intent handling, custom output types, visual list API and using the SwiftUI app protocol_
* [Part 4]({% post_url /tutorials/2020-10-23-adding-shortcuts-to-an-app-4 %}): _Visual Lists in parameters and pushing data from Shortcuts into a SwiftUI view_
 
The complete code for the tutorials is also on GitHub:
* [Parts 1 & 2](https://github.com/mralexhay/ShortcutsExample) (iOS 13)
* [Parts 3 & 4](https://github.com/mralexhay/Shortcuts-Example-iOS14) (iOS 14)






