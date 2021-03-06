# Scraping

Web scraping is a way of extracting data from websites. While this process could be done manually (by reading information on a website, and then copying that information to a file) it is usually done through the use of software. Scraping can be a valuable tool for extracting data. A website might not give you an option to download the content, either through an API or a direct download link. One example of such a website is the IMDB page that we will be scraping in this exercise.

In this assignment you will learn to use the Document Object Model (DOM) using Python via the BeautifulSoup library. We'll provide some scaffolding for the programming exercise. We will be trying to answer the question: Between 2008 and now, were there any years in which movies (from the top 50) scored significantly higher than in other years? With years, we mean the year the movie was released.

<!-- * The IMDB highest rated movies exercise: [moviescraper.py] -->

<!-- [moviescraper.py]: moviescraper.py -->
Below the pipeline of this assignment is shown. 

| Pipeline step          	| This week                                                                        	         |
|------------------------	|------------------------------------------------------------------------------------------- |
| Asking a Question      	| Were there any years in which movies (from the top 50) scored higher than in other years?  |
| Acquiring the data     	| Scraping IMDB to CSV                                                                       |
| Transforming the data 	| During scraping, and in calculating averages in visualizer.py                              |
| Visualizing the data  	| Plotting the average scores visualiser.py                                                  |
| Analyzing the data    	| Using your final product: a line chart                                         	           |

## Before starting

1. We assume Python 3, Requests, Pandas, Seaborn, and BeautifulSoup4 are installed (see [preparations] if this is not the case).

2. We will be looking at IMDB movies and getting data off this website. To get started, you should look at [the BeautifulSoup documentation].

3. To get you started we have provided you with a .zip file ([scraping_files.zip](scraping_files.zip)) that contains several files to help you to get started. Before you continue, download this file and check out the contents.

### Contents of `scraping_files.zip`

The zip file contains three files:

* The script `moviescraper.py` contains code to load the IMDB website, makes a local backup of it (`movies.html`) and outputs a CSV file (`movies.csv`).  You'll add code to the functions `extract_movies(dom)` and `save_csv(outfile, movies)` to ensure `movies.csv` contains the correct data. Keep reading for more information about the functions in this script and the things you should implement! 

* To help you validate your script we provide an example output CSV file `output.csv`. You can compare the formatting of your output (`movies.csv`) with the formatting of this file.

* When you have your output data (`movies.csv`), you'll try to get some insights into what we scraped. For this you'll be using `visualizer.py`.



<!-- You should run the `moviescraper.py` script with the command `python moviescraper.py`. This will copy the IMDB webpage to the current directory and save a CSV file. The example output file only contains the first two movies.


When you hand this exercise in be sure to submit: your `moviescraper.py`, `visualizer.py`, `movies.html`, and `movies.csv`. This will allow us to verify that your output CSV file is correct and that the script actually works given the HTML from IMDB.

 It could be that there are missing data (for instance the runtime). Insert an appropriate value when something is missing. Note that the director is not an actor or actress. -->

[preparations]: /resources/preparations
[the BeautifulSoup documentation]: https://www.crummy.com/software/BeautifulSoup/bs4/doc/

## DOM scraping and traversal

A webpage is a document. This document can be either displayed in the browser window or as the HTML source. The Document Object Model (DOM) represents that same document so it can be manipulated. The DOM is an object-oriented representation of the webpage and can be used as a programming interface for HTML and XML documents. It represents the page so that programs can change the document structure, style, and content.

To scrape data from webpages, we will be using BeautifulSoup, a Python web mining module. Its description is as follows: _Beautiful Soup is a Python library for pulling data out of HTML and XML files. It works with your favorite parser to provide idiomatic ways of navigating, searching, and modifying the parse tree. It commonly saves programmers hours or days of work._

This is the introductory exercise to BeautifulSoup. We will try to guide you along as much as possible, but you should read up on documentation and get used to doing that. It's a really useful skill and a big part of programming is self-­learning! Watch the following videos on HTML and the DOM. Ignore any reference to JavaScript (you may replace it in your mind with BeautifulSoup), as it will not be needed in our exercises.

