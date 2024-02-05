# Capstone2---Supermarket-Customer
## **Latar Belakang**

A-Mart supermarket merupakan perusahaan bisnis retail yang menjual berbagai produk kebutuhan sehari-hari. Barang yang dijual meliputi produk makanan, minuman, olahan daging, minuman beralkohol, dan emas. Pada dasarnya, customer akan belanja kebutuhan sehari-hari setiap harinya atau bahkan terjadwal setiap bulan. Pembelian ini dapat melalui website, katalog, dan secara langsung ke supermarket.

Pembelian kebutuhan sehari-hari berdasarkan dari keadaan customer. Keadaan customer dalam hal status, kepemilikan anak atau remaja, dan loyalitas customer mempengaruhi produk yang dibeli di supermarket. Hal ini berdampak pada operasional supermarket.
## **Pernyataan Masalah**

Dalam mengelola supermarket, perusahaan perlu memastikan bahwa operasionalnya efisien untuk memenuhi kebutuhan customernya. Selain itu, strategi pemasaran yang efektif sangatlah penting dalam meningkatkan penjualan kedepannya. Bagaimana A-Mart dapat lebih aktif menggunakan data pelanggan untuk meningkatkan layanan, menyesuaikan penawaran promosi, dan menciptakan pengalaman belanja untuk meningkatkan penjualan?


## **Data**
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
import missingno as msno
from datetime import datetime
Dataset menginformasikan identitas pribadi customer, jenis produk, media penjualan, dan penawaran yang diadakan oleh A-Mart supermarket. Secara detail, terdapat 29 kolom yang dibagi menjadi 4 kategori pada dataset Supermarket Customers, meliputi:

### Identitas
- ID : nomor unik customer
- Year_Birth : tahun lahir customer
- Education : level pendidikan customer
- Marital_Status : status pernikahan customer
- Income : pendapatan customer
- Kidhome : banyaknya anak-anak di rumah tangga customer
- Teenhome : banyaknya remaja di rumah tangga customer
- Dt_Customer : tanggal customer bergabung dengan perusahaan
- Recency : jarak hari dari pembelian terakhir hingga sekarang
- Complain : (1) customer complain dengan rentang waktu 2 tahun terkhir ; (0) lain-lain

### Produk
- MntWines : jumlah belanja wine dalam 2 tahun terakhir
- MntFruits : jumlah belanja buah dalam 2 tahun terakhir
- MntMeatProducts : jumlah belanja produk daging dalam 2 tahun terakhir
- MntFishProducts : jumlah belanja produk ikan dalam 2 tahun terakhir
- MntSweetProducts : jumlah belanja produk manis dalam 2 tahun terakhir
- MntGoldProds : jumlah belanja emas dalam 2 tahun terakhir

### Promosi
- NumDealsPurchases : jumlah pembelian dengan adanya diskon
- AcceptedCmp1 : (1) menerima tawaran diskon di putaran ke-1 ; (0) lain-lain
- AcceptedCmp2 : (1) menerima tawaran diskon di putaran ke-2 ; (0) lain-lain
- AcceptedCmp3 : (1) menerima tawaran diskon di putaran ke-3 ; (0) lain-lain
- AcceptedCmp4 : (1) menerima tawaran diskon di putaran ke-4 ; (0) lain-lain
- AcceptedCmp5 : (1) menerima tawaran diskon di putaran ke-5 ; (0) lain-lain

### Media Penjualan
- NumWebPurchases : jumlah pembelian melalui website perusahaan
- NumCatalogPurchases : jumlah pembelian melalui katalog
- NumStorePurchases : jumlah pembelian secara langsung ke supermarket
- NumWebVisitsMonth : jumlah kunjungan ke webaite perusahaan dalam sebulan terakhir
# DATA SUPERMARKET CUSTOMER
data_cust = pd.read_csv('Supermarket Customers.csv',sep='\t') #untuk melacak \t merupakan pemisah
data_cust.head()
# KESELURUHAN KOLOM
data_cust.columns
## **Data Understanding dan Cleaning**

