# Лабораторна робота 1. Завантаження та зчитування даних.

1. За допомогою download.file() завантажте любий excel файл з порталу http://data.gov.ua та зчитайте його (xls, xlsx – бінарні формати, тому встановить mode = “wb”. Виведіть перші 6 строк отриманого фрейму даних.
```r
library(xlsx)

xlsxUrl <- "https://data.gov.ua/dataset/841644c2-4eca-40b0-8e58-6b70db3fc481/resource/162f60ce-2048-4bb5-8546-c28f98aa6f4f/download/fuel_monthly_202002_full.xlsx"

download.file(xlsxUrl, "data1.xlsx", "auto", TRUE, "wb")
data1 <- read.xlsx("data1.xlsx", 1)
head(data1, 6);
```
```
  year month                          company company_code     plant_name fuel_type income  spend reserve_fact
1 2018     8              АТ "Херсонська ТЕЦ"     00131771 Херсонська ТЕЦ      АШ+П  0.000  0.000        0.000
2 2018     8              АТ "Херсонська ТЕЦ"     00131771 Херсонська ТЕЦ        ГД  0.000  0.000        0.000
3 2018     8              АТ "Херсонська ТЕЦ"     00131771 Херсонська ТЕЦ     мазут  0.000  0.000        1.008
4 2018     8              АТ "Херсонська ТЕЦ"     00131771 Херсонська ТЕЦ       газ     NA  0.000           NA
5 2018     8 ДВ "Нафтогазовидобувна компанія"     34181461  ТЕЦ-2 (Есхар)      АШ+П  1.941 18.378       18.254
6 2018     8 ДВ "Нафтогазовидобувна компанія"     34181461  ТЕЦ-2 (Есхар)        ГД  0.000  0.000        0.000
```
2. За допомогою download.file() завантажте файл getdata_data_ss06hid.csv за посиланням https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv та завантажте дані в R. Code book, що пояснює значення змінних знаходиться за посиланням https://www.dropbox.com/s/dijv0rlwo4mryv5/PUMSDataDict06.pdf?dl=0
Необхідно знайти, скільки property мають value $1000000+
```r
getdata_data_ss06hid_Url = "https://d396qusza40orc.cloudfront.net/getdata/data/ss06hid.csv"
download.file(getdata_data_ss06hid_Url, "data2.csv", "auto", TRUE)
data2 <- read.csv("data2.csv")

# Згідно з Cook book, значення "24" поля VAL відповідають значенням $1m+
sum(data2$VAL == 24, na.rm = TRUE)
```
```
[1] 53
```
3. Зчитайте xml файл за посиланням http://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Frestaurants.xml
Скільки ресторанів мають zipcode 21231?
```r
library(XML)
xmlUrl = "http://d396qusza40orc.cloudfront.net/getdata/data/restaurants.xml"

download.file(xmlUrl, "data3.xml", "auto", TRUE)
data3 <- xmlParse("data3.xml")
sum(xpathSApply(data3, "//zipcode", xmlValue) == 21231)
```
```
[1] 127
```
