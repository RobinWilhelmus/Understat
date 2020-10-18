# Understat
Repo for scraping and plotting Understat data

There is an easy way to plot expected goal timelines and shotmaps yourself. Understat.com provides data of all shots in a game and their xG value. In some easy steps we will:

[- Install Understatr and tidy data](#install-understatr-and-tidy-data)

[- Inspect and modify the data](#inspect-and-modify-the-data)

[- Plot an xG timeline](#plot-an-xg-timeline)



I will assume that you have at least some knowledge with R and already have some packages installed. If not, start by searching info and tutorials about R and RStudio. 

# Install Understatr and get data

Download the Understatr package from [https://github.com/ewenme/understatr](https://github.com/ewenme/understatr) and load it along with the tidyverse

```
pacman::p_load(tidyverse, understatr)
```

Understatr has a few functions, see: 

[https://rdrr.io/github/ewenme/understatr/man/](https://rdrr.io/github/ewenme/understatr/man/) 

and 

[https://github.com/ewenme/understatr](https://github.com/ewenme/understatr)

Go to your preferred match on understat.com. The url will look like this : https://understat.com/match/14828

The number at the end is the match id. You need this number to get the shots. With this number you can run:
```
match <- get_match_shots(14828)
head(match)
```
Which will give you this:
```
# A tibble: 6 x 20
      id minute result     X     Y     xG player h_a   player_id situation season shotType match_id
   <dbl>  <dbl> <chr>  <dbl> <dbl>  <dbl> <chr>  <chr>     <dbl> <chr>      <dbl> <chr>       <dbl>
1 381911     14 Saved… 0.902 0.707 0.0362 Iago … h          2290 OpenPlay    2020 LeftFoot    14828
2 381912     23 Saved… 0.942 0.435 0.355  Santi… h          2384 SetPiece    2020 Head        14828
3 381913     25 ShotO… 0.919 0.371 0.355  Sergi… h          8122 OpenPlay    2020 RightFo…    14828
4 381914     27 Saved… 0.89  0.542 0.442  Santi… h          2384 OpenPlay    2020 LeftFoot    14828
5 381915     27 Block… 0.878 0.452 0.0999 Iago … h          2290 OpenPlay    2020 RightFo…    14828
6 381916     27 Misse… 0.853 0.34  0.0458 Fran … h          6931 OpenPlay    2020 RightFo…    14828
# … with 7 more variables: h_team <chr>, a_team <chr>, h_goals <dbl>, a_goals <dbl>, date <dttm>,
#   player_assisted <chr>, lastAction <chr>
```


# Inspect and modify the data

You can see the whole table with:

```
view(match)
```
For plotting the xG Time line, you don't need all the columns. You do need an extra column though, that will take the cumulative sum of the xG per team everytime there is a shot. To select the necessary columns (you can also keep them, but I prefer it this way. It also saves us some time in the next step) and get a new columnn we run:
```
shot_data <- match %>% 
      select(minute,result,xG,player,h_a,h_team,a_team) %>% 
      group_by(h_a) %>%
      mutate(cumulativexG = cumsum(xG))
 ```
The data is almost ready, the last thing that needs to be done is adding the start and end of the match to the data. Otherwise the plot will only have a line between the first and the last plot.
Besides that, we need the max cumulatice xG for both teams.

```
home <- shot_data %>% filter(h_a == "h")
homexG <- max(home$cumulativexG)
away <- shot_data %>% filter(h_a == "a")
awayxG <- max(away$cumulativexG)

start_h <- data.frame(0,"Missed",0,"Player","h",0,shot_data$h_team[1],shot_data$a_team[1]) %>% 
  setNames(c("minute","result","xG","player","h_a","cumulativexG","h_team","a_team"))
start_a <- data.frame(0,"Missed",0,"Player","a",0,shot_data$h_team[1],shot_data$a_team[1])%>% 
  setNames(c("minute","result","xG","player","h_a","cumulativexG","h_team","a_team"))
end_h <- data.frame(pmax(90,max(home$minute)),"Missed",0,"Player","h",homexG,shot_data$h_team[1],shot_data$a_team[1]) %>% 
  setNames(c("minute","result","xG","player","h_a","cumulativexG","h_team","a_team"))
end_a <- data.frame(pmax(90,max(away$minute)),"Missed",0,"Player","a",awayxG,shot_data$h_team[1],shot_data$a_team[1])%>% 
  setNames(c("minute","result","xG","player","h_a","cumulativexG","a_team","h_team"))
  
df <- rbind(start_h,start_a,shot_data,end_h,end_a)
```
df is now your data frame/tibble with all the shots and their (cumulative) xG per team. Now we can plot the timeline with ggplot!

# Plot an xG timeline

Install {ggthemes} and {ggtext} for this part





# Plot an xG shotmap



# Make everything look prettier 
