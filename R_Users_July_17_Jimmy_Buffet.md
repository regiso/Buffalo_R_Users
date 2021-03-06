<style>
.midcenter {
    position: fixed;
    top: 50%;
    left: 50%;
}
.footer {
    color: blue;
    position: fixed; top: 90%;
    text-align:left; width:100%;
}
</style>

<style>
.small-code pre code {
  font-size: 1em;
}
</style>



A Jimmy Buffet Inspired, Unsupervised Learning and Text Mining of Emotion Terms in R
========================================================
author: Regis O'Connor
date: July 2017
width: 1440
height: 900



<div class="footer" style="margin-top:-50px;font-size:70%;">
analyticsrmo@gmail.com</div>


***


<div class="midcenter" style="margin-left:400px; margin-top:-100px;">
<img src="buffet2.jpg"></img>
</div>




Objective & Methodology
========================================================

This is a reverse engineering of a similar analysis published May 17, 2017 in in Data Science + and shared on R-bloggers.

https://datascienceplus.com/unsupervised-learning-and-text-mining-of-emotion-terms-using-r/

Raw data is a csv file of 18 songs written by Jimmy Buffet.
The code uses tidyverse and tidytext to process the 18x2 data.frame through 8 steps including:
- tokenization     (tidytext)
- filtering        (tidyverse/dplyr)
- anti-joining     (tidyverse/dplyr)
- joining          (tidyverse/dplyr)
- lexicon matching (tidytext)
- summarizing      (tidyverse/dplyr)
- spreading        (tidyverse/tidyr)

<div class="footer" style="margin-top:-10px;font-size:70%;">
Regis O'Connor<br>
analyticsrmo@gmail.com<br>
https://github.com/regiso/Buffalo_R_Users </div>

<div class="midcenter" style="margin-left:100px; margin-top:-100px;">
<img src="Process_ppt.png"></img>
</div>

Pull Emotion Words, Aggregate by Song & Emotion
========================================================


```r
emotions_Jimmy <- song_lyrics %>%
  unnest_tokens(word, lyrics) %>%                           
  anti_join(stop_words, by = "word") %>%                  
  filter(!grepl('[0-9]', word)) %>%
  left_join(get_sentiments("nrc"), by = "word") %>%
  filter(!(sentiment == "negative" | sentiment == "positive")) %>%
  group_by(song, sentiment) %>%
  summarize( freq = n()) %>%
  mutate(percent=round(freq/sum(freq)*100)) %>%
  select(-freq) %>%
  spread(sentiment, percent, fill=0) %>%
  ungroup()

sd_scale_Jimmy <- function(x) {
  (x - mean(x))/sd(x)
}
emotions_Jimmy[,c(2:9)] <- apply(emotions_Jimmy[,c(2:9)], 2, sd_scale_Jimmy)
emotions_Jimmy <- as.data.frame(emotions_Jimmy)
rownames(emotions_Jimmy) <- emotions_Jimmy[,1]
emotions3_JB <- emotions_Jimmy[,-1]
emotions3_JB <- as.matrix(emotions3_JB)
```


<div class="footer" style="margin-top:-50px;font-size:50%;">
Regis O'Connor<br>
analyticsrmo@gmail.com<br>
https://github.com/regiso/Buffalo_R_Users </div>

Heatmap produced in RPres
========================================================
<div class="left" style="margin-left:100px">

<img src="R_Users_July_17_Jimmy_Buffet-figure/unnamed-chunk-2-1.png" title="plot of chunk unnamed-chunk-2" alt="plot of chunk unnamed-chunk-2" width="700px" height="1000px" />

</div>

<div class="footer" style="margin-top:-10px;font-size:70%;">
Regis O'Connor<br>
analyticsrmo@gmail.com<br>
https://github.com/regiso/Buffalo_R_Users </div>

Heatmap processed in Power Point and imported to RPres
========================================================

<div class="footer" style="margin-top:-50px;font-size:70%;">
Regis O'Connor<br>
analyticsrmo@gmail.com<br>
https://github.com/regiso/Buffalo_R_Users </div>


<div class="midcenter" style="margin-left:-500px; margin-top:-300px;">
<img src="ppt_heatmap.png"></img>
</div>
