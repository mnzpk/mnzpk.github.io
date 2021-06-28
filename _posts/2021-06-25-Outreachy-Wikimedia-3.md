---
layout: post
title: 'Wikimedia Internship: Weeks 3 & 4' 
---
About a month ago, I began my [Outreachy internship](/_posts/2021-05-23-Outreachy-Wikimedia.md) with Wikimedia. As part of this internship, I have been working on developing a tool that visualizes reader navigation behavior on Wikipedia, an [experimental version](https://wn-api-test.toolforge.org) of which is now up on [Toolforge](https://wikitech.wikimedia.org/wiki/Portal:Toolforge).  
Developing this tool has been very fun but it comes with its own set of challenges. These range from choosing the right way to process millions of records to making sure that the vizualizations that this tool generates are intuitive enough.  
One that has been particularly interesting is figuring out how to design an API that can filter the clickstream dump to return the incoming and outgoing links for an article instantaneously. The [Wikipedia clickstream](https://meta.wikimedia.org/wiki/Research:Wikipedia_clickstream) dataset contains dumps for 11 languages that get updated every month and while some of these are small enough to fit inside memory, the ones for more widely spoken languages such as Russian and English contain as many as 32 million records for just one month. As I mentioned above, this tool is hosted on Toolforge which is a platform maintained by Wikimedia foundation staff and volunteers to host tools built around Wikimedia projects. It is really cool as it is accessible to everyone and makes setting things up really easy but because it is a shared platform, there are some limitations on how much computational resources you can use per tool. In order to get around these limitations, my mentors and I decided to generate sqlite files for some of the larger dumps so that we could create indexes on columns that contain article names, ahead of time. This way we were able to filter rows for a particular article without reading the whole dataset into memory. I know this is common knowledge but I'll just say it anyway: Indexes are amazing. At their core, indexes are b-trees: a self-balancing tree data structure that keeps the data sorted and is capable of performing lookups in logarithmic time. This essentially means that if you have a billion values to look through, you'll only need to look at 5 nodes in the index to find the one you're looking for. There was only one problem with this solution though. The Toolforge filesystem is a networked one and sqlite doesn't work too well with NFS. This meant that even though indexes are very fast, reading from the sqlite db over NFS was still pretty slow. It occurred to me that the Toolforge documentation mentioned something about maintaining a [Redis](https://redis.io/) instance and so I decided to give that a try and it worked! I mean the latency is still a little noticeable the first time you request the API for an article but subsequent lookups are about 100x faster.  
The Outreachy theme for week 3 is 'Everyone Struggles' and this little incident made me realize why that's a good thing. Not ever struggling doesn't mean you know everything, it just means you're not trying out new ones and what fun would any of this be if it didn't involve learning something new everyday?  
The API and the tool are still far from done so don't forget to check back in a week or so if you're interested in learning more :)  