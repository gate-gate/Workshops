R Graphing Workshop
================
Marissa Barlaz
March 12 and 16, 2018

A lot of the information in this workshop is based on workshops given at Harvard: <https://dss.iq.harvard.edu/workshop-materials>

Please make good use of the following cheat sheet! <https://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf>

Data
----

The data I will be using during this tutorial is from the University of Wisconsin's X-Ray Microbeam Speech Production Database. The database has the following variables:

-   v = vowel
-   f0
-   f1-f4
-   ulx and uly = x and y coordinates of the upper lip
-   llx and lly = x and y coordinates of the lower lip
-   t(1-4)x and t(1-4)y = x and y coordinates of the tongue at position (1-4)
-   mnix and mniy = x and y coordinates of the mandibular incisor
-   mnmx and mnmy = x and y coordinates of the mandibular molar
-   I have artificially introduced 5 "speakers" randomly \#\# Introduction to ggplot

As linguists, we are often dealing with data. And dealing with data means that when we are presenting results, we need to both describe these results in words, and display these results using plots.

R is very powerful when it comes to making graphics. There are a lot of functions in base R that can create these graphics:

``` r
hist(ubdb$f1)
```

![](R_Graphing_Workshop_files/figure-markdown_github/basegraph-1.png)

``` r
plot(ubdb$t1y, ubdb$f1)
```

![](R_Graphing_Workshop_files/figure-markdown_github/basegraph-2.png)

``` r
boxplot(f1~v, notch = TRUE, data = ubdb)
```

    ## Warning in bxp(structure(list(stats = structure(c(419.198, 574.662,
    ## 611.469, : some notches went outside hinges ('box'): maybe set notch=FALSE

![](R_Graphing_Workshop_files/figure-markdown_github/basegraph-3.png)

However, each of these functions have slightly different syntactic forms, and the code is more complex to create plots. The *ggplot()* function, on the other hand, has a consistent coding structure, and creates much prettier plots. Once you understand the basic functions in ggplot, pretty and informative graphics are only a few steps away!

The basic idea is similar to that of Photoshop. You independently specify building blocks of your plot, and combine them to create a graphical display. We add these building blocks (i.e., layers) to the graph using the "+" sign. Building blocks include:

-   data
-   aesthetic mapping
-   geometric object
-   statistical transformations
-   scales
-   coordinate systems
-   position adjustments
-   faceting

It is important to include everything you need in one data frame for the graph. The first thing that happens is you call the data frame, and every subsequent call for a variable will come directly from that data frame.

Aesthetics
----------

In *ggplot()*, aesthetics include things you can see. Aesthetics are called with the function *aes()*. Some example aesthetics are:

-   x: positioning along x-axis
-   y: positioning along y-axis
-   color: color of objects, or the color of the object’s outline (compare to fill below)
-   fill: fill color of objects
-   alpha: transparency of objects (value between 0, transparent, and 1, opaque)
-   linetype: how lines should be drawn (solid, dashed, dotted, etc.)
-   shape: shape of markers in scatter plots
-   size: how large objects appear

Let's see what happens when we use *ggplot()* and *aes()* together:

``` r
ggplot(ubdb, aes(x=t1y, y=f1))
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot1-1.png)

What's going on here? We've told ggplot to make a graph for the data file ubdb, with the x axis being t1y, and the y axis being f1. However, we didn't specify what kind of graph we want. There are a few options for this, many of which are considered geometric objects:

-   geom\_point()
-   geom\_boxplot()
-   geom\_line()
-   geom\_ribbon()
-   geom\_label() or geom\_text()
-   geom\_violin()

Let's try some of these:

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_point()
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot11-1.png)

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_point(aes(color = v))
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot11-2.png)

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_point(aes(color = f1))
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot11-3.png)

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_point(aes(color = t4y))
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot11-4.png) What's the difference here? For the first plot, we are plotting basic geometric points. For the subsequent plots, we are adding an additional layer to the plot - changing the color of the plots from the default black. Therefore, the aesthetics of the points need to be included.

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_point(aes(color = t4y, size = 2))
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot2-1.png)

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_point(aes(color = t4y), size = 2)
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot2-2.png)

Here, the size is included as a variable, which is why we end up with an extra legend. Therefore, it is important to keep any aesthetics that do not call a variable (i.e., are fixed for the entire plot) stay outside of the *aes()* call. If you want to to base size on a variable (or color, linetype, etc.)) but do not want the legend, you can use *guides()* after your call for that geometric object, and specify which guide you do not want to be shown by using *FLASE*.

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_point(aes(color = t4y, size = t1y))+guides(size = FALSE)
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot22-1.png)

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_point(aes(color = t4y, size = t1y))+guides(color = FALSE)
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot22-2.png)

So what's going on here? Why do we get an error for the second plot call?

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_line(group = v, color = v)
```

    ## Error in layer(data = data, mapping = mapping, stat = stat, geom = GeomLine, : object 'v' not found

