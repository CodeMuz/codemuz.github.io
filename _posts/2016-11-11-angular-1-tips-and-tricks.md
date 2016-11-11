---
layout: post
published: false
title: Angular 1 - Tips and Tricks
---
## 1. Pre-Fetch Users Data into Angulars HTTP cache

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
