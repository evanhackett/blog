---
title:  "Voiceover Bug in React Native"
description: Description, Causes, and Workarounds
date: 2018-03-10 10:03:18
---

React Native has a huge accessibility problem. To put it into perspective, imagine if every element had its opacity set to 0.5. When you navigate to a new screen, you can still see all the elements from the previous screen. This is what it is like for blind users using voiceover technology with React Native apps. At the time of this writing, elements with accessibility identifiers are detected by voiceover from previously navigated to — but now inactive — screens. Meaning, voiceover will read aloud elements that should no longer be "visible".

There is a github issue describing the problem [here][issue1].

What exactly is causing this problem? After sifting through the discussion on [this pull request][PR1], the reasons for the issue start to become clear.

It all starts with the fact that native Android and iOS handle accessibility in different ways. Android has a property called "importantForAccessibility", while iOS has a very similar property called "accessibilityElementsHidden".

According to the docs, importantForAccessibility "Describes whether or not this view is important for accessibility. If it is important, the view fires accessibility events and is reported to accessibility services that query the screen."

On iOS, accessibilityElementsHidden is described as "A Boolean value indicating whether the accessibility elements contained within this accessibility element are hidden."

At first these may seem like different things. However, each one is used on their respective platforms to accomplish the desired voiceover behavior (don’t read hidden elements).

It is pretty clear how the iOS property accomplishes this with the accessibilityElementsHidden property. If an element has this property set to true, the voiceover will not detect the contained elements. On Android, it is slightly more complicated. importantForAccessibility accepts 4 different values:

|-----------------+------------|
| Constant | Description |
|-----------------|------------|
| auto | The system determines whether the view is important for accessibility - default (recommended). |
|-----------------+------------|
| no     | The view is not important for accessibility.           |
|-----------------+------------|
| noHideDescendants | The view is not important for accessibility, nor are any of its descendant views. |
|-----------------+------------|
| yes | The view is important for accessibility. |
|-----------------|------------|


If you don’t want inactive elements to be detected by voiceover (such as the ones that aren’t on the active screen), setting importantForAccessibility to "auto" will usually do the trick. In the github issue thread linked above, omeid explains why the voiceover bug may not appear in Android: "The reason it works in Android is because the default value of importantForAccessibility is auto which does some magic to detect what elements are not important, for example when opacity is 0, when the element is more than one layer deep, and I think when height and/or width is 0, and so forth."

So, why exactly does voiceover read hidden elements on iOS? Essentially it is because there is no magic being done to mark elements as accessibilityElementsHidden when they are hidden, the way Android does with its  importantForAccessibility: auto.

The question remains: how should this be fixed?

There seems to be 2 major options being discussed. The first is to add accessibilityElementsHidden to react-native, and then add support for Android to keep the API consistent. On iOS it directly maps to the native accessibilityElementsHidden. On Android it can be simulated with importantForAccessibility. This is the strategy that omeid was attempting in his [PR#11788][PR1]. The second option is to implement importantForAccessibility on iOS in terms of accessibilityElementsHidden. This is what sdg9 has done in [his commit][sdg9_commit].

These fixes will expose the needed properties in react-native, but the Navigation library will need to use them before the bug will actually be fixed. Remember, the issue at hand is when navigating to new screens the voiceover will read elements from previous screens. The issue is tied to navigation, although perhaps other issues will arise due to this problem. Github user awseeley has [published a fork][fork] of react-navigation which adds the importantForAccessibility prop to the relevant render method (in file Card.js). **Combining this fork of react-navigation with a fork of react-native that adds importantForAccessibility to iOS has fixed the issue for me.** I'm currently working at the [Universal Design Lab][ulab] at PSU, where accessibility is a top priority.

This is not the end. The important question shouldn't be how to fix it in an individual project (although I hope I helped answer that!). The important question is how can we get a PR merged in React Native that will fix this for everyone? Why hasn’t a fix like this already been merged in?

The original reason omeid’s PR wasn’t merged in is complicated. A github user named lacker objected to the idea of deprecating importantForAccessibility. Remember, omeid’s original fix was to add accessibilityElementsHidden and deprecate importantForAccessibility. lacker suggested making importantForAccessibility work on iOS, and not deprecate anything. omeid's idea was that importantForAccessibility is a superset of accessibilityElementsHidden. He thought you could achieve the 2 possible values (true or false) of accessibilityElementsHidden by using importantForAccessibility’s "yes" and "noHideDescendants". But there is no iOS equivalent of "auto" and "no". This means that using this method will lead to an inconsistent api on iOS and Android. omeid made a good point, but it was then pointed out that omeid’s original PR was itself inconsistent. On iOS, when accessibilityElementsHidden is true, only the descendants are hidden. On Android, both the descendant’s and the view are hidden. More discussion followed on ways to resolve this, but ultimately the discussion waned, and it seems no one has thought of a way to make a consistent API across the two device types.

Until a fix with a consistent API is created, and preferably with no deprecations, it is very likely that React Native will remain inaccessible. At the time of this writing, omeid’s original PR has been open for over a year.

For now, developers using React-Native that want accessible apps have to use forks of react-native and react-navigation.

[issue1]: https://github.com/facebook/react-native/issues/9725#issuecomment-303104243
[PR1]: https://github.com/facebook/react-native/pull/11788
[sdg9_commit]: https://github.com/sdg9/react-native/commit/a001149ccb6c071a020794861008970038612978
[fork]: https://github.com/awseeley/react-navigation
[ulab]: http://www.universaldesignlab.com/
