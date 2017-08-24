# ngModelOptions #

Today we will talk about Angular.js, and more specifically - about the directive ngModelOptions. This directive allows you to change the behavior of ngModel directives in your application. NgModelOptions can be connected to any element on your page that contains the ngModel directive.
One of the most interesting features of this directive is the possibility to inherit values from the parent directive ngModelOptions, using "$inherit". It will inherit the value from the first ngModelOptions directive found by traversing up the DOM tree. If there is no parent, the element containing the ngModelOptions directive will use the default settings. For example:

<div ng-model-options="{ allowInvalid: true, debounce: 200 }">
     	 <form ng-model-options="{ updateOn: 'blur', allowInvalid: '$inherit' }">
     	            	<input ng-model-options="{ updateOn: 'default', allowInvalid: '$inherit' }" />
     	</form>
</div>

In this example, the ngModelOptions directive in the form tag inherits the value of the allowInvalid option from its parent (div). Next, ngModelOptions in the input inherits the value of the allowInvalid option from the directive in form, which is its immediate parent.
One more example:

<div ng-model-options="{ allowInvalid: true, debounce: 200 }">
<form ng-model-options="{ updateOn: 'blur', "*": '$inherit' }">
<input ng-model-options="{ updateOn: 'default', "*": '$inherit' }" />
</form>
</div>

In this example I would like to draw your attention to the appearance of such a value as "*": '$ inherit'. This value specifies that the ngModelOptions directive in the current tag inherits all options from the parent element, except those already specified in the current directive. The ng-model-options directive in the form tag inherits the values from the parent tag "allowInvalid: true, debounce: 200", because they are not listed in the current directive. In its turn, ng-model-options in the input will inherit the values "allowInvalid: true, debounce: 200", but not "updateOn", because the value of the updateOn option is specified in the current directive.
The ngModelOptions directive has the following options: updateOn, debounce, getterSetter, timezone, allowInvalid. The most commonly used ones are updateOn and debounce.
Using the updateOn option, we can determine to which event a change of the variable is to be bound. You can set several events.
Imagine a situation where we have a block with advanced search settings containing a lot of inputs. Each input defines a specific value from the scope to which the ng-model is bound. A watcher is connected to each variable, which tracks the changes and sends requests in case of a trigger.

<div ng-controller="ExampleController">
	<form name="userForm">
   <label>
	Name:
	        <input type="text" name="userName"
                               	ng-model="user.name"
                               	ng-model-options="{ updateOn: 'blur' }"
                               	ng-keyup="cancel($event)" />
	</label><br />
	<label>
	Other data:
            <input type="text" ng-model="user.data" />
	</label><br />
	</form>
<pre>user.name = <span ng-bind="user.name"></span></pre>
</div>
<script>
	app. controller('CompaniesAllController', ['$scope',  function ($scope) {
       $scope.user = {
 name: "brian ",
                       data: "some data"
};

$scope.watch("user.name", function(){
	//some http request
})
</script>
In this case, to avoid the situation when a request is sent for each change, even if you haven’t finished the input, you can define the value of the ngModelOptions = "{updateOn: 'blur'}" directive. Here, the value change will only occur after the user has removed the focus from the input field. Thus, we have significantly reduced the number of requests.
“Debounce” specifies the amount of update delay in milliseconds. As an argument, several parameters can be passed through a comma, with the help of which we can determine our behavior for each event.
Let’s consider a situation similar to the previous one. One of the solutions is also to set the delay for updating the value so that the user can enter the new value completely or at least partially.

<div ng-controller="ExampleController">
	<form name="userForm">
   <label>
	Name:
	         <input type="text" name="userName"
                               	ng-model="user.name"
                               	ng-model-options="{ debounce: 500 }"
                               	ng-keyup="cancel($event)" />
	</label><br />
	<label>
	Other data:
               	<input type="text" ng-model="user.data" />
	</label><br />
	</form>
<pre>user.name = <span ng-bind="user.name"></span></pre>
</div>
<script>
	app. controller('CompaniesAllController', ['$scope',  function ($scope) {
      $scope.user = {
                     name: "brian ",
                     data: "some data"
				    };

$scope.watch("user.name", function(){
	//some http request
})
</script> 

The less used attribute is getterSetter. It determines whether the functions related to ngModel should be treated as getters / setters.
The following example shows us how to use this option.

<div ng-controller="ExampleController">
 <form name="userForm">
	<label>
 	Name:
 	<input type="text" name="userName"
        	ng-model="user.name"
        	ng-model-options="{ getterSetter: true }" />
	</label>
 </form>
 <pre>user.name = <span ng-bind="user.name()"></span></pre>
</div>
<script>
 angular.module('getterSetterExample', [])
.controller('ExampleController', ['$scope', function($scope) {
 var _name = 'Brian';
 $scope.user = {
	name: function(newName) {
 	return angular.isDefined(newName) ? (_name = newName) : _name;
	}
 };
}]);
</script>

“Timezone” specifies which time zone to use when working with the Date object.
It understands UTC / GMT and continental reductions in US time zones. Also, you can use the time zone offset, for example, "+0430" (4 hours, 30 minutes east of the Greenwich meridian). If not specified, the time zone used in the browser will be used.

<body ng-controller="MainCtrl">
 	<p>Hello {{name}}!</p>
		{{dateModel}}
	<input type="date" ng-model="dateModel" ng-model-options="{timezone: '-0100'}" /> 
</body>

“AllowInvalid” a boolean value that determines whether a binding to a non-valid value will be performed. In case if the value is true and you try to enter a non-valid value in the field, it will be equal to an empty string instead of undefined. This can be convenient in the case when we want to ensure ourselves in case we suddenly get a non-valid value, and we don’t want to send the value undefined to the server. This option is used extremely rarely.