*Data understanding* sangat diperlukan dalam mengenal data yang dimiliki. Sebuah dataset akan memiliki banyak insight dan tentunya memiliki banyak anomali. Hal ini yang menyebabkan adanya data dump atau data mentah. Dalam menanganinya, diperlukan tahapan *data cleaning* untuk meminimalisir adanya anomali dari data yang akan di analisis. Tahapan *data cleaning* dapat berupa menyortir, menyusun, menghapus, mengisi *missing value* dan memprosesnya lebih lanjut.
# MENCARI RINGKASAN INFORMASI DATASET
data_cust.info()
print(f"Terdapat jumlah baris dan kolom di data set supermarket customer sebanyak {data_cust.shape}")
# MENCARI RINGKASAN DATASET YANG BERUPA INT DAN OBJ
display(data_cust.describe()) #int
data_cust.describe(include= 'object') #obj
# MENCARI UNIQUE DI SETIAP KOLOM
df = pd.DataFrame(data_cust)

t_unique = []

for column in df.columns:
    t_unique.append([column, data_cust[column].dtype, data_cust[column].nunique(), data_cust[column].unique()])
    
unique_values = pd.DataFrame(columns=['Column Name','Tipe Data','Data Unique','Nilai Unique'],data=t_unique)
unique_values

**Penjelasan hasil output data understanding:**
- Dataset supermarket customer memiliki 29 kolom dan 2240 baris. 
- Terdapat satu kolom yang hanya memiliki data kosong, yaitu kolom `Income`.
- Tipe data kolom `Dt_Customer` berupa object dan tipe data kolom `Year_Birth` berupa integer. Dari hasil values data, seharusnya kedua kolom ini memiliki tipe data datetime karena menyimpan informasi tanggal, waktu, dan tahun.
- Hasil nilai data unique, kolom `Education` dan `Marital_Status` memiliki values yang tidak umum. Kolom `Education` memiliki values: Graduation, PhD, Master, Basic, 2n Cycle. Sedangkan kolom `Marital_Status` memiliki values: Single, Together, Married, Divorced, Widow, Alone, Absurd, YOLO.
- Kolom `Z_CostContact` dan `Z_Revenue` masing-masing hanya memiliki 1 value yang tidak relevan untuk digunakan dalam tahapan analisis. Kedua kolom ini akan di drop dari dataset.
### Missing Value

Mencari persentase dan jumlah dari missing value yang ada di dataset.
# MENCARI MISSING VALUE
display(data_cust.isna().sum()/data_cust.shape[0]*100) #persentase
display(data_cust.isna().sum()) #jumlah
print(f"Kolom yang memiliki missing value, yaitu: {data_cust.columns[data_cust.isnull().any()]}")
# DIAGRAM MISSING VALUE
msno.matrix(data_cust, figsize=(17,3))
plt.title('Matriks Missing Value', size=30)
plt.show()
**Penjelasan hasil output *missing value*:**
- Hanya kolom `Income` sebanyak 24 data yang memiliki *missing values*. Persentase *missing value* dari kolom `Income` hanya 1% terbilang sangat rendah. 
- Diagram Matriks Missing Value menjelaskan bahwasannya kolom `Income` yang memiliki data kosong tidak berkaitan dengan kolom-kolom lainnya.

Penanganan *missing value* pada kolom `Income` mengisi menggunakan nilai median dari kolom yang berkaitan. Nilai medium digunakan karena kolom `Income` memiliki outliers dan tidak terdistribusi normal. Jenis outliers pada kolom `Income` termasuk right skewed terlihat dari diagram berikut:


# DIAGRAM OUTLIER INCOME
plt.figure(figsize=(8,2))
sns.boxplot(x=data_cust['Income'], color='skyblue')
plt.title('Analisis Outlier Income')
plt.xlabel('Income')
plt.show()
**Penanganan *missing value* pada kolom `Income`, yaitu:**
# INPUT MEDIAN FILL INCOME'S MISSING VALUE
income_fill = data_cust['Income'].median()
data_cust['Income'].fillna(income_fill, inplace=True)

print(f"Kolom yang memiliki missing value, yaitu: {data_cust.columns[data_cust.isnull().any()]}")
- Penanganan *misisng value* sudah teratasi dengan menggunakan median atau nilai tengah dari values kolom `Income` yang telah diurutkan.
### Mengubah Tipe Data

Kolom `Dt_Customer` dan `Year_Birth` memiliki anomali pada tipe datanya. Dalam penanganannya, kedua kolom tersebut seharusnya memiliki tipe data datetime.


Tipe data kolom `Dt_Customer` berupa object dan tipe data kolom `Year_Birth` berupa integer. Dari values data yang ada, seharusnya kedua kolom ini memiliki tipe data datetime karena menyimpan informasi tanggal, waktu, dan tahun.
**Penanganan pengubahan tip data yang tertera menjadi tipe data datetime, yaitu:**
# MENGUBAH OBJ - DATETIME
data_cust['Dt_Customer'] = pd.to_datetime(data_cust['Dt_Customer'], format= '%d-%m-%Y')
# MENGUBAH INT - DATETIME
data_cust['Year_Birth'] = pd.to_datetime(data_cust['Year_Birth'], format= '%Y')

data_cust.info()
- Pengubahan tipe data pada kolom `Dt_Customer` dan `Year_Birth` menjadi tipe data datetime sudah sesuai dengan ketentuan tipe data. Serta dapat memudahkan dalam operasi matematika jika diperlukan.
### Anomali Value
Pada kolom `Education` memiliki values yang tidak sesuai dari tingkat pendidikan. Values berupa Graduation, PhD, Master, Basic, 2n Cycle. Tingkatan pendidikan masing-masing memiliki arti, yaitu:

- Basic --> merupakan tingkatan pendidikan awal, dapat dikatakan sebagai sekolah dasar.

- 2n cycle --> tingkat pendidikan tinggi setelah gelar sarjana / siklus pertama atau merujuk gelar master's degree.

- Graduation --> lulus dari suatu tingkatan pendidikan. Pada tingkat sarjana dinamakan undergraduate, dimungkinkan merujuk pada gelar sarjana / bachelor's degree

- Master --> master degree atau studi pascasarjana setelah lulus dari gelar sarjana.

- PhD --> tingkatan pendidikan tertinggi, disebut sebagai gelar doktor.
data_cust['Education'].unique()
# MENGHITUNG BANYAKNYA EDUCATION SETIAP TINGKATAN
data_cust[['Education']].value_counts()
**Penanganan anomali values pada kolom `Education`, yaitu:**
# MENGGABUNGKAN VALUES 2N CYCLE DENGAN VALUES MASTER
data_cust['Education'] = data_cust['Education'].replace('2n Cycle', 'Master')

# MENGUBAH VALUES GRADUATION KE BACHELOR
data_cust['Education'] = data_cust['Education'].replace('Graduation', 'Bachelor')

data_cust[['Education']].value_counts()
- Pada kolom `Education` dengan values 2n Cycle diubah ataupun digabungkan dengan values master. Melihat pengertian dari 2n Cycle sendiri merujuk pada tingkatan setelah bachelor degree yang berarti tingkatan pendidikan master degree.
- Kolom `Education` dengan values graduation diubah menjadi bachelor atau sama saja dengan undergraduate. Pengertian Graduation yang tidak spesifik umumnya merujuk pada undergraduate.
Values dari kolom `Marital_Status` tidak sesuai dengan status pernikahan pada umumnya. Values yang ada terdiri dari Single, Together, Married, Divorced, Widow, Alone, Absurd, YOLO. Pengertian dari masing-masing values marital status, berikut:

- Single --> status seseorang yang belum menikah atau tidak memiliki ikatan pernikahan

- Together --> Pasangan yang tinggal bersama tanpa adanya status pernikahan resmi. Terbilang hubungan pacaran atau pasangan yang memilih untuk hidup bersama.

- Married --> seseorang yang telah resmi menikah dan memiliki ikatan pernikahan sah.

- Divorced --> seseorang yang telah mengalami perceraian dari proses hukum atau resmi yang mengakhiri pernikahan.

- Widow --> seorang wanita yang suaminya telah meninggal dunia dan tidak menikah lagi.

- Alone --> seseorang yang hidup sendiri tanpa pasangan, tidak mementingkan status pernah menikah atau tidak.

- Absurd --> istilah ini tidak umum digunakan pada ranah marital status.

- YOLO --> tidak ada keterkaitan dengan status pernikahan. YOLO (you only live once) yang artinya lebih berkaitan dengan semangat hidup dan pengalaman
data_cust['Marital_Status'].unique()
# MENGHITUNG BANYAKNYA MARITAL STATUS SETIAP STATUSNYA
data_cust[['Marital_Status']].value_counts()
**Penanganan anomali values pada kolom `Marital_Status`, yaitu:**
# MENGGABUNGKAN VALUES SINGLE DENGAN VALUES ALONE
data_cust['Marital_Status'] = data_cust['Marital_Status'].replace('Alone', 'Single')

