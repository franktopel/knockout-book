# 1 Introduction to the [Knockout.js] MVVM library

Following the wake of [Node.js] and the foundation of a giant and rapidly growing Javascript ecosystem, multiple implementations of the [Model View Controller (MVC)] architectural pattern have emerged. The by far simplest one of these is [Knockout.js], which makes use of a variation of MVC called [Model View Viewmodel (MVVM)]. Please *make yourself familiar with the key concepts behind MVVW* since it will greatly help you to understand how Knockout works.

In contrast to many of the other available MVC/MVVM implementations, Knockout.js is a library that takes care of the **data-binding** between the model and the view. By doing this, it frees developers from the hassle of manually updating the DOM when data in the JS model changes, respectively vice versa, updating the JS model when the user modifies the data in the course of interaction with the web application.

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

##1.3 Observable and computed values

The second example shows observable and computed properties for a dynamic view. Find the snippets in Codepen.io here: <http://codepen.io/DirtyHerby/pen/epPbRr>

### View

```html
<p>My first name is <input data-bind="value: firstName"></span>.</p>
<p>My last name is <input data-bind="value: lastName"></span>.</p>
<hr />
<p>My first name is <span data-bind="text: firstName"></span>.</p>
<p>My last name is <span data-bind="text: lastName"></span>.</p>
<hr />
<p>My full name is <span data-bind="text: fullName()"></span>.</p>
```

### Viewmodel
```javascript
self.firstName = ko.observable("Peter");
self.lastName = ko.observable("Miller");
self.fullName = ko.computed(function() {
	return self.firstName() + " " + self.lastName();
});
```

##1.4 Lists and Collections

The third example shows lists and collections for generate repeating blocks of UI elements by using observable arrays and the foreach binding. Find the snippets in Codepen.io here: <http://codepen.io/DirtyHerby/pen/gaBqMx>

### View

```html
  <h2>License overview (<span data-bind="text: users().length"></span>)</h2>

  <table>
    <thead>
      <tr>
        <th>User name</th>
        <th>Edition</th>
        <th>Surcharge</th>
        <th></th>
      </tr>
    </thead>
    <tbody data-bind="foreach: users">
      <tr>
        <td>
          <input data-bind="value: name" />
        </td>
        <td>
          <select data-bind="options: $root.availableEditions, value: edition, optionsText: 'editionName'"></select>
        </td>
        <td data-bind="text: formattedPrice"></td>
        <td><a href="#" data-bind="click: $root.removeUser">Remove</a></td>
      </tr>
    </tbody>
  </table>

  <button data-bind="click: addUser, enable: users().length < 5">Add user</button>

  <h3 data-bind="visible: totalSurcharge() > 0">
    Total surcharge: <span data-bind="text: totalSurcharge().toFixed(2)"></span>€
  </h3>
```

### Viewmodel
```javascript
function UserLicense(name, initialEdition) {
  var self = this;
  self.name = name;
  self.edition = ko.observable(initialEdition);

  self.formattedPrice = ko.computed(function() {
    var price = self.edition().price;
    return price ? price.toFixed(2) + "€" : "free";
  });
}

function LicenseViewModel() {
  var self = this;

  self.availableEditions = [{
    editionName: "Standard Edition",
    price: 0
  }, {
    editionName: "Premium Edition",
    price: 29.95
  }, {
    editionName: "Ultimate Edition",
    price: 49.95
  }];

  self.users = ko.observableArray([
    new UserLicense("Peter", self.availableEditions[0]),
    new UserLicense("John", self.availableEditions[0])
  ]);

  self.totalSurcharge = ko.computed(function() {
    var total = 0;
    for (var i = 0; i < self.users().length; i++)
      total += self.users()[i].edition().price;
    return total;
  });

  self.addUser = function() {
    self.users.push(new UserLicense("", self.availableEditions[0]));
  }
  self.removeUser = function(user) {
    self.users.remove(user)
  }
}

ko.applyBindings(new LicenseViewModel());
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