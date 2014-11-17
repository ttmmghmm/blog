---
layout: post
title:  "One sample location estimation"
categories: [jekyll, rstats]
tags: [knitr, servr, httpuv, websocket]
---


Thought I'd start simple: with estimation of a mean.

We'll do a simulation study. Three scenarios: a normal distribution, a uniform distribution
and a Cauchy (t distribution on 1df). Three methods: mean, median and winsorized mean. 

In general, we might want to simulate the simulation parameters from some distribution,
but in this simple case it is enough I think to fix them in advance. Just for
the purposes of illustration we'll use this facility to set the number of samples (nsamp)
here to be 1000. Also we have to specify dataseed, which is used by the datamaker
function. (Here I set dataseed to be the user-supplied seed+1, which I suggest as default behaviour).


{% highlight r %}
#  library(devtools)
#  install_github(repo="stephens999/dscr")
{% endhighlight %}


{% highlight r %}
parammaker = function(indexlist){
  set.seed(indexlist$seed)

  #here is the meat of the function that needs to be defined for each dsc to be done
  param = list(nsamp=1000, dataseed=indexlist$seed+1)
  # end meat of function
  

  save(param,file=paramfile(indexlist))
  return(param)
}
{% endhighlight %}

Now define the function to create data: input, and meta-data. 
The input is what the methods will be given. The meta data will be used
when scoring the methods. So here, the input is a random sample, and the meta-data is the true mean (0).



{% highlight r %}
datamaker = function(indexlist){
  load(file=paramfile(indexlist))
  set.seed(param$dataseed)
  
 #here is the meat of the function that needs to be defined for each dsc to be done
  if(indexlist$scenario=="normal"){
    input = list(x=rnorm(param$nsamp,0,1))
    meta =  list(truemean=0)
  }
  
 if(indexlist$scenario=="uniform"){
    input = list(x=runif(param$nsamp,-1,1))
    meta = list(truemean=0)
 }
  
  if(indexlist$scenario=="Cauchy"){
    input = list(x=rt(param$nsamp,df=1))
    meta = list(truemean=0)
 }
 #end of meat of function
 
  data = list(meta=meta,input=input)
  save(data,file=datafile(indexlist))
  return(data)
}
{% endhighlight %}


Now define the methods. They have to have the form where they take "input" and produce "output"
in a specified format. In this case the input format is a list with one component (x).
The output format is a list with one component (meanest), the estimated mean. 
Effectively we have to write a "wrapper" function for each of our three methods
that makes sure that they conform to this input-output requirement. (Note that the
winsor.wrapper function makes use of the function winsor.mean from the psych package.)


{% highlight r %}
mean.wrapper = function(input){
  return(list(meanest = mean(input$x)))  
}

median.wrapper = function(input){
  return(list(meanest = median(input$x)))    
}

winsor.wrapper = function(input){
  return(list(meanest = winsor.mean(input$x,trim=0.2)))
}
{% endhighlight %}

Now define a list of the methods we'll use. 
Each method is defined by its name and the function used to implement it:

{% highlight r %}
  methods=list()
  methods$mean = list(name="mean",fn = "mean.wrapper")
  methods$median = list(name="median",fn="median.wrapper")
  methods$winsor = list(name="winsor",fn="winsor.wrapper")
{% endhighlight %}


And define a score function that says how well a method has done. Here we'll use squared error
and absolute error:

{% highlight r %}
score = function(param, data, output){
  return(list(squared_error = (data$meta$truemean-output$meanest)^2, abs_error = abs(data$meta$truemean-output$meanest)))
}
{% endhighlight %}

Finally, for each scenario we need to define what seeds we will use for the pseudo-random number generator.
They don't have to be the same, but here we simply use seeds in 1 to 100 for each scenario.

{% highlight r %}
scenario_seedlist= list("normal"=1:100,"uniform"=1:100,"Cauchy"=1:100)
{% endhighlight %}


Now we'll run all the methods on all the scenarios:

{% highlight r %}
  library(dscr)
{% endhighlight %}



{% highlight text %}
## Loading required package: psych
## Loading required package: plyr
## Loading required package: reshape2
{% endhighlight %}



