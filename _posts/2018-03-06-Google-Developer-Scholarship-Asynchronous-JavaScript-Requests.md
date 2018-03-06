---
title: Google Developer Scholarship&#58; Asynchronous JavaScript Requests
layout: post
---

After [finishing up](https://github.com/krbnite/GoogleDeveloperScholarship2018/tree/master/Round1/Supplementary-Courses/03__JavaScript-and-the-DOM)
the course on [JavaScript and the DOM](https://www.udacity.com/course/javascript-and-the-dom--ud117)
from Udacity, I figured I'd get back on the main track of the web development course.  It was refreshing to
get a sense of how much I had learned from doing these side courses...and yet I still could not understand
the solution to one of the [service worker]() quizzes. Like, at all.  

Basically, when registering the service
worker, we use a path that does not appear to exist in the code tree.  Yesterday, I saw someone discussing this on the forums:
apparently, it has something to do with [gulp](https://gulpjs.com/) creating the file...which actually
doesn't help clarify things for me at all! (Will ave to look more into gulp.) Anyway, being stubborn 
and not yet wanting to seek help, I took another side course,
hoping that I'd get it all after some more practice with creating JavaScript apps: 
[Asynchronous JavaScript Requests](https://www.udacity.com/course/asynchronous-javascript-requests--ud109)

Long story, short: this course did not bring me closer to understanding the service worker issue. That said,
it was an excellent course, and I learned a lot!

* [Notes on the XHR obejct](https://github.com/krbnite/GoogleDeveloperScholarship2018/blob/master/Round1/Supplementary-Courses/04__Asynchronous-JavaScript-Requests/01__Ajax-with-XHR.md)
* [Notes on jQuery](https://github.com/krbnite/GoogleDeveloperScholarship2018/blob/master/Round1/Supplementary-Courses/04__Asynchronous-JavaScript-Requests/02__Ajax-with-jQuery.md)
* [Notes on the Fetch API](https://github.com/krbnite/GoogleDeveloperScholarship2018/blob/master/Round1/Supplementary-Courses/04__Asynchronous-JavaScript-Requests/03__Ajax-with-Fetch.md)

In the course, we registered for API keys at [Unsplash](https://unsplash.com/) and the 
[New York Times](https://www.nytimes.com/), then created a simple HTML/JavaScript app using the
various approaches to Ajax covered in the course (
[XHR](https://github.com/krbnite/GoogleDeveloperScholarship2018/tree/master/Round1/Supplementary-Courses/04__Asynchronous-JavaScript-Requests/course-ajax/lesson-1-async-w-xhr), 
[jQuery](https://github.com/krbnite/GoogleDeveloperScholarship2018/tree/master/Round1/Supplementary-Courses/04__Asynchronous-JavaScript-Requests/course-ajax/lesson-2-async-w-jQuery), 
[Fetch](https://github.com/krbnite/GoogleDeveloperScholarship2018/tree/master/Round1/Supplementary-Courses/04__Asynchronous-JavaScript-Requests/course-ajax/lesson-3-async-w-fetch)).  These 
apps were essentially the first apps I've ever created, so it was exciting and enlightening.  What I found really intriguing
is just how simple it is to get off the ground: the app consists of a single HTML file and a single
JavaScript file, both of which are incredibly short and to the point!

Here is the 3rd version of the app that we created, using the Fetch API and a bunch of ES6 
conventions (e.g., promises, arrow functions).

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Make Asynchronous Requests</title>
    <link href="https://fonts.googleapis.com/css?family=Open+Sans" rel="stylesheet">
    <link href="../css/styles.css" rel="stylesheet">
</head>
<body>

    <header class="masthead">
        <h1>What are you interested in today?</h1>

        <div class="site-container">
            <form id="search-form" action="#">
                <label for="search-keyword" class="visuallyhidden">What are you interested in today?</label>
                <input id="search-keyword" type="text" name="search-keyword" placeholder="e.g. Android" required>
                <input id="submit-btn" type="submit" value="Submit">
            </form>
        </div>
    </header>

    <div class="site-container">
        <div id="response-container"></div>
    </div>

    <script src="clientid.js"></script>
    <script src="app.js"></script>
</body>
</html>
```

```js
(function () {
  // variables, constants
  const form = document.querySelector('#search-form');
  const searchField = document.querySelector('#search-keyword');
  let searchedForText;
  const responseContainer = document.querySelector('#response-container');

  // functions
  //--Unsplash
  function addImage(data) {
    let htmlContent = '';
    const firstImage = data.results[0];
    if (firstImage) {
        htmlContent = `<figure>
            <img src="${firstImage.urls.small}" alt="${searchedForText}">
            <figcaption>${searchedForText} by ${firstImage.user.name}</figcaption>
        </figure>`;
    } else {
        htmlContent = 'Unfortunately, no image was returned for your search.'
    }
    responseContainer.insertAdjacentHTML('afterbegin', htmlContent);
  }

  //--NYT
  function addArticles (data) {
    //debugger;//initially used to monitor response in DevTools
    let htmlContent = '';
    const copyright = data.copyright;
    const firstArticle = data.response.docs[0];
    if(data.response && data.response.docs && data.response.docs[0]) {
      htmlContent = `
      <table>
        <tr><td>
        <h1>${firstArticle.headline.main}</h1>
        <h3>${firstArticle.byline.original}</h3>
        <h4>${firstArticle.pub_date.substring(0,10)}</h4>
        ${firstArticle.snippet} ... 
        (<a href="${firstArticle.web_url}">Continue reading @ NYT</a>)
        <br>${copyright}<br>
        </td></tr>
        <tr><td>
        <h2>More Articles...</h2>` + '<ul>' + data.response.docs.map(
        article => `<li class="article">
          <h2><a href="${article.web_url}">${article.headline.main}</a></h2>
          <p>${article.snippet}</p></li>`).join('') + `</ul>
        </td></tr>
        </table>` ;
    } else {
      htmlContent = '<div class="error-no-image">No images available</div>'
    }
    responseContainer.insertAdjacentHTML('beforeend', htmlContent);
  }

  //--ErrFcn
  function requestError(e, part) {
        console.log(e);
        responseContainer.insertAdjacentHTML('beforeend', 
          `<p class="network-warning">Oh no! There was an error making a request 
          for the ${part}.</p>`);
  }

  // Main
    form.addEventListener('submit', function (e) {
        e.preventDefault();
        responseContainer.innerHTML = '';
        searchedForText = searchField.value;
  //--Unsplash
    fetch(`https://api.unsplash.com/search/photos?page=1&query=${searchedForText}`, {
      headers: {
        Authorization: `Client-ID ${unsplashId}`
      }
    }).then(response => response.json())
    .then(addImage)
    .catch(e => requestError(e, 'image'));

  //--NYT
    fetch(`http://api.nytimes.com/svc/search/v2/articlesearch.json?q=${searchedForText}&api-key=${nytArticleId}`)
    .then(response => response.json())
    .then(addArticles)
    .catch(e => requestError(e, 'article'));

  });

})();
```

# Some References
* Udacity: [Asynchronous JavaScript Requests](https://www.udacity.com/course/asynchronous-javascript-requests--ud109)
* https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest
* https://developers.google.com/apis-explorer/#p/
* https://www.programmableweb.com/apis/directory
* https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy
* https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS
* https://unsplash.com/developers
* https://developer.nytimes.com/
* https://developers.google.com/web/tools/chrome-devtools/javascript/
* https://developers.google.com/web/tools/chrome-devtools/javascript/breakpoints
* https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch
