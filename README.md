# Website Performance Monitoring tool using Sitespeed.io and Jenkins
**Sitespeed.io is a *complete web performance tool* that helps you measure the performance of your website.**

**What is sitespeed.io good for?**

It is usually used in two different areas:

 - Running in your continuous integration to find web performance regressions early: on commits or when you move code to your test environment
 - Monitoring your performance in production, alerting on regressions.

To understand how sitespeed.io does these things, let's talk about how it works.

First a few key concepts:

 - Sitespeed.io is built upon a couple of other Open Source tools in the sitespeed.io suite.
 - [Browsertime](https://github.com/sitespeedio/browsertime) is the tool that drives the browser and collect metrics.
 - [The Coach](https://github.com/sitespeedio/coach) knows how to build fast websites and analyse your page and give you feedback what you should change.
 - Visual Metrics is metrics collected from a video recording of the browser screen.
 - Everything in sitespeed.io is a [plugin](https://www.sitespeed.io/documentation/sitespeed.io/plugins/) and they communicate by passing messages on a queue.

When you as user choose to test a URL, this is what happens on a high level:

 1. sitespeed.io starts and initialise all configured plugins.
 2. The URL is passed around the plugins through the queue.
    1. Browsertime gets the URL and opens the browser.
    2. It starts to record a video of the browser screen.
    3. The browser access the URL.
    4. When the page is finished, Browsertime takes a screenshot of the page.
    5. Then run some JavaScripts to analyse the page (using Coach and Browsertime scripts).
    6. Stop the video and close the browser.
    7. Analyse the video to get Visual Metrics like First Visual Change and Speed Index.
    8. Browsertime passes all metrics and data on the queue so other plugins can use it.
 3. The HTML/Graphite/InfluxDB plugin collects the metrics in queue.
 4. When all URLs are tested, sitespeed sends a message telling plugins to summarise the metrics and then render it.
 5. Plugins pickup the render message and the HTML plugin writes the HTML to disk.

 ## Try it out

Using Jenkins Pipeline :
* Create pipeline project and add this repo with Jenkinsfile
* Following screen appreas to execute the website test
![GrafanaPostImport](/images/form.PNG)
Where dropdown values represents following tools for executions:
  1) sitespeedtest : Using Sitespeed.io
  2) chromeuserexperience : Using Chrome User Experience Report (CrUx)
  3) lighthouse : Using Lighthouse
  4) gpsi : Using Google Page Speed Insights
* Once job run is successfull, HTML report is published and availiable in right side navigation panel
![GrafanaPostImport](/images/report1.PNG)
* Following HTML report will be published
![GrafanaPostImport](/images/report2.PNG)

## More details

Using sitespeed.io you can:
* Test your web site against Web Performance best practices using the [Coach](https://github.com/sitespeedio/coach).
* Collect Navigation Timing API, User Timing API and Visual Metrics from Firefox/Chrome using [Browsertime](https://github.com/sitespeedio/browsertime).
* Run your custom-made JavaScript and collect whichever metric(s) you need.
* Test one or multiple pages, across one or many runs to get more-accurate metrics.
* Create HTML-result pages or store the metrics in Graphite.
* Write your own plugins that can do whatever tests you want/need.

See all the latest changes in the [Changelog](https://github.com/sitespeedio/sitespeed.io/blob/main/CHANGELOG.md).

Checkout our example [dashboard.sitespeed.io](https://dashboard.sitespeed.io/dashboard/db/page-summary)

A summary report in HTML:
<img src="https://raw.githubusercontent.com/sitespeedio/sitespeed.io/main/docs/img/start-readme.jpg">

Individual page report:
<img src="https://raw.githubusercontent.com/sitespeedio/sitespeed.io/main/docs/img/start-url-readme.jpg">

Collected metrics from a URL in Graphite/Grafana:
<img src="https://raw.githubusercontent.com/sitespeedio/sitespeed.io/main/docs/img/grafana-readme.jpg">

And look at trends in Grafana:
<img src="https://raw.githubusercontent.com/sitespeedio/sitespeed.io/main/docs/img/grafana-trends-readme.jpg">

Video - easiest using Docker. This gif is optimized, the quality is much better IRL:

<img src="https://raw.githubusercontent.com/sitespeedio/sitespeed.io/main/docs/img/barack.gif">

<hr>