# MENGUBAH VALUES ABSURD DAN YOLO MENJADI OTHERS
data_cust['Marital_Status'] = data_cust['Marital_Status'].replace(['Absurd','YOLO'],'Others')

data_cust[['Marital_Status']].value_counts()
- Pada kolom `Marital_Status` dengan values Alone diubah ataupun digabungkan dengan values Single. Dikarenakan pengertian alone dan single hidup tanpa atau belum dengan pasangan dan adanya ikatan pernikahan.
- Kolom `Marital_Status` dengan values absurd dan YOLO diubah menjadi other. Penggantian kedua values ini karena values absurd dan YOLO tidak teridentifikasikan pada marital status pada umumnya.
### Drop

Penghapusan kolom dilakukan pada kolom `Z_CostContact` dan `Z_Revenue`. Kedua kolom tersebut tidak dapat diidentifikasikan secara detail. Selain itu, hanya memiliki satu value angka yang tidak berarti. Maka itu, kedua kolom ini nantinya tidak akan digunakan pada tahap analisis.
# MENGHAPUS KOLOM Z_COSTCONTACT DAN Z_REVENUE
data_cust.drop(columns=['Z_CostContact','Z_Revenue'], inplace=True)
### Menambahkan Kolom

Penambahan kolom age untuk mengetahui umur customer berdasarkan tahun lahirnya.
# MENAMBAHKAN KOLOM UMUR
tahun_ini = datetime.now().year
data_cust['Umur'] = tahun_ini - data_cust['Year_Birth'].dt.year
data_cust
### Data yang sudah bersih

Cleaning data yang dilakukan:
1. Mengatasi missing value pada kolom `Income` dengan mengisi menggunakan mean
2. Mengubah tipe data kolom `Dt_Customer` dan `Year_Birth` menjadi tipe data datetime
3. Menggabungkan dan mengubah values kolom `Education` dan `Marital_Status` sesuai dengan standar yang umum.
4. Menghapus kolom `Z_CostContact` dan `Z_Revenue` yang tidak relevan untuk tahapan analisis.
5. Menambahkan kolom umur dari operasi matematika pengurangan tahun ini dengan kolom `Year_Birth`.
# MENUNJUKKAN DATA SET YANG SUDAH BERSIH
df = pd.DataFrame(data_cust)

new = []

for i in df.columns:
    new.append([i, data_cust[i].isna().sum(), data_cust[i].dtype, data_cust[i].nunique(), data_cust[i].unique()])
    
new_dataset = pd.DataFrame(columns=['Column Name','NaN','Tipe Data','Data Unique','Nilai Unique'],data=new)
display(new_dataset)

print(f"Terdapat jumlah baris dan kolom di data set supermarket customer terbaru sebanyak {data_cust.shape}")

data_cust.head()
Data awal dan data akhir mengalami selisih yang tidak terlalu jauh. Untuk data akhir yang telah dibersihkan tersisa 28 kolom dan masih memiliki jumlah baris yang sama. Hanya selisih 2 kolom dari dataset awal yang memiliki 29 kolom.
## **Data Analysis**

Setelah melakukan *data cleaning* didapatkan data dengan minim anomali. Data ini lebih baik digunakan untuk analisis data lebih lanjut. 
Analisis pertama akan melakukan perbandingan media yang sering digunakan dalam berbelanja. Dari data dimiliki 3 media untuk berbelanja, yaitu dapat melalui website, catalog, dan berbelanja langsung ke store.
# MEDIA BERBELANJA TERFAVORIT
media_fav = data_cust[['NumWebPurchases','NumCatalogPurchases','NumStorePurchases']].sum().reset_index()
display(media_fav)

