---
title: "Curing my YouTube addiction with yt-dlp"
date: "2023-01-02T00:20:16-06:00"
draft: false
---

# Curing my YouTube addiction with yt-dlp

Of all the social media sites that I have been moving away from, I find that my YouTube addiction is the hardest to kick. There are so many channels that I really do enjoy and whose content has had a profound impact, whether for their artistry or for their educational value. The creators are great, but lately I've started to wonder about the platform itself. 

My main issue is with the algorithm. I'm especially susceptible to algorithmic content recommendation, and yet I still find that 95% of what is recommended to me is garbage that doesn't bring me any joy. So why do I still consume content? Here are a couple of reasons:

#### Little effort required
    
Watching videos is typically a very passive experience. It's easy to throw it on in the background while doing something else like cooking or working. The YouTube algorithm makes it so there is almost no friction in content consumption/discovery.
    
#### Lack of continuity

Videos tend to vary in length, especially depending on the genre of video. I tend to watch a mix of both long-form video essays that are 1-hour plus, and shorter form clips lasting 5-10 minutes. Because different channel can focus on such disparate themes and topics, it can often feel like there is a lack of progression between videos. Episodes of television may stand on their own, but at least there is usually some overarching plot line that the viewer can use to determine progress in a show. Although I didn't grow up watching a lot of TV, I never felt like overconsumption was as tempting. Even in the cases when I binged a show, I was consciously aware of how many episodes I watched based on the evolution of the plot. The differing lengths and lack of progression between videos contributes to a general lack of continuity, and makes it hard to gage exactly how much YouTube I consume.

#### Dopamine

It's common to associate addiction with with vices like drugs, sex, or gambling. I consider myself fortunate that I haven't personally struggled with any of these addictions, and I don't want to detract from the fact that they can be extremely debilitating. However, I feel that because Internet addiction doesn't have the same social stigma, it makes it difficult to identify it as a problem. There are no laws limiting YouTube use or consumption as there are with the above. Perhaps the other reason it is so hard to recognize is because we are all addicted. We are so dependent on technology that it has become hard to distinguish using it as a tool versus using it self-destructively.

#### Habit

The above factors all contribute to the formation of bad habits of unconscious consumption. It has gotten to the point where I'll mindlessly play a video at every possible pause in my day, as if the moment alone with my thoughts is too much to handle. I really value moments of introspection, and the fact that I seem to be afraid of it is really upsetting. 

## The solution?

> Every object, every being,\
> is a jar full of delight.\
> Be a connoisseur,\
> and taste with caution.\
> \- Rumi, *The Many Wines*

*The Many Wines*, by Rumi, is a Sufi poem cautioning others on the perils of pleasure. He compares the world's pleasures to "thousands of wines, that can take of our minds." The message is not to completely cut off all desire, but to reduce our interactions with the world to only the things that bring positivity to our lives.

Lately I feel like I haven't been a good connoisseur. I've gotten drunk in my sampling, and I've stopped making conscious decisions. I need some way to re-empower myself so that I can once again make deliberate choices.

My solution is simple: Get off the platform, and consume only the content I want to see. I wrote a Python script using `yt-dlp` to download and organize the five most recent videos from the channels that I want to follow. I deleted my YouTube account as well so that there isn't even a possibility to go back to the algorithm. 

```python
#!/bin/python
# Download yt sub content en masse using yt-dlp
import os

sub_list = open("/home/nick/Videos/subs.txt", "r")

for sub in sub_list.readlines():
    name, link = sub.split(",")
    os.system(f"""
        yt-dlp -f mp4 -I "1:5" "{link}" -o "$HOME/Videos/channels/{name}/%(upload_date>%Y-%m-%d)s-%(title)s.%(ext)s"
    """)
```

The script is dead-simple. We're looping through a file containing the aliases and links of all the channels I want to follow, then we download the 5 most recent videos and place them inside `~/Videos/channels/` in a directory under each channel alias.

There's almost certainly a better way to do this. In fact, it might even be possible to do this all by only using some of the many flags that yt-dlp comes with. I intiially tried writing an `awk` script to do this, but I was having a hard time getting it to recognize the video attribute variables (in the form `%(var)s`). Another thing I'll have to look into is whether its possible to use an RSS reader to organize things.

I feel like a lot of people, myself included, will talk about how they need to stop engaging in self-destructive behaviors, but will continue to do so regardless. It's hard to follow through with vague promises you make to yourself, so here's my attempt at a concrete and actionable solution.
