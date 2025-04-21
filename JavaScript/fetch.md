How to Use Fetch with async/await

The Fetch API is the default tool for performing network operations in web applications. Although fetch() is generally easy to use, there are some nuances to be aware of.

In this post, you'll find the common scenarios of how to use fetch() with async/await syntax. You'll understand how to fetch data, handle fetch errors, cancel a fetch request, and more.

Before I go on, let me recommend something to you because I know that the path to becoming a professional JavaScript developer isn't easy...

I recommend certificates.dev’s Official JavaScript Certification — a practical, expert-led program (created in collaboration with experts like Evan You) perfect for mastering JavaScript. Earning a professional certificate sets you apart, potentially opening doors to better job opportunities, higher salaries, and career advancement.

Table of Contents
1. Intro to fetch()
2. Fetching JSON
3. Handling fetch errors
4. Canceling a fetch request
5. Parallel fetch requests
6. Summary

1. Intro to fetch()
The Fetch API accesses resources across the network. You can make HTTP requests (using GET, POST and other methods), download, and upload files.

To start a request, call the special function fetch():

const response = await fetch(resource[, options]);

which accepts 2 arguments:

- resource: the URL string, or a Request object
- options: the configuration object with properties like method, headers, body, credentials, and more.
- fetch() starts a request and returns a promise. 
- When the request completes, the promise is resolved with the Response object. If the request fails due to some network problems, the promise is rejected.

- async/await syntax fits great with fetch() because it simplifies the work with promises with syntactic sugar.

When the request completes, response is assigned with the response object of the request. Let's see in the next section how to extract useful data, like JSON or plain text, from the response.

2. Fetching JSON

The response object, returned by the await fetch(), is a generic placeholder for multiple data formats.

For example, you can extract the JSON object from a fetch response:

```bash
async function fetchMoviesJSON() {
  const response = await fetch('/movies');
  const movies = await response.json();
  return movies;
}

fetchMoviesJSON().then(movies => {
  movies; // fetched movies
});

```

<code>response.json()</code> is a method of the Response object that allows a JSON object to be extracted from the response. The method returns a promise, so you have to wait for the JSON: <code>await response.json().</code> 

The Response object offers a lot of useful methods (all returning promises):

- response.json() returns a promise resolved to a JSON object
- response.text() returns a promise resolved to raw text
- response.formData() returns a promise resolved to FormData
- response.blob() returns a promise resolved to a Blob (a file-like object of raw data)
- response.arrayBuffer()() returns a promise resolved to an ArryBuffer (raw generic binary data)

3. Handling fetch errors

When I was familiarizing with fetch(), I was surprised that fetch() doesn't throw an error when the server returns a bad HTTP status, e.g. client errors (400–499) or server errors (500–599).

4. Canceling a fetch request

Unfortunately, fetch() API alone doesn't allow to cancel a fetch request once started. To cancel a fetch request you need an additional tool AbortController.

Connecting fetch() and AbortController requires 3 steps:

```bash

// Step 1: instantiate the abort controller
const controller = new AbortController();

// Step 2: make the fetch() aware of controller.signal
fetch(..., { signal: controller.signal });

// Step 3: call to cancel the request
controller.abort();

```

A) Before starting the request, create an abort controller instance: controller = new AbortController().

B) When starting the request properly, use the options argument of fetch(url, { signal: controller.signal }) and set signal property to be controller.signal.

C) Finally, if you need to cancel the request, just call controller.abort() method.

For example, let's implement 2 buttons that control a fetch request. Clicking the button Fetch movies starts a fetch() request, while clicking Cancel fetch aborts the request in progress:

5. Parallel fetch requests

To perform parallel fetch requests use the Promise.all() helper function.

Let's start 2 parallel requests to fetch movies and categories:

```bash
async function fetchMoviesAndCategories() {
  const [moviesResponse, categoriesResponse] = await Promise.all([
    fetch('/movies'),
    fetch('/categories')
  ]);

  const movies = await moviesResponse.json();
  const categories = await categoriesResponse.json();

  return [movies, categories];
}

fetchMoviesAndCategories().then(([movies, categories]) => {
  movies;     // fetched movies
  categories; // fetched categories
}).catch(error => {
  // /movies or /categories request failed
});
```

<code>await Promise.all([...])</code> starts fetch requests in parallel, and waits until all of them are resolved.

If any request fails, then the whole parallel promise gets rejected right away with the failed request error.

In case if you want all parallel requests to complete, despite any of them fail, consider using Promise.allSettled().