# filtering-zomato-
# importing modules
import requests
from bs4 import BeautifulSoup
import pandas as pd

# https://www.whoishostingthis.com/tools/user-agent/

city = input('Enter your city name : ')
url = 'https://www.zomato.com/'+city+'/top-restaurants'
header = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.122 Safari/537.36'}

response = requests.get(url,headers=header)
html = response.text

soup = BeautifulSoup(html,'lxml')
print(soup.title.text,'\n')

# scraping raw data and stroing it in a array

top_rest = soup.find_all('div',class_="bke1zw-0 cMipmx")
arr = []

for tr in top_rest:
	rsts = tr.find_all('div',class_="bke1zw-1")
	for i in rsts:
		name = i.select('a',class_='sc-muxYx sc-euitrJ hIVRVA')
		for k in name:
			arr.append(k.text)
	break

# print(arr)

# rearranging restaurant data in a single list

data = []
temp = [arr[0]]
for i in arr[1:]:
	if i.replace('.','',1).isdigit():
		data.append(temp)
		temp = [i]
	else:
		temp.append(i)

# print(data)

# strong different attributes in 4 lists

restraunts = []
address = []
cuisine = []
ratings = []

for lst in data:
	ratings.append(lst[0])
	restraunts.append(lst[1])
	address.append(lst[2])
	cuisine.append(','.join(lst[3:]))


header = ['Restraunt','Address','Cuisine','Ratings']
indices = [i for i in range(1,len(restraunts)+1)]
all_rests = zip(restraunts,address,cuisine,ratings)
df = pd.DataFrame(list(all_rests),index=indices,columns=header)
df.to_csv('Top restraunts in '+city+'.csv')
print(df)
