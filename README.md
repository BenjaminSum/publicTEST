(index=palo_proxy earliest=-7d@d latest=@d)
| eval category_time=if(_time >= relative_time(now(), "@d"), "Today", "PastWeek")
| stats list(URLCategory) as Categories by URL, category_time
| eval Categories=mvdedup(Categories)
| eval TodayCategories=if(category_time="Today", mvjoin(Categories, ","), "")
| eval PastWeekCategories=if(category_time="PastWeek", mvjoin(Categories, ","), "")
| stats values(TodayCategories) as TodayCats, values(PastWeekCategories) as PastWeekCats by URL
| eval TodayCats=mvjoin(TodayCats, ",")
| eval PastWeekCats=mvjoin(PastWeekCats, ",")
| eval TodayCats=split(TodayCats, ",")
| eval PastWeekCats=split(PastWeekCats, ",")
| eval TodayUnique=mvfilter(NOT match(PastWeekCats, mvindex(TodayCats, 0)))
| eval PastUnique=mvfilter(NOT match(TodayCats, mvindex(PastWeekCats, 0)))
| eval ChangeDetected=if(mvcount(TodayUnique)>0 OR mvcount(PastUnique)>0, "Yes", "No")
| where ChangeDetected="Yes"
| table URL, TodayCats, PastWeekCats
