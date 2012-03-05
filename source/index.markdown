---
layout: page
comments: false
sharing: true
footer: false
---

## Note: 

This is a site under construction. Information below is subject to change

## Features

* A simple intuitive API
* Support for *http*, *https*, and *websockets*
* Clean HTTP code based pattern matching
* Basic Authentication
* Auto follows redirections

## Usage

### Get a page

``` scala Get a page
    val host = "http://example.com"
    val response = (host / "page.html").get
    response process {
        case Success() => println(response.contents)
        case Failure() => println("Oops.. unsuccessful request " + response.code)
    }
```

### Advanced get

``` scala Get a page over https, with parameters and custom headers
    val host = "https://example.com"             // sets https
    val response = 
        ((host / "page.html") ? 
            ("foo" -> "bar", "baz" -> "jaz"))     // query string params
            .withHeaders {                        // set headers
              "Accept" -> "text/plain", 
              "Accept-Charset"-> "utf-8"
            } get
    response process {
        case Success() => println(response.contents)
        case Failure() => println("Oops.. unsuccessful request " + response.code)
    }
```

### Websockets

``` scala ping/pong using websockets
    val connection = "ws://websockets.example.com" open "protocol.name"

    // install message handler, to process incoming text messages
    // every incoming message will be processed by this handler
    connection onMessage { msg =>
      println("Received message: " + msg)
    }

    // send message
    connection ! "ping"

    // close the connections after all the interactions are done
    connection.close()
```               
### Custom error classes

While *Success* and *Failure* as shown above are built in classes amongst many others, you can choose to define your own as necessary. eg. you may create your own class *AccessControlFailure* and use it as follows

``` scala Custom Error Class
object AccessControlFailure {
  val codes = Set(401,402,403,405,406)
  def unapply(code: Int) : Boolean = {
    codes contains code
  }
}


val host = "http://example.com"
val response = (host / "page.html").get
response process {
    case Success()              => println(response.contents)
    case 404                    => println("Page not found")
    case AccessControlFailure() => println("Failure: Access control")
    case Failure()              => println("Oops.. unsuccessful request " + response.code)
}
```

## Source

  [Github Repository](https://github.com/Hattip/hat.tip)