# DIAGRAM
plt.figure(figsize=(8,5))
plt.pie(media_fav[0], labels=media_fav['index'], autopct='%1.1f%%', colors=['red', 'orange', 'yellow'])
plt.title('Banyaknya Transaksi Berdasarkan Media Berbelanja')
plt.show()
Hasil dari diagram diatas, tertera bahwasannya customer paling banyak berbelanja langsung ke store A-mart sebanyak 46,2% setara dengan 12.970 customer. Selanjutnya, berbelanja melalui website dengan sebanyak 32,6% setara 9.150 customer. Serta 21,2% setara dengan 5.963 customer yang berbelanja melalui katalog perusahaan.
Analisis kedua mengetahui banyaknya jumlah setiap produk yang terjual. Banyaknya jumlah jenis penjualan produk berpengaruh terhadap:
1. Apakah jumlah penjualan setiap produk berpengaruh dengan adanya faktor keberadaan anak dan remaja dirumah?
2. Apakah jumlah penjualan setiap produk berpengaruh dengan adanya faktor umur customer?
3. Apakah jumlah penjualan setiap produk berpengaruh dengan adanya pendapatan customer?
# JENIS PRODUK YANG TERJUAL
product_sell = data_cust[['MntWines', 'MntFruits','MntMeatProducts', 'MntFishProducts', 'MntSweetProducts','MntGoldProds']].sum().reset_index()
display(product_sell)

# DIAGRAM
plt.figure(figsize=(8,5))
sns.barplot(x='index', y=0, data=product_sell, palette=['red','orange','yellow','green','blue','purple'])
plt.title('Banyaknya Jenis Produk yang Terjual')
plt.xlabel('Jenis Produk')
plt.ylabel('Jumlah Transaksi')
plt.xticks(rotation=60)
plt.show()
Jenis produk secara umum yang banyak terjual ialah produk wines atau minuman beralkohol. Hal ini masih belum dapat dipastikan apakah jenis penjualan produk berdasarkan faktor keberadaan anak dan remaja di rumah, umur, dan pendapatan customer?
# MENGHITUNG KORELASI JENIS PENJUALAN PRODUK
correlation_matrix = data_cust[['Income', 'Kidhome',
       'Teenhome','Umur','MntWines', 'MntFruits',
       'MntMeatProducts', 'MntFishProducts', 'MntSweetProducts',
       'MntGoldProds']].corr()

# DIAGRAM
plt.figure(figsize=(10, 8))
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', fmt='.2f', linewidths=.5)
plt.title('Korelasi antara Variabel Numerik dan Jumlah Penjualan Produk')
plt.show()
Keterhubungan antara data numerik dengan jumlah penjualan produk hanya berpengaruh pada pendapatan saja. Utamanya pendapatan akan mempengaruhi dalam membeli produk wines dan meat di A-mart supermarket. Disisi lain, kolom kepemilikan anak dan remaja dan umur tidak berpengaruh terhadap jenis penjualan produk tertentu.
Detail hubungan income dengan jenis produk yang dibeli:
# HUBUNGAN PENDAPATAN DENGAN PENJUALAN JENIS PRODUK
plt.figure(figsize=(10, 6))
sns.scatterplot(x='Income', y='MntWines', data=data_cust, label='Wines', color='red')
plt.title('Hubungan antara Pendapatan dan Jenis Penjualan Produk')
plt.xlabel('Pendapatan')
plt.ylabel('Jumlah Penjualan Produk')
plt.legend()
plt.show()

plt.figure(figsize=(10, 6))
sns.scatterplot(x='Income', y='MntFruits', data=data_cust, label='Fruits', color='orange')
plt.title('Hubungan antara Pendapatan dan Jenis Penjualan Produk')
plt.xlabel('Pendapatan')
plt.ylabel('Jumlah Penjualan Produk')
plt.legend()
plt.show()

plt.figure(figsize=(10, 6))
sns.scatterplot(x='Income', y='MntMeatProducts', data=data_cust, label='Meat Products', color='yellow')
plt.title('Hubungan antara Pendapatan dan Jenis Penjualan Produk')
plt.xlabel('Pendapatan')
plt.ylabel('Jumlah Penjualan Produk')
plt.legend()
plt.show()

plt.figure(figsize=(10, 6))
sns.scatterplot(x='Income', y='MntFishProducts', data=data_cust, label='Fish Products', color='green')
plt.title('Hubungan antara Pendapatan dan Jenis Penjualan Produk')
plt.xlabel('Pendapatan')
plt.ylabel('Jumlah Penjualan Produk')
plt.legend()
plt.show()

