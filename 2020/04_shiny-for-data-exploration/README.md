# A Shiny-er future: R Shiny as a data exploration and visualization tool

- Author: Apratim Mitra, NICHD/NIH
- github: [mitraak](https://github.com/mitraak)

In my group, we think a lot about the best ways to present data and report
results to collaborators. Often this process is inefficient, involving extensive
back-and-forth, including meetings, emails, scheduling challenges, etc. In this talk,
I will discuss using R Shiny as a tool to streamline this process, putting the power
in the hands of the collaborators themselves. To that end, I will be describing and
demoing a couple of proof-of-concept Shiny apps that make the process of result
exploration and visualization, intuitive, interactive and (hopefully) fun!

## Talk outline

- `Rmarkdown` is a powerful reporting tool
   - Pro: great way of organizing, navigating large sets of complex results
   - Con: static format leads to inefficiencies
- `Shiny` can be used to bridge the gap
   - relatively simple to set up
   - integrated with `RStudio`
   - built in examples
- `Shiny` components
   - `ui` object
   - `server()` function
   - `shinyApp()` function call
- Proof-of-concept apps
   - interactive MA plot: https://github.com/mitraak/plotma-shiny
   - functional enrichment tool: https://github.com/mitraak/clusterprofiler-ui-shiny
- App deployment
   - share code
   - external hosting
      - Free/individual: http://shinyapps.io
      - Enterprise/institute: http://rstudio.com/products/connect
   - standalone app *Work in progress*
      - some ideas using electron: https://www.travishinkelman.com/posts/deploy-shiny-electron/
- Tips
   - start small
   - code versioning is your friend
   - R or package version updates might break things
   - Test, test, test
- Resources
   - https://shiny.rstudio.com/tutorial

## Reactivity

- Reactive values
   - values changed based on user input, e.g. values returned by inputs
- Reactive functions
   - functions triggered by changes in reactive values, e.g. new plots
   created with user input
- `ui` object
   - interface to get user input
   - return list of reactive values
- `server` function
   - runs reactive functions based on input reactive values
   - returns to `ui` for display

## `ui` object

### Application layout

- `fluidPage`: adaptively resizes panels based on browser window size
- `fluidRow`: adaptively resizes rows based on element dimensions
- `titlePanel`: panel at top of the web page
- `sidebarLayout`: layout with a side bar
   - `mainPanel`: main web page panel
   - `sidebarPanel`: side panel of web page

### Inputs

These create reactive objects based on user input.

- `numericInput`: takes numeric input
- `sliderInput`: takes numeric input as a slider
- `checkboxInput`, `checkboxGroupInput`: single or group of check boxes 
- `radioInput`: radio button input
- `selectInput`: dropdown menu input
- `textInput`: takes text input

### Buttons

These are widgets useful for their side effects, so the reactive
values themselves are not meaningful.

- `actionButton`: button to perform designated action
- `downloadButton`: button to download file

### Outputs

These are output elements based on server-side computation.

- `textOutput`: types text to html. Can be styled with `html` or `css`
- `plotOutput`: outputs plot to html. Takes dimensions as arguments.
   - `plot_click` returns coordinates of user click on plot (in units of x- and y-axes)
   - `plot_dblclick` - same as above but for double click
   - `plot_brush` returns list with coordinates of box from mouse click-and-drag: `(xmin, xmax, ymin, ymax)`
   - `plot_hover` returns coordinates of mouse hover
- `dataTableOutput`: returns interactive `datatable` of `matrix` or `data.frame`
- `plotlyOutput`: returns interactive `plotly` plot

## `server` function

```
server <- function(input, output, session){}
```

- `input`: named list with reactive values.
- `output`: names list with reactive values calculated server-side. This connects the `server` and `ui`.
- `session`: list with client information, connecting `server` and client.

Next, I outline the components that can be part of the server function. 
The example code can be used in the server function body.

### Render reactive output: `render*()`

These are reactive functions that take an object to be displayed.

- `renderPlot`: this can be base R graphics or `ggplot` object
- `renderPlotly`: interactive `plotly` plot
- `renderDataTable`: interactive data table
- `renderPrint`: print an R object like it would appear in the R console
- `renderTable`: simple table output
- `renderText`: print text
- `renderUI`: this is a reactive function that is used to create user 
  controls/input widgets on the server side.
- `renderImage`: output image

These functions take R code enclosed in `{}` as function arguments, that
references or uses a reactive value. Every time the reactive value changes
the code is rerun.

- example:

```
output$maplot <- renderPlot({
    # code to create MA plot
})
```

### Modularize reactions: `reactive()`

These are functions that create a reactive *object*. 

- These are dependent on reactive values.
- These are cached/saved and can be used in other reactive functions.
- They are called like functions.
- Used to modularize apps and avoid unnecessary computation.

- example:

```
# this will calculate the log2() of a number pulled in from input$num
# and prints it as text

# reactive expression
data <- reactive({ log2(input$num) })

# render as text
output$renderText({
    data()
})
```

### Prevent reactions: `isolate()`

These functions make non-reactive object from reactive inputs.

- output from these functions can be used as regular R objects.
- example:

```
# here we just take an input number and print it.
# further changes don't do anything

# isolate input number
num <- isolate({ input$num })

output$renderText({ num })
```

### Trigger arbitrary code: `observeEvent()`

These functions will trigger an event/code based on a reactive value.

- The code may not be reactive itself.
- example:

```
# this prints a random normal number in R console every time
# input$click is updated
observeEvent(input$click, { print( rnorm(1) ) })
```

### Trigger arbitrary code II: `observe()`

These are reactive functions that just contain a block of code
to be rerun in response to one or multiple reactive values.

- Similar to `observeEvent`, but different syntax
- code is rerun if *any* reactive values in the code block are changed.
- example:

```
observe({
    # code block
})
```

### Delay reactions: `eventReactive()`

These are functions that can be used to delay rerun of reactive expressions
based on some specific reactive inputs (e.g. action buttons).

- example:

```
# here the reactive input value will trigger a recomputing of
# data() only when input$click is renewed, which will refresh
# the histogram plot
data <- eventReactive( input$click, { rnorm(input$num) })

output$hist <- renderPlot({
    hist(data())
})
```

### Create reactive values: `reactiveValues()`

This function creates reactive values that can be programmatically manipulated.

- Similar to reactive values taken as input, except they are created on the 
server-side.
- These are reactive values that can be referenced as a normal R object.
- example:

```
# here two reactive inputs, input$plotnorm, input$plotunif
# will trigger a different histogram
rv <- reactiveValues( data = rnorm(100) )

observeEvent(input$plotnorm, { rv$data = rnorm(100) })
observeEvent(input$plotunif, { rv$data = runif(100) })

output$hist <- renderPlot({
    hist(rv$data)
})
```
