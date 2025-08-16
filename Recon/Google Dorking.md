# Google Dorking

## Task 1 - Ye Ol' Search Engine

Researching as a whole - especially in the context of Cybersecurity encapsulates almost everything you do as a pentester. "Search Engines" such as Google are huge indexers – specifically, indexers of content spread across the World Wide Web.

These essentials in surfing the internet use “Crawlers” or “Spiders” to search for this content across the World Wide Web, which will be discussed in 
the next task.

## Task 2 - Let's Learn About Crawlers

### What are Crawlers and how do They Work?

Crawlers discover content through various means. 

One being by pure discovery, where URL is visited by the crawler and information regarding the content type of the website is returned to the search engine. Another method crawlers use to discover content is by following any or all URLS found from previousy crawled websites.

Once a web crawler discovers a domain such as **mywebsite.com**, it will index the entire contents of the domain, looking for keywords and other miscellaneous information.

![Image](/IMAGES/Google%20Dorking/Google%20Dorking.png)

In the diagram, the search engine crawls a website named **mywebsite.com** and finds the words "Apple", "Banana" and "Pear". These words are then stored in a dictionary by the crawler, who then returns these to search engine i.e, Google.

Google now knows the domain **mywebsite.com** has the keywords "Apple", "Banana" and "Pear". If a user searches for any of these three keywords this website would appear and will be displayed to the user. 

However, crawlers tend to traverse every possible URL and file they can find. So if **mywebsite.com** had another URL to a website **anotherwebsite.com** the crawler will attempt to traverse that site as well and retrieve all of its content.

