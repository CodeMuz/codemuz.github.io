---
layout: post
published: true
title: Angular 1 - Tips and Tricks
---
## 1. Pre-Fetch Users Data into Angular HTTP cache

Whichever token authentication you're using with Angular, it's a good idea to load data that you know the User is going to need on the front end. As long as the browser is not already consumed with rendering the page with lots of digest cycles. The Idea is to populate the services before the User requests them, so that when they call the controllers that make use of the services then the HTTP promise instantly returns as the HTTP cache will have already populated. Here is an example of a user logging in, then their profile data is fetched (Account.getProfile()) ready for controllers to make use of throughout the whole application.

~~~
$scope.login = function () {

            loginLoadingBar.style.visibility = "visible";

            $auth.login($scope.newuser)
                .then(function () {
                    Toast.showToast('You have successfully signed in');

                    //cache the users HTTP requests
                    Account.getProfile();
                    Account.getUserData();

                    updateUser();

                    $state.go('mainView');

                })
                .catch(function (error) {
                    Toast.showToast(error.data.message);
                    loginLoadingBar.style.visibility = "hidden";
                });
        };
~~~

## 2. Use Angular Material $mdMedia Directive to reduce asset count

If you're using Angular Material, a tip I've been using for a while now is using the $mdMedia directive to conditionally load elements depending on the page width.

For example:
~~~
<a ng-if="$mdMedia('gt-md')" style="width:728px" class="affiliate-link large" href="http:www.exampleimage.com/affiliatelink" target="_BLANK"><img src="http:www.exampleimage.com/pic.jpg" border=0></a>
~~~

This will only load the image if the page is 1280px minimum reducing bandwidth, and in this case impressions on links which can scew with your site analytics and statistics. There is a long list of these avaiable here: [NgMedia Docs](https://material.angularjs.org/latest/api/service/$mdMedia). 

## 3. Karma Testing Traps

When writing unit tests for your Angular controllers be careful when assessing arrays for equality. For example the following will return a fail:

~~~
expect(transformed.data).toBe(['one', 'two', 'three']);
~~~

However a correct way to test if arrays are equal is to use:

~~~
expect(transformed.data).toEqual(['one', 'two', 'three']);
~~~

Also you may have programmed your application to protect it from being reverse engineered or being scraped. If these measures occur before the build step then it's a good idea to disable them when unit testing. An example is this vanilla JS command I use to change the location so that a page cannot be loaded into an IFrame. This will prevent PhtantomJS from working as expected and needs to be disabled for testing:

~~~
if (window !== top) top.location = window.location
~~~
