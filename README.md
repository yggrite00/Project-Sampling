# Project-Sampling

project ini mensampling data bus jakarta pada tahun 2021, teknik sampling yang digunakan adalah 
stratified sampling dan simple random sampling

```python
#data https://data.jakarta.go.id/dataset/data-penumpang-bus-transjakarta-januari-2021
# langkah-langkah menemukan dan menggabungkan file csv menjadi satu
import numpy as np
import pandas as pd
from pandas import Series, DataFrame 
 
 import glob
glob.glob('../project_sampling/data/data*.csv') # glob digunakan untuk menemukan file dengan pola nama tertentu

#menyatukan beberapa dataset menjadi satu dataset
dataset1=[]
for file in glob.glob('../project_sampling/data/data*.csv'):
    print(f'loading{file}')
    data=pd.read_csv(file)
    dataset1.append(data)
    
dataset2=pd.concat(dataset1)
dataset2.head(100) 
```

melakukan stratifikasi 
```python
#menemukan strata 
dataset2['jenis'].value_counts()
#menemukan proporsinya 
dataset2['jenis'].value_counts("BRT") # "BRT" bisa diganti dengan value apa saja yang ada dalam tabel dataset yang dipilih
```

setelah melakukan stratifikasi, langkah selanjutnya adalah sampling menggunakan proporsi strata.
sampling dilakukan dengan mengalikan proporsi strata dengan jumlah sample yang diinginkan.
untuk project ini sample perstrata adalah hasil perkalian proporsi strata dengan populasi strata.

```python
#sampling strata 1 berdasarkan proporsi populasinya
mikrotrans=dataset2.loc[dataset2['jenis']=='Mikrotrans']
strata1 = mikrotrans.sample(frac=0.57)
print(f'populasi strata 1 adalah {mikrotrans.shape}')
print(f'sample strata 1 adalah {strata1.shape}')


#sampling strata 2 berdasarkan proporsi populasinya
brt=dataset2.loc[dataset2['jenis']=='BRT']
strata2=brt.sample(frac=0.10)
print(f'populasi strata 2 adalah {brt.shape}')
print(f'sample strata 2 adalah {strata2.shape}')


#sampling strata 3 berdasarkan proporsi populasinya
Angkutan_Umum_Integrasi =dataset2.loc[dataset2['jenis']=='Angkutan Umum Integrasi']
strata3 =Angkutan_Umum_Integrasi.sample(frac=0.31)
print(f'populasi strata 3 adalah {Angkutan_Umum_Integrasi.shape}')
print(f'sample strata 3 adalah {strata3.shape}')

```

menghitung parameter strata.
sebelum menghitung parameter populasi, diperlukan parameter masing-masing strata (mean dan total)

```python

# estimating total strata-h
total_st1 =strata1['jumlah_penumpang'].sum()
total_st2 =strata2['jumlah_penumpang'].sum()
total_st3 =strata3['jumlah_penumpang'].sum()
print(f'estimasi total penumpang sample strata 1 adalah {total_st1}')
print(f'estimasi total penumpang sample strata 2 adalah {total_st2}')
print(f'estimasi total penumpang sample strata 3 adalah {total_st3}')


#mencari rata-rata sample setiap strata
avg_1= strata1['jumlah_penumpang'].mean()
avg_2= strata2['jumlah_penumpang'].mean()
avg_3= strata3['jumlah_penumpang'].mean()
print(f'rata-rata sample strata 1 adalah {avg_1}')
print(f'rata-rata sample strata 2 adalah {avg_2}')
print(f'rata-rata sample strata 3 adalah {avg_3}')

```

setelah mendapatkan parameter masing-masing strata maka parameter populasi bisa diestimasi (unbiased mean, total, dan varains)

```python

#unbiased total population
unbiased_total1 = avg_1*848
unbiased_total2 =avg_2* 156
unbiased_total3 = avg_3*469

T_st =unbiased_total1 +unbiased_total2+unbiased_total3
print(f'unbiased total penumpang adalah {T_st}')

#unbiased mean population
unbiased_mean1 = avg_1*848
unbiased_mean2 = avg_2*156
unbiased_mean3 = avg_3*469

Mean_st = (unbiased_mean1 + unbiased_mean2 + unbiased_mean3)/dataset2['jenis'].count()
print(f'unbiased mean penumpang adalah {Mean_st}')


import math
#unbiased estimated variance of population total estimator
 #variance masing-masing strata
a=(strata1['jumlah_penumpang']-58600)**2
b= (strata2['jumlah_penumpang']-496570)**2
c= (strata3['jumlah_penumpang']-23979)**2
#######
s2_1= a.sum()/482
s2_2= b.sum()/15
s2_3= c.sum()/144

L_1=(848*(848-483)*(s2_1/483)) 
L_2=(156*(156-16)*(s2_1/16)) 
L_3=(469*(469-145)*(s2_1/145))
var_Tst = L_1+L_2+L_3
var_Tst

print(f' unbiased varians dari total population estimator adalah {var_Tst} ')
print(f'strandar deviation of total population estimator adalah {math.sqrt(var_Tst)}')


#unbiased estimated variance of population mean
L_A=(483/848)**2 * (848-483/848) *(s2_1/483)
L_B=(16/156)**2 * (156-16/156) *(s2_1/16)
L_C=(145/469)**2 * (469-145/469) *(s2_1/145)
var_Meanst = L_A + L_B +L_C

print(f' unbiased varians dari mean population estimator adalah {var_Meanst} ')
print(f'strandar deviation of mean population estimator adalah {math.sqrt(var_Meanst)}')

```
