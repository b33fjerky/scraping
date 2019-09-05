# scrapy-cheatsheet

## EXPORT to CSV

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
### OR
```python
import csv
def write_to_csv(item):
	file_exists = os.path.isfile("items.csv")
	
	if isinstance(item, ypItems):
		fieldnames = ['yplistingTitle', 'yplistingWebpage', 'yplistingAddress', 'yplistingPhone', 'yplistingHours', 'yplistingDescription', 'yplistingSocial', 'listingGalleryurls', 'listingImageurl', 'listingImagepaths']
	elif isinstance(item, ListingItems):
		fieldnames = ['listingBusiness', 'listingAddress', 'listingDescription', 'listingTitle', 'listingGalleryurls', 'listingImagepaths']
	

	with open("items.csv", 'a') as csvfile:
		writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
		
		if not file_exists:
			writer.writeheader()

		writer.writerow(item)
	
	return item


class CsvPipeline(object):

	def process_item(self, item, spider):
		write_to_csv(item)
		return item
```
## JAVASCRIPT (Dynamic Content)

### Switching webpage iframe 
```python
.
.
driver.get("https://my.domain.com/directory/url")
driver.switch_to.frame("name_of_iframe")
.
.
```
### Parsing response to html with lxml and/or using Regex
```python
from lxml import html
.
.
def parse(self, response):
	parser = html.fromstring(response.text)
	XPATH_DESCRIP = "//article[@class='merchant']//text()"
	XPATH_FACEB   = "//a[@title='Facebook']/@href"
	ypItem['yplistingDescript'] =  parser.xpath(XPATH_DESCRIP)
	ypItem['yplistingSocial']	=  list(dict.fromkeys(parser.xpath(XPATH_SOCIAL)))
	##     -	-  	 -	  -	  ^ Removes duplicates 
	ypItem['yplistingImg']		=  re.findall("(https:\/\/ssmscdn\.yp\.ca\/image.*?)\"", response.text)))[:10]
	##	   -  	-	 -	  -	   -	-	 -	  -	   -	^ Grab first 10 results
```
## ITEMS

### Formating item fields
```python
class Items():
	listingPhone = scrapy.Field(
		input_processor=MapCompose(str.strip),
        output_processor=TakeFirst()
		) ##	-	-	-	-	-	-	^ Removes List
	listingTitle = scrapy.Field(
		input_processor=MapCompose(str.strip),
        output_processor=TakeFirst()
		)
```
### Passing item.field to callback {meta arguments)}
```python
def parse(self, response):
    for sel in response.xpath('//tbody/tr'):
        item = HeroItem()
        # Item assignment here
        url = 'https://' + item['server'] + '.battle.net/' + sel.xpath('td[@class="cell-BattleTag"]//a/@href').extract()[0].strip()

        yield Request(url, callback=self.parse_profile, meta={'hero_item': item})

def parse_profile(self, response):
    item = response.meta.get('hero_item')
    item['weapon'] = response.xpath('//li[@class="slot-mainHand"]/a[@class="slot-link"]/@href').extract()[0].split('/')[4]
    yield item
```