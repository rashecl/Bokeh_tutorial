# [Bokeh_tutorial](https://rashecl.github.io/Bokeh_tutorial/)

[Check out the slides for this tutorial](https://rashecl.github.io/Bokeh_tutorial/prod/bokeh_tutorial.slides.html#/)
<br>
This overview covers the basics of creating interactive bokeh visualizations in a jupyter notebook/lab and how to serve a bokeh app locally. `./prod/bokeh_tutorial.ipynb` contains the main tutorial notebook. For convenience, you can open it in [google colab](https://colab.research.google.com/github/rashecl/Bokeh_tutorial/blob/master/prod/bokeh_tutorial.ipynb).<br>

I used bokeh to create my Insight data science project - [a web-app to predict blood test results](http://healthforecaster.xyz/HF). I also created a [COVID-19 tracking dashboard](predict.rocks).


## Installation
<b> Note: The latest version (as of 06-15-20) of bokeh has a bug that doesn't allow you to run visualizations in notebooks. Therefore, I recommend explicitly installing an older version for now: </b>
``` console
$ conda install bokeh=2.0
```
If you're using jupyterlab as opposed to jupyter notebook, you will need to install this extension.
``` console
$ jupyter labextension install @bokeh/jupyter_bokeh
```

## Basic plotting syntax
See the [slides](https://rashecl.github.io/Bokeh_tutorial/prod/prod.slides.html#/) associated with this repo. <br>

Bokeh has a great [tutorial](https://mybinder.org/v2/gh/bokeh/bokeh-notebooks/master?filepath=tutorial%2F00%20-%20Introduction%20and%20Setup.ipynb) that covers the basic syntax for bokeh plots.

``` python
import numpy as np
from bokeh.plotting import figure
from bokeh.io import show, output_notebook
output_notebook() # All subsequent outputs go to notebook
fig = figure(plot_width=350, plot_height=250,
             title='Speed vs. time',
             y_axis_label='speed (m/s)', x_axis_label='time (s)')
t = np.arange(10)
fig.scatter(t, 9.8*t)
show(fig)
```
![basic plot](/figs/basic_syntax_1.png)

I highly advise using ColumnDataSource for the data source for your bokeh visualizations to take full advantage of the figure tools. ColumnDataSource takes a simple dictionary or pandas DataFrame argument:
``` python 
from bokeh.models import ColumnDataSource
from bokeh.layouts import Row 
t = np.arange(10)
CDS = ColumnDataSource({'time':t,'speed':9.8*t,'dist':4.9*t**2})
left = figure(plot_width=350, plot_height=250,
              tools='lasso_select',
              title='Speed vs. time',
              y_axis_label='speed (m/s)', x_axis_label='time (s)')
right = figure(plot_width=350, plot_height=250,
              tools='lasso_select',
              title='Speed vs. time',
              y_axis_label='distance (m)', x_axis_label='time (s)')

left.scatter('time', 'speed', source=CDS)
right.scatter('time', 'dist', source=CDS)
show(Row(left,right))
```
![linked plot](/figs/basic_syntax_2.png)
## Creating bokeh visualizations for servers
You can get more complicated, but bokeh allows you to create an interactive visualization with widgets using a single script file. 
If you're starting off, check out [sliders.py](https://demo.bokeh.org/sliders) and it's [source code](https://github.com/bokeh/bokeh/blob/master/examples/app/sliders.py) to understand the basic logic. 

## Serving bokeh apps

* Locally <br> 
During development, you want to be able to debug to see what's happennig on the backend and printed statements that you've coded in. This is done using the `--log-level=debug` flag:
  
``` console
$ bokeh serve --show --log-level=debug sliders.py
```

* In AWS (development mode) <br>
First, you will have to setup an AWS EC2 instance. You can also link the app to a domain name that you've purchased (optional). These steps are beyond the scope of this tutorial, but you can find an excellent tutorial [here](https://docs.google.com/presentation/d/1Z18wadUskWYdW2iOaX3rypoO1HftaOS-mHw2qHr_sSs/edit?usp=sharing).

In AWS, you'll want to allow for connections to your app through the web, which is done using `--allow-websocket-origin` flags.
To run the app in AWS, navigate to your bokeh app python script and run something like this:

``` console 
$ bokeh serve --show --log-level=debug sliders.py --port 5000\
--allow-websocket-origin=44.233.227.189:5000 \
--allow-websocket-origin=ec2-44-233-227-189.us-west-2.compute.amazonaws.com \
--allow-websocket-origin=predict.rocks \
--allow-websocket-origin=www.predict.rocks \
/
```

This assumes:<br>
your elasticIP is: 44.233.227.189 <br>
your ec2 address is: ec2-44-233-227-189.us-west-2.compute.amazonaws.com <br>
your linked domain name is: www.predict.rocks <br>

* In AWS (production mode)
If you want the app not to hangup (after you've disconnected from your instance) just add `nohup`. I also add the `--keep-alive 10000` which pings the server to keep it alive. Note: I haven't tested its necessity. 


``` console 
$ nohup bokeh serve --show --log-level=debug sliders.py --port 5000\
--allow-websocket-origin=44.233.227.189:5000 \
--allow-websocket-origin=ec2-44-233-227-189.us-west-2.compute.amazonaws.com \
--allow-websocket-origin=predict.rocks \
--allow-websocket-origin=www.predict.rocks \
--keep-alive 10000/
```

If you're using Nginx (an HTTP and reverse-proxying server), you'll also want to follow the configuration instructions found [here](https://docs.bokeh.org/en/latest/docs/user_guide/server.html#nginx).