{% highlight r %}
  res=run_dsc(parammaker,datamaker,methods,score,scenario_seedlist)
{% endhighlight %}

This returns a dataframe with the results of running all the methods on all the scenarios:

{% highlight r %}
  head(res)
{% endhighlight %}



{% highlight text %}
##    .id method flavor seed scenario squared_error   abs_error
## 1 mean   mean     NA    1   Cauchy  8.163616e-01 0.903527302
## 2 mean   mean     NA    1   normal  3.843843e-03 0.061998736
## 3 mean   mean     NA    1  uniform  1.506838e-05 0.003881801
## 4 mean   mean     NA    2   Cauchy  8.665931e-02 0.294379533
## 5 mean   mean     NA    2   normal  4.091567e-05 0.006396535
## 6 mean   mean     NA    2  uniform  1.849011e-04 0.013597836
{% endhighlight %}

And we can summarize the results (eg mean squared error) using the aggregate function

{% highlight r %}
  aggregate(abs_error~method+scenario,res,mean)
{% endhighlight %}



{% highlight text %}
##   method scenario  abs_error
## 1   mean   Cauchy 1.57119365
## 2 median   Cauchy 0.04010486
## 3 winsor   Cauchy 0.05097203
## 4   mean   normal 0.02746906
## 5 median   normal 0.03295318
## 6 winsor   normal 0.02831930
## 7   mean  uniform 0.01449181
## 8 median  uniform 0.02364241
## 9 winsor  uniform 0.01752364
{% endhighlight %}



{% highlight r %}
  aggregate(squared_error~method+scenario,res,mean)
{% endhighlight %}



{% highlight text %}
##   method scenario squared_error
## 1   mean   Cauchy  1.081422e+01
## 2 median   Cauchy  2.756888e-03
## 3 winsor   Cauchy  4.576878e-03
## 4   mean   normal  1.139656e-03
## 5 median   normal  1.627071e-03
## 6 winsor   normal  1.229033e-03
## 7   mean  uniform  2.945710e-04
## 8 median  uniform  7.922258e-04
## 9 winsor  uniform  4.401482e-04
{% endhighlight %}

Now suppose we are coming in and want to add a method, say the trimmed mean, to the comparison.
Here is what we do:

{% highlight r %}
  trimmedmean.wrapper = function(input){
    return(list(meanest=mean(input$x,trim=0.2)))
  }

  methods$trimmedmean = list(name="trimmedmean",fn = "trimmedmean.wrapper")

  res=run_dsc(parammaker,datamaker,methods,score,scenario_seedlist)
  aggregate(abs_error~method+scenario,res,mean)
{% endhighlight %}



{% highlight text %}
##         method scenario  abs_error
## 1         mean   Cauchy 1.57119365
## 2       median   Cauchy 0.04010486
## 3       winsor   Cauchy 0.05097203
## 4  trimmedmean   Cauchy 0.04126585
## 5         mean   normal 0.02746906
## 6       median   normal 0.03295318
## 7       winsor   normal 0.02831930
## 8  trimmedmean   normal 0.02881119
## 9         mean  uniform 0.01449181
## 10      median  uniform 0.02364241
## 11      winsor  uniform 0.01752364
## 12 trimmedmean  uniform 0.01938852
{% endhighlight %}



{% highlight r %}
  aggregate(squared_error~method+scenario,res,mean)
{% endhighlight %}



{% highlight text %}
##         method scenario squared_error
## 1         mean   Cauchy  1.081422e+01
## 2       median   Cauchy  2.756888e-03
## 3       winsor   Cauchy  4.576878e-03
## 4  trimmedmean   Cauchy  3.041492e-03
## 5         mean   normal  1.139656e-03
## 6       median   normal  1.627071e-03
## 7       winsor   normal  1.229033e-03
## 8  trimmedmean   normal  1.246792e-03
## 9         mean  uniform  2.945710e-04
## 10      median  uniform  7.922258e-04
## 11      winsor  uniform  4.401482e-04
## 12 trimmedmean  uniform  5.344231e-04
{% endhighlight %}

