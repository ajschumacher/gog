# `gog`

connector | server | visualization
----------------------------------
[gogr](https://github.com/ajschumacher/gogr) for [R](http://www.r-project.org/) |  * [gogd](https://github.com/ajschumacher/gogd) (in [Clojure](http://clojure.org/)) |  [gogi](https://github.com/ajschumacher/gogi) (toy example)
[gogpy](https://github.com/ajschumacher/gogpy) for [Python](https://www.python.org/) |  [gog-charted.co](https://github.com/ajschumacher/gog-charted.co) (the [charted.co](http://www.charted.co/) interface)


### What is `gog`?

`gog` separates data processing and data visualization. Everybody wants to have nice interactive visualizations in a browser anyway. `gog` is a three-piece architecture:

 1. connector from data processing environment to 2
 2. gog server to pass data from 1 to 3
 3. browser-based data visualization that accepts data from 2

All the pieces can be swapped around and even hosted in different places, allowing quite a few combinations.


### Why is this good?

You should be able to use whatever language you want for data processing and still have all the same visualization tools at your fingertips.

You should be able to visualize interactively—both quickly making new plots and interacting with your current plots—regardless of what machine(s) your data code is running on.

You should be able to have total control over your data and visualization systems, without handing data over to or otherwise relying on external providers.


### Why is this bad?

We need more and better browser-based data visualization tools that are gog-compatible, sufficiently flexible, and support useful features like exporting to common formats. Something like the "graphboard" from Wilkinson's [Grammar of Graphics](http://www.amazon.com/The-Grammar-Graphics-Statistics-Computing/dp/0387245448) would be nice.

There are places where the separation between data processing and data visualization is not always clear. Which end of the system is responsible for binning a histogram?

Ad hoc development and extension of `gog` could break compatibility between components.


#### 1. connector from data processing environment

All you need is a function that HTTP POSTs your data to a gog server. As currently implemented, that means POST to `http://localhost:4808/data`. Currently, data is passed as a JSON array of simple objects, like `[{"var_name": 5, ...`.

 * [gogr](https://github.com/ajschumacher/gogr): an [R](http://www.r-project.org/) package for sending data to a gog server
 * [gogpy](https://github.com/ajschumacher/gogpy): a [Python](https://www.python.org/) package for sending data to a gog server

These are super easy to make in any language with support for JSON and HTTP.


#### 2. gog server

As currently implemented, a gog server runs on port 4808. That port is also used by the game "Command and Conquer Red Alert" and it is certainly acceptable to use another port.

As currently implemented, a gog server accepts a POST body at `/data` and rebroadcasts it to all clients listening to the websocket at `/data`. The server only passes the contents through, as text.

 * [gogd](https://github.com/ajschumacher/gogd): a gog server written in [Clojure](http://clojure.org/)

These are super easy to make in any language with support for HTTP and websockets.


#### 3. browser-based data visualization

 * [gogi](https://github.com/ajschumacher/gogi): a toy example that just displays the received text data
 * [gog-charted.co](https://github.com/ajschumacher/gog-charted.co): the [charted.co](http://www.charted.co/) interface extended to support `gog`
