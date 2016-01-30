#iOS Testing

In iOS the practice of implementing TDD or BDD practices is not that common. There is a lot of confusion in the iOS developer community about what to test, or even if it is actually worth it. What do I mean by this?

First of all TDD practices imply that you should write test first and then write your code so that those test pass. That sounds easy, but the truth is that given the tools, architecture, and language nature used for iOS development that becomes not actually harder, but also can't follow the rule exactly as it is. Why is that you ask? 

First of all, when we work in XCode there are many things that can be done in Interface Builder whithout needing any code at all. You can connect segues, to make a button work. You can connect actions with code just by dragging and dropping. So that made it impossible to test this kind of things. Well not entirely true there was this tool called automation that made it possible to make authomated UI tests, but in order to make it work not only you had to know javascript, but also code a whole lot just to make a simple test. That was up until version 7 of Xcode. Which as of the writting of this post is the latest mayor version of XCode. After that apple finally released tools for simple UI test which you can integrate with your test target. And even though apple released these tools, it still leaves the question of what would you test with these UI, because interfaces in iOS change constantly, so making very specific test would make testing a hassle way bigger than of the benefits we get from testing.

Now the combination of graphic tools, and code is not the only problem with testing in iOS, but also the way it's architecture work. iOS base architecture is MVC. Models we know how to test they are have tipical functions that receibe some parameter and return some information. The problem arises when you need to test controllers, their main function should be only to act as an intermediary between the view and the models, and give control to the flow of the app. Those things can't be tested by simple unit tests. In fact if you have functions in a controller which you can test that way, there is strong probablity that the method should be on the controller but rather on some business logic class. Now there is also the problem that many methods in the view controller are called by the own system, and finding ways to test those methods would also result in so much more code. Which would complicate even simple apps. Now if you don't test those methods you will also have a low code coverage, wich is also reflects bad on your tests.

The third main problem with iOS and TDD, is the nature of the language. As it is a compiled language you can not write fake functions of a class first, and then have them written. You must have at least the skeleton of the class to make your test otherwise the testing class will complain that the functions do not exist and won't run any tests. Thus breaking a little with the process of TDD. Now there also comes the part when you have to test for responses of server, or getting data into or from a database. Usually you would get help of tools to make mocks of those clases working in diferent conditions easily. Fact is there are many libraries that help for this in Objective-C. The problem is with swift due to the nature of the language those mocks can't be done dinamically, you would have to code the mocking classes yourself, and then again you have the problem of having double work. Now if you make your test in Objective-C you could only test those methods and classes that objc could see, thus either you would have to eliminate the use of the new features that swift provides, or not meet the code coverage that it should cover.

I hope by now you notice why testing in iOS has been a practice that very few have adopted, and this also creates a problem of not having many resources and places to go in matters of testing in this platforms. So what should we do then? are these nuissances enough reason to ignore completly having test in our code? If not how should we go about testing our apps?

