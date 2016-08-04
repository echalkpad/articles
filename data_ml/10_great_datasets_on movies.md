# 10 Great Datasets on Movies

[Original URL](http://zanmel.com/2015/12/14/10-great-datasets-on-movies/)

> Data on movies is very useful from a statistical learning perspective. Instructors of statistics & machine learning programs use movie data instead of dryer & more esoteric data sets to...

Data on movies is very useful from a statistical learning perspective. Instructors of statistics &amp; machine learning programs use movie data instead of dryer &amp; more esoteric data sets to explain key concepts. Since movies are universally understood, teaching statistics becomes easier since the domain is not that hard to understand.

Here are 10 great datasets on movies. Please feel free to add any I may have missed out.

[Movielens Data by GroupLens](http://grouplens.org/datasets/movielens/)

GroupLens Research has collected and made available rating data sets from the MovieLens web site (

[http://movielens.org](http://movielens.org/)

). The data sets were collected over various periods of time, depending on the size of the set. Before using these data sets, please review their README files for the usage licenses and other details.

[UC Irvine Machine Learning Lab’s Movie Data Set](https://archive.ics.uci.edu/ml/datasets/Movie)

This data set contains a list of over 10000 films including many older, odd, and cult films. There is information on actors, casts, directors, producers, studios, etc. The data is stored in relational form across several files. The central file (MAIN) is a list of movies, each with a unique identifier. These identifiers may change in successive versions. The actors (CAST) for those movies are listed with their roles in a distinct file. More information about individual actors (ACTORS) is in a third file. All directors in MAIN are listed in a fourth file (PEOPLE), with a number of important producers, writers, and cinematographers. A fifth file (REMAKES) links movies that were copied to a substantial extent from each other. The sixth file (STUDIOS) provides some information about studios shown in MAIN.

[Hadley Wickham’s Normalized IMDB Movie Data](http://had.co.nz/data/movies/)

IMDB makes their raw data available. Unfortunately, the data is divided into many text files and the format of each file differs slightly. To create one data file containing all the desired information Wickham wrote a script in the ruby to extract the relevant information and store in a database. This data was then exported into csv for easy import into many programs. The following text files were downloaded and used:

- business.list. : Total budget

- Genres.list. Genres that a movie belongs to (eg. comedy and action)

- movies.list. Master list of all movie titles with year of production.

-  mpaa-ratings-reasons.list. MPAA ratings.

-  ratings.list. IMDB fan ratings.

- running-times.list. Movie length in minutes.

[AM Stat Movie Data Set](http://www.amstat.org/publications/jse/v17n1/datasets.mclaren.html)

The Movie dataset contains weekend and daily per theater box office receipt data as well as total U.S. gross receipts for a set of 49 movies. Dates are provided for all time series values. The diverse list of movies was selected, not at random, but to spark student interest and to provide a range of box office values. The values provide a rich dataset to use for applications such as simple graphical analysis, a variety of time series and causal forecasting models, curve-fitting, and rate of change analysis. A series of assignment questions is included and the accompanying Instructor’s Manual provides representative solutions.

[LinkedMDB](http://data.linkedmdb.org/)

[LinkedMDB](http://data.linkedmdb.org/)

 publishes linked open data using the

[D2R Server](http://www4.wiwiss.fu-berlin.de/bizer/d2r-server/)

. The

[project](http://wiki.linkedmdb.org/Main/About)

 aims at publishing the first open semantic web database for movies, including

[a large number](http://wiki.linkedmdb.org/Main/Statistics)

 of

[interlinks](http://wiki.linkedmdb.org/Main/Interlinking)

 to several datasets on the open data cloud and references to related web pages.

The data is stored

[here](https://datahub.io/dataset/linkedmdb)

[Open Movie Database](http://www.omdbapi.com/)

The OMDb API is a free web service to obtain movie information, all content and images on the site are contributed and maintained by our users.

[Freebase Film Database](https://www.freebase.com/film/film?instances)

Freebase’s database of movie title &amp; all its metadata includes more than 340,000 titles.

[Cornell University Movie Review Data](http://www.cs.cornell.edu/people/pabo/movie-review-data/)

This page is a distribution site for movie-review data for use in sentiment-analysis experiments. Available are collections of movie-review documents labeled with respect to their overall

_sentiment polarity_

(positive or negative) or

_subjective rating_

 (e.g., “two and a half stars”) and sentences labeled with respect to their

_subjectivity status_

 (subjective or objective) or

_polarity_

.

[Jonathan Koren Movies Data Set](http://jonathankoren.com/movies-dataset.html)

This dataset contains faceted metadata describing contemporary American films, along with relevant judgements by actual human users. This dataset can be used for developing personalized faceted search interfaces, among other projects requiring rich, structured metadata. This dataset was used for the experiments

[presented at WWW 08](http://www.soe.ucsc.edu/~jonathan/fp810-koren.pdf)

.

This dataset is a mashup of three seperate datasets:

- [IMDB Metadata](http://www.imdb.com/interfaces)

- [Netflix Prize User Ratings](http://www.netflixprize.com/)

- [MovieLens User Ratings](http://www.grouplens.org/node/73)
