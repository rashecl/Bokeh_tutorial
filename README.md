# Bokeh_tutorial

[Check out the slides for this tutorial](prod/prod.slides.html)
<br>
This overview covers the basics of creating interactive bokeh visualizations in a jupyter notebook/lab and how to serve a bokeh app locally. If you're starting off, check out [this simple app](https://demo.bokeh.org/sliders) and it's [source code](https://github.com/bokeh/bokeh/blob/master/examples/app/sliders.py) to understand the basic logic. 
Bokeh has a great [tutorial](https://mybinder.org/v2/gh/bokeh/bokeh-notebooks/master?filepath=tutorial%2F00%20-%20Introduction%20and%20Setup.ipynb) for making interactive visualizations for different purposes!  


I used bokeh to create my Insight data science project - [a web-app to predict blood test results](http://healthforecaster.xyz/HF). I also created a [COVID-19 tracking dashboard](predict.rocks).


## Installation
<b> Note, latest version of bokeh has a bug that doesn't allow you to run visualizations in notebooks. </b>
``` console
$ conda install bokeh=2.0
```
If you're using jupyterlab as opposed to jupyter notebook, you will need to install this extension.
``` console
$ jupyter labextension install @bokeh/jupyter_bokeh
```

## Basic plotting syntax

---- Need to update this section later ---

## Serving bokeh apps

* Locally 
During development, you want to be able to debug to see what happennig on the backend and printed statements that you've coded in. This is done using the `--log-level=debug` flag.
  
``` console
$ bokeh serve --show --log-level=debug sliders.py
```

* In AWS (development mode)
First, you will have to setup an AWS EC2 instance. You can also link the app to a domain name that you purchased (optional). These steps are beyond the scope of this tutorial, but you can find an excellent tutorial [here](https://docs.google.com/presentation/d/1Z18wadUskWYdW2iOaX3rypoO1HftaOS-mHw2qHr_sSs/edit?usp=sharing).

In AWS, you want to allow for connections to your app through the web, which is done using `--allow-websocket-origin` flags.
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