``` r
#This is one way to get around the aes() issue, but is not the best! It means you have to call your data file again, and if you have some similarly-titled data frames, a simple typo can give you an inaccurate graph!
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_line(group = ubdb$v, color = ubdb$v)
```

    ## Error in grDevices::col2rgb(colour, TRUE): invalid color name 'AA'

``` r
#Sing it with me - this is how you do it!
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_line(aes(group = v, color = v))
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot3-1.png)

What about other aesthetics?

``` r
ggplot(ubdb, aes(x=v, y=f1)) + geom_boxplot(aes(group = v))
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot33-1.png)

``` r
ggplot(ubdb, aes(x=t1y, fill=v)) + geom_density(size = 1, alpha=.2)
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot33-2.png)

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_line(aes(group = v))+geom_ribbon(aes(group = v, ymin = f1-sd(f1), ymax = f1+sd(f1)))
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot33-3.png)

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_line(aes(group = v))+geom_smooth(aes(group =v, color = v))
```

    ## `geom_smooth()` using method = 'loess'

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot33-4.png)

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_smooth(aes(color = v), method = "gam", size = 2)+geom_ribbon(aes(color = v, ymin = f1-sd(f1), ymax = f1+sd(f1)), alpha = .2)
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot33-5.png)

``` r
ggplot(ubdb, aes(x=t1y, y=f1))+geom_ribbon(aes(color = v, ymin = f1-sd(f1), ymax = f1+sd(f1)), alpha = .2)+ geom_line(aes(color = v), size = 1.2)
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot33-6.png)

``` r
ggplot(ubdb, aes(x=t1y, y=f1))+geom_text(aes(label=v, color = v))
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot33-7.png)

Faceting
--------

Some of these graphs look pretty gross, because there is too much information on one single plot. A common way to deal with this is to do faceting, which splits the graph up into multiple graphs, based on one or more variable.

There are two functions that can be used to split the plots - *facet\_wrap()* and *facet\_grid()*. The first function, *facet\_wrap()*, takes one grouping variable, splits the data up by that variable into multiple graphs, and then wraps the data into multiple rows. The variable being wrapped follows a tilde. The number of rows or columns can be specified (but not both!).

The second function, *facet\_grid()*, takes two or more grouping variables, splits the data up by those variables into multiple graphs, and then wraps the data into multiple rows and columns based on the specified formula. Put the row-splitting variable(s) before the tilde, and the column-splitting variable(s) after the tilde. A period specifies no faceting along that dimension.

``` r
ggplot(ubdb, aes(x=t1y, fill=v)) + geom_density(size = 1, alpha=.2)+facet_wrap(~v)
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot4-1.png)

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_point(aes(color = v), size = 2)+facet_grid(speaker~v)
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot4-2.png)

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_point(aes(color = t4y), size = 2)+facet_grid(speaker~.)
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot4-3.png)

Statistical Transformations
---------------------------

So we've gone through various aesthetic calls for geometric objects. Note, however, that these objects are direct visualizations of the data, with no statistical transformations. What if we wanted to do something that showed statistical transforms? This could be helpful, as it would give summaries or more informative depictions of data, without the messiness of all of the points on the screen.

There are a few basic ones. First, you can do a histogram using *stat\_bin()*. You can also use *stat\_summary()*, which gives the mean and standard error of y at each x value (although other summary functions, such as median, mean and confidence interval, etc. can be used). *stat\_unique()* removes duplicate values.

``` r
ggplot(ubdb, aes(x=t1y))+stat_bin()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot5-1.png)

``` r
ggplot(ubdb, aes(x=t1y))+stat_bin(geom="point")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot5-2.png)

``` r
ggplot(ubdb, aes(x=t1y))+stat_bin(geom="line", bins = 50)
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot5-3.png)

