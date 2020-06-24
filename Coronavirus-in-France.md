---
title: "Coronavirus in France"
date: "24/06/2020"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = FALSE)
```
```{r,message=FALSE}
library(leaflet)
library(data.table)
library(dplyr)
library(lubridate)
library(readxl)
nom <- read_xls("departements-francais.xls")
nom <- as.data.frame(nom)$NOM[1:96]
loc <- read.csv("Localisation_departements.csv")
covid <- as.data.frame(fread("coronavirus.csv"))
date <- max (ymd (unique (covid$jour)))
covid <- filter (covid, jour==date, sexe==0, dep<=95)
df <- data.frame (departement = covid$dep,
                  nom = paste(nom , ": deaths = ", covid$dc),
                  hospitalisation = covid$hosp,
                  reanimation = covid$rea,
                  out = covid$rad,
                  death = covid$dc,
                  latitude = loc$Latitude,
                  longitude = loc$Longitude)
```
```{r}
df %>%
     leaflet() %>%
      addTiles() %>%
      addCircles(lng = df$longitude,
                 lat = df$latitude,
                 weight = df$death/50,
                 fill=TRUE,
                 color = rgb(0.5,0,1,0.6),
                 label = df$nom)
```

