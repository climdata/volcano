---
title: "Volcano Activity"
author: "Kmicha71"
date: "19 8 2019"
output:
  html_document: 
    keep_md: true
  pdf_document: default
---



## Volcano Eruptions

Download the data from 

National Geophysical Data Center / World Data Service (NGDC/WDS): Significant Volcanic Eruptions Database. National Geophysical Data Center, NOAA. doi:10.7289/V5JW8BSH



```sh
 [ -f ./download/volcano.tsv ] && mv -f ./download/volcano.tsv ./download/volcano.tsv.bck

curl 'https://www.ngdc.noaa.gov/nndc/struts/results?ge_23=&le_23=&type_15=Like&query_15=&op_30=eq&v_30=&type_16=Like&query_16=&op_29=eq&v_29=&type_31=EXACT&query_31=None+Selected&le_17=&ge_18=&le_18=&ge_17=&op_20=eq&v_20=&ge_7=2&le_7=&bt_24=&st_24=&ge_25=&le_25=&bt_26=&st_26=&ge_27=&le_27=&type_13=Like&query_13=&type_12=Exact&query_12=&type_11=Exact&query_11=&display_look=54&t=102557&s=50' -H 'Connection: keep-alive' -H 'Upgrade-Insecure-Requests: 1' -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.62 Safari/537.36' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8' -H 'Referer: https://www.ngdc.noaa.gov/nndc/servlet/ShowDatasets?dataset=102557&search_look=50&display_look=50' -H 'Accept-Encoding: gzip, deflate, br' -H 'Accept-Language: en-US,en;q=0.9,de;q=0.8' -H 'Cookie: JSESSIONID=5A955F9D16A7EC2A7735904D203E4BBF; _ga=GA1.3.328504439.1565208239; _ga=GA1.2.334952429.1565210136; _gid=GA1.2.509708967.1566230961; _gid=GA1.3.509708967.1566230961' --compressed > ./download/volcano.tsv

```

```
##   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
##                                  Dload  Upload   Total   Spent    Left  Speed
##   0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0100 69900    0 69900    0     0  69900      0 --:--:--  0:00:01 --:--:-- 57436
```





```r
volcano <- read.csv("./download/volcano.tsv", sep="\t")
names(volcano)[names(volcano) == "Year"] <- "year"
names(volcano)[names(volcano) == "Month"] <- "month"
names(volcano)[names(volcano) == "VEI"] <- "vei"
names(volcano)[names(volcano) == "Latitude"] <- "latitude"
names(volcano)[names(volcano) == "Longitude"] <- "longitude"
names(volcano)[names(volcano) == "Name"] <- "name"
names(volcano)[names(volcano) == "DEATHS"] <- "deaths"
volcano$time <- NULL
volcano$ts <- NULL
volcano1 <- subset(volcano, !is.na(volcano$month))
volcano2 <- subset(volcano, is.na(volcano$month))
volcano1$ts <- signif(volcano1$year + (volcano1$month-0.5)/12, digits=6)
volcano2$ts <- signif(volcano2$year + 0.5, digits=6)
volcano1$time <- paste(volcano1$year,volcano1$month, '15 00:00:00', sep='-')
volcano2$time <- paste(volcano2$year,'07-01 00:00:00', sep='-')
volcano <- rbind(volcano1, volcano2)
volcano <- volcano[order(volcano$ts),]
volcanonew <-volcano[,c("year","month","time","ts","vei","latitude","longitude", "deaths")]

write.table(volcanonew, file = "csv/monthly_volcano.csv", append = FALSE, quote = TRUE, sep = ",",
            eol = "\n", na = "NA", dec = ".", row.names = FALSE,
            col.names = TRUE, qmethod = "escape", fileEncoding = "UTF-8")
```




## Plot Volcano Eruptions


```r
require("ggplot2")
```

```
## Loading required package: ggplot2
```

```
## Warning: package 'ggplot2' was built under R version 3.5.3
```

```r
volcano <- read.csv("./csv/monthly_volcano.csv", sep=",")
volcano1500 <- subset(volcano, volcano$year > 1499)
mp <- ggplot() +
      #geom_line(aes(y=volcano1500$vei, x=volcano1500$time), color="blue") +
      geom_segment(aes(y=volcano1500$vei, yend=0, x=volcano1500$ts, xend=volcano1500$ts), color="blue") +
      xlab("Year") + ylab("VEI []")
mp
```

![](README_files/figure-html/plot-1.png)<!-- -->

