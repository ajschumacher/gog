# `gog`

connector | server | visualization
----------|--------|--------------
[gogr](https://github.com/ajschumacher/gogr) (for [R](http://www.r-project.org/)) | [gogd](https://github.com/ajschumacher/gogd) (in [Clojure](http://clojure.org/)) |  [gogi](https://github.com/ajschumacher/gogi) (toy example)
[gogpy](https://github.com/ajschumacher/gogpy) (for [Python](https://www.python.org/)) | | [gog-charted.co](https://github.com/ajschumacher/gog-charted.co) (the [charted.co](http://www.charted.co/) interface)


### What is `gog`?

`gog` separates data processing and data visualization. Everybody wants to have nice interactive visualizations in a browser anyway. `gog` is a three-piece architecture:

 1. connector from data processing environment to server
 2. gog server to pass data from connector to visualization
 3. browser-based data visualization that accepts data from server

All the pieces can be swapped around and even hosted in different places, allowing quite a few combinations.

#### 1. connector from data processing environment

> Analogy with [ggplot2](http://ggplot2.org/): `ggplot(data=your_data)`

All you need is a function (`gog`) that HTTP POSTs your data to a `gog` server. As currently implemented, that means POST to `http://localhost:4808/data`. Currently, data is passed as a JSON array of simple objects, like `[{"var_name": 5, ...`.

 * [gogr](https://github.com/ajschumacher/gogr): an [R](http://www.r-project.org/) package for sending data to a gog server
 * [gogpy](https://github.com/ajschumacher/gogpy): a [Python](https://www.python.org/) package for sending data to a gog server

These are super easy to make in any language with support for JSON and HTTP.


#### 2. gog server

> Analogy with [ggplot2](http://ggplot2.org/): you don't need a server because everything's in `R`

As currently implemented, a gog server runs on port 4808. That port is also used by the game "Command and Conquer Red Alert" and it is certainly acceptable to use another port.

As currently implemented, a gog server accepts a POST body at `/data` and rebroadcasts it to all clients listening to the websocket at `/data`. The server only passes the contents through, as text.

 * [gogd](https://github.com/ajschumacher/gogd): a gog server written in [Clojure](http://clojure.org/)

These are super easy to make in any language with support for HTTP and websockets.


#### 3. browser-based data visualization

> Analogy with [ggplot2](http://ggplot2.org/): `aes(x=variable) + geom_histogram()` etc.

These are just HTML/CSS/JavaScript, viewed in a browser. They connect via websocket to `http://localhost:4808/data` and accepting incoming JSON arrays of simple objects, like `[{"var_name": 5, ...`. Then they present a data visualization and support some level of interactivity.

 * [gogi](https://github.com/ajschumacher/gogi): a toy example that just displays the received text data
 * [gog-charted.co](https://github.com/ajschumacher/gog-charted.co): the [charted.co](http://www.charted.co/) interface extended to support `gog`

It's not super easy to make a good component here, but a component can then be used with any language/environment/system that sends data into `gog`.

It would be nice to have visualizations that support useful features like exporting to common formats, maintaining a history of recent data sets and visualizations, and switching between common visualization types.

Something like the "graphboard" from Wilkinson's [Grammar of Graphics](http://www.amazon.com/The-Grammar-Graphics-Statistics-Computing/dp/0387245448) would be nice.


### Why is this good?

You should be able to use whatever language you want for data processing and still have all the same visualization tools at your fingertips.

You should be able to visualize interactively—both quickly making new plots and interacting with your current plots—regardless of what machine(s) your data code is running on.

You should be able to have total control over your data and visualization systems, without handing data over to or otherwise relying on external providers.


### Why is this bad?

We need more and better browser-based data visualization tools that are gog-compatible, sufficiently flexible, and sufficiently feature-rich.

There are places where the separation between data processing and data visualization is not always clear. Which end of the system is responsible for binning a histogram?

Ad hoc development and extension of `gog` could break compatibility between components.


### Ideas for extension

 * Gosh a lot of it is just building out cool front-end pieces.
 * Add an additional control channel for interacting with visualizations from programming environments.
 * Develop or implement an existing format for representing a visualization for interoperability.
 * Some clever scheme for dynamic port assignments and so on.
 * Bundle a gog server with some good visualizations and distribute as an easy-to-run package.
 * A web service for sharing visualizations.


### How is this not exactly Plotly?

It's similar. [Plotly](https://plot.ly/) is pretty neat. But there are two problems with Plotly: how it works, and how it works.

 1. How it works
     * Plotly's APIs force you to put your data into their format. I don't think Plotly `traces` are a nice abstraction. I don't think it's the right design to have the data processing side say so much about how the plot should be made. I just want to throw data in and see it.
     * Plotly's service is remote; you have to send your data up to them and wait for it. It's too slow for interactive use.
 2. How it works
     * Plotly's not free. They depend on customers to exist and fundamentally, getting money from customers is their goal. Want to [run their software on your own machine](https://plot.ly/product/enterprise/)? They want to find out how much money you have before they tell you how much it'll cost you.
     * Plotly's not [Free](https://www.gnu.org/philosophy/free-sw.html). You can't add a feature. You can't fix a bug. You certainly can't build Plotly into your own project/product. If Plotly disappears, everything you've built with Plotly could be as good as dust.
