# web Scrape Car Details 

### Web page scrape to get Vehicle infos from
(https://www.caranddriver.com/features/g32463239/new-ev-models-us/)

 ```  
import requests
from bs4 import BeautifulSoup
from csv import writer
import re
webpage = requests.get('https://www.caranddriver.com/features/g32463239/new-ev-models-us/')
soup = BeautifulSoup(webpage.content,'html.parser')
 ```  
# 1. simple method:
 parse Car details and save as 'electric_cars.csv'
  ```  
lists = soup.find('div',class_='listicle-body-content')
vehicles = lists.find_all('div', class_="listicle-slide listicle-slide-landscape listicle-slide-image listicle-slide-multi-retailer")

# Create CSV files 
with open('electric_cars.csv','w',newline='') as f:
  thewriter = writer(f)
  header = ['Make','Model','Base Price(USD$)', 'Range(miles)'] # headers 
  thewriter.writerow(header)

  for vehicle in vehicles:
      vehiclehead = vehicle.div.span.text.split('—') #title
      vehicleinfo = vehicle.find(class_="listicle-slide-dek").find_all('li') #find all the list tags
      vechiclePrice =  re.findall("[$].*",vehicleinfo[0].text.strip('*')) #trimming and also strip last'*' too
      vehicleRange = re.findall("[0-9][0-9][0-9]",vehicleinfo[2].text) #find 3digits numbers

      x = vehiclehead[0].find(" ") # find first ' ' empty space

      make = vehiclehead[0][:x].strip() #make
      model = vehiclehead[0][x:].strip() #model
      price = vechiclePrice[0].strip()  #base price
      range = vehicleRange[0].strip()  #range 

      infos = [make,model,price,range]
      thewriter.writerow(infos)      
```  

# 2. PANDA DATAFRAME method
web scrape, data cleaning, export as CSV file
import pandas as pd
 ```  
lists = soup.find('div',class_='listicle-body-content')
vehicles = lists.find_all('div', class_="listicle-slide listicle-slide-landscape listicle-slide-image listicle-slide-multi-retailer")

infos = []
for vehicle in vehicles:
    vehiclehead = vehicle.div.span.text.split('—') #title
    vehicleinfo = vehicle.find(class_="listicle-slide-dek").find_all('li') #find all the list tags
    vechiclePrice =  re.findall('[$].*',vehicleinfo[0].text.strip('$ *')) #trimming and also strip last'*' too
    vehicleRange = re.findall("[0-9][0-9][0-9]",vehicleinfo[2].text) #find 3digits numbers
    x = vehiclehead[0].find(" ") # find first ' ' empty space
    make = vehiclehead[0][:x].strip() #make
    model = vehiclehead[0][x:].strip() #model
    price = vechiclePrice[0].strip() #base price
    range = vehicleRange[0].strip()  #range 
    infos.append([make,model,price,range])
. give columns title name and convert Make into uppercase

df = pd.DataFrame(infos,columns=['Make','Model','Price(usd)','Range(miles)'] )
df['Make'] = df['Make'].str.upper() 
Export to csv files

df.to_csv('electric_cars.csv',index=False)
 ```  