To answer this question lets have a quick look of the advantages vs the disadvantages of using tests in our code gathered from a post from [raywenderlich](http://www.raywenderlich.com/101306/unit-testing-tutorial-mocking-objects):

| Advantages | Disadvantages |
| --- | --- |
| **Confidence:** You can demonstrate that your code works. | **More code:** In projects with high test coverage it’s possible to have more test code than functional code. |
| **Quick feedback:** You can use unit tests to quickly validate code that is buried many layers deep in your app navigation — things that are cumbersome to test manually. | **More to maintain:** When there is more code, there is more to maintain. |
| **Modularity:** Unit tests help keep you focused on writing more modular code. | **No silver bullet:** Unit tests don’t (and can’t) ensure that your code is free of bugs. |
| **Focus:** Writing tests for micro features keep you focused on the small details. | **Takes longer:** Writing tests takes time |
| **Regression:** Be sure that the bugs you fixed stay fixed — and aren’t broken by subsequent fixes. |
| **Refactoring:** Until Xcode gets smart enough to refactor your code on its own, you’ll need unit tests to validate your refactoring. |
| **Documentation:** Unit tests describe what you think the code should do; they serve as another way to document your code. |

So as we can see there are many advantages to using tests that we don't wan't to be missing when we are striving to write the best code for the client as possible. So that leaves us to decide how to, and what to test. What would be the optimal way of writing test without compromising too much time, and without our coding of test becomes even harder than making the actual code, and also **don't compromise the good coding practices** in doing so.
So here are the steps to follow and tips in order to test your code in iOS.

## How to get started

### Before starting

First of all let me tell you of some tool that you will need to download or add to your pod file for use with our tests:

* Quick - pod 'Quick'
* Nimble - pod 'Nimble'

This tools will help us to have a BDD testing style rather than the raw test that come integrated with XCode. You can read more about the advantages of this in the following links, but basically this let us have a cleaner arrangement and readability of our tests:

* http://www.toptal.com/freelance/your-boss-won-t-appreciate-tdd-try-bdd
* http://www.slideshare.net/bgesiak/quick-better-tests-with-incremental-setup
* http://davemeehan.com/technology/unit-testing/test-and-behaviour-driven-development-on-ios-with-kiwi
* https://realm.io/news/testing-in-swift/
* https://github.com/Quick/Quick/tree/master/Documentation

Why did we choose this framework versus his other friends out there? This one has thight integration not only with objective-c, but it is written in swift which lets you take advantage of all the features that swift brings. Also it has a very good integration with Xcode, which makes it a perfect fit for our development tools, which is something that the other frameworks do not have.

### Setup

We will setup our proyect to have test Coverage, code tests, and UI tests.

The Unit Testing and UITesting support can be added when creating the project.
<div style="width:100%; text-align:center"><img src="./resources/Screen Shot 2016-01-29 at 2.29.25 p.m..png" style="height:300px"/></div>
First we will enable Test Coverage. Go to your squeme settings, and make sure the gather coverage data box is checked: 
<div style="width:100%; text-align:center"><img src="./resources/Screen Shot 2016-01-29 at 2.46.59 p.m..png" style="height:300px"/></div>
Now if you didn't add the testing targets when creating the project, here is the way to add them manually. Go to your project's target list. And select the `+` sign at the bottom to add a new target
<div style="width:100%; text-align:center"><img src="./resources/Screen Shot 2016-01-29 at 2.54.20 p.m..png" style="height:300px"/></div>
In the target menu that you see select the Test section and add the corresponding bundle you want to add.
<div style="width:100%; text-align:center"><img src="./resources/Screen Shot 2016-01-29 at 2.58.11 p.m..png" style="height:300px"/></div>
Give it a name in the screen that follows. And the language you will be testing on. For this guide purposes we used swift.
<div style="width:100%; text-align:center"><img src="./resources/Screen Shot 2016-01-29 at 3.01.35 p.m..png" style="height:300px"/></div>
Once you have added those bundles two new folders will appear:
<div style="width:100%; text-align:center"><img src="./resources/Screen Shot 2016-01-29 at 3.05.20 p.m..png" style="height:300px"/></div>
We are now ready to add quick and nimble. In your pod file add them to your testing targets only. And run a pod install.
<div style="width:100%; text-align:center"><img src="./resources/Screen Shot 2016-01-29 at 3.08.58 p.m..png" style="height:300px"/></div>
Now that everything is ready to start making our tests, lets first get a look at the main screens you will be looking at when making your tests. Build your project for testing.
<div style="width:100%; text-align:center"><img src="./resources/Screen Shot 2016-01-29 at 3.13.22 p.m. (2).png" style="height:300px"/></div>
It should compile and finish with all tests passing. You can have a look at the status of the tests and verify this if you select the test navigator bar.
<div style="width:100%; text-align:center"><img src="./resources/Screen Shot 2016-01-29 at 3.25.49 p.m..png" style="height:300px"/></div>
Now to see your test coverage. Select the report Navigation tab. Select the test you just ran. And in the main screen you are presented with all the tests runned, and three tabs on the top. Select the coverage tab.
<div style="width:100%; text-align:center"><img src="./resources/Screen Shot 2016-01-29 at 3.32.44 p.m..png" style="height:300px"/></div>
For the rest of this guide let's work on a demo project

## Using Quick and Nimble with Models

To begin using quick and nimble. Lets imagine we will have to test the following things of a model person:

* A person should be able to always display his or her full name as a string.
* A person can be considered the same person as long as it has the same name and the same lastname
* A person must always have a name and a lastname

To do this you will have to:

1. Add a Unit Test Case Class File to the Unit Testing Target
2. Delete all default info created by XCode
3. import Nimble and Quick
4. define a class that inherits from `QuickSpec`
5. you will be required to override a spec function declared by the QuickSpec parent, this is where all your specs will be.
6. Last but not least in order to access code from your app target you must import it as testable so that even internal methods are testable
```swift
import Quick
import Nimble
@testable import ILTestingDemo

class PersonSpec: QuickSpec {
	override func spec() {
    }
}
```
7. We can now start writing our test conditions (For information on all matchers and functions of Quick and Nimble review their documentation)
```swift
	override func spec() {
        let personSample = Person(name: "Monica", lastName: "Geller")
        
        describe("full name") {
            context("when required") {
                it("should always have the right info") {
                    expect(personSample.fullName).to(equal("Monica Geller"))
                }
            }
        }
        
        describe("a person") {
            context("when compared for equality to another person") {
                let anotherPerson = Person(name: "Monica", lastName: "Geller")
                let anotherPersonWithDifferentName = Person(name: "Monica", lastName: "Schultz")
                
                it("should be considered the same as long as the name and lastname are equal") {
                    expect(personSample == anotherPerson).to(beTrue())
                    expect(personSample == anotherPersonWithDifferentName).to(beFalse())
                }
            }
            
            context("when intantiating with name and lastname parameters") {
                let name = "Timmy"
                let lastName = "Turner"
                let person = Person(name: name, lastName: lastName)
                it("must reflect that result when being used") {
                    expect(person.name) == name
                    expect(person.lastName) == lastName
                }
                
                it("must always have a name and a lastname") {
                    expect(Person(name: "", lastName: "")).to(beNil())
                    expect(Person(name: "", lastName: "Morgan")).to(beNil())
                    expect(Person(name: "Jack", lastName: "")).to(beNil())
                    expect(Person(name: "Jack", lastName: "Morgan")).toNot(beNil())
                }
            }
        }
    }
```
8. Now if you try to compile it won't let you, even though we told it not to run those test cases with the `x` before each spec in Quick, the compiler still runs and complaints when something is not declared. So this is why I was telling you that you must have at least the skeleton of the class to be able to write the tests. So lets write the skeleton now.
9. Make a new file in your's apps target for the class Person.
10. Write the following code as a skeleton:
```swift
public func ==(lhs: Person, rhs: Person) -> Bool {
    return false
}

public class Person {
    let name: String
    let lastName: String
    var fullName: String {
        return ""
    }
    
    init(name: String, lastName: String) {
        self.name = name
        self.lastName = lastName
    }
}
```
11. Now if you build for tests, it will compile but no quick test of that spec will be run, from here you would be taking out the `x`'s you are ready to test. That means the parts of the code for which your app already is supposed to comply with the spec.
12. This is the way in which we can more closely conform to the TDD process. Some expects are not necessary, and some might need to change depending on how you write your skeleton. To see the resulting code to make this work, refer to the demo project.


# Tips and tricks of common tasks

* server request
* ui test
* core data
* viewDidLoad
* models

## Useful Links you might also want to check

http://www.raywenderlich.com/101306/unit-testing-tutorial-mocking-objects
http://www.slideshare.net/bgesiak/everything-you-never-wanted
https://www.natashatherobot.com/ios-testing-view-controllers-swift/
https://www.natashatherobot.com/unit-testing-swift-dependency-injection/
http://www.slideshare.net/bgesiak/quick-better-tests-with-incremental-setup
https://www.bignerdranch.com/blog/ui-testing-in-xcode-7-part-1-ui-testing-gotchas/
https://www.objc.io/issues/15-testing/dependency-injection/
http://masilotti.com/better-swift-unit-testing/
https://sharpfivesoftware.com/2015/02/03/testing-singletons-in-swift/
http://stackoverflow.com/questions/28115991/inject-mock-class-into-method-to-unit-test-method

