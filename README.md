<img src="gog.png" height="160" alt="gog" />

connector | server | visualization
----------|--------|--------------
[gogr](https://github.com/ajschumacher/gogr) (for [R](http://www.r-project.org/)) | [gogd](https://github.com/ajschumacher/gogd) (in [Clojure](http://clojure.org/)) |  [gog-dummy](https://github.com/ajschumacher/gog-dummy) (toy example)
[gogpy](https://github.com/ajschumacher/gogpy) (for [Python](https://www.python.org/)) | | [gog-charted.co](https://github.com/ajschumacher/gog-charted.co) (the [charted.co](http://www.charted.co/) interface)
 | | [gogi](https://github.com/ajschumacher/gogi) (general scatterplot)

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

or

> "Dear internet, please port ggplot from R to Javascript" - Joshua Gourneau, [2011](https://twitter.com/gourneau/status/99294355097001984)

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
 * Would it make sense to implement with web components somehow?
 * Bundle a gog server with some good visualizations and distribute as an easy-to-run package.
 * A web service for sharing visualizations.


### Related Things


#### R `htmlwidgets`

[htmlwidgets](https://github.com/ramnathv/htmlwidgets) is a very neat project that makes it easy to generate web visualizations from and in R. It's all very R-based, and the functions that get produced can take any sort of input data and arguments. The way they've standardized the approach, however, means it would likely be relatively straightforward to take an `htmlwidget`-ized visualization and transform it to a `gog` visualization.


#### Plotly

[Plotly](https://plot.ly/) is pretty neat. It's similar to `gog` but has more requirements for articulating data and plot options in the connectors (specifying `traces`, etc.) and the server and front-ends come from Plotly's machines. Also Plotly is a business that needs to make money, and their products are not Free or open source.
