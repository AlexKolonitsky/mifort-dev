# Unit Tests in Node.js

How long are developers going to bear the injustice in the existing application development cycle? Think about, how often you were distracted by the testing department with a jubilant exclamation: "Found!", "Your code doesn't work right again!". 
And how often it turned out, that it is not a conceptual pitfall, but some defect, that was occurred when new functionality was added?
But now, we as developments have the opportunity to rectify the situation: to provide several layers of testing ourselves.

Haven't you any desire to write tests yet?

Do you have any gift of prophecy by accident?
Does your code know itself, when it will change if new functionality appears?
Or, maybe you are the only, who develops the project? Honestly speaking in this case too, after a couple of months you will not recognize why there are some actions happened in your code.
And if is it necessary to make changes quickly? What is the probability, that they don't break anything in the code?


### What are tests like?
In various resources you can find differenet classification of tests and approaches to their writing.
The following presentation is used in common variant of tests' description:
the pyramid, the base of which unit tests are, then integration and on the top end-to-end tests.

![](https://github.com/PerminovaAnastasia/Images/blob/master/rules-of-thumb.png)

The graphic interpretation describes the optimal quantitative ratio of tests in the application.

### What are unit tests? 

Unit testing is the process in programming, which allows checking separate modules or functions of the application for correctness.

In other words, this type of test is the first barrier to potential bugs. And to be exact, they bear the brunt of changes in the code.

### What should be unit tests?

* Fast in execution
> Because there is a practice, in which every change in the code of the master branch is verified by the already implemented system of the tests
(not end-to-end tests) and in case of when at least one doesn't pass - amendments haven't been accepted before remedy the situation with the tests (suddenly out of date) or the code. It is so-called approach of red-green testing. 
* Independent from the environment
* Easily supported and modifiable
> How often do you write the code, which you never change after? And the so with tests, if you refactor the code, it can entail the necessity to fix the tests too, that verify the code.
* Figuration of the tests should be peculiar specification for a piece of the code under testing. 
> Special syntax and set of functions exist that allow providing it. You will find information about how later in this article.   
* Reliable
> Don't do that the function under testing calculates the sum of two numbers and at the same time the test verifies multiplication. 
* By unit tests should be sure what is going wrong in the code.
> This is a significant difference from other types of tests: the unit one is intended for error localization.

## Implementation unit tests in Node.js.

For Node.js you can find different frameworks and libraries that allow automating testing. Let's go with following combination: Mocha and Chai.

##### So, necessary means for work:

| Object | What is used for? |
| :---:       |     :---      |
|[Mocha](https://mochajs.org)|Framework for creating a testing environment; includes functions `describe` and `it` *for tests' specification* |
|[Chai](http://chaijs.com)|Library that provides verification functions: *should, expect, assert*|

And, of course the code that is under test. For example, we will check the following function: the sum of two complex numbers.

```
The complex number we represent as an object:
{
	re: number,
	im: number
}
function complexSum(a, b) {
  return {
    re: a.re + b.re,
    im: a.im + b.im
  }
}
```
The implementation of the test for the addition function can be presented in this form:
```
const assert = require('chai').assert;
const complex = require('./complex');

describe("Summarize complex numbers", () => {

  it("Summarize complex numbers", (done) => {

    let complexNumber = {
      re: 2,
      im: 3
    };

    assert.deepEqual(complex.complexSum(complexNumber, {re: 1, im: 4}), {re: 3, im: 7});
    assert.deepEqual(complex.complexSum(complexNumber, 0), complexNumber);
    
    done();
  });

});

```

*What's going on here?:*

	1. describe(`the title of the object`, () => { ... })
  Specifies what exactly is described - helps to form logical blocks of `it`
  
	2. it(`the title of the object`, () => { ... })
  
  The name of the block `it` is used to describe what the function should actually do in one or another case
  
	3. assert.equal(...) - it is verification; there is a place in which the test passes or not
	
So, in the first `assert`, we check the sum of two complex numbers, in the second - the sum with zero. Don't worry, so is planned, that when we run the test the error will occur. The zero is not an object. And it turns out, that the possibility of addition with a usual number wasn't foreseen in the function.

The console output of the test's result: 

![](https://github.com/PerminovaAnastasia/Images/blob/master/Unit-Test-1.png)

If you click on `Click to see difference`, you will see the difference between current and expected result.

![](https://github.com/PerminovaAnastasia/Images/blob/master/Unit-Test-3.png)

So let's fix our function!

```
function isComplex(value) {

  return !isNaN(Number(value.re)) && !isNaN(Number(value.im)) || !isNaN(Number(value));
}

function complexSum(a, b) {

  if(isComplex(a) && isComplex(b)){

    let result = {};

    result.re = a.re && b.re ? a.re + b.re : result.re = a.re ? a.re + b : result.re = b.re ? b.re + a : a + b;
    result.im = a.im && b.im ? a.im + b.im : result.im = a.im ? a.im : result.im = b.im ? b.im : 0;

    return result;

  }
  return null;
}

```
Run the tests and make sure that everything is happened as planned.

![](https://github.com/PerminovaAnastasia/Images/blob/master/Unit-Test-2.png)


<details>
  <summary> But what's still wrong with the test case can you see? </summary>
  <p>
If we remain only the second `assert`, we won't know if it will succeed (how it is happened in the example, cause the 
initial function could calculate 2 complex numbers). Therefore, you should not forget the important idea: `one test should check one thing`.
And in the case if you want to check the work of your service on a different set of parameters simultaneously it will be better to divide them into separate blocks.

</p></details>
<br />

**The preferred variant of dividing into separate blocks `it`:**

```
const assert = require('chai').assert;
const complex = require('./complex');

describe("Summarize complex numbers", () => {

  it("Summarize complex number with zero", function() {

    let complexNumber = {re:2, im:3};

    assert.deepEqual(complex.complexSum(complexNumber, 0), complexNumber);
  });

  it("Summarize 2 complex numbers", function() {

    let complexNumber = {re:2, im:3};

    assert.deepEqual(complex.complexSum(complexNumber, {re:1, im:4}), {re:3, im:7});
  });

});
```	

![](https://github.com/PerminovaAnastasia/Images/blob/master/test-result-3.1.png)

As you can notice, suggested separation allows you to save time, which will be required to determine what case failed the intended test.

### The main conclusions:

- Tests are not needed to prevent *all* potential errors - don't take away the latest from testers!
But you as a developer are responsible for the quality of the code.

- Your code always changes: a new functionality is added, the existing code is refactored.
And tests are your guarantees that these changes didn't harm the current state of the application.

- Tests reduce the development cycle time of the program. How many days and how long does it take to check your functionality? A couple of days, a week or weeks?
But the detected error was related to the fact, that you didn't take into account some simplest cases, made a mistake in working with the object, etc.
You will correct - and send for review. And again all around.

![](https://github.com/PerminovaAnastasia/Images/blob/master/Diagram.png)

- When you write a test, you look at your from other perspective and notice unnecessary or missing moments - it helps to improve the structure.
- Sometimes they can help to simplify understanding the logic of a piece of the code that is under test.
