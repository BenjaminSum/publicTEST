
(index=palo_proxy earliest=-7d@d latest=@d)
| eval today=if(_time >= relative_time(now(), "@d"), "today", "past")
| stats list(eval(if(today="today", URLCategory, null()))) as TodayCategories,
        list(eval(if(today="past", URLCategory, null()))) as PastCategories by URL
| eval TodayCategories=mvdedup(TodayCategories)
| eval PastCategories=mvdedup(PastCategories)
| eval IsChanged=if(mvcount(mvintersect(TodayCategories, PastCategories))=0 AND mvcount(TodayCategories)>0 AND mvcount(PastCategories)>0, "Yes", "No")
| where IsChanged="Yes"
| table URL, TodayCategories, PastCategories
