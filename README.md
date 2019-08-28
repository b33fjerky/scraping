# scrapy-cheatsheet

## Export to CSV

#### settings.py
```python
ITEM_PIPELINES = {
   'discogs.pipelines.CsvPipeline': 800
}
```

#### pipelines.py
```python
import csv
def write_to_csv(item):
    writer = csv.writer(open("items.csv", 'a'), lineterminator='\n')
    writer.writerow([item[key] for key in item.keys()])

class CsvPipeline(object):
    
    def process_item(self, item, spider):
        write_to_csv(item)
        return item
```

## Switching webpage iframe (Dynamic Content)

```python

driver.switch_to.frame("name_of_iframe")

```
