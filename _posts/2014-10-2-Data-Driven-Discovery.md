---
layout: post
title: Data Driven Discovery
---

As I mentioned yesterday, one of the reasons I started this blog is
because I thought it might help kickstart a new project that I'm very
excited about. This new project is ultimately going to require some
community involvement and buy-in if it is to be successful, and a blog
post seems like one good way to kick things off. So here, as promised,
are more details.

As you may or may not have heard, the Gordon and Betty Moore
Foundation today announced the selection of 14 new investigators in
Data Driven Discovery (DDD), and I was fortunate enough to be one
these. I'm really excited about this initiative, and particularly
about its potential to help the sharing of information and ideas
across disciplines. To give you an idea of the variety of disciplines
represented, and the potential for new links to be made, I had met
only one of the 13 other selectees in person before the selection
process started - and yet I believe that I could have useful and
interesting interactions with all of them.

My proposal had a couple of aims, but the one I want to talk about
today is about "Dynamic Statistical Comparisons". This aim came out of
my belief that the way that we (the community) currently compare
statistical methods is horribly ineffective and inefficient. And, I
think we can do better by making comparisons more "dynamic" - by which
I mean easily update-able by adding new methods or new
datasets. Here's an extract from my grant proposal:

> Many new [statistical] methods are published without software
implementation, and without comparisons with existing methods. Even
when comparisons are made, they are usually performed by the research
group that developed one of the methods, which almost inevitably
favors that method. Furthermore, performing these kinds of comparisons
is incredibly time-consuming, requiring careful familiarization with
software implementing the methods, and the creation of pipelines and
scripts for running and comparing them. And in fast-moving fields new
methods or software updates appear so frequently that comparisons are
out of date before they even appear. In summary, the current system
results in a large amount of wasted effort, with multiple groups
performing redundant and sub-optimal comparisons.

And here's a bit more of a personal story I wanted to tell, but didn't have space.

In 2009 I was working with my then-postdoc Yongtao Guan on a Bayesian
approach to variable selection in large-scale regression (published
here in the Annals of Applied Statistics). It seemed natural to
compare our method with the regularized regression method LASSO, which
also attacks this problem. Surprisingly, even though both Bayesian
sparse regression and LASSO have been around 15-20 years, we could
find not one empirical comparison of these approaches! (In the
meantime some comparisons, including ours, have been published.) When
we applied LASSO to our problem we were surprised how poorly it
performed in our hands. Since LASSO has had a lot written about it,
with few if any mentions of poor performance, we were concerned that
we might be mis-applying it, or at least not making the best use of
it. After all, this was the first time we had actually used LASSO in
practice ourselves. So we talked to a few people with more experience
than us, who gave us a variety of suggestions. However, none of these
seemed to help much, and some made things considerably worse. Since we
never intended our research focus to be ``how to get LASSO to work on
this problem", we published our best attempt at a fair comparison.

But I remained concerned that our comparisons may be unfair to LASSO,
or at least could be done better. So I tried a different strategy,
with a Masters student (Weiling Tang). We took the paper on the
"Elastic Net" (EN), by Zou and Hastie, who are experts in regularized
regression, and who compared EN with LASSO under several simple
simulation scenarios. After brief correspondence with the authors we
were able to largely reproduce their results (boosting our confidence
that we were using the methods reasonably). Then we added our Bayesian
method to the comparisons. We found it generally outperformed both EN
and LASSO on these simulations.  However, on reflection I now had
another concern: the EN paper used 2-fold Cross-Validation (CV) to
select tuning parameters for both LASSO and EN, which being the same
for both methods, could be regarded as "fair", even if 2-fold CV
performed less well than, say, 10-fold CV.  However, our Bayesian
method does not use CV. With a different CV strategy, might EN or
LASSO outperform our method? So I'm left wondering what the best CV
strategy is. I feel like I can't do the comparison properly without
knowing that. But at the same time that almost seems like a research
question in itself, lying outside of the main thrust of my own
research program.

# The Solution

OK, so the obvious solution is to work together with other people,
right? For example, we could have contacted Zou and Hastie and invited
them to work with us on this and perhaps publish a joint paper? But
although I'm certainly open to this idea, I think we can do even
better. We should start by acknowledging that these types of
comparisons are better done as a dynamic exercise, with the aim of
steadily improving methods - not as static exercise to determine a
single best method once and for all.  Indeed, I really believe these
types of benchmarks and comparisons are more important for what they
can help us learn about the methods - what does and does not work well
in various situations - than for declaring some kind of "winner". And
I also believe this gets lost in most published comparisons, partly of
course because you have to show that your method is the best to get it
published....

 So here's the proposal (again, excerpted from my grant text):

> To address this we will create public Internet repositories that
compare methods with one another in a reproducible and
easily-extensible way. The repositories will provide “push-button”
reproducibility of all comparisons: running a single script will run
all the methods in the repository on all the data sets, and produce
tables and graphs comparing performances. It will be simple to add
code for a new method, or a new data set, and re-run the comparison
script. These repositories will help establish which methods perform
well on which data, and allow users to easily investigate how software
settings or types of input data affect performance. Having code for
each method available will substantially reduce the bar for others to
build on and improve methods. The repositories will be under version
control (git), so that the full history will be available, progress
can be tracked, and citations can reference a specific version. A
wiki-style forum will allow discussion or comments on results and
comparisons - for example, allowing users to note why some comparisons
favor certain types of methods. We envisage the repository as
complementing, rather than replacing, the standard publication model:
papers introducing a new method will “deposit” the method in the
comparison repository, in the same way that scientific data are
deposited in data repositories.

[At this point I should probably acknowledge that there are a whole
bunch of examples of this kind of community-based comparisons out
there, some of which I did mention elsewhere in my grant proposal, and
which serve as inspiration: CASP, the Netflix prize, DREAM, GAW, Sage
Bionetworks, just to start a list.]

So will it work? Obviously there are some non-trivial barriers to
making this work in complete generality. In some cases there will be
cross-platform compatibility issues, and data privacy issues, not to
mention the problem that coming up with good benchmark criteria can be
really hard for many problems. But I think that getting some simple
examples up and running should really be doable quite quickly - even
by a single research group working alone if we can be relieved of the
burden of being sure that we have done the best possible job for every
method. At that point anyone can join in to refine their favorite
method, or add a method or data set to the repository.

Although it is probably already clear that I'm super-enthusiastic
about this idea, I can't help but point out some of the reasons I'm
enthusiastic.

First, I think it will actually make comparisons not only better, but
also easier and less work! This is because everyone will only have to
be responsible for getting their own method to work, and not all the
others.

Second, this provides what I'm going to call a carrot for
reproducibility. Usually, making your work reproducible is a lot of
work, and perhaps the gain is not immediately obvious to
everyone. Here, by adding your method to the repository in a
reproducible way you get to exploit and take advantage of all the
infrastructure that other people have helped build that runs all the
other methods reproducibly.  So everyone reaps the benefits of
everyone's careful work to make things reproducible!

Finally, I am hopeful that this approach might help promote
collaboration through competition. It may seem like collaboration and
competition do not naturally go together... but I believe that once
people have competed methods against one another, they may naturally
move towards collaborating. (Effectively this is what happened with
the Netflix competition.) And the availability of code etc will of
course facilitate this whole process.

There's a lot more to say, but this post is already a bit long, and I
think it is time to post it.  I do of course welcome, and indeed
actively encourage, comments and suggestions.


