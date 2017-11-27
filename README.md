## Web part of workflow.

We need to implement landing web page where user lands on during profile matching redirects flow and has ability to return to the App by a deep link (SDK Workflow step 4).

![SDK Workflow](https://s3-eu-west-1.amazonaws.com/allunite-main/doc/Workflow.jpg)
To secure user's confidence in the process we advise to locate static web page on the customer's own domain.

Landing page doesn't contain any functionality, but redirect.

(Android) OS opens deeplink without user confirmation.

(iOS specific)
In an ideal world we would redirect directly to a deeplink to return to the App immediately, but due to confirmation message(iOS >= 10.3.1) we need to create a page that gives users a chance to try it again.

There are two options - make separate static page or inject startup script to existing page (main page for instance) that replaces content on receiving specific parameter in query string.   
We propose to use the first option.

The page should follow logic below:

* Try to open the deep link to App immediately after loading.  
When content loaded script tries to go to deeplink received in the query string to return back to App.
So just after opening the page user sees confirmation message "Open this page in 'H&M'?" and on [Yes] returns to App or leave on the page on [No].

* Explain the necessity to click [OK] on confirmation dialog in case of no success on the previous step and show the button that opens the deep link to allow the user to back to App manually.  
On the page user has two options - click the button to repeat previous step and this time agree with the proposal or close the browser and return (or not) to the App manually.

Deep link can be hardcoded on the page or received in the query string.

We also recommend to exclude this page from search indexes using disallow option in robots.txt file.

See the sample page below.

```html
<!doctype html>
<html lang="da-DK" class="site-frontpage">
<head>
  <meta charset="UTF-8">
  <title>AllUnite SDK Landing Page</title>
  <style>
  body {
    font-family: AlrightSansLT,"helvetica neue",helvetica,arial,sans-serif;
    background-color: #fe8705;
    color: #fff;
  }
  img {
    position:relative;;
    left: 50%;
    margin-left: -60px;
    margin-bottom: 8px;
  }
  div {
    text-align: center;
    font-size: 0.7em;
  }
  .button {
    display:inline-block;
    text-decoration: none;
    position:absolute;
    top:50%;
    left: 50%;
    margin-left: -100px;
    height: 2.2rem;
    line-height: 2.1rem;
    font-size: 0.9rem;
    padding: 0 1rem;
    border-width: .2rem;
    border-radius: 1.6rem;
    min-width: 10rem;
    background: transparent;
    border-style: solid;
    border-color: #fff;
    color: #fff;
  }
  </style>
  <script type="application/javascript">
    function getDeepLink() {
      var userAgent = navigator.userAgent || navigator.vendor || window.opera;

      if (/iPad|iPhone|iPod/.test(userAgent) && !window.MSStream) {
          return "my-app-ios://allunite-sdk-mode?enable=true";
      } 
      return "my-app://allunite-sdk-mode?enable=true";
    }
  </script>
</head>
<body>
  <div>Please click the button below to return to the App</div>
  <div><a id="btn" class="button">BACK TO APP</a></div>

  <script type="application/javascript">
    var link = getDeepLink();
    window.location.href = link;
    document.getElementById('btn').href = link;
  </script>
</body>
</html>
```