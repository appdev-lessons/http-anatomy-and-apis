# Anatomy of an HTTP request

[Here is a video for this lesson](https://share.descript.com/view/jql3w76ls7h). You should not rely entirely on the video. PLEASE READ the below lesson as you are going through the steps, since there is much more detail in the text than in the video.

## The simplest possible HTTP request

The fundamental unit of a web application is the **HTTP request**. Any time you type a URL into your browser and press <kbd>return</kbd>, or click on a link, or submit a form, you are placing an HTTP request (or just "request", for short).

![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1688554262/request-response_i0gzs7.png)
{: .bleed-full }

- The word "**client**" refers to the program that _sends_ the HTTP request.

  "Client" is also often used to refer to the computer that the program is running on.
- The word "**server**" refers to the program that _receives_ the HTTP request, and sends back an **HTTP response** (or just "response", for short).

    "Server" is also often used to refer to the computer that the program is running on.
- These two computers are able to talk to each other no matter where they are in the world because they are part of a giant network of interconnected computers: "the internet".

An HTTP request is a plain text file that looks like this:

```http
GET /wiki/Chicago HTTP/1.1
Host: en.wikipedia.org
```

- The first line is called the **request line**. It has three parts:
	- The **verb** — in this example, `GET`. The verb indicates what the client is trying to _do_ — read some information (`GET`), create some information (`POST`), update some information (`PATCH`), or delete some information (`DELETE`). (There are a few other HTTP verbs, but these four cover 99.99% of our needs.)
	- The **resource path** (or  just "path", for short) — in this example, `/wiki/Chicago`.
	- The **HTTP version** — in this example, `HTTP/1.1`. (This is the predominant version and we don't need to worry about any others right now.)
- Starting on the second line are a list of key-value pairs called **headers**. The `Host` header is required: it specifies the address of the server that we're sending the request to — in this example, `en.wikipedia.org`.
		
If you send this request, it will find its way to Wikipedia's server through a system known as the Domain Name System (DNS). Wikipedia will look at the path, and based on that decide what to send back as the response. The response is also a plain text file — in this example, it would look something like this:

<aside markdown="1">
We're not going to delve into the workings of DNS for now; we'll just trust that it works. [If you're curious, though, you can read more about DNS here.](https://www.cloudflare.com/learning/dns/what-is-dns/).
</aside>

```http
HTTP/1.1 200 OK
Content-Type: text/html

<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Chicago - Wikipedia</title>

  <!-- Hundreds more lines of HTML describing Chicago -->
```

- The first line of the response is the **status line**.  It has three parts:
    - The HTTP version — in this example, `HTTP/1.1`.
    - A **status code** — in this example, `200`. Other common status codes are `404` (resource not found), `500` (internal server error), `403` (don't have permission to access), `302` ("redirect", or "keep on moving to a different URL"), etc.
    - A **reason phrase** that gives a short textual description of the status code — in this example, `OK`. 
- Starting on the second line are a list of key-value pairs called **headers**, just as there were in the request. The `Content-Type` header should be included to let the client know what kind of document is being returned — in this example, text that is HTML.
- After the headers comes a blank line.
- Everything after the blank line is the **body** of the response. In this example, the source code for a whole HTML document.

Voilà! A complete HTTP **request** and **response** cycle — it turns out that both are just plain text files sent back and forth!

If the body of the response contains HTML source code, and if the client happens to know how to interpret it (like a browser does), it will render it in a human friendly way:

![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1688454683/wikipedia-chicago_zxh58m.jpg)

Here, [you try it](https://en.wikipedia.org/wiki/Chicago)!

- Select all that are true:
- `GET` is an HTTP "verb".
  - Yes!
- An HTTP response contains a body with the requested page's HTML.
  - Yes!
- We send HTTP responses when we visit a URL in our browser
  - Not quite. Re-read above.
- An example resource path is `www.google.com`
  - Not quite. Re-read above.
- The request line in an HTTP request contains a verb, the resource path, and the HTTP version
  - Yes!
- The request line in an HTTP request contains a verb, the resource path, and the HTML body
  - Not quite. Re-read above.
{: .choose_all #http_quiz title="HTTP quiz" points="3" answer="[1,2,5]" }

## Placing requests with a browser

So: when we type a URL (Uniform Resource Locator, also known as Uniform Resource Identifier — URI) like `https://rubyweekly.com/issues/660` into a browser's address bar and press <kbd>return</kbd>, or click on an `<a>` tag, what all is the browser doing?

First, the browser parses the URL into parts:

![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1688556191/anatomy-of-url_arrpms.png)

- The browser starts on the left and looks for `://`. Everything before that is the **scheme**.
 
  The scheme can either be `http` or `https`. With `http`, the request is sent from client to server in plain text. That means all the computers along the way could, in theory, read the contents of the request; which isn't very secure.

  With `https`, the request is first encrypted by the client and then decrypted by the server upon arrival, so the computers along the way can't read the contents (except for the `Host` header, which is required to send the request along to the right place). Nowadays, we should almost always be using `https`.
- After the `://` up until the first slash (`/`) it encounters is the **host**.
- Everything from the first `/` onwards is the **resource path**.

<aside markdown="1">
There are other schemes, like `ftp` (for transferring files), `ssh` (for securely connecting to the command line on a remote computer), and `ws` (WebSockets, for two way real-time communication between client and server). We don't need to worry about those for now.
</aside>

With the host and the resource path, the browser can then assemble an HTTP request file:

```http
GET /issues/660 HTTP/1.1
Host: rubyweekly.com
```

Since we used `https` rather than `http`, the browser will encrypt the request and send it along. It then replaces the contents of the viewport with the HTML from the response (if the request was successful).

But wait — how did the browser know the **verb** should be `GET`?

It turns out that the address bars of browsers can _only_ place `GET` requests. Similarly, when a user clicks on an `<a>` link, the browser assembles a `GET` request out of the value of the `href` attribute, sends it, and replaces the contents of the viewport with the HTML from the body of the response.

This makes sense because we're usually trying to _read_ information when we're navigating around the web by clicking on links. But eventually we'll want to make `POST`, `PATCH`, and `DELETE` requests as well (to _create_, _update_, and _delete_, respectively).

To make these other kinds of requests, we'll have to use other clients besides the browser's address bar and `<a>` tags.

- The URL bar in a browser can place... 
- HTTP `GET` requests.
  - Yes!
- HTTP `POST` requests.
  - Not quite.
- Any request.
  - Not quite.
{: .choose_best #get_request title="Browsers can place..." points="1" answer="1" }

- What is a `PATCH` request used for?
- Creating information.
  - Not quite. That's `POST`.
- Reading information.
  - Not quite. That's `GET`.
- Updating information.
  - Yes!
- Deleting information.
  - Not quite. That's `DELETE`.
{: .choose_best #patch_request title="A PATCH request is..." points="1" answer="3" }

## Placing requests with Ruby

Ruby has many handy libraries for placing HTTP requests. I like the [http.rb gem](https://github.com/httprb/http). Let's see how it works:

```ruby
# Pull in the HTTP class
require "http"

# Place the request and store the return value in x
x = HTTP.get("https://en.wikipedia.org/wiki/Chicago")

# Print the body of the response
puts x.to_s
```
{: .repl #http_rb_1 title="http gem" points="1"}

- The http.rb gem includes a class, `HTTP`, that has a class method `get` (and `post`, `patch`, `delete`, etc, for the other verbs).
- The argument to `get()` should be a `String` containing the full URL of the resource you want to request.
- The `get` method returns an object that represents the HTTP response.
- The `HTTP::Response` object has a method called `to_s` which will return the contents of the body portion of the response as a `String`.

The `HTTP::Response` object also has methods that allow us to inspect the `headers`, status `code`, etc. But we're mostly interested in the contents of the body, so `to_s` is what we'll use most often.

The ability to place HTTP requests with Ruby is _incredibly_ powerful. It allows our programs to interact with the outside world. What we just did, pulling in the contents of a web page, is known as **web scraping**. We could then use some techniques to try and extract valuable information out of the `String`, which the rest of our program can use.

## Intro to APIs

And, even better, now that we can place HTTP requests with Ruby, we can use Application Programming Interfaces (APIs): a parallel internet designed for programs to use rather than humans.

### Pirate Weather

Imagine we have a latitude and longitude, and we want to get the current weather and future forecast at that location, so that our program can do something interesting — for example, if it's going to rain within the next 12 hours at their location, maybe we want to warn the user to take an umbrella with them before leaving the house.

The weather conditions and forecast at every location on the planet is not something that is feasible for us to collect. Are we out of luck, then? Can we not write our Umbrella program?

Well, now that our programs can communicate with the outside world, maybe not. We can outsource the work of collecting all that weather data to someone else, and pull it in to our program with an HTTP request.

[Here is a web page that shows the weather forecast at The Merchandise Mart in Chicago.](https://merrysky.net/forecast/merchandise%20mart/us) The URL is `https://merrysky.net/forecast/merchandise%20mart/us`, and we could pull the source code of the HTML in to our program:

```ruby
# Pull in the HTTP class
require "http"

# Place the request and store the return value in x
x = HTTP.get("https://merrysky.net/forecast/merchandise%20mart/us")

# Print the body of the response
puts x.to_s
```
{: .repl #merry_sky title="Merry Sky" points="1"}

If I'm interested in extracting just the current temperature, that HTML source code is not very helpful. The vast majority of HTML is `<div>`s and `class`es intended for visual presentation; the actual content is buried in the div soup, and hard for us to extract reliably. Sometimes the content isn't even in the source code, because it's loaded in dynamically later.

Fortunately, there's a parallel version of that same page but in a format that is much better for programs to understand. Try visiting this URL in a browser tab:

`https://api.pirateweather.net/forecast/REPLACE_THIS_WITH_API_KEY/41.8887,-87.6355`

Replace the second path segment, `REPLACE_THIS_WITH_API_KEY`, with the Pirate Weather API key in the Canvas assignment for this reading. We have to include this key in the request so that Pirate Weather can meter our usage and, eventually, bill us for it.

After you put the API token in the second path segment and visit that URL, you should see information related to the weather at the Merchandise Mart. It's not very nice to look at for us, but trust me — it's _great_ for our Ruby programs to consume.

This format is called JSON (JavaScript Object Notation), and unlike HTML, it doesn't have any code dedicated to styling. JSON is meant to efficiently structure content, and only content, in a way that's easy for programs to parse and use.

### Hoppscotch: a client designed for learning

As we're working with APIs, we're going to spend a lot of time looking at JSON. But the browser isn't the greatest for displaying JSON, and as we already learned, browser address bars can't make `POST`, `PATCH`, and `DELETE` requests, which we will eventually need to do.

To make it easier to learn about APIs, there are clients designed specifically for exploring APIs. One that I like is called Hoppscotch. Let's give it a try right now. Head over to [https://hoppscotch.io](https://hoppscotch.io/).

Within the app, next to the `GET`, there is an address bar. Paste in the URL that we want to place a request to:

`https://api.pirateweather.net/forecast/REPLACE_THIS_WITH_API_KEY/41.8887,-87.6355`

And click "Send". In the pane at the bottom, you should see the response body (you can also explore other parts of the response — headers, etc):

![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1688593198/hoppscotch-1_mh2zj4.png)
{: .bleed-full }

Hoppscotch nicely indents the JSON for us, so that it's easier to understand. 

- What does the JSON remind you of? It's a list of key-value pairs, just like a Ruby... 
- `Array`!
  - Not quite.
- `String`!
  - Not quite.
- `Hash`! 
  - Yes!
{: .choose_best #json_looks_like title="JSON looks like..." points="1" answer="3" }


And there are some `Array`s in there too. This is why we spent so much energy practicing deeply nested `Hash`/`Array` structures.

If we explore the JSON using Hoppscotch, we can see that there's a key `"currently"` that is associated with an inner hash. And within that inner hash, there's a key called `"temperature"` that has the info we want.

### Accessing values within JSON

Now, let's try printing the temperature within our Ruby program.

```ruby
require "http"

# Hidden variables
pirate_weather_api_key = ENV["PIRATE_WEATHER_API_KEY"]

# Assemble the full URL string by adding the first part, the API token, and the last part together
pirate_weather_url = "https://api.pirateweather.net/forecast/" + pirate_weather_api_key + "/41.8887,-87.6355"

# Place a GET request to the URL
x = HTTP.get(pirate_weather_url)

# Print the body of the response
puts x.to_s
```
{: .repl #pirate_weather_1 title="Pirate Weather, 1" points="1"}

This is great! We can see the temperature in that `String`. But, how do we get just the temperature out?

If only we could convert the `String` containing JSON into actual `Hash`es & `Array`s — 

- then we could easily dig the data we want out of it using... 
- `.sample`
  - Not quite. `.sample` randomly selects elements, we want to select specific ones.
- `.fetch` and `.at`
  - Yes! `.fetch` operates on `Hash`es and `.at` on `Array`s
- `.gsub`
  - Not quite. That's a technique for "global substitution" on `String`s
{: .choose_best #how_dig title="How do we dig?" points="1" answer="2" }

Happily, we can! Ruby includes a class called `JSON` that can convert a `String` containing JSON into Ruby `Hash`es & `Array`s:

```ruby
require "http"

# Hidden variables
pirate_weather_api_key = ENV["PIRATE_WEATHER_API_KEY"]

# Assemble the full URL string by adding the first part, the API token, and the last part together
pirate_weather_url = "https://api.pirateweather.net/forecast/" + pirate_weather_api_key + "/41.8887,-87.6355"

# Place a GET request to the URL
raw_response = HTTP.get(pirate_weather_url)

require "json"

parsed_response = JSON.parse(raw_response)

pp parsed_response
```
{: .repl #pirate_weather_2 title="Pirate Weather, 2" points="1"}

We're so close! Let's use `fetch` to get the current temperature:

```ruby
require "http"

# Hidden variables
pirate_weather_api_key = ENV["PIRATE_WEATHER_API_KEY"]

# Assemble the full URL string by adding the first part, the API token, and the last part together
pirate_weather_url = "https://api.pirateweather.net/forecast/" + pirate_weather_api_key + "/41.8887,-87.6355"

# Place a GET request to the URL
raw_response = HTTP.get(pirate_weather_url)

require "json"

parsed_response = JSON.parse(raw_response)

currently_hash = parsed_response.fetch("currently")

current_temp = currently_hash.fetch("temperature")

puts "The current temperature is " + current_temp.to_s + "."
```
{: .repl #pirate_weather_3 title="Pirate Weather, 3" points="1"}

### Google Maps Geocoding

Let's look at another API: the Google Maps Geocoding API. This API is useful when you have the name or street address of a place, and you want to know things like the latitude/longitude, state, or neighborhood.

Here is the URL (or "API endpoint") for getting that information from Google:

`https://maps.googleapis.com/maps/api/geocode/json?address=Merchandise%20Mart%20Chicago&key=REPLACE_THIS_WITH_YOUR_API_KEY`

This URL looks a little different than the Pirate Weather URL:

- The host is `maps.googleapis.com`.
- The resource path is `/maps/api/geocode/json`.
- After that, there's something new: a **query string**. This is the last part of the anatomy of a URL.

  Query strings start with a question mark (`?`) and contain a list of key-value pairs. Each key-value pair is separated from the next by an ampersand (`&`). Each key and value are separated from one another by an equals sign (`=`).
	
	In this example, there are two key-value pairs in the query string:
	
	- `address=Merchandise%20Mart%20Chicago`
	- `key=REPLACE_THIS_WITH_YOUR_API_KEY`

We can modify the value for both, but not the key itself.

The value for `address` is how you tell Google which place you are trying to geocode. Technically, you're supposed to replace spaces with `%20`, but Google can figure it out if you don't. Try replacing it with a different place.

The value for `key` must be a valid API key (found on Canvas).

Try it in a browser tab, or [click this link to try it in Hoppscotch.](https://hopp.sh/r/8bAQfMy2bG0Z)

Now that we've established the URL of the page that has the info we want in JSON, can you try pulling it in to a Ruby program, parsing it, and digging through it to retrieve the latitude and longitude?

Open up the next Canvas assignment called Umbrella. There, you will set up a Codespace to continue with this exercise.

### Twilio

There's a company called Twilio that provides lots of services related to telephone calls and text messaging; but for programs to use, rather than humans.

If I wanted to send a text message using Twilio, I would have to send the following HTTP request:

```http
POST /2010-04-01/Accounts/MY_TWILIO_ID_GOES_HERE/Messages HTTP/1.1
Host: api.twilio.com
Authorization: Basic MY_ENCODED_TWILIO_ID_AND_API_KEY_GOES_HERE
Content-Type: application/x-www-form-urlencoded

From=+13126636198&To=+19876543210&Body=A+message+from+Ruby!
```

This is a more complicated request than the ones we saw above:

- It uses `POST`, not `GET`.
- It includes additional headers (`Authorization`, `Content-Type`).
- The request has a body, after the blank line following the headers. (Unlike `GET` requests, `POST` requests can have a body, since they are intended to send information to be stored.)

The body is a list of key-value pairs that Twilio is expecting in order to send the message — which number to send it from, which number to send it to, and the message itself.

I already went to the Twilio developer website, signed up for an account, added my credit card to it so they can bill me, and copy-pasted my credentials. So we are now ready to make the above request in Ruby like this:

```ruby
require "http"

# Hidden variables
twilio_id = ENV["TWILIO_ID"]
twilio_api_key = ENV["TWILIO_API_KEY"]
twilio_sending_number = ENV["TWILIO_SENDING_NUMBER"]

# twilio_id, twilio_api_key, twilio_sending_number
# contain sensitive credentials that hackers would love to steal so are hidden for security reasons.

# Uncomment this and put your own number in — not mine!
# your_number = "+19876543210"

# Assembling the scheme, host, and resource path
create_sms_url = "https://api.twilio.com/2010-04-01/Accounts/" + twilio_id + "/Messages"

# The request requires a specific Content-Type header
twilio_headers = {"Content-Type" => "application/x-www-form-urlencoded"}

# Modify the value of the "Body" key below and run the program to send a text message!

HTTP.basic_auth({ :user => twilio_id, :pass => twilio_api_key})
  .headers(twilio_headers)
  .post(create_sms_url, :form => {
    "From" => twilio_sending_number,
    "To" => your_number,
    "Body" => "A message from Ruby!"
  })
```
{: .repl #twilio title="Twilio" points="1" setup_code="1-4"}

This request is about as complicated as it gets! But we are able to put it together with `HTTP` and deliver it without any problem. The request creates an order to deliver a text message for us, and charges our account. Neat!

We'll delve more into how I figured out how to structure this request in future lessons (hint: I read the Twilio documentation and did lots of trial and error).

---

Now that we know how to _send_ HTTP requests with Ruby, it's time to learn how to _receive_ HTTP requests and send back responses — in other words, how to build a web app. That's next.
