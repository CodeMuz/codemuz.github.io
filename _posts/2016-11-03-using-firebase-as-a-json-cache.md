---
layout: post
published: true
title: Using Firebase as a JSON Cache
---
## Using A JSON Cache service

Good caching can reduce load on API's, save bandwidth costs, improve page load times and ultimately gives a better user experience. Any call can be cached on the server or in the browser, one way to reduce the load for a non dynamic server call for some JSON is to load the data into a JSON storage online as a intermediary stage. Your sever will essentially be shadowing this JSON cache and pushing to it when necessary, therefore all the logic performed by your models needed to generate the JSON endpoint can be performed when needed and not every time a user requests the data. 

I'll show an example using Firebase (https://firebase.google.com/), which is a service which allows you to read and write rapidly and concurrently to JSON storage online. 

The first step is to create a Storage container using the Firebase link above. 

~~~
service firebase.storage {
  match /b/myapp.appspot.com/o {
    match /{allPaths=**} {
      allow read, write: if request.auth != null;
    }
  }
}
~~~

Make sure to set the rules, for debugging it can be a good idea to simply open up read and write for everyone, make sure to restrict them for production.

The next stage is for you server API to push to the Firebase Storage. Here is an example of PHP Laravel Middleware using the observer pattern to push JSON to firebase storage, the API is first calling itself to get the response and then caching it. The logic will be similar for different languages and frameworks.

~~~
$request = Request::create('/api/v2/sessiondata', 'GET');
        $userData = Route::dispatch($request);
        $json = $userData->getOriginalContent();

        $client = new Client(); //GuzzleHttp\Client
        $firebaseResponse = $client->request('PUT', 'https://herokuapp174.firebaseio.com/data.json', ['json' => json_decode($json)]);
    ~~~
    
    
    Finally the front-end application, whether you have a mobile app or a web application, needs to load the firebase plugin API and make a GET request to this https://herokuapp174.firebaseio.com/data.json. You may also need to authenticate correctly. The benefit of this system is that any encryption or expensive database calls are no longer made by the server, it's essentially a backup data store which pushes whenever an admin updated a route with the middleware attached.
    
    Here is an example for an Angular app consuming the Firebase JSON cache with CryptoJS decryption so that the data is at least obfuscated:
    
    ~~~
    return $http.jsonp('https://herokuapp174.firebaseio.com/data.json?callback=JSON_CALLBACK')
                    .success(function(response){

                        var responseString = JSON.stringify(response);
                        var message = CryptoJS.AES.decrypt(responseString, '~' + appToken, {format: CryptoJSAesJson}).toString(CryptoJS.enc.Utf8);
                        var data = JSON.parse(message);

                        data = FestivalTransformer.transformArray(festivals);

                        callback(data);

                    });
                    ~~~
    
