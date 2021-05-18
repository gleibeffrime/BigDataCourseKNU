# Code Book
Описує зміни, трансформацію даних та роботу з ними, яку виконує скрипт `run_analysis.R`

0. Підготовка до роботи з даними
Вказуємо шляхи до файлів, з якими будемо працювати
```r
testSubjectPath <- "./UCI_HAR_Dataset/test/subject_test.txt"
testXPath <- "./UCI_HAR_Dataset/test/X_test.txt"
testYPath <- "./UCI_HAR_Dataset/test/Y_test.txt"
trainSubjectPath <- "./UCI_HAR_Dataset/train/subject_train.txt"
trainXPath <- "./UCI_HAR_Dataset/train/X_train.txt"
trainYPath <- "./UCI_HAR_Dataset/train/Y_train.txt"
featuresNamesPath <- "./UCI_HAR_Dataset/features.txt"
```
Зчитуємо назви атрибутів (features)
```r
featuresNamesData <- read.table(featuresNamesPath)
```
Перетворюємо таблицю атрибутів на вектор
```r
featuresNames <- featuresNamesData[[2]]
```
Зчитуємо test та train дані, присвоюємо відповідні імена колонкам
```r
testSubjectDataFrame <- read.table(testSubjectPath, col.names=c("subject"))
testXDataFrame <- read.table(testXPath, col.names=featuresNames)
testYDataFrame <- read.table(testYPath, col.names=c("y"))

trainSubjectDataFrame <- read.table(trainSubjectPath, col.names=c("subject"))
trainXDataFrame <- read.table(trainXPath, col.names=featuresNames)
trainYDataFrame <- read.table(trainYPath, col.names=c("y"))
```
1. Об’єднуємо навчальний та тестовий набори, щоб створити один набір даних.
```r
fullSubjectDataFrame <- rbind(testSubjectDataFrame, trainSubjectDataFrame)
fullXDataFrame <- rbind(testXDataFrame, trainXDataFrame)
fullYDataFrame <- rbind(testYDataFrame, trainYDataFrame)
```
2. Витягуємо лише вимірювання середнього значення та стандартного
відхилення (mean and standard deviation) для кожного вимірювання.
```r
filteredXDataFrame <- cbind(
  fullXDataFrame[, Reduce(`&`, list(grepl("mean", names(fullXDataFrame)), !grepl("meanFreq", names(fullXDataFrame))))],
  fullXDataFrame[, grepl("std", names(fullXDataFrame))]
)
```
3. Використаємо описові назви діяльностей для найменування
діяльностей у наборі даних.
```r
activityLabelsPath <- "./UCI_HAR_Dataset/activity_labels.txt"
activityLabels <- read.table(activityLabelsPath, col.names=c("activity_id", "activity_description"))
```
4. Відповідно присвоїмо змінним у наборі даних описові імена.
```r
namedYDataFrame <- data.frame(activity=activityLabels$activity_description[match(fullYDataFrame$y, activityLabels$activity_id)])
```
5. З набору даних з кроку 4 створити другий незалежний акуратний набір
даних (tidy dataset) із середнім значенням для кожної змінної для кожної діяльності та кожного суб’єкту (subject).
```r
library(dplyr)

mergedDataFrame <- cbind(fullSubjectDataFrame, namedYDataFrame, filteredXDataFrame)

# групуємо за діяльністю, суб’єктом та для кожного атрибуту знаходимо середнє значення
tidyDataset <- data.frame(mergedDataFrame %>% group_by(subject, activity) %>% summarise_all(funs(mean)))

# записуємо дата фрейм у файл
write.table(tidyDataset, "tidy_dataset.txt", row.names=FALSE)
```
