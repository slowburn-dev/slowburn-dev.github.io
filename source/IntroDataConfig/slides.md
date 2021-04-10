---
title: DataConfig Introduction
theme: night
showNotes: true
revealOptions:
    transition: 'none'
---
### Introducing DataConfig

* https://slowburn.dev/dataconfig
* Serialization framework for Unreal Engine that just works!

Note:

Hi. In this presentation I'll introduce you DataConfig. It's an open source serialization framework for UnrealEngine which I've
been working on for a while now.

You can find links to these slides and github repository in the video descriptions below.

---

### What is DataConfig

* C++ Library
* Deals with `UCLASS/USTRUCT/UPROPERTY`
* Abstraction over `FProperty` API

Note: 

So now what is DataConfig?

Technically it's a C++ library that built on top of Unreal Engine codebase. 

Unreal Engine programmers deals with macros like `UCLASS/USTRUCT/UPROPERTY` on a daily basis.

These macros belongs to "Unreal Property System" which is
C++ runtime reflection implementation. Many core engine features like serialization, editor UI and garbage collection are taking advantage of the property system. 

Programmers needs to use a set of C++ classes to access the property system data. A dominant one is the `FProperty` class.
which was `UProperty` in older releases

With these info in mind it's now easier to explain what DataConfig is. It's an abstraction built on top of the Property System C++ API.

You can choose to use DataConfig when dealing with the property system.

---

## Demo - Basic

Note:

Now we're going to do a simple demo.

**--Switch to Project--**

Here we have a project open with a official build of Unreal Engine 4.26. In the project I've already get the DataConfig plugin installed.

To verify that select "Settings -> Plugins" and search for "DataConfig". You should be able to find it in the "Project -> Editor" section.

**--Open "Plugins"--**

We also shipped a ton of automation tests along with the plugin. Go to "Window -> Developer Tools -> Session Frontend" and find the "Automation" tab.

**--Open "Frontend Sessions"--**

Tick the "DataConfig" checkbox, which would select all DataConfig tests. Hit the "Start Tests" button to run them to verify DataConfig is integrated properly.

**--Tick "DataConfig" checkbox and click "Start Tests"--**

Here we can see all the tests passed.

Now we'll focus on this one test called "Blurb.FrontPage", it's the example we're using in our readme file.

**--Select the "Blurb.FrontPage"--**

In this test we deserialize a JSON string into a struct instance.

**--Open Code "DcTestBlurbs.cpp"--**

Here's the code of this test.

At the top we have a JSON string literal, which contains three fields:

- One is String
- One is Enum
- And the last one is a array of hex color strings.

**--Open Code "DcTestBlurbs.h"--**

The struct we're deserializing into is also pretty straightforward. Struct property names match with JSON keys.

**--Open Code "DcTestBlurbs.cpp"--**

The rest of the code is creating a deserializer instance and context, and then kick off the deserialization.

Note that DataConfig API is struct based and all instances are allocated on the stack.

At the end we validate the results are correct.

Note that the enums are correctly loaded from string, and the `FColor` array are loaded from hex color strings.

To demonstrate the error handling capability, I'll now change the `EnumField` value to a incorrect one. 

**-- Change "Bar" to "Far" --**

We have LiveCoding enabled for this project, so I'll recompile now.

**-- Ctrl+Alt+F11 to Recompile --**

Now that the compilation is done, I'll run this test again.

**-- Select "Blurb.Frontpage" and Hit "Start Tests" --**

Note that the error message points out this exact token and reported that 'Far' is not a valid enum option.

**-- Switch Back TO SLIDES !!! --**

---

It's `FJsonObjectConverter` !

- Trivia to implement with DataConfig: `DcJsonConverter`
- Extendable with custom deserialization logic
- Better diagnostics 

Note:

Proficient Unreal Engine programmers would notice that this example is very similar to the built-in class `FJsonObjectConverter` functionality. It provide a set of methods that convert between JSON and UStruct instances back and forth.

We actually shipped an example named `DcJsonConverter` that implements similar APIs. It's relatively easy to implement in around 20 lines of code. On top of that we allow adding custom deserialization logic, like loading `FColor` from hex strings; and we provide better error messages, as we've seen in the last example.

---

### Why

- Friendly utility for the Property System
- Shipped with a relaxed JSON Parser
- Good test and doc coverage


Note:

This bring us to the reasons that why we're doing this and why DataConfig might be useful to you.

First DataConfig aims to be a friendly utility around the property system API. As we've seen in the examples above you can write custom deserialization logic with DataConfig, and it would report pretty good error messages. 

It's also possible to support additional textual formats. All you have to do is to write a new parser and plug it into the deserializer. Most existing deserialization logic can be reused and you can easily add or remove ones as you need.

DataConfig is bundled with a relaxed JSON parser. It support a super set of JSON that allows C++ style comments and trailing comma. It's common quality-of-life improvements for users that authors JSON files manually.

Finally we're developing DataConfig as a opportunity to explore maintaining an out of tree C++ module for unreal engine. We have pretty good test and documentation coverage already. We have plan to try out other C++ tooling like sanitizers and static analysis over the DataConfig code base, just to see if it works in this setup.

---

#### Demo - Load GameplayAbility from JSON

Note:

Before we end this presentation I'll quickly show another demo of loading a gameplay abillity instance from JSON.

**--Switch to UE Project--**

In the content browser we can find some assets under "DataConfig Content". Here we're openning the "DcTestGameplayAbilityAlpha" blueprint. 

**--Open "DataConfig Content" -> DcTestGameplayAbility.uasset --**

GameplayAbility is an optional module in Unreal Engine that implements data driven ability and attributes system. GameplayAbility blueprint is a specialized blueprint that represents a single ability setup.

This blueprint is mostly empty. We'll fill the contents from an external JSON file.

**--Open "Fixture_AbilityAlpha.json"--**

In this JSON file the key field names matches the Blueprint property names.

`AbilityTags` and `CancelAbilityWithTags` each are a list of strings of gameplay tag literals. The underlying C++ property is a `FGameplayTagContainer`.

The `CostGameplayEffectClass` field matches a class reference property.

The two policy fields are enums.

**--Switch to UE Project--**

Now we right click on the asset, in the context menu, select "Load From JSON" and select this JSON File.

**--Right click on asset, Select "Load From JSON", Select "Fixutre_AbilityAlpha.json"--**

You can see that the blueprint values are correctly populated from the JSON. 

**--Click "Show Only Modified"--**

Note how we are doing custom deserialization logic for UnrealEngine built-in classes. The class references is also correctly loaded.

Now we right click blueprint asset and select "Dump To Log":

**--Right click asset and "Dump To Log"--**

It would pretty print the asset to Output Log. This feature is also done with DataConfig.

**--Switch Back TO SLIDES--**

---

- Repo: <https://github.com/slowburn-dev/dataconfig>
- Doc: <https://slowburn.dev/dataconfig/>
- Email: hislowburn@gmail.com
- Twitter: <https://twitter.com/slowburndev>


Note:

And that's it.

Checkout DataConfig repository and docs. You can reach us with the links here.

Thank you for your attention and have a nice day!

