# Formatting
### How do we format code?
Most formatting-related guidelines are enforced by [SwiftLint](https://github.com/realm/SwiftLint) using [our documented configuration file](https://github.com/Lickability/swift-best-practices/blob/master/.swiftlint.yml). Xcode’s default behaviors and preferences are preferred for considerations not covered by SwiftLint rules.

### Key Considerations
* Resolve any added SwiftLint warnings prior to opening a pull request with your changes.
* In the rare case in which you need to opt out of a SwiftLint rule, use [swiftlint:disable:this](https://github.com/realm/SwiftLint#disable-rules-in-code) on the lines that require the exception, along with a comment explaining the exception(s).
* Use Xcode’s default indentation preferences (spaces, not tabs, with a tab width of four spaces).
* Use Xcode’s Re-Indent feature (Editor → Structure → Re-Indent, or `⌃I`) to ensure code and documentation is properly indented.
    * There are times in which Xcode indents something in an unfavorable way. These occurrences are fairly rare. Rather than fighting the tools, it is still preferred to use Xcode’s behavior in these cases. For example, when a function takes multiple closures as parameters and capture list is used at the callsite, the closure bodies indent differently from each other:

    ```swift
    // Without capture list:
    updateFeedFromNetwork(networkCompletion: { result in
        // code…
    }, persistenceCompletion: { result in
        // code…
    })

    // With capture list:
    updateFeedFromNetwork(networkCompletion: { [weak self] result in
        // code…
        }, persistenceCompletion: { result in
            // code…
    })
    ```
