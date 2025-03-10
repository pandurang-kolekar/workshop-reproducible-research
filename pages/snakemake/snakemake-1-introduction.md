A *workflow management system* (WMS) is a piece of software that sets up,
performs and monitors a defined sequence of computational tasks (*i.e.* "a
workflow"). Snakemake is a WMS that was developed in the bioinformatics
community, and as such it has a number of features that make it particularly
well-suited for creating reproducible and scalable data analyses.

First of all the language you use to formulate your workflows is based on
Python, which is a language with strong standing in academia. However, users
are not required to know how to code in Python to work efficiently with
Snakemake. Workflows can easily be scaled from your desktop to server, cluster,
grid or cloud environments. This makes it possible to develop a workflow on
your laptop, maybe using only a small subset of your data, and then run the
real analysis on a cluster. Snakemake also has several features for defining
the environment with which each task is carried out. This is important in
bioinformatics, where workflows often involve running a large number of small
third-party tools.

Snakemake is primarily intended to work on _files_ (rather than for example
streams, reading/writing from databases or passing variables in memory). This
fits well with many fields of bioinformatics, notably next-generation
sequencing, that often involve computationally expensive operations on large
files. It's also a good fit for a scientific research setting, where the exact
specifications of the final workflow aren't always known at the beginning of
a project.

Lastly, a WMS is a very important tool for making your analyses reproducible.
By keeping track of when each file was generated, and by which operation, it is
possible to ensure that there is a consistent "paper trail" from raw data to
final results. Snakemake also has features that allow you to package and
distribute the workflow, and any files it involves, once it's done.

If you want to read more, you can find several resources here:

* The Snakemake documentation is available on [readthedocs](
  https://snakemake.readthedocs.io/en/stable/#).
* Here is another (quite in-depth) [tutorial](
  https://snakemake.readthedocs.io/en/stable/tutorial/tutorial.html#tutorial).
* If you have questions, check out [stack overflow](
  https://stackoverflow.com/questions/tagged/snakemake).

This tutorial depends on files from the course GitHub repo. Take a look at the
[setup](pre-course-setup) for instructions on how to set it up if you haven't
done so already, then open up a terminal and go to
`workshop-reproducible-research/tutorials/snakemake` and activate your
`snakemake-env` Conda environment.