``` r
ggplot(ubdb, aes(x=t1y))+stat_bin(bins = 40)
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot5-4.png)

``` r
ggplot(ubdb, aes(x=v, y=t1y))+stat_summary()
```

    ## No summary function supplied, defaulting to `mean_se()

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot5-5.png)

``` r
ggplot(ubdb, aes(x=t1y, y=f1)) + geom_point(aes(color = v)) + stat_unique()
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot5-6.png)

``` r
ggplot(ubdb, aes(x=f2, y=f1))+geom_point(aes(color = v))+stat_ellipse()
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot5-7.png)

``` r
ggplot(ubdb, aes(x=f2, y=f1, color = v))+stat_ellipse(type = "norm")
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot5-8.png)

``` r
ggplot(ubdb, aes(x=f2, y=f1))+geom_point(aes(color = v))+stat_ellipse(aes(color = v))
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot5-9.png)

``` r
#Here I am breaking my own rule and using a second dataset, mymeans (which is the mean of F1 and F2 by vowel), in the plot to plot the centers of each ellipse.
mymeans = aggregate(ubdb[c("f1", "f2")], list(v=ubdb$v), mean)
mymeans2= aggregate(ubdb[c("f1", "f2")], list(v=ubdb$v, speaker = ubdb$speaker), mean)
ggplot(ubdb, aes(x=f2, y=f1))+geom_text(data = mymeans, aes(color = v, label = v), size = 4)+stat_ellipse(aes(color = v))
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot5-10.png)

``` r
ggplot(ubdb, aes(x=f2, y=f1))+geom_text(data = mymeans, aes(color = v, label = v), size = 4)+stat_ellipse(aes(color = v, linetype = v))
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot5-11.png)

``` r
ggplot(ubdb, aes(x=f2, y=f1))+geom_text(data = mymeans2, aes(color = v, label = v), size = 4,show.legend = FALSE)+stat_ellipse(aes(color = v, linetype = v))+facet_wrap(~speaker)
```

    ## Too few points to calculate an ellipse

    ## Too few points to calculate an ellipse

    ## Warning: Removed 1 rows containing missing values (geom_path).

![](R_Graphing_Workshop_files/figure-markdown_github/ggplot5-12.png)

Other Aspects of the graph
--------------------------

### Colors

There's a lot to be said about different color palettes in R. You can specify your color scheme based on personal taste, school colors, using a colorblind friendly palette, etc. Note that it's generally in good taste to use a colorblind friendly palette, which the base colors in *ggplot* are not, as they have the same luminescence. Below, I am defining a colorblind friendly palette and using it in graphs using *scale\_fill\_manual()* and *scale\_colour\_manual()*. You can do use these two functions with a predefined palette, as I am, or by calling color codes (either HTML or basic color names) in the function.

``` r
cbPalette <- c("#999999", "#E69F00", "#56B4E9", "#009E73", "#F0E442", "#0072B2", "#D55E00", "#CC79A7", "#000000")

#To use for fills, add  scale_fill_manual(values=cbPalette)

# To use for line and point colors, add  scale_colour_manual(values=cbPalette)

ggplot(ubdb, aes(x=f2, y=f1))+geom_point(aes(color = v))+stat_ellipse(aes(color = v))+scale_color_manual(values = cbPalette)
```

![](R_Graphing_Workshop_files/figure-markdown_github/colors-1.png) There are many other ways to define and change colors, including changing hues, luminance, saturation, etc. If you have any questions about these things, please ask me! You can also find more information on color in R here:

<http://www.cookbook-r.com/Graphs/Colors_(ggplot2)/>

### Labels, titles, and Axes

If you want to relabel the axes, or give a title, you can use the *xlab()*, *ylab()*, and *ggtitle()* functions.

To change limits of the x and y axes, you can use *xlim()* and *ylim()*.

You can reverse the order of the x and y axes using *scale\_x\_reverse()* and *scale\_y\_reverse()*.

If you want to move the axes to the right/top, put these positions in the *scale\_x\_continuous()* and *scale\_y\_continuous()* functions. These can also go in the *scale\_x\_reverse()* and *scale\_y\_reverse()* functions. You can also specify two axes by calling for a second, duplicate axis. If you are using custom x and y scales and want to change the limits of the axes, do so inside of the scale functions calling *limits = c(lim1, lim2)*. NOTE that if you are using *scale\_x\_reverse()* and/or *scale\_y\_reverse()* you have to include your limits in reverse!!

