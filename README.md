(index=palo_proxy earliest=-7d@d latest=@d)
| eval category_time=if(_time >= relative_time(now(), "@d"), "Today", "PastWeek")
| stats list(URLCategory) as Categories by URL, category_time
| eval TodayCategories=if(category_time="Today", mvdedup(Categories), null())
| eval PastWeekCategories=if(category_time="PastWeek", mvdedup(Categories), null())
| stats list(TodayCategories) as Today, list(PastWeekCategories) as PastWeek by URL
| eval Today=mvjoin(Today, ",")
| eval PastWeek=mvjoin(PastWeek, ",")
| eval Today=split(Today, ",")
| eval PastWeek=split(PastWeek, ",")
| eval ChangeDetected=if(mvcount(Today)>0 AND mvcount(PastWeek)>0 AND NOT mvfind(PastWeek, Today[0]), "Yes", "No")
| where ChangeDetected="Yes"
| table URL, Today, PastWeek