plt.figure(figsize=(10, 6))
sns.scatterplot(x='Income', y='MntSweetProducts', data=data_cust, label='Sweet Products', color='blue')
plt.title('Hubungan antara Pendapatan dan Jenis Penjualan Produk')
plt.xlabel('Pendapatan')
plt.ylabel('Jumlah Penjualan Produk')
plt.legend()
plt.show()

plt.figure(figsize=(10, 6))
sns.scatterplot(x='Income', y='MntGoldProds', data=data_cust, label='Gold Prods', color='purple')
plt.title('Hubungan antara Pendapatan dan Jenis Penjualan Produk')
plt.xlabel('Pendapatan')
plt.ylabel('Jumlah Penjualan Produk')
plt.legend()
plt.show()
Detail hubungan pendapatan dengan jenis penjualan produk berbanding lurus. Apabila pendapatan lebih besar, maka jenis produk yang dibeli cenderung yang lebih berharga. Sedangkan apabila pendapatan kecil, maka akan membeli jenis produk yang sesuai. Terlihat dari diagram penjualan produk daging dan emas. Kedua produk ini tersebar di rentang pendapatan yang rendah, maka itu jumlah penjualan produk pun tidak secara drastis terjual.
Analisis terakhir banyaknya rentang promosi yang dibutuhkan untuk menaikkan penjualan. Dibutuhkan berapa kali promosi untuk dapat menarik customer membeli produk yang berada di perusahaan?
# JUMLAH NILAI CAMPAIGN
campaign_cust = data_cust[['AcceptedCmp1', 'AcceptedCmp2', 'AcceptedCmp3', 'AcceptedCmp4',
       'AcceptedCmp5']].apply(pd.value_counts).transpose()

display(campaign_cust)

# DIAGRAM
campaign_cust.plot(kind='bar')
plt.title('Banyaknya Promosi di A-mart Supermarket')
plt.xlabel('Promosi')
plt.ylabel('Jumlah')
plt.legend(['(0)Tidak Ber-efek', '(1)Ber-efek'])
plt.xticks(rotation = 90)
plt.show()
5 kali promosi yang dijalankan A-mart supermarket tidak banyak mempengaruhi adanya kenaikan pendapatan bagi perusahaan. Lebih banyak customer yang tidak membeli pada saat diadakannya promosi. Namun, promosi yang dilakukan secara berkala akan meningkatkan pendapatan.
## **Kesimpulan dan Rekomendasi**

Dari analisis yang dilakukan, terdapat banyak data yang dapat disimpulkan:
- Transaksi jual-beli banyak dilakukan secara langsung di store A-mart supermarket dengan presentase 46,2%.
- Perbelanjaan melalui website terbilang cukup tinggi peminat dengan presentase 32,6%.
- Jenis produk yang banyak terjual ialah produk wines atau minumal beralkohol sebanyak 680.816 botol.
- Penjualan jenis produk hanya berdasarkan dari faktor pendapatan dan berbanding lurus.
- Nilai keefektifan dengan adanya promosi terhadap tingkat keuntungan perusahaan tidak terlalu tinggi.
**Rekomendasi**

1. Peningkatan Promosi
   Mengoptimalkan strategi promosi dapat meningkatkan efektivitas promosi. Utamanya menargetkan promosi kepada customer yang berbelanja secara langsung di store. Selain itu, menargetkan juga promosi kepada pelanggan yang berbelanja melalui website, karena presentasenya cukup tinggi.

2. Optimalkan Transaksi Online
   Peningkatan pengalaman pembelian online dapat meningkatkan frekuensi dan nilai transaksi melalui website. Ini dapat mencakup peningkatan keamanan, kenyamanan, dan penawaran khusus online ataupun website perusahaan.

3. Evaluasi Strategi Penjualan di Store
   Melakukan evaluasi lebih lanjut terhadap transaksi yang dilakukan di store dapat membantu memahami preferensi pelanggan. Peningkatan layanan atau penawaran spesial di store dapat meningkatkan kunjungan pelanggan.

4. Diversifikasi Produk
   Meskipun produk wines sangat diminati, mencoba untuk diversifikasi jenis produk atau menghadirkan produk-produk baru dapat meningkatkan variasi dan minat pelanggan. Selain itu, pada produk yang banyak demand perlu juga diperbanyak untuk stok penjualan.
