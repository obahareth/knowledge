# Building Extensible Platforms

{% embed url="https://www.youtube.com/watch?v=GqGNA8GnOOE" %}

[Talk link](https://www.youtube.com/watch?v=GqGNA8GnOOE).

## Platforms

> "Platforms provide a layer of abstraction to simplify otherwise complex tasks."

In platforms, all functionalitywe care about as users is generally provided by apps. The difference between platforms and apps is that platforms don't necessarily provide functionality but they are an important foundation for apps.

The purpose of a platform is to simplify application development and manage shared resources and services and access them through APIs.

Examples of platforms:

* Operating systems.
* Web browsers \(They're an application from the perspective of an OS, but they also provide platform capabilities\).

## Extensibility

> "Designed to allow the addition of new capabilities and functionality"

Extensibility is core to Shopify product philosphy.

* Platform team works with teams involved in the entire customer journey \(from business operations to selling in-person\) towards making Shopify extensible as a whole.
* [Shopify Flow](https://apps.shopify.com/flow), a tool for merchants to build automated workflows based on their business operations. Works a lot like Zapier \(triggers, conditions, and actions\). Third party developers can also build extensions to build custom triggers, conditions, and actions. It works and feels like Shopify first party functionality.

> "The experience of third-party developers is just as important as end-users' experience."

**Other examples**

* [Todoist Chrome extension](https://chrome.google.com/webstore/detail/todoist-to-do-list-and-ta/jldhpllghnbhlbpcmnajkpdmadaolakh?hl=en). Uses Chrome to render an icon button and present an overlay to manage Todoist. It feels like a native part of the browser experience thanks to the extension capabilities offered by Google Chrome.
* [Slack apps](https://slack.com/apps). Extend Slack functionality using bots and slash commands. Use apps without distrupting flow of conversations.

## [App Extensions](https://help.shopify.com/en/api/embedded-apps/app-extensions)

> ""App Extensions" are a deep third-party integration that feels indistinguishable from native functionality."

_Example of using Shopify Flow to build new triggers and actions starts at 11:23._

App extensions are the counterpart to APIs. When using APIs the app initiates the data exchange with your platform. When using app extensions allow your platform to initiate the data exchange aas required.

With app extensions, Shopify is in charge of pushing/pulling data to/from the extension, and the merchants never leave Shopify and have a consistent and seamless experience.

> "It is the combination of great user experience and great developer experience that make app extensions so powerful."

Merchants use Shopify Core \(can be accessed through web, apps, smart text-based assistants\), developers use the Partners Dashboard.

> "You might ask: "Why provide a graphical interface \(to programmers\) instead of a programmatic one?"
>
> Our goal is to create a developer experience that doesn't require reading any API documentation, we want to promote exploration and allow developers to learn about the capabilities of our platform interactively."

## App Extensions From Internal Teams' Perspective

> "Treat your internal teams as your customers too and provide them with the same quality tools you provide yor external customers"

To reduce friction they built an internal framework that manages all persistence and communication between their core product and partner's dashboard. This significantly reduced the number of steps involved in building app extensions to only 3 steps:

* Domain model.
* Merchant facing UI.
* Partner facing UI.

They also provide a wealth of UX resources to make it easier for teams to design app extensions.

Good internal documentation \(education\) was a must to enable this process. Broadcasting information on a regular basis is just as important as being around to answer questions. They maintained a Slack channel to support teams during the initial phases of the project.

## How to Build Extensibility

> "Make it easy to think about 'What to make extensible' rather than 'how to build extensibility'"
>
> "What parts of a user workflow should we enable our ecosystem to plug into?
>
> Which parts of our user experience should we make adaptable to whatever our ecosystem might build?"

Shopify tackles these problems by:

1. Building features that **most** of their users need \(the 80/20 rule, 80% is a core commerce need, 20% is divergent needs. Shopify enables their ecosystem to build for their niche divergent needs\).
2. Enable their partner ecosystem to build the rest.

> "Company-wide platform commitment has an incredible network effect \(when a product or service gains more value as more people use it\). Merchants are drawn to Shopify because of the ways Shopify and Shopify apps solve their end to end commerce needs. At the same time partners are drawn to our platform for our growing user base and our growing business needs and growing opportunities for innovation and entrepreneurship."