![embed](https://www.youtube.com/embed/YK78KhMf7bs?t=68)

![embed](https://www.youtube.com/embed/GBKwdFEyJks)

### Building `moviescraper.py`

To get you started we have provided you with a script (`moviescraper.py`) that loads the correct IMDB address, makes a local backup of it (`movies.html`) and outputs a CSV file (`movies.csv`) that will contain only a header until you complete the implementation of the functions `extract_movies(dom)` and `save_csv(outfile, movies)`. Note that if you want to check your `movies.csv` file in Excel on a Mac, add 'sep=,' as the first line of the file. This way the file is parsed correctly in columns.

Open up and read the `moviescraper.py`-file (note that this is just some scaffolding, so you actually don't have to use this at all. As long as your code runs at the end of the day and produces the right results in a CSV file, we're happy). You can run the `moviescraper.py` script with the command `python moviescraper.py`.  

`moviescraper.py` contains 4 functions:

* `extract_movies(dom)`, which will be implemented by you. This function should extract a list of the top 50 movies from the DOM (from the IMDB website). 
* `save_csv(outfile, movies)` will also be implemented by you. This function should output a CSV file that contains the top 50 movies you've found in the `extract_movies(dom)` function.
*  `simple_get(url)` and `is_good_response(resp)` are already implemented. `simple_get(url)` returns the HTML content of the website. 

In the main function the HTML content is retrieved, which is then written to the current directory. Next, the HTML file is parsed into a DOM representation. With this dom representation, the movies can be extracted and saved into a csv file. 

The first step for you is to implement the `extract_movies(dom)` function. This function should extract a list of the highest rated movies from the passed DOM (which is of the IMDB page). Each movie entry should be a dictionary that contains the following fields:
  - Title
  - Rating
  - Year of release (only a number!)
  - Actors/actresses (comma separated if more than one)
  - Runtime (only a number!)

You might need to filter out some characters from a string. One method to do this is through the use of [Regular Expressions]. After importing `re`, `re.findall` can be used to find all occurances in a string, while `re.search` can be used to find the first occurence. Keep in mind that the resulting type after these Regular Expressions is still a string!

    >>> import re
    >>> re.findall(r'\d+', '123 dogs jumped the fence and ate over 4400 sheep!')
    ['123', '4400']
    >>> re.search(r'\d{4}', '123 dogs jumped the fence and ate over 4400 sheep!').group()
    '4400'

[Regular Expressions]: https://www.w3schools.com/python/python_regex.asp

Then, implement the `save_csv(outfile, movies)` function. It should write the list of the highest rated movies (`movies`) to `outfile`.

### Hints for scraping

`print()` is probably going to be your best friend for debugging, so print often, especially if something goes wrong.

Take a look at the following attributes, taken from [the BeautifulSoup documentation], that show some basic functionalities of BeautifulSoup.

        soup.title
        # <title>The Dormouse's story</title>

        soup.title.name
        # u'title'

        soup.title.string
        # u'The Dormouse's story'

        soup.title.parent.name
        # u'head'

        soup.p
        # <p class="title"><b>The Dormouse's story</b></p>

        soup.p['class']
        # u'title'

        soup.a
        # <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

        soup.find_all('a')
        # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
        #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
        #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

        soup.find(id="link3")
        # <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>

An easy method to finding what class, tag, or id to target with BeautifulSoup is:
- Go to the webpage in your favorite browser
- Right click on the element you are interested in
- Click 'Inspect element'

This will open the browser's inspector functionality which shows you the source HTML of the element you have clicked. Hovering over this HTML source will show the corresponding webpage element.

Also have a look at the `find()` function in the documentation of BeautifulSoup4 and look for the CSS selectors, they will make this exercise much easier! Read this part of the documentation and note that you can search by CSS class using the keyword argument `class_`:

        soup.find_all("a", class_="sister")
        # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
        #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
        #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]




## Visualizing the data

Now that we have the data in a `.csv`-format, it is time to try to get some insights into what we scraped. For this you will be using `visualizer.py`.

For now, the main function only prints the variable `data_dict` and it's type, to give you some insight into what the variable is.

Your aim in this part of the exercise is to visualize the data scraped from IMDB in a line chart. In order for the data to be plotted in a line chart, the data has to be transformed. Find the average rating a movie in the top 50 of IMDB has gotten for the years 2008-now. Again, year means the year movies came out. With your visualization you should be able to answer the research question: "Between 2008 and now, were there any years in which movies (from the top 50) scored significantly higher than in other years?".

Potentially interesting things to look at for this part of the exercise are the [Pandas read csv method] and [Seaborn]. If you feel that there is perhaps a better way to answer our question using your data, feel free to **also** create any other plots.

[Pandas read csv method]: https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html
[Seaborn]: https://seaborn.pydata.org/api.html

Once the data is in the correct format, you can plot it using Matplotlib or Seaborn. You can read [this tutorial] on some of the basics of plotting data using Matplotlib. Of course, there is much more documentation to be found online, which can either help you on your way with plotting the data, or make your already existing plot more aesthetically pleasing or more effective.

[this tutorial]: https://matplotlib.org/users/pyplot_tutorial.html

Reflect on the figure your script creates. How do you choose the range of the y-axis? Are all elements present in the figure? Think about the consequences of the design choices you make.

Don't forget to keep an eye on code design. Use functions, choose useful names for your variables, prevent code repetition, place comments, etc.


## Finished?

If you have finished the scraper and visualization, you can continue to the next exercise: [Crawler]

[Crawler]: /acquisition/crawling

## Submitting

1. `moviescraper.py`
2. `visualizer.py`
3. `movies.html`
4. `movies.csv`




<!-- In this course you will use GitHub to submit your code and all other documents. You will also use GitHub Pages to publish your visualizations.


### Git

Please attend the lecture on Introduction to GitHub which will guide your through the setup and basic git commands. When you are all set-up follow the instructions below.


### Github

Structure your assignments folder as follows: make two subfolders *Homework* and *Design*. In each folder you will weekly make a new folder with a week number and put your documents from that week in there. Specific instructions on what should be committed each week are to be found in the assignments description. For more information on the lay-out of your repository, see the documentation in the menu on the left under 'Resources'.

__Submit the URL of your repository__ below. Make sure your submit a link to the root of your repository, i.e. https://github.com/*username*/dataprocessing

Please may refer to this [Github manual] if you forgot your commands.

[Github manual]: Github_manual.pdf



Add a folder Week_1 to your DataProcessing/Homework repository containing the following files:

1. `moviescraper.py`
2. `visualizer.py`
3. `movies.html`
4. `movies.csv` -->
