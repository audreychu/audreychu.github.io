## Let's connect!
[LinkedIn](https://www.linkedin.com/in/audreychu/) | [Email](audreychu27@gmail.com) | [Github](https://github.com/audreychu)



<center>
<h2>Final Grades of Students in Secondary School</h2>
<h3>Data Visualization and Regression Analysis</h3>
<b>Statistical Data Science</b> - December 2016

<img width=40 src="https://www.r-project.org/logo/Rlogo.png"><br>
</center>


<p> Real dataset from <a href="https://archive.ics.uci.edu/ml/datasets/STUDENT+ALCOHOL+CONSUMPTION">UCI Machine Learning Repository</a> that contains a survey responses conducted on 396 students in a math class in secondary school.  With the application of stepwise procedure methods, data visualization, and classification methods, a multiple regression model will be selected to fit the best prediction on final grades.


<h3>Import R packages and libraries</h3>


```R
install.packages("rpart")
install.packages("rpart.plot")
install.packages("plyr")
install.packages("ggplot2")
install.packages("MASS")
```

    Updating HTML index of packages in '.Library'
    Making 'packages.html' ... done
    Updating HTML index of packages in '.Library'
    Making 'packages.html' ... done
    Updating HTML index of packages in '.Library'
    Making 'packages.html' ... done
    Updating HTML index of packages in '.Library'
    Making 'packages.html' ... done
    Updating HTML index of packages in '.Library'
    Making 'packages.html' ... done



```R
library(rpart.plot)
library(rpart)
library(plyr)
library(ggplot2)
library(MASS)
```

    Loading required package: rpart


<h3>Read in and Clean Data</h3>


```R
d1 = read.csv("~/Documents/4th Year/STA 141A/student-mat.csv")
# Drop G1 and G2 since we are only observing G3 (final grade)
d1 = d1[,-c(31:32)] 
```

<h2>I. Data Exploration</h2>

<h3>1.1 Stepwise Selection</h3>


```R
fit = lm(G3~.,d1)
step = stepAIC(fit, direction="both")
# step$anova
```

    Start:  AIC=1154.04
    G3 ~ school + sex + age + address + famsize + Pstatus + Medu + 
        Fedu + Mjob + Fjob + reason + guardian + traveltime + studytime + 
        failures + schoolsup + famsup + paid + activities + nursery + 
        higher + internet + romantic + famrel + freetime + goout + 
        Dalc + Walc + health + absences
    
                 Df Sum of Sq    RSS    AIC
    - reason      3     31.33 6022.2 1150.1
    - guardian    2     10.18 6001.0 1150.7
    - Fjob        4     80.83 6071.7 1151.3
    - nursery     1      1.76 5992.6 1152.2
    - Fedu        1      2.39 5993.2 1152.2
    - Pstatus     1      3.30 5994.1 1152.3
    - traveltime  1      8.48 5999.3 1152.6
    - paid        1      8.53 5999.4 1152.6
    - activities  1      9.26 6000.1 1152.7
    - internet    1     10.91 6001.7 1152.8
    - Dalc        1     11.42 6002.3 1152.8
    - school      1     14.18 6005.0 1153.0
    - famrel      1     14.96 6005.8 1153.0
    - address     1     15.04 6005.9 1153.0
    - Walc        1     19.03 6009.9 1153.3
    - health      1     20.34 6011.2 1153.4
    - higher      1     27.28 6018.1 1153.8
    - freetime    1     27.40 6018.2 1153.8
    <none>                    5990.8 1154.0
    - Medu        1     33.73 6024.6 1154.3
    - famsize     1     34.97 6025.8 1154.3
    - age         1     50.34 6041.2 1155.3
    - famsup      1     54.70 6045.5 1155.6
    - studytime   1     61.59 6052.4 1156.1
    - absences    1     63.73 6054.6 1156.2
    - schoolsup   1     69.21 6060.0 1156.6
    - Mjob        4    163.57 6154.4 1156.7
    - romantic    1     91.81 6082.6 1158.0
    - sex         1    107.56 6098.4 1159.1
    - goout       1    118.00 6108.8 1159.8
    - failures    1    452.56 6443.4 1180.8
    
    Step:  AIC=1150.1
    G3 ~ school + sex + age + address + famsize + Pstatus + Medu + 
        Fedu + Mjob + Fjob + guardian + traveltime + studytime + 
        failures + schoolsup + famsup + paid + activities + nursery + 
        higher + internet + romantic + famrel + freetime + goout + 
        Dalc + Walc + health + absences
    
                 Df Sum of Sq    RSS    AIC
    - guardian    2     10.92 6033.1 1146.8
    - Fjob        4     93.07 6115.2 1148.2
    - nursery     1      1.39 6023.6 1148.2
    - Pstatus     1      2.83 6025.0 1148.3
    - Fedu        1      3.66 6025.8 1148.3
    - activities  1      5.94 6028.1 1148.5
    - traveltime  1     10.00 6032.2 1148.8
    - Dalc        1     10.49 6032.7 1148.8
    - address     1     10.60 6032.8 1148.8
    - internet    1     10.67 6032.8 1148.8
    - paid        1     11.58 6033.7 1148.9
    - school      1     14.07 6036.2 1149.0
    - famrel      1     15.45 6037.6 1149.1
    - Walc        1     20.73 6042.9 1149.5
    - higher      1     23.43 6045.6 1149.6
    - health      1     28.99 6051.2 1150.0
    - freetime    1     29.41 6051.6 1150.0
    <none>                    6022.2 1150.1
    - famsize     1     34.54 6056.7 1150.4
    - Medu        1     38.05 6060.2 1150.6
    - age         1     50.91 6073.1 1151.4
    - famsup      1     55.74 6077.9 1151.7
    - studytime   1     64.67 6086.8 1152.3
    - absences    1     69.81 6092.0 1152.7
    - schoolsup   1     71.01 6093.2 1152.7
    + reason      3     31.33 5990.8 1154.0
    - romantic    1     91.29 6113.5 1154.0
    - Mjob        4    191.28 6213.4 1154.5
    - sex         1    100.84 6123.0 1154.7
    - goout       1    127.59 6149.8 1156.4
    - failures    1    472.77 6494.9 1178.0
    
    Step:  AIC=1146.82
    G3 ~ school + sex + age + address + famsize + Pstatus + Medu + 
        Fedu + Mjob + Fjob + traveltime + studytime + failures + 
        schoolsup + famsup + paid + activities + nursery + higher + 
        internet + romantic + famrel + freetime + goout + Dalc + 
        Walc + health + absences
    
                 Df Sum of Sq    RSS    AIC
    - Fjob        4     88.57 6121.7 1144.6
    - Fedu        1      2.96 6036.0 1145.0
    - nursery     1      3.01 6036.1 1145.0
    - Pstatus     1      3.97 6037.0 1145.1
    - activities  1      6.11 6039.2 1145.2
    - traveltime  1      8.58 6041.7 1145.4
    - Dalc        1      9.49 6042.6 1145.4
    - internet    1      9.79 6042.9 1145.5
    - school      1     11.97 6045.0 1145.6
    - paid        1     12.65 6045.7 1145.7
    - address     1     13.13 6046.2 1145.7
    - famrel      1     15.98 6049.1 1145.9
    - Walc        1     17.49 6050.6 1146.0
    - higher      1     28.34 6061.4 1146.7
    <none>                    6033.1 1146.8
    - health      1     30.90 6064.0 1146.8
    - famsize     1     33.15 6066.2 1147.0
    - freetime    1     33.48 6066.6 1147.0
    - Medu        1     35.81 6068.9 1147.2
    - age         1     40.78 6073.9 1147.5
    - famsup      1     54.73 6087.8 1148.4
    - studytime   1     67.72 6100.8 1149.2
    - schoolsup   1     70.08 6103.2 1149.4
    - absences    1     76.20 6109.3 1149.8
    + guardian    2     10.92 6022.2 1150.1
    - romantic    1     87.69 6120.8 1150.5
    + reason      3     32.07 6001.0 1150.7
    - Mjob        4    192.32 6225.4 1151.2
    - sex         1    101.51 6134.6 1151.4
    - goout       1    135.22 6168.3 1153.6
    - failures    1    467.23 6500.3 1174.3
    
    Step:  AIC=1144.58
    G3 ~ school + sex + age + address + famsize + Pstatus + Medu + 
        Fedu + Mjob + traveltime + studytime + failures + schoolsup + 
        famsup + paid + activities + nursery + higher + internet + 
        romantic + famrel + freetime + goout + Dalc + Walc + health + 
        absences
    
                 Df Sum of Sq    RSS    AIC
    - Fedu        1      1.46 6123.1 1142.7
    - nursery     1      3.03 6124.7 1142.8
    - Dalc        1      4.54 6126.2 1142.9
    - internet    1      5.39 6127.0 1142.9
    - Pstatus     1      5.51 6127.2 1142.9
    - Walc        1      6.90 6128.6 1143.0
    - traveltime  1      7.29 6128.9 1143.0
    - paid        1      8.61 6130.3 1143.1
    - activities  1     10.09 6131.7 1143.2
    - famrel      1     10.20 6131.9 1143.2
    - school      1     10.84 6132.5 1143.3
    - address     1     14.53 6136.2 1143.5
    - higher      1     25.80 6147.5 1144.2
    - health      1     27.08 6148.7 1144.3
    - famsize     1     28.98 6150.6 1144.4
    <none>                    6121.7 1144.6
    - freetime    1     32.93 6154.6 1144.7
    - Medu        1     33.24 6154.9 1144.7
    - age         1     42.20 6163.8 1145.3
    - famsup      1     53.33 6175.0 1146.0
    - schoolsup   1     56.53 6178.2 1146.2
    + Fjob        4     88.57 6033.1 1146.8
    - studytime   1     71.20 6192.9 1147.2
    - absences    1     71.93 6193.6 1147.2
    - romantic    1     77.77 6199.4 1147.6
    + reason      3     44.13 6077.5 1147.7
    + guardian    2      6.42 6115.2 1148.2
    - Mjob        4    184.01 6305.7 1148.3
    - sex         1    103.74 6225.4 1149.2
    - goout       1    129.27 6250.9 1150.8
    - failures    1    463.80 6585.5 1171.4
    
    Step:  AIC=1142.67
    G3 ~ school + sex + age + address + famsize + Pstatus + Medu + 
        Mjob + traveltime + studytime + failures + schoolsup + famsup + 
        paid + activities + nursery + higher + internet + romantic + 
        famrel + freetime + goout + Dalc + Walc + health + absences
    
                 Df Sum of Sq    RSS    AIC
    - nursery     1      2.79 6125.9 1140.8
    - Dalc        1      4.64 6127.8 1141.0
    - internet    1      5.68 6128.8 1141.0
    - Pstatus     1      5.71 6128.8 1141.0
    - Walc        1      7.25 6130.4 1141.1
    - traveltime  1      8.12 6131.2 1141.2
    - paid        1      8.17 6131.3 1141.2
    - activities  1      9.66 6132.8 1141.3
    - famrel      1     10.25 6133.4 1141.3
    - school      1     11.30 6134.4 1141.4
    - address     1     14.25 6137.4 1141.6
    - health      1     26.37 6149.5 1142.4
    - higher      1     26.78 6149.9 1142.4
    - famsize     1     28.52 6151.6 1142.5
    <none>                    6123.1 1142.7
    - freetime    1     32.24 6155.4 1142.8
    - age         1     42.46 6165.6 1143.4
    - famsup      1     52.01 6175.1 1144.0
    - Medu        1     54.73 6177.8 1144.2
    - schoolsup   1     55.68 6178.8 1144.2
    + Fedu        1      1.46 6121.7 1144.6
    + Fjob        4     87.07 6036.0 1145.0
    - studytime   1     69.78 6192.9 1145.2
    - absences    1     71.00 6194.1 1145.2
    - romantic    1     77.35 6200.5 1145.6
    + reason      3     43.75 6079.4 1145.8
    + guardian    2      7.04 6116.1 1146.2
    - Mjob        4    183.19 6306.3 1146.3
    - sex         1    103.46 6226.6 1147.3
    - goout       1    128.64 6251.8 1148.9
    - failures    1    477.93 6601.0 1170.4
    
    Step:  AIC=1140.85
    G3 ~ school + sex + age + address + famsize + Pstatus + Medu + 
        Mjob + traveltime + studytime + failures + schoolsup + famsup + 
        paid + activities + higher + internet + romantic + famrel + 
        freetime + goout + Dalc + Walc + health + absences
    
                 Df Sum of Sq    RSS    AIC
    - Dalc        1      4.35 6130.3 1139.1
    - Pstatus     1      5.34 6131.2 1139.2
    - internet    1      6.13 6132.0 1139.2
    - paid        1      7.46 6133.4 1139.3
    - Walc        1      7.89 6133.8 1139.4
    - traveltime  1      8.39 6134.3 1139.4
    - activities  1      9.25 6135.2 1139.5
    - famrel      1     10.42 6136.3 1139.5
    - school      1     11.87 6137.8 1139.6
    - address     1     14.25 6140.2 1139.8
    - health      1     26.42 6152.3 1140.5
    - famsize     1     26.93 6152.8 1140.6
    - higher      1     26.95 6152.9 1140.6
    <none>                    6125.9 1140.8
    - freetime    1     32.33 6158.2 1140.9
    - age         1     41.93 6167.8 1141.5
    - famsup      1     51.77 6177.7 1142.2
    - Medu        1     52.84 6178.7 1142.2
    - schoolsup   1     56.94 6182.8 1142.5
    + nursery     1      2.79 6123.1 1142.7
    + Fedu        1      1.22 6124.7 1142.8
    + Fjob        4     86.49 6039.4 1143.2
    - studytime   1     68.61 6194.5 1143.2
    - absences    1     70.50 6196.4 1143.4
    - romantic    1     78.49 6204.4 1143.9
    + reason      3     43.37 6082.5 1144.0
    + guardian    2      8.33 6117.6 1144.3
    - Mjob        4    182.29 6308.2 1144.4
    - sex         1    102.30 6228.2 1145.4
    - goout       1    131.06 6257.0 1147.2
    - failures    1    476.14 6602.0 1168.4
    
    Step:  AIC=1139.13
    G3 ~ school + sex + age + address + famsize + Pstatus + Medu + 
        Mjob + traveltime + studytime + failures + schoolsup + famsup + 
        paid + activities + higher + internet + romantic + famrel + 
        freetime + goout + Walc + health + absences
    
                 Df Sum of Sq    RSS    AIC
    - Walc        1      3.96 6134.2 1137.4
    - Pstatus     1      5.10 6135.4 1137.5
    - internet    1      5.71 6136.0 1137.5
    - paid        1      6.85 6137.1 1137.6
    - activities  1      8.41 6138.7 1137.7
    - traveltime  1      9.07 6139.3 1137.7
    - famrel      1     11.08 6141.3 1137.8
    - school      1     11.08 6141.3 1137.8
    - address     1     14.48 6144.7 1138.1
    - famsize     1     26.04 6156.3 1138.8
    - higher      1     26.30 6156.6 1138.8
    - health      1     27.17 6157.4 1138.9
    - freetime    1     29.64 6159.9 1139.0
    <none>                    6130.3 1139.1
    - age         1     44.06 6174.3 1140.0
    - Medu        1     50.32 6180.6 1140.4
    - famsup      1     52.72 6183.0 1140.5
    + Dalc        1      4.35 6125.9 1140.8
    + nursery     1      2.50 6127.8 1141.0
    - schoolsup   1     60.09 6190.3 1141.0
    + Fedu        1      1.32 6128.9 1141.0
    - studytime   1     68.68 6198.9 1141.5
    - absences    1     70.14 6200.4 1141.6
    + Fjob        4     82.56 6047.7 1141.8
    - romantic    1     78.98 6209.2 1142.2
    + reason      3     41.68 6088.6 1142.4
    + guardian    2      7.64 6122.6 1142.6
    - Mjob        4    186.29 6316.5 1143.0
    - sex         1     98.91 6229.2 1143.5
    - goout       1    129.36 6259.6 1145.4
    - failures    1    482.47 6612.7 1167.1
    
    Step:  AIC=1137.39
    G3 ~ school + sex + age + address + famsize + Pstatus + Medu + 
        Mjob + traveltime + studytime + failures + schoolsup + famsup + 
        paid + activities + higher + internet + romantic + famrel + 
        freetime + goout + health + absences
    
                 Df Sum of Sq    RSS    AIC
    - Pstatus     1      4.99 6139.2 1135.7
    - internet    1      5.87 6140.1 1135.8
    - traveltime  1      8.38 6142.6 1135.9
    - paid        1      8.96 6143.2 1136.0
    - activities  1      9.17 6143.4 1136.0
    - famrel      1      9.19 6143.4 1136.0
    - school      1     10.93 6145.1 1136.1
    - address     1     13.02 6147.2 1136.2
    - health      1     25.43 6159.6 1137.0
    - higher      1     26.34 6160.6 1137.1
    - famsize     1     27.61 6161.8 1137.2
    - freetime    1     29.76 6164.0 1137.3
    <none>                    6134.2 1137.4
    - age         1     43.53 6177.7 1138.2
    - Medu        1     49.10 6183.3 1138.5
    - famsup      1     54.95 6189.2 1138.9
    + Walc        1      3.96 6130.3 1139.1
    + nursery     1      3.18 6131.0 1139.2
    - schoolsup   1     60.84 6195.0 1139.3
    + Fedu        1      1.53 6132.7 1139.3
    + Dalc        1      0.41 6133.8 1139.4
    - studytime   1     65.25 6199.5 1139.6
    - absences    1     76.31 6210.5 1140.3
    + Fjob        4     77.19 6057.0 1140.4
    - romantic    1     79.98 6214.2 1140.5
    + reason      3     42.88 6091.3 1140.6
    + guardian    2      7.05 6127.2 1140.9
    - Mjob        4    187.73 6321.9 1141.3
    - sex         1    113.68 6247.9 1142.6
    - goout       1    134.66 6268.9 1144.0
    - failures    1    479.97 6614.2 1165.1
    
    Step:  AIC=1135.71
    G3 ~ school + sex + age + address + famsize + Medu + Mjob + traveltime + 
        studytime + failures + schoolsup + famsup + paid + activities + 
        higher + internet + romantic + famrel + freetime + goout + 
        health + absences
    
                 Df Sum of Sq    RSS    AIC
    - internet    1      4.84 6144.0 1134.0
    - paid        1      8.38 6147.6 1134.2
    - traveltime  1      8.52 6147.7 1134.3
    - famrel      1      9.08 6148.3 1134.3
    - school      1     10.50 6149.7 1134.4
    - activities  1     10.86 6150.1 1134.4
    - address     1     13.20 6152.4 1134.6
    - health      1     25.80 6165.0 1135.4
    - higher      1     27.34 6166.5 1135.5
    - freetime    1     29.40 6168.6 1135.6
    <none>                    6139.2 1135.7
    - famsize     1     32.38 6171.6 1135.8
    - age         1     44.64 6183.8 1136.6
    - Medu        1     55.11 6194.3 1137.2
    - famsup      1     55.65 6194.8 1137.3
    + Pstatus     1      4.99 6134.2 1137.4
    + Walc        1      3.84 6135.4 1137.5
    + nursery     1      2.80 6136.4 1137.5
    - schoolsup   1     60.02 6199.2 1137.5
    + Fedu        1      1.72 6137.5 1137.6
    + Dalc        1      0.37 6138.8 1137.7
    - studytime   1     64.97 6204.2 1137.9
    + Fjob        4     78.94 6060.3 1138.6
    - romantic    1     78.80 6218.0 1138.8
    - absences    1     81.55 6220.7 1138.9
    + reason      3     42.34 6096.9 1139.0
    + guardian    2      7.58 6131.6 1139.2
    - Mjob        4    189.67 6328.9 1139.7
    - sex         1    113.08 6252.3 1140.9
    - goout       1    134.78 6274.0 1142.3
    - failures    1    477.65 6616.9 1163.3
    
    Step:  AIC=1134.02
    G3 ~ school + sex + age + address + famsize + Medu + Mjob + traveltime + 
        studytime + failures + schoolsup + famsup + paid + activities + 
        higher + romantic + famrel + freetime + goout + health + 
        absences
    
                 Df Sum of Sq    RSS    AIC
    - traveltime  1      8.60 6152.6 1132.6
    - famrel      1      9.73 6153.8 1132.6
    - paid        1      9.73 6153.8 1132.6
    - school      1     10.33 6154.4 1132.7
    - activities  1     10.64 6154.7 1132.7
    - address     1     16.04 6160.1 1133.0
    - higher      1     26.28 6170.3 1133.7
    - health      1     28.78 6172.8 1133.9
    - freetime    1     29.76 6173.8 1133.9
    <none>                    6144.0 1134.0
    - famsize     1     31.64 6175.7 1134.0
    - age         1     48.12 6192.2 1135.1
    - Medu        1     54.37 6198.4 1135.5
    - famsup      1     54.48 6198.5 1135.5
    + internet    1      4.84 6139.2 1135.7
    + Walc        1      4.00 6140.0 1135.8
    + Pstatus     1      3.96 6140.1 1135.8
    + nursery     1      3.27 6140.8 1135.8
    + Fedu        1      1.96 6142.1 1135.9
    - schoolsup   1     60.69 6204.7 1135.9
    + Dalc        1      0.26 6143.8 1136.0
    - studytime   1     67.65 6211.7 1136.3
    - romantic    1     75.57 6219.6 1136.8
    + Fjob        4     75.44 6068.6 1137.1
    + reason      3     41.61 6102.4 1137.3
    + guardian    2      7.42 6136.6 1137.5
    - absences    1     87.05 6231.1 1137.6
    - Mjob        4    191.71 6335.7 1138.2
    - sex         1    116.16 6260.2 1139.4
    - goout       1    132.36 6276.4 1140.4
    - failures    1    478.62 6622.7 1161.7
    
    Step:  AIC=1132.57
    G3 ~ school + sex + age + address + famsize + Medu + Mjob + studytime + 
        failures + schoolsup + famsup + paid + activities + higher + 
        romantic + famrel + freetime + goout + health + absences
    
                 Df Sum of Sq    RSS    AIC
    - school      1      7.63 6160.3 1131.1
    - famrel      1      9.55 6162.2 1131.2
    - paid        1     10.26 6162.9 1131.2
    - activities  1     10.92 6163.6 1131.3
    - address     1     24.44 6177.1 1132.1
    - higher      1     27.24 6179.9 1132.3
    - health      1     28.50 6181.1 1132.4
    - famsize     1     29.28 6181.9 1132.5
    <none>                    6152.6 1132.6
    - freetime    1     31.70 6184.3 1132.6
    - age         1     45.60 6198.2 1133.5
    + traveltime  1      8.60 6144.0 1134.0
    - Medu        1     57.11 6209.8 1134.2
    + internet    1      4.92 6147.7 1134.3
    + Pstatus     1      4.08 6148.6 1134.3
    - famsup      1     58.82 6211.5 1134.3
    + nursery     1      3.47 6149.2 1134.3
    + Walc        1      3.30 6149.3 1134.4
    + Fedu        1      2.91 6149.7 1134.4
    - schoolsup   1     60.68 6213.3 1134.5
    + Dalc        1      0.54 6152.1 1134.5
    - studytime   1     71.49 6224.1 1135.1
    - romantic    1     77.03 6229.7 1135.5
    + Fjob        4     75.67 6077.0 1135.7
    + reason      3     42.82 6109.8 1135.8
    - absences    1     87.15 6239.8 1136.1
    + guardian    2      6.03 6146.6 1136.2
    - Mjob        4    196.28 6348.9 1137.0
    - sex         1    113.20 6265.8 1137.8
    - goout       1    137.98 6290.6 1139.3
    - failures    1    482.60 6635.2 1160.4
    
    Step:  AIC=1131.06
    G3 ~ sex + age + address + famsize + Medu + Mjob + studytime + 
        failures + schoolsup + famsup + paid + activities + higher + 
        romantic + famrel + freetime + goout + health + absences
    
                 Df Sum of Sq    RSS    AIC
    - famrel      1      8.34 6168.6 1129.6
    - paid        1     10.91 6171.2 1129.8
    - activities  1     12.91 6173.2 1129.9
    - address     1     19.04 6179.3 1130.3
    - health      1     29.30 6189.6 1130.9
    - higher      1     29.73 6190.0 1131.0
    <none>                    6160.3 1131.1
    - famsize     1     31.27 6191.5 1131.1
    - freetime    1     33.92 6194.2 1131.2
    - age         1     38.19 6198.5 1131.5
    + school      1      7.63 6152.6 1132.6
    - Medu        1     56.45 6216.7 1132.7
    + traveltime  1      5.90 6154.4 1132.7
    + internet    1      4.76 6155.5 1132.8
    + nursery     1      3.96 6156.3 1132.8
    + Pstatus     1      3.74 6156.5 1132.8
    + Walc        1      3.29 6157.0 1132.8
    + Fedu        1      3.20 6157.1 1132.9
    - schoolsup   1     61.92 6222.2 1133.0
    + Dalc        1      0.34 6159.9 1133.0
    - famsup      1     64.48 6224.7 1133.2
    - studytime   1     66.98 6227.3 1133.3
    - romantic    1     75.14 6235.4 1133.8
    + Fjob        4     75.74 6084.5 1134.2
    - absences    1     80.70 6241.0 1134.2
    + reason      3     42.76 6117.5 1134.3
    + guardian    2      5.49 6154.8 1134.7
    - Mjob        4    194.50 6354.8 1135.3
    - sex         1    110.20 6270.5 1136.1
    - goout       1    140.71 6301.0 1138.0
    - failures    1    489.10 6649.4 1159.2
    
    Step:  AIC=1129.6
    G3 ~ sex + age + address + famsize + Medu + Mjob + studytime + 
        failures + schoolsup + famsup + paid + activities + higher + 
        romantic + freetime + goout + health + absences
    
                 Df Sum of Sq    RSS    AIC
    - paid        1     11.18 6179.8 1128.3
    - activities  1     12.40 6181.0 1128.4
    - address     1     19.73 6188.3 1128.9
    - health      1     26.53 6195.1 1129.3
    - higher      1     30.29 6198.9 1129.5
    - famsize     1     30.63 6199.2 1129.5
    <none>                    6168.6 1129.6
    - age         1     35.33 6203.9 1129.8
    - freetime    1     39.30 6207.9 1130.1
    + famrel      1      8.34 6160.3 1131.1
    + school      1      6.42 6162.2 1131.2
    + traveltime  1      5.94 6162.7 1131.2
    + internet    1      5.37 6163.2 1131.2
    - Medu        1     57.64 6226.3 1131.3
    + nursery     1      3.90 6164.7 1131.3
    + Pstatus     1      3.62 6165.0 1131.4
    + Fedu        1      3.17 6165.4 1131.4
    - schoolsup   1     61.04 6229.6 1131.5
    + Walc        1      1.68 6166.9 1131.5
    + Dalc        1      0.94 6167.7 1131.5
    - famsup      1     65.49 6234.1 1131.8
    - studytime   1     69.84 6238.5 1132.0
    - romantic    1     78.82 6247.4 1132.6
    - absences    1     78.99 6247.6 1132.6
    + reason      3     42.65 6126.0 1132.9
    + Fjob        4     71.95 6096.7 1133.0
    + guardian    2      5.99 6162.6 1133.2
    - Mjob        4    195.42 6364.0 1133.9
    - sex         1    112.19 6280.8 1134.7
    - goout       1    139.27 6307.9 1136.4
    - failures    1    499.43 6668.0 1158.3
    
    Step:  AIC=1128.31
    G3 ~ sex + age + address + famsize + Medu + Mjob + studytime + 
        failures + schoolsup + famsup + activities + higher + romantic + 
        freetime + goout + health + absences
    
                 Df Sum of Sq    RSS    AIC
    - activities  1     13.71 6193.5 1127.2
    - address     1     20.29 6200.1 1127.6
    - health      1     29.17 6209.0 1128.2
    - famsize     1     30.70 6210.5 1128.3
    <none>                    6179.8 1128.3
    - age         1     33.95 6213.7 1128.5
    - higher      1     34.79 6214.6 1128.5
    - freetime    1     37.41 6217.2 1128.7
    + paid        1     11.18 6168.6 1129.6
    + famrel      1      8.61 6171.2 1129.8
    + school      1      7.00 6172.8 1129.9
    + internet    1      6.91 6172.9 1129.9
    - famsup      1     56.09 6235.9 1129.9
    + traveltime  1      6.30 6173.5 1129.9
    - Medu        1     56.55 6236.3 1129.9
    + Walc        1      3.33 6176.5 1130.1
    + nursery     1      3.25 6176.5 1130.1
    + Pstatus     1      2.93 6176.9 1130.1
    + Fedu        1      2.56 6177.2 1130.2
    + Dalc        1      0.24 6179.6 1130.3
    - schoolsup   1     63.14 6242.9 1130.3
    - studytime   1     74.69 6254.5 1131.1
    - romantic    1     77.77 6257.6 1131.2
    + reason      3     47.09 6132.7 1131.3
    - absences    1     79.34 6259.1 1131.3
    + guardian    2      5.90 6173.9 1131.9
    + Fjob        4     65.14 6114.7 1132.1
    - Mjob        4    195.58 6375.4 1132.6
    - sex         1    109.28 6289.1 1133.2
    - goout       1    135.69 6315.5 1134.9
    - failures    1    525.05 6704.8 1158.5
    
    Step:  AIC=1127.19
    G3 ~ sex + age + address + famsize + Medu + Mjob + studytime + 
        failures + schoolsup + famsup + higher + romantic + freetime + 
        goout + health + absences
    
                 Df Sum of Sq    RSS    AIC
    - address     1     23.19 6216.7 1126.7
    - health      1     29.24 6222.7 1127.0
    - age         1     30.73 6224.2 1127.1
    - famsize     1     31.15 6224.7 1127.2
    <none>                    6193.5 1127.2
    - higher      1     31.85 6225.4 1127.2
    - freetime    1     34.49 6228.0 1127.4
    + activities  1     13.71 6179.8 1128.3
    + paid        1     12.49 6181.0 1128.4
    - famsup      1     53.66 6247.2 1128.6
    + school      1      9.06 6184.4 1128.6
    + famrel      1      8.09 6185.4 1128.7
    - Medu        1     55.81 6249.3 1128.7
    + internet    1      6.66 6186.8 1128.8
    + traveltime  1      6.27 6187.2 1128.8
    + Pstatus     1      4.40 6189.1 1128.9
    + Walc        1      4.34 6189.2 1128.9
    + nursery     1      2.79 6190.7 1129.0
    + Fedu        1      1.96 6191.5 1129.1
    + Dalc        1      0.01 6193.5 1129.2
    - schoolsup   1     66.62 6260.1 1129.4
    - studytime   1     68.42 6261.9 1129.5
    - absences    1     78.41 6271.9 1130.2
    - romantic    1     82.09 6275.6 1130.4
    + reason      3     43.11 6150.4 1130.4
    + guardian    2      5.76 6187.7 1130.8
    + Fjob        4     68.02 6125.5 1130.8
    - Mjob        4    193.71 6387.2 1131.3
    - sex         1    102.28 6295.8 1131.7
    - goout       1    139.88 6333.4 1134.0
    - failures    1    521.42 6714.9 1157.1
    
    Step:  AIC=1126.66
    G3 ~ sex + age + famsize + Medu + Mjob + studytime + failures + 
        schoolsup + famsup + higher + romantic + freetime + goout + 
        health + absences
    
                 Df Sum of Sq    RSS    AIC
    - higher      1     30.51 6247.2 1126.6
    <none>                    6216.7 1126.7
    - health      1     32.15 6248.8 1126.7
    - famsize     1     35.29 6252.0 1126.9
    - freetime    1     35.60 6252.3 1126.9
    - age         1     38.25 6254.9 1127.1
    + address     1     23.19 6193.5 1127.2
    + activities  1     16.61 6200.1 1127.6
    + traveltime  1     14.98 6201.7 1127.7
    + paid        1     13.26 6203.4 1127.8
    + internet    1     11.18 6205.5 1128.0
    + famrel      1      8.79 6207.9 1128.1
    - famsup      1     54.57 6271.3 1128.1
    + Pstatus     1      4.68 6212.0 1128.4
    - Medu        1     58.58 6275.3 1128.4
    + school      1      2.73 6214.0 1128.5
    + nursery     1      2.53 6214.2 1128.5
    + Walc        1      2.14 6214.6 1128.5
    + Fedu        1      1.75 6214.9 1128.5
    + Dalc        1      0.31 6216.4 1128.6
    - studytime   1     65.49 6282.2 1128.8
    - schoolsup   1     66.73 6283.4 1128.9
    - absences    1     76.04 6292.7 1129.5
    - romantic    1     79.77 6296.5 1129.7
    + guardian    2      9.18 6207.5 1130.1
    + Fjob        4     69.25 6147.4 1130.2
    + reason      3     37.30 6179.4 1130.3
    - sex         1     95.98 6312.7 1130.7
    - Mjob        4    206.04 6422.7 1131.5
    - goout       1    132.37 6349.1 1133.0
    - failures    1    532.78 6749.5 1157.1
    
    Step:  AIC=1126.6
    G3 ~ sex + age + famsize + Medu + Mjob + studytime + failures + 
        schoolsup + famsup + romantic + freetime + goout + health + 
        absences
    
                 Df Sum of Sq    RSS    AIC
    - health      1     31.19 6278.4 1126.6
    <none>                    6247.2 1126.6
    + higher      1     30.51 6216.7 1126.7
    - freetime    1     34.90 6282.1 1126.8
    - famsize     1     36.85 6284.1 1126.9
    + address     1     21.84 6225.4 1127.2
    + paid        1     17.59 6229.6 1127.5
    - age         1     47.12 6294.3 1127.6
    + traveltime  1     15.45 6231.8 1127.6
    + activities  1     13.34 6233.9 1127.8
    - famsup      1     52.52 6299.7 1127.9
    + internet    1      9.68 6237.5 1128.0
    + famrel      1      9.46 6237.7 1128.0
    + Pstatus     1      5.41 6241.8 1128.2
    + school      1      4.31 6242.9 1128.3
    + Fedu        1      2.93 6244.3 1128.4
    + nursery     1      2.68 6244.5 1128.4
    + Walc        1      2.33 6244.9 1128.5
    - Medu        1     62.83 6310.0 1128.5
    + Dalc        1      0.13 6247.1 1128.6
    - schoolsup   1     66.79 6314.0 1128.8
    - absences    1     73.57 6320.8 1129.2
    - studytime   1     74.79 6322.0 1129.3
    + guardian    2     14.07 6233.1 1129.7
    - sex         1     83.06 6330.3 1129.8
    - romantic    1     89.44 6336.6 1130.2
    + Fjob        4     67.63 6179.6 1130.3
    + reason      3     33.51 6213.7 1130.5
    - Mjob        4    209.79 6457.0 1131.6
    - goout       1    130.43 6377.6 1132.8
    - failures    1    616.91 6864.1 1161.8
    
    Step:  AIC=1126.56
    G3 ~ sex + age + famsize + Medu + Mjob + studytime + failures + 
        schoolsup + famsup + romantic + freetime + goout + absences
    
                 Df Sum of Sq    RSS    AIC
    <none>                    6278.4 1126.6
    + health      1     31.19 6247.2 1126.6
    - freetime    1     32.73 6311.1 1126.6
    + higher      1     29.55 6248.8 1126.7
    + address     1     24.65 6253.7 1127.0
    - famsize     1     40.88 6319.3 1127.1
    - age         1     41.69 6320.1 1127.2
    + paid        1     20.93 6257.5 1127.2
    + traveltime  1     15.67 6262.7 1127.6
    + internet    1     14.25 6264.1 1127.7
    + activities  1     13.63 6264.8 1127.7
    - famsup      1     56.48 6334.9 1128.1
    + famrel      1      6.33 6272.1 1128.2
    + Pstatus     1      5.43 6273.0 1128.2
    + school      1      4.78 6273.6 1128.3
    + nursery     1      2.58 6275.8 1128.4
    + Fedu        1      1.71 6276.7 1128.5
    + Walc        1      1.26 6277.1 1128.5
    + Dalc        1      0.46 6277.9 1128.5
    - schoolsup   1     64.02 6342.4 1128.6
    - sex         1     72.33 6350.7 1129.1
    - absences    1     73.66 6352.1 1129.2
    - Medu        1     75.35 6353.7 1129.3
    - studytime   1     76.94 6355.3 1129.4
    + guardian    2     15.84 6262.6 1129.6
    + reason      3     41.84 6236.6 1129.9
    - romantic    1     96.59 6375.0 1130.6
    + Fjob        4     62.20 6216.2 1130.6
    - Mjob        4    196.96 6475.3 1130.8
    - goout       1    127.62 6406.0 1132.5
    - failures    1    628.39 6906.8 1162.2



<table>
<thead><tr><th scope=col>Step</th><th scope=col>Df</th><th scope=col>Deviance</th><th scope=col>Resid. Df</th><th scope=col>Resid. Dev</th><th scope=col>AIC</th></tr></thead>
<tbody>
	<tr><td>            </td><td>NA          </td><td>       NA   </td><td>355         </td><td>5990.833    </td><td>1154.044    </td></tr>
	<tr><td>- reason    </td><td> 3          </td><td>31.328693   </td><td>358         </td><td>6022.161    </td><td>1150.105    </td></tr>
	<tr><td>- guardian  </td><td> 2          </td><td>10.918410   </td><td>360         </td><td>6033.080    </td><td>1146.820    </td></tr>
	<tr><td>- Fjob      </td><td> 4          </td><td>88.573827   </td><td>364         </td><td>6121.654    </td><td>1144.577    </td></tr>
	<tr><td>- Fedu      </td><td> 1          </td><td> 1.460092   </td><td>365         </td><td>6123.114    </td><td>1142.671    </td></tr>
	<tr><td>- nursery   </td><td> 1          </td><td> 2.789976   </td><td>366         </td><td>6125.904    </td><td>1140.851    </td></tr>
	<tr><td>- Dalc      </td><td> 1          </td><td> 4.347820   </td><td>367         </td><td>6130.252    </td><td>1139.132    </td></tr>
	<tr><td>- Walc      </td><td> 1          </td><td> 3.955184   </td><td>368         </td><td>6134.207    </td><td>1137.386    </td></tr>
	<tr><td>- Pstatus   </td><td> 1          </td><td> 4.990484   </td><td>369         </td><td>6139.197    </td><td>1135.708    </td></tr>
	<tr><td>- internet  </td><td> 1          </td><td> 4.842193   </td><td>370         </td><td>6144.039    </td><td>1134.019    </td></tr>
	<tr><td>- traveltime</td><td> 1          </td><td> 8.601817   </td><td>371         </td><td>6152.641    </td><td>1132.572    </td></tr>
	<tr><td>- school    </td><td> 1          </td><td> 7.628992   </td><td>372         </td><td>6160.270    </td><td>1131.061    </td></tr>
	<tr><td>- famrel    </td><td> 1          </td><td> 8.341278   </td><td>373         </td><td>6168.611    </td><td>1129.596    </td></tr>
	<tr><td>- paid      </td><td> 1          </td><td>11.183352   </td><td>374         </td><td>6179.795    </td><td>1128.311    </td></tr>
	<tr><td>- activities</td><td> 1          </td><td>13.707231   </td><td>375         </td><td>6193.502    </td><td>1127.186    </td></tr>
	<tr><td>- address   </td><td> 1          </td><td>23.189741   </td><td>376         </td><td>6216.692    </td><td>1126.662    </td></tr>
	<tr><td>- higher    </td><td> 1          </td><td>30.509338   </td><td>377         </td><td>6247.201    </td><td>1126.596    </td></tr>
	<tr><td>- health    </td><td> 1          </td><td>31.191651   </td><td>378         </td><td>6278.393    </td><td>1126.563    </td></tr>
</tbody>
</table>



<h3>1.2 All-variable Model</h3>

<p><i>This model includes the significant variables as concluded by the stepwise procedure performed earlier as well as the additional of alcohol variables; weekday alcohol consumption (Dalc) and weekend alcohol consumption (Walc).  We include these two variables since we are interested in also studying the effects of substances in final grades.


```R
mod = lm(G3~sex + age + famsize + Medu + Mjob + studytime + failures + 
schoolsup + famsup + romantic + freetime + goout + absences + Dalc + Walc, data=d1)
summary(mod)
```


    
    Call:
    lm(formula = G3 ~ sex + age + famsize + Medu + Mjob + studytime + 
        failures + schoolsup + famsup + romantic + freetime + goout + 
        absences + Dalc + Walc, data = d1)
    
    Residuals:
         Min       1Q   Median       3Q      Max 
    -13.5239  -1.7739   0.3229   2.8448   8.8652 
    
    Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
    (Intercept)  13.48855    3.27643   4.117 4.72e-05 ***
    sexM          0.95057    0.47578   1.998  0.04645 *  
    age          -0.28047    0.18201  -1.541  0.12416    
    famsizeLE3    0.72991    0.46670   1.564  0.11866    
    Medu          0.56759    0.26174   2.168  0.03075 *  
    Mjobhealth    1.43387    1.01880   1.407  0.16013    
    Mjobother    -0.16623    0.66523  -0.250  0.80281    
    Mjobservices  0.98556    0.73737   1.337  0.18217    
    Mjobteacher  -0.84729    0.96700  -0.876  0.38148    
    studytime     0.58141    0.26917   2.160  0.03141 *  
    failures     -1.85786    0.30368  -6.118 2.38e-09 ***
    schoolsupyes -1.25105    0.65463  -1.911  0.05675 .  
    famsupyes    -0.81115    0.44715  -1.814  0.07047 .  
    romanticyes  -1.08732    0.45420  -2.394  0.01716 *  
    freetime      0.32686    0.22557   1.449  0.14816    
    goout        -0.57358    0.21571  -2.659  0.00817 ** 
    absences      0.05580    0.02737   2.039  0.04216 *  
    Dalc         -0.12652    0.31553  -0.401  0.68868    
    Walc          0.10709    0.23442   0.457  0.64805    
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 4.085 on 376 degrees of freedom
    Multiple R-squared:  0.2413,	Adjusted R-squared:  0.205 
    F-statistic: 6.643 on 18 and 376 DF,  p-value: 1.757e-14



<b>Create a data frame to compare parents' jobs and eduations against student grades.</b>


```R
student = data.frame(MotherJob=d1$Mjob, 
                     FatherJob=d1$Fjob, 
                     MotherEducation = d1$Medu, 
                     FatherEducation = d1$Fedu, G3=d1$G3)
set.seed(1)
```

<b>Create training data (around 75% of rows).</b>


```R
idx.train = sample(1:nrow(student), 0.75 * nrow(student))
student.tree = rpart(G3~MotherJob+FatherJob+MotherEducation+FatherEducation, data=student[idx.train, ], method='class')
```

<h3>1.3 Cross Validation Graph</h3>


```R
crossval = plotcp(student.tree)
plot(student.tree)
text(student.tree, pretty=0)
```


![png](output_15_0.png)



![png](output_15_1.png)


<b>The graph which analyzes deterministic factors for students who enter college.</b>


```R
tree = prp(student.tree)
```


![png](output_17_0.png)


<h3>1.4 Predicive Model</h3>
<b>Removing training data</b>


```R
student.pred = predict(student.tree, student[-idx.train, ], type = 'class')
```

<h3>1.5 Confusion Matrix</h3>

<p>To determine the classification method</p>


```R
tree.con = table(student.pred, student[-idx.train, ]$G3)
tree.con
```


                
    student.pred 0 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19
              0  0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0
              4  0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0
              5  0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0
              6  0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0
              7  0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0
              8  0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0
              9  0 0 1 1 0 1  3  3  0  0  2  3  2  0  0  2
              10 3 1 1 1 4 2  5  1  2  2  1  1  1  0  0  0
              11 0 0 2 0 3 1  5  4  3  4  3  5  1  3  3  0
              12 0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0
              13 0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0
              14 1 1 1 1 1 0  4  4  1  0  1  1  1  2  0  0
              15 0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0
              16 0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0
              17 0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0
              18 0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0
              19 0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0
              20 0 0 0 0 0 0  0  0  0  0  0  0  0  0  0  0


<ul>
<li>Drop Age since range is very small and there are many outliers.  This can be a factor in why the the stepwise model thinks this variable is signficant.
<li>Add in alcohol consumption because we would like to study this effect.
</ul>

<b>Subset the data using the variables above, 'd2' is our final dataset.</b>


```R
d2 = d1[,c(2,5,7,14,15,16,17,23,25,26,27,28,30,31)]
```

<h2>II. Graphical Representations </h2>

<h3>2.1 Frequency of Grades</h3>


```R
grade_count = count(d2$G3)                        #shows tally of grades
grade_count
```


<table>
<thead><tr><th scope=col>x</th><th scope=col>freq</th></tr></thead>
<tbody>
	<tr><td> 0</td><td>38</td></tr>
	<tr><td> 4</td><td> 1</td></tr>
	<tr><td> 5</td><td> 7</td></tr>
	<tr><td> 6</td><td>15</td></tr>
	<tr><td> 7</td><td> 9</td></tr>
	<tr><td> 8</td><td>32</td></tr>
	<tr><td> 9</td><td>28</td></tr>
	<tr><td>10</td><td>56</td></tr>
	<tr><td>11</td><td>47</td></tr>
	<tr><td>12</td><td>31</td></tr>
	<tr><td>13</td><td>31</td></tr>
	<tr><td>14</td><td>27</td></tr>
	<tr><td>15</td><td>33</td></tr>
	<tr><td>16</td><td>16</td></tr>
	<tr><td>17</td><td> 6</td></tr>
	<tr><td>18</td><td>12</td></tr>
	<tr><td>19</td><td> 5</td></tr>
	<tr><td>20</td><td> 1</td></tr>
</tbody>
</table>



<h3>2.2 Histogram</h3>


```R
grade_frequencies = ggplot(d2, aes(G3)) + geom_bar(color ="black",  fill = "green", alpha = 0.7) +
  labs(title = "Histogram of Grades") + ylim(c(0, 60)) + labs(x = "Final Grades", y = "Count")
grade_frequencies
```




![png](output_28_1.png)


<p><i>Histogram above shows the frequency of final grades among students in the math class.  We see that it is approximately normally distributed, with a slight skew to the right.  Outlier is at final grade (G3) equal to 0.  Highest frequencies occur at final grade equal to 10.</i></p>

<h3>2.3 Alcohol Consumption</h3>


```R
walc_g3 = ggplot(d2, aes(G3, colour=factor(Walc))) + geom_density(adjust=2, alpha = 0.5, size = 1.5) +
  ggtitle("PDF of Final Grade by Weekend Alcohol Consumption") + labs(x = "Final Grade")
walc_g3
```




![png](output_31_1.png)


<p><i> PDF of final grades above is factored by weekend alcohol consumption variable (Walc).  The graph shows that there is no clear relationship.  This challenges the convential thought that student alcohol consumption is negatively related to students's grades.</i></p>


```R
ggplot(d2, aes(Dalc, G3)) + geom_bar(aes(fill = factor(Walc)), position = "dodge", stat="identity") + ggtitle("Weekend Drinking vs. Weekday Drinking")
```




![png](output_33_1.png)


<p><i>Histogram above shows the relationship between weekend alcohol consumption and weekday alcohol consumption for final grades.  The highest frequency is among students who drink once or zero times during the week.  We can also see that for students who drink five or more times during the weekday, they also drink five or more times during the weekend, and should except to receive lower final grades.</i></p>


```R
cor(d2$goout, d2$Walc)
cor(d2$goout, d2$Dalc)
```


0.420385745471789



0.266993848010015



```R
out_dalc = ggplot(d2, aes(Dalc, colour=factor(goout))) + geom_density(adjust=2, alpha = 0.5, size = 1) +
  ggtitle("PDF of Weekday Alcohol Consumption by Going Out") + labs(x = "Weekday Alcohol Consumption")
out_dalc
```




![png](output_36_1.png)


<p><i>Above, the plot shows the PDF of weekday alcohol consumption factored by the number of times a student goes out (goout).  This shows that people are less likely to report higher rates of drinking during the week.  Students that report going out more are still more likely to report drinking more during the week than those who report going out less.</i></p>

<p>A similiar PDF is produced when analyzing weekend alcohol consumption factored by the number of times a students goes out.  From these two PDFs, we can expect to see students's alcohol consumption preference mirroed by their frequency in going out.  We should also see those implications as a potential partial indicator of the effect of alcohol consumption on final grade.</p>

<h3>2.4 Gender and Romantic Relationship Study</h3>


```R
sex_study = ggplot(d2, aes(studytime, colour=factor(sex))) + geom_density(adjust=2, alpha = 0.5, size = 1) +
  ggtitle("PDF of Gender and Study Time") + labs(x = "Study Time")
sex_study
```




![png](output_39_1.png)


<p><i>When analyzing the amount of time spent studying between genders, we see that female students spend more time studying on average than male students do.</i></p>


```R
Gender = as.character(d2$sex)
Romantic = as.character(d2$romantic)
ggplot(d2, aes(x=Romantic, y=G3, fill=Gender)) + geom_boxplot() + labs(title="Boxplot between Gender and Romantic Relationship")
```




![png](output_41_1.png)


<p><i>Boxplot above separates the romantic relationship status and gender of students and plots the frequency of final grades.  It seems that on aveerage, students who are not in a romantic relationship do the same in terms of final grades.  The spread of male single students is bigger in the top 25% is higher than that of female.  Note that for students in relationships, the average of male students is the same, with slight changes in the spread.  It is interesting to see that female students who are in a relationship have lower final grades on average that female students who are not in a relationship.</i></p>

<h3>2.5 Failures and Final Grades</h3>


```R
ggplot(d2, aes(failures)) + ggtitle("Grade by Failures") + geom_histogram(aes(fill=factor(G3)),binwidth=1, position="fill")
```




![png](output_44_1.png)


<p><i>The above histogram shows that frequence of failures for students by their final grades.  It depicts a relatively even distribution of probability to receive grades across all spectrums.  If a student has failed three classes, he or she has an extremely low probability of receiving a final grade of 20 in this math class.  This histogram supporst the widely accepted notion that if failing a class decreases the likelihood of receving a high grade.</i></p>

<h3>2.6 Absences</h3>

<p>Frequency of abscences among students is as shown below.</p>


```R
abs_hist = ggplot(d2, aes(absences)) + geom_histogram(fill = "green", alpha = "0.7", color = "black") +
  labs(title = "Histogram of Absences", x = "Absences")
abs_hist
```

    `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.





![png](output_47_2.png)



```R
abs_bar <- ggplot(d2,aes(x= Gender,y = absences,group = Gender)) + geom_boxplot(aes(fill = Gender)) +
  facet_wrap(~cut(G3,4))
abs_bar
```




![png](output_48_1.png)



```R
abs_hex <- ggplot(d2, aes(absences, G3)) + stat_binhex(colour="grey") + labs(title = "Hexbin Absences vs. Final Grade") +
  labs(y = "Final Grade")
abs_hex
```




![png](output_49_1.png)


<p><i>The hexbin plot above shows that the abscences clustered as the relate to the final grade.  We see there is a much higher concentration for students with less abscenes, which supports earlier histogram.  Additionally, the hexbin diagnostic shows that the count of students based on final grades is not significant among number of abscences (as shown with the shade of blue data points).</i></p>

<h3>2.7 Additional Variables in Consideration</h3>

<ul>
<li><b>Family Support</b> (famsup)</li>
<li><b>School Support</b> (schoolsup)</li>
<li><b>Family Size</b> (famsize)</li>
</ul>


```R
ggplot(d2, aes(G3, fill = factor(famsup), colour = factor(famsup))) + geom_density(alpha = 0.1) + ggtitle("PDF Grade by Family Support")
```




![png](output_52_1.png)



```R
ggplot(d2, aes(G3, fill=factor(schoolsup), colour = factor(schoolsup))) + geom_density(alpha = 0.1) + ggtitle("PDF Grade by School Support")
```




![png](output_53_1.png)


<p><i>The PDF above showing final grades by family support and the PDF above showing final grades by school support has similiar conclusions.  They imply that students who receving support from school or their families have a higher probability of receving lower final grades.  This is most likely due to <b>selection bias</b> in that students who require support are most likely struggling in the class and thus in need of this additional support.  We cannot interpret support to mean higher likelihood of receiving a higher final grade.</i></p>


```R
ggplot(d2, aes(G3)) + ggtitle("Grade by Family Size") + geom_density(aes(fill=factor(famsize)), position="fill")
```




![png](output_55_1.png)


<p><i>Low-score side is due to the gap for high scores and the higher likelihood of being a single child.  The LE3 (less than three family members) does not necessarily imply being an only child since this could be a family with a single parent and two children.  Thus, this provides minimal avidence of significance of family size in determining final grade in a math class.</i></p>

<h2>III. Final Model and Conclusion</h2>

<p>In examining all variables selected from the stepwise procedure as well as adding in the alcohol consumption variables, the final selected model is as follows,</p>

<p>\begin{equation}
G3 = \beta_0 + \beta_1 Sex + \beta_2 Age + \beta_3 FamSize + \beta_4 MomEdu + \beta_5 MomJob + \beta_6 StudyTime + \beta_7 Failures + \beta_8 SchoolSup + \beta_9 FamSup + \beta_10 Romantic + \beta_11 FreeTime + \beta_12 GoOut + \beta_13 Abscence + \beta_14 DayAlc + \beta_15 WeekendAlc
\end{equation}</p>

<p>with the understanding that G3 denotes final grade and $\beta_0$ = 13.67, $\beta_1$ = 0.96, $\beta_2$ = -0.28.  All parameter estimates are shown in the earlier summary of our model (mod).The more significant variables in this model are number of class failures with a p-value of 1.96e-09, number of days going out with a p-value of 0.005, and amount of time spent study with a p-value of 0.0320.  This model has the lowest AIC value of 2249.525 as stated earlier.</p>

<p>As expected, this model’s negative coefficients suggestion a negative relationship between number of times going out and final grades. For each increase in going out value, there will be a decrease of -0.545 in final grade ceteris paribus.  Similarly, for each class failure, there will be a decrease of -1.86 in final grade all else held constant.  With a positive coefficient, every unit increase in study time should result in an increase of 0.571 for final grade.  Note that alcohol consumption, weekend and weekday are not included.  Considering their relationship to going out, it seems that the going out variable is sufficient enough to influence final grade alone, rather than adding an alcohol consumption variable.
</p>

<h2>Next Steps</h2>

<p>
Improvements on this final model would include looking at two-factor and three-factor level interactions among significant variables.  Given the broad instruction of this report and emphasis on diagnostics through homework two and package “ggplot2”, we opted to focus more on analyzing relationships among variables rather than find the best model.  It would be interesting to visualize the other variables not selected in the originally implemented stepwise function.  Additionally, given the high number of provided variables, it would also be interesting to create a model predicting alcohol consumption using logistic regression or other classification methods as taught in class.  
</p>
