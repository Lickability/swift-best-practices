# Lickability Swift Best Practices Guide

Code is written for humans. I’m not the first to say that, and won’t be the last. What you write will eventually be compiled away into something unintelligible, so how you choose to write code isn’t for the computer’s benefit. It’s for yourself, both now and later. It’s for any people working on a team with you. And it’s for anyone that stumbles upon your code after you’re gone.

In order to understand our code better, Lickability employs consistent practices—and to do that, we have a defined structure and style for the way we write code. This style guide is intended to be a living repository that will be updated as the Swift language and our experience evolves. You can click through the links below to read individual sections, or you can read the full document [here](https://github.com/Lickability/swift-best-practices/blob/master/CombinedDocument.md).

If you want to use this, great! If you want to fork it and make changes, go ahead. We won't be accepting issues or pull requests at this time, but we hope that you'll find it our approach to writing software interesting-and if there are aspects that you'd love to chat about, let us know!

## Architecture Info

[Architecture Development Diagram](https://github.com/Lickability/swift-style-guide/blob/master/ArchitectureDiagram.md)

* **M** - [Model](https://github.com/Lickability/swift-style-guide/blob/master/Model.md)
* **V** - [View](https://github.com/Lickability/swift-style-guide/blob/master/View.md)
* **C** - [Controller](https://github.com/Lickability/swift-style-guide/blob/master/Controller.md)
* **VM** - [View Model](https://github.com/Lickability/swift-style-guide/blob/master/ViewModel.md)

### Related Resources
* [Common Controller Types](https://github.com/Lickability/swift-style-guide/blob/master/CommonControllerTypes.md)

## Organizational Info 
* [Assets](https://github.com/Lickability/swift-style-guide/blob/master/Assets.md)
* [Documentation and Comments](https://github.com/Lickability/swift-style-guide/blob/master/DocumentationAndComments.md)
* [Extensions](https://github.com/Lickability/swift-style-guide/blob/master/Extensions.md)
* [Imports](https://github.com/Lickability/swift-style-guide/blob/master/Imports.md)
* [Organization Within a File](https://github.com/Lickability/swift-style-guide/blob/master/OrganizationWithinAFile.md)
* [Project Groups](https://github.com/Lickability/swift-style-guide/blob/master/ProjectGroups.md)

## Technical Info
* [Custom Operators](https://github.com/Lickability/swift-style-guide/blob/master/CustomOperators.md)
* [Default Parameters](https://github.com/Lickability/swift-style-guide/blob/master/DefaultParameters.md)
* [Localization](https://github.com/Lickability/swift-style-guide/blob/master/Localization.md)
* [Naming](https://github.com/Lickability/swift-style-guide/blob/master/Naming.md)
* [Interface Builder](https://github.com/Lickability/swift-style-guide/blob/master/InterfaceBuilder.md)
* [Object Communication](https://github.com/Lickability/swift-style-guide/blob/master/Object%20Communication.md)
* [Optionals](https://github.com/Lickability/swift-style-guide/blob/master/Optionals.md)
* [Protocols](https://github.com/Lickability/swift-style-guide/blob/master/Protocols.md)
* [Testing](https://github.com/Lickability/swift-style-guide/blob/master/Testing.md)
* [Zero Usage](https://github.com/Lickability/swift-style-guide/blob/master/Zero%20Usage.md)

## Style

We use [SwiftLint](https://github.com/realm/SwiftLint) for code linting. We avoid making style comments on pull requests wherever possible, instead allowing the linter to catch them while developing. The fully documented `.yml` file we use is located [here](https://github.com/Lickability/swift-style-guide/blob/master/.swiftlint.yml).

## SwiftUI / Combine
The majority of this document was written prior to the introduction of SwiftUI and Combine. As we become more familiar with these technologies and use them in our own projects, we expect them to reshape the best practices as it exists today.
