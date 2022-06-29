---
title:  "Scrapy 사용하기"
date:   2022-06-29 14:17:40 +0900
categories: dataset
---

## Install

{% highlight shell %}
$ conda create -n scrapy
$ conda activate scrapy
# Install scrapy
(scrapy) $ conda install scrapy 
# Install fake-useragent, selenium
(scrapy) $ conda install -c conda-forge fake-useragent selenium
# Install AWS SDK (for using S3 storage)
(scrapy) $ conda install -c conda-forge boto3
# Install redis & mongodb plugins
(scrapy) $ pip install scrapy-redis scrapy-dynamodb
{% endhighlight %}