``` r
ggplot(ubdb, aes(x=f2, y=f1))+geom_point(aes(color = v))+stat_ellipse(aes(color = v))+scale_color_manual(values = cbPalette)
```

![](R_Graphing_Workshop_files/figure-markdown_github/labels-1.png)

``` r
ggplot(ubdb, aes(x=f2, y=f1))+geom_point(aes(color = v))+stat_ellipse(aes(color = v))+scale_color_manual(values = cbPalette)+scale_x_reverse(position = "top")+scale_y_reverse(position = "right")+ xlab("F2!!!!") + ylab("F1!!!!!") + ggtitle("My vowel plot")
```

![](R_Graphing_Workshop_files/figure-markdown_github/labels-2.png)

``` r
ggplot(ubdb, aes(x=f2, y=f1))+geom_point(aes(color = v))+stat_ellipse(aes(color = v))+scale_color_manual(values = cbPalette)+scale_x_reverse(position = "top", limits = c(2500,1000))+scale_y_reverse(position = "right", limits = c(2500,0))+ xlab("F2!!!!") + ylab("F1!!!!!") + ggtitle("My vowel plot")
```

    ## Warning: Removed 107 rows containing non-finite values (stat_ellipse).

    ## Warning: Removed 107 rows containing missing values (geom_point).

    ## Warning: Removed 46 rows containing missing values (geom_path).

![](R_Graphing_Workshop_files/figure-markdown_github/labels-3.png)

``` r
ggplot(ubdb, aes(x=f2, y=f1))+geom_point(aes(color = v))+stat_ellipse(aes(color = v))+scale_color_manual(values = cbPalette)+scale_x_reverse(sec.axis = dup_axis()) + scale_y_reverse(limits = (c(0,1500)),sec.axis = dup_axis())+ xlab("F2!!!!") + ylab("F1!!!!!") + ggtitle("My vowel plot")
```

    ## Warning: Removed 1073 rows containing non-finite values (stat_ellipse).

    ## Warning: Removed 1073 rows containing missing values (geom_point).

![](R_Graphing_Workshop_files/figure-markdown_github/labels-4.png) <!-- ###Position -->

### Theme

As you've noticed, there is a set theme about the graphs - the background is grey, the text is black, the gridlines are white, etc. There are a number of ways to change the theme around. The most common is to use a different complete theme, such as *theme\_minimal()* or *theme\_grey()*. You can also define your own theme settings.

``` r
ggplot(ubdb, aes(x=f2, y=f1))+geom_point(aes(color = v))+stat_ellipse(aes(color = v))+scale_color_manual(values = cbPalette)+scale_x_reverse(position = "top")+scale_y_reverse(position = "right")+ xlab("F2!!!!") + ylab("F1!!!!!") + ggtitle("My vowel plot") + theme_minimal()
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplottheme-1.png)

``` r
ggplot(ubdb, aes(x=f2, y=f1))+geom_point(aes(color = v))+stat_ellipse(aes(color = v))+scale_color_manual(values = cbPalette)+scale_x_reverse(position = "top")+scale_y_reverse(position = "right")+ xlab("F2!!!!") + ylab("F1!!!!!") + ggtitle("My vowel plot") + theme(panel.grid.major = element_blank(),panel.grid.minor = element_blank(),panel.border = element_blank(), panel.background = element_blank())
```

![](R_Graphing_Workshop_files/figure-markdown_github/ggplottheme-2.png)

Wrapping up
-----------

In sum, *ggplot2* is an extremely powerful package that can create lots of beautiful plots for your research. Think carefully about what you want to plot, and add layers as needed. I encourage you to save your plots as variables. You can then view and save them.

``` r
myplot = ggplot(ubdb, aes(x=f2, y=f1))+geom_point(aes(color = v))+stat_ellipse(aes(color = v))+scale_color_manual(values = cbPalette)+scale_x_reverse(position = "top")+scale_y_reverse(position = "right")+ xlab("F2!!!!") + ylab("F1!!!!!") + ggtitle("My vowel plot") + theme_minimal()

myplot
```

![](R_Graphing_Workshop_files/figure-markdown_github/wrapup-1.png)

``` r
#Not run = to save the plot!
#ggsave("myplot.pdf", plot = myplot, height = 6, width = 10, units = "in", device = cairo_pdf)
```

I highly recommend using the following reference whenever you need to find more information about a graphing mechanism in *ggplot*: <http://ggplot2.tidyverse.org/reference/>