So building up on this scenario, the crawler first crawls the domain **mywebsite.co** where it discovers teh same keywords (“Apple", “Banana” and “Pear”) , but it also finds an external URL. The external URL of **anotherwebsite.com** is then crawled where the keywords ("Tomatoes", “Strawberries” and “Pineapples”) are found. The crawler dictionary now contains the contents of both websites, which is then stored and saved within the search engine.

#### Recapping

So to recap, the search engine now has knowledge of two domains that have been crawled:
1. mywebsite.com
2. anotherwebsite.com

Although note that “anotherwebsite.com” was only crawled because it was referenced by the first domain “mywebsite.com”. Because of this reference, the search engine knows the following about the two domains:

| Domain Name        | Keyword       |
|--------------------|---------------|
| mywebsite.com      | Apples        |
| mywebsite.com      | Bananas       |
| mywebsite.com      | Pears         |
| anotherwebsite.com | Tomatoes      |
| anotherwebsite.com | Strawberries  |
| anotherwebsite.com | Pineapples    |

Now that the search engine has some knowledge about keywords, say if a user was to search for “Pears” the domain “mywebsite.com” will be displayed - as it is the only crawled domain containing "Pears". 

Likewise, the user now searches for "Strawberries". The domain "anotherwebsite.com" will be displayed, as it is the only domain that has been crawled by the search engine that contains the keyword "Strawberries".

### Answer the questions below

- Name the key term of what a "Crawler" is used to do. This is known as a collection of resources and their locations

    **Index**

- What is the name of the technique that "Search Engines" use to retrieve this information about websites?

    **Crawling**

- What is an example of the type of contents that could be gathered from a website?

    **Keywords**

---

## Task 3 - Search Engine Optimisation

Imagine if a website had multiple external URL's (as they often do!) That'll require a lot of crawling to take place. There's always the chance that another website might have similar information as of that another website crawled - right? So how does the "Search Engine" decide on the hierarchy of the domains that are displayed to the user?

The answer to this is SEO (Search Engine Optimization) which is a prevalent topic in modern-day search engines. Business capitilize on improving domains SEO **"ranking"**. At an abstract view search engines will priotrize those domains that are easier to index. There are many factors in how “optimal” a domain is - resulting in something similar to a point-scoring system.

To highlight a few factors influencing this point scoring, includes:

- How responsive your website is to different browsers (Chrome, Firefox and Internet Explorer) - this includes mobile phones as well

- How easy it is to crawl your website (or if crawling is allowed or not) through the use of "Sitemaps"

- What kind of keywords your site has

Although there is a lot of diversity in ranking done by different search engines involving vast algorithms which are usually hidden but you as they are businesses at the end of the day, so you can pay to advertise the order of your domains display.

## Task 4 - Beepboop - Robots.txt

Aside from the search engines who provide these "Crawlers", website/web-server owners themselves ultimately stipulate what content "Crawlers" can scrape. Search engines will want to retrieve everything from a website - but there are a few cases where we wouldn't want all of the contents of our website to be indexed! How about a secret administrator login page? We don't want everyone to be able to find that directory - especially through a google search.

### Robots.txt

Similar to "Sitemaps" which we will later discuss, this file is the first thing indexed by "Crawlers" when visiting a website.

This file is served at the root directory - specified by the web server itself. 

This file determines the permissions the "Crawler" has to the site. For e.g, what type of crawlers are allowed to index the site d (i.e. You only want Google's "Crawler" to index your site and not MSN's). Moreover it can specify what files and directories that we do or dont want to be indexed by the crawler.

A very basic markup of a Robots.txt is like the following:

```
1   User-agent: *
2   Allow: /
3
4   Sitemap: http://mywebsite.com/sitemap.xml
5
6
```

Here we have a few keywords:

| Keyword        | Function       |
|--------------------|---------------|
| User-agent     | Allows the type of crawlers that are allowed to index the site (Here * determines that all type of crawlers are allowed)       |
| Allow      | Specify the directories or files that the "Crawler" can index       |
| Disallow  | Soecify the directories or files that the "Crawler" cannot index         |
| Sitemap | Provides a reference as to where the sitemap is located (Improves SEO and will be discussed later)      |


In this case:

1. Any "Crawler" can index the site

2. The "Crawler" is allowed to index the entire contents of the site

3. The "Sitemap" is located at http://mywebsite.com/sitemap.xml

Say we wanted to hide directories or files from a "Crawler"? Robots.txt works on a "blacklisting" basis. Essentially, unless told otherwise, the Crawler will index whatever it can find.

```
User-agent: *
Disallow: /super-secret-directory/
Disallow: /not-a-secret/but-this-is/

Sitemap: http://mywebsite.com/sitemap.xml
```

In this case:

1. Any "Crawler" can index the site

2. The "Crawler" can index every other content that isn't contained within "/super-secret-directory/".

Crawlers also know the differences between sub-directories, directories and files. Such as in the case of the second "Disallow:" ("/not-a-secret/but-this-is/")

The "Crawler" will index all the contents within "/not-a-secret/", but will not index anything contained within the sub-directory "/but-this-is/".

3. The "Sitemap" is located at http://mywebsite.com/sitemap.xml

### What if we Only Wanted Certain "Crawlers" to Index our Site?

We can stipulate this, as below:

```
User-agent: Googlebot
Allow: /

User-agent: msnbot
Disallow: /
```

In this case:

1. The "Crawler" "Googlebot" is allowed to index the entire site ("Allow: /")

2. The "Crawler" "msnbot" is not allowed to index the site (Disallow: /")

### How about Preventing Files From Being Indexed? 

Whilst you can make manual entries for every file extension that you don't want to be indexed, you will have to provide the directory it is within, as well as the full filename. Imagine if you had a huge site! What a pain...Here's where we can use a bit of regexing.

```
User-agent: *

Disallow: /*.ini$

Sitemap: http://mywebsite.com/sitemap.xml
```

In this case:

1. Any "Crawler" can index the site

2. However, the "Crawler" cannot index any file that has the extension of .ini within any directory/sub-directory using ("$") of the site.

3. The "Sitemap" is located at http://mywebsite.com/sitemap.xml

Why would you want to hide a .ini file for example? Well, files like this contain sensitive configuration details.

### Answer the questions below

- Where would "robots.txt" be located on the domain "ablog.com"

    **ablog.com/robots.txt**

- If a website was to have a sitemap, where would that be located?

    **/sitemap.xml**

- How would we only allow "Bingbot" to index the website?

    **User-agent: Bingbot**

- How would we prevent a "Crawler" from indexing the directory "/dont-index-me/"?

    **Disallow: /dont-index-me/**

- What is the extension of a Unix/Linux system configuration file that we might want to hide from "Crawlers"?

    **.conf**

---

## Task 5 - Sitemaps

### Sitemaps

Comparable to geographical maps in real life, “Sitemaps” are just that - but for websites!

“Sitemaps” are indicative resources that are helpful for crawlers, as they specify the necessary routes to find content on the domain. The below illustration is a good example of the structure of a website, and how it may look on a "Sitemap":

![Sitemaps](/IMAGES/Google%20Dorking/Sitemaps.png)

The blue rectangles represent the route to nested-content, similar to a directory I.e. “Products” for a store. Whereas, the green rounded-rectangles represent an actual page. However, this is for illustration purposes only - “Sitemaps” don't look like this in the real world.

They look something like this:

![Sitemaps XML](/IMAGES/Google%20Dorking/Sitemaps_XML.png)

“Sitemaps” are XML formatted

The presence of "Sitemaps" holds a fair amount of weight in influencing the "optimisation" and favorability of a website

### Why are "Sitemaps" so Favourable for Search Engines?

Search engines have a lot of data to process. The efficiency of how this data is collected is paramount. 

Resources like "Sitemaps" are extremely helpful for "Crawlers" as the necessary routes to content are already provided! All the crawler has to do is scrape this content - rather than going through the process of manually finding and scraping. Think of it as using a wordlist to find files instead of randomly guessing their names!

### Answer the questions below

- What is the typical file structure of a "Sitemap"?

    **XML**

- What real life example can "Sitemaps" be compared to?

    **Map**

- Name the keyword for the path taken for content on a website

    **Route**

## Task 6 - What is Google Dorking?

Google has a lot of websites crawled and indexed. For e.g, a user uses Google to look up Cat pictures. Whilst Google will have many Cat pictures indexed ready to serve, this is a rather trivial use of the search engine in comparison to what it can be used for.

Say if we wanted to narrow down our search query, we can use quotation marks. Google will interpret everything in between these quotation marks as exact and only return the results of the exact phrase provided...Rather useful to filter through the rubbish that we don't need as we have done so below:

![Google Dorking Example](/IMAGES/Google%20Dorking/Google_Dorking(1).png)

### Refining our Queries

We can use terms such as “site” (such as bbc.co.uk) and a query (such as "gchq news") to search the specified site for the keyword we have provided to filter out content that may be harder to find otherwise. For example, using the “site” and "query" of "bbc" and "gchq", we have modified the order of which Google returns the results.

For e.g, **site: bbc.co.uk gchq news**

### So What Makes "Google Dorking" so Appealing?

A few common terms we can search and combine include:

| Term       | Action                                                  |
|------------|---------------------------------------------------------|
| filetype:  | Search for a file by its extension (e.g. PDF)           |
| cache:     | View Google's Cached version of a specified URL         |
| intitle:   | The specified phrase MUST appear in the title of the page |


For example, let's say we wanted to use Google to search for all PDFs on bbc.co.uk:

**site:bbc.co.uk filetype:pdf**

![Google Dorking Example](/IMAGES/Google%20Dorking/Google_Dorking(2).png)

### Answer the questions below

- What would be the format used to query the site bbc.co.uk about flood defences

    **site: bbc.co.uk flood defenses**

- What term would you use to search by file type?

    **filetype**

- What term can we use to look for login pages?

    **intitle: login**