# 1 Introduction to the [Knockout.js] MVVM library

Following the wake of [Node.js] and the foundation of a giant and rapidly growing Javascript ecosystem, multiple implementations of the [Model View Controller (MVC)] architectural pattern have emerged. The by far simplest one of these is [Knockout.js], which makes use of a variation of MVC called [Model View Viewmodel (MVVM)]. Please *make yourself familiar with the key concepts behind MVVW* since it will greatly help you to understand how Knockout works.

In contrast to many of the other available MVC/MVVM implementations, Knockout.js is a library that takes care of the **data-binding** between the model and the view. By oing this, it frees developers from the hassle of manually updating the DOM when data in the JS model changes, respectively vice versa, updating the JS model when the user modifies the data in the course of interaction with the web application.

It also offers binding of viewmodel functionality to events, and last but not least, [templating]. v3.2.0 introduced [Knockout components]


Knockout was developed and is maintained as an open source project by Steve Sanderson, a Microsoft employee. Despite this fact Knockout never was and never will be a Microsoft product, and is release under the [MIT License].

As of November 2015, the most recent stable version of Knockout.js is [Knockout.js v3.3.0]. There currently is a [release candidate of v3.4.0] available that, besides new functionality and a number of fixes, is claimed to make core features 2-3 times faster than in v.3.3.0.

>- Performance Improvement - for named templates that use a template or normal element use the element directly as the template to clone
>- Performance Improvement - Make observables 2-3x faster to instantiate and reduce memory usage by 50%
>- Performance Improvement - Make computeds 2x faster to instantiate and reduce memory usage by 50%

## 1.1 Brief summary of the differences between MVC and MVVW

For the sake of completeness, despite this not being the topic of this book, here's an excerpt from a blog post by [Eric Hoff] that briefly describes the differences between MVC and MVVW.

>The most obvious answer, and the most over-simplified answer is that MVC is primarily a server-side programming pattern, while MVVM is more often a client-side pattern.

>MVVM is concerned with keeping the View up-to-date with the server data in the Model and ViewModel. The ViewModel is a special object separate from the Model (though not always). In a web app using MVVM the Model usually represents the data on a server, while the ViewModel is the data on the client. MVVM often also uses the Observer pattern to keep the View in sync with the ViewModel data that backs it up, and the same for the ViewModel and Model. When a change in the Model or ViewModel data is observed the View will refresh itself to show the latest Model data. Likewise, when a form is used in the View to update the Model these changes can be passed back to the Model on the server immediately.

><http://willcodeforcoffee.com/2013/06/12/what-is-mvc-vs-mvvm-pattern/>

![MVVW illustration](../images/mvvw.png "MVVM Illustration")

##1.2 First example

The first example shows the basic concept that ties a viewmodel to a view. Find the snippets in Codepen.io here: <http://codepen.io/connexo/pen/wKYpLP>

### View (HTML with declarative bindings)

```html
<!DOCTYPE html>
<html>
	<head>
		<script src="path/to/knockout.js"></script>
	</head>
	<body>
		<h1>Introduction to Knockout.js</h1>
		<p>My first name is <span data-bind="text: firstName"></span>.</p>
		<p>My last name is <span data-bind="text: lastName"></span>.</p>
		<hr />
		<p>My full name is <span data-bind="text: fullName()"></span>.</p>
	</body>
</html>
```

### Viewmodel containing the data
```javascript
var ExampleViewModel = function() {
	var self = this; // Store a stable reference to this
	
	/* BEGIN Properties */ 
	self.firstName = "Peter";
	self.lastName = "Miller";
	self.fullName = function() {
		return self.firstName + " " + self.lastName;
	};
	/* END Properties */ 
};

ko.applyBindings(new ExampleViewModel()); // This is where all the magic happens
```


[Knockout.js]: http://www.knockoutjs.com
[Node.js]: http://nodejs.org
[Model View Controller (MVC)]: https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller
[Model View Viewmodel (MVVM)]: https://en.wikipedia.org/wiki/Model_View_ViewModel
[MIT License]: http://www.opensource.org/licenses/mit-license.php
[Knockout.js v3.3.0]: https://github.com/knockout/knockout/releases/tag/v3.3.0
[Knockout components]: http://knockoutjs.com/documentation/component-overview.html
[templating]: http://knockoutjs.com/documentation/template-binding.html
[release candidate of v3.4.0]: https://github.com/knockout/knockout/releases/tag/v3.4.0-rc
[Eric Hoff]: http://willcodeforcoffee.com/about/