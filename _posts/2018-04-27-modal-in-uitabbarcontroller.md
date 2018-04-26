---
layout: post
title:  "Modal screen inside UITabBarController"
date:   2018-04-27 01:34:53 +0200
categories: jekyll update
---
Let's say you want to present `SearchViewController` modally over current controller, which is inside of `UITabBarController`.
```swift
func showSearch() {
    present(SearchVC(), animated: true, completion: nil)
}
```
Do what it should. But presented controller will overlap `UITabBar`, so you can't switch between tabs at this point. 
To resolve this issue:
```swift
func showSearch() {
    // uncomment this one if your controller not wrapped in UINavigationController
    // definesPresentationContext = true
    let search = SearchVC()
    search.modalPresentationStyle = .currentContext
    present(search, animated: true, completion: nil)
}
```
`search.modalPresentationStyle = .currentContext` this line says to UIKit, that he need to find first UIViewController in stack, 
that defines context, and show modal in this context. By default `UINavigationController` and `UITabBarController` have this 
property `true`.

But now we lose ability to pop to root controller in stack by tapping on selected `UITabItem`. Here we getting help from
`UITabBarControllerDelegate`:
```swift
func tabBarController(_ tabBarController: UITabBarController, shouldSelect viewController: UIViewController) -> Bool {
    guard tabBarController.selectedViewController == viewController else { return true }
    viewController.dismiss(animated: true, completion: nil)
    return true
}
```

Work like a charm! But if you present modal from 2+ screen in navigation stack, you will get warnings in console:  
`Unbalanced calls to begin/end appearance transitions for <TBModal.MiddleVC: 0x7fad0d417b90>.`  

What's going on?  
`search.modalPresentationStyle = .currentContext` remove presenter's view after modal controller presentation, and put it
back right before dismiss. But in same time `UINavigationController` removing this view from stack in `popToRootViewController` 
call (or some equivalent used by UIKit). So you adding and removing view at same time.  

Easy fix: `search.modalPresentationStyle = .overCurrentContext`  
Here we say: "Show this modal over current context, but don't remove presenters's view from hierarchy"
