# Dask - Parallel Processing Through Blocked Algorithms¶

[Original URL](http://dask.pydata.org/en/latest/)

> Dask collections provide parallel computation on larger-than-memory data while mimicking existing libraries dask.array = numpy + threading dask.bag = map, filter, toolz + multiprocessing...

Dask collections provide parallel computation on larger-than-memory data while mimicking existing libraries

- `dask.array` = `numpy` + `threading`
- `dask.bag` = `map, filter, toolz` + `multiprocessing`
- `dask.dataframe` = `pandas` + `multiprocessing` (experimental)

This increases the scale of comfortable data from _fits-in-memory_ to _fits-on-disk_ by intelligently streaming data from disk and by leveraging all the cores of a modern CPU.

Dask primarily targets parallel computations that run on a single machine. It integrates nicely with the existing PyData ecosystem and is trivial to setup and use:

```
conda install dask
or
pip install dask
```

Operations on dask collections (array, bag, dataframe) produce task graphs that encode blocked algorithms. Task schedulers execute these task graphs in parallel in a variety of contexts.

[![Dask collections and schedulers](http://dask.pydata.org/en/latest/_images/collections-schedulers.png)](http://dask.pydata.org/en/latest/_images/collections-schedulers.png) **Collections:**

Dask collections are the main interaction point for users. They look like NumPy and Pandas but generate dask graphs internally. If you are a dask _user_ then you should start here.

**Graphs:**

Dask graphs encode algorithms in a simple format involving Python dicts, tuples, and functions. This graph format can be used in isolation from the dask collections. If you are a dask _developer_ then you should start here.

- [Motivation](http://dask.pydata.org/en/latest/graphs.html)
- [Specification](http://dask.pydata.org/en/latest/spec.html)
- [Custom Graphs](http://dask.pydata.org/en/latest/custom-graphs.html)
- [Optimization](http://dask.pydata.org/en/latest/optimize.html)

**Scheduling:**

Schedulers execute task graphs. After a collection produces a graph we execute this graph in parallel, either using all of the cores on a single workstation or using a distributed cluster.

- [Shared Memory](http://dask.pydata.org/en/latest/shared.html)
- [Distributed Scheduling](http://dask.pydata.org/en/latest/distributed.html)

**Administrative:**

- [Install Dask](http://dask.pydata.org/en/latest/install.html)
- [How to inspect dask objects](http://dask.pydata.org/en/latest/inspect.html)
- [Frequently Asked Questions](http://dask.pydata.org/en/latest/faq.html)

Dask is part of the [Blaze](http://continuum.io/open-source/blaze/) project supported by [Continuum Analytics](http://continuum.io)

© Copyright 2015, Continuum Analytics.

Built with [Sphinx](http://sphinx-doc.org/) using a [theme](https://github.com/snide/sphinx_rtd_theme) provided by [Read the Docs](https://readthedocs.org).
