# Eğitim Arenasında Türkiye: PISA Performansı Analizi

Bu proje, Eskişehir Teknik Üniversitesi İstatistik Bölümü lisans programında 2023-2024 Güz döneminde yürütülen Veri Görselleştirme dersi projesi kapsamında yapılmıştır. PISA (Programme for International Student Assessment - Uluslararası Öğrenci Değerlendirme Programı), OECD tarafından yürütülen ve 15 yaşındaki öğrencilerin bilgi ve becerilerini ölçen dünya çapında bir sınavdır.
Matematik, fen bilimleri ve okuma alanlarında öğrencilerin problem çözme, eleştirel düşünme ve bilgiye erişim yeteneklerini değerlendirerek ülkeler arasında eğitim performansının karşılaştırılmasını sağlar. Bu sınav, farklı ülkelerin eğitim sistemlerini değerlendirirken öğrenci başarısının analiz edilmesine olanak tanır.

Veri seti, 'learningtower' isimli R kütüphanesinden alınmıştır. Çalışmadaki kodlara ait veri setleri 
'Veri Kaynağı Dosyaları' içinde bulunmaktadır.

Proje, 'pyhton' dili ile hazırlanmıştır. 

## Veri Seti

Veri seti link: https://cran.r-project.org/web/packages/learningtower/index.html

Learningtower kütüphanesinde temelde 3 adet veri seti bulunmaktadır. 
 -Student: Öğrencilerin sınavlarda aldıkları puanları içeren 22 sütunlu veri seti. Öğrencinin anne-baba eğitim durumu, varlık durumu gibi çeşitli bilgileri de barındırmaktadır. Sütunların açıklamaları için yukarıdaki linki inceleyebilirsiniz. 
 -School: Sınavların yapıldığı okulların bilgilerini içeren veri seti.
 -CountryCode: Ülke kodları ve ülke isimlerini barındıran veri seti.

Bu çalışmada,

• Türkiye'de PISA sınavına giren öğrenci sayısının yıllar içindeki durumu incelendi.

• Türkiye'de PISA sınavlarında cinsiyet kırılımında ortalama puanların dağılımı karşılaştırıldı.

• Türkiye'deki öğrencilerin cinsiyet ve sınav türü kırılımında puanlarının dağılımı karşılaştırıldı.

• OECD ülkeleri ile Türkiye'deki öğrencilerin sahip olduğu kitap sayısının okuma sınavı puanlarına etkisi araştırıldı.



## Gerekli Kütüphaneler

```

import pandas as pd     
import numpy as np
import seaborn as sns
import plotly.express as px
import matplotlib.pyplot as plt
import plotly.graph_objects as go
from plotly.subplots import make_subplots

```

## Veri Okuma ve Manipülasyon
```
student = pd.read_excel("student.xlsx") #2003-2018 yılları arasında Türkiye'deki öğrencilerin puan bilgilerini içeren set - learningtower kütüphanesinden filtrelenerek alınmıştır.
school = pd.read_excel("school.xlsx")   #PISA sınavlarının yapıldığı okullara dair set
country = pd.read_excel("country.xlsx") #Ülke kodları ve isimlerini içeren set

#Veri setindeki cinsiyet sütununu grafik lejantı için Türkçe karşılıkları için manipülasyon işlemi

turkish_labels = {
    'female': 'Kadın',
    'male': 'Erkek'
}
student['gender'] = student['gender'].replace(turkish_labels)

#Öğrenci dataseti ile ülke kodlarını içeren veri setlerinin birleştirilmesi
data = student.merge(country, on='country', how='left')
data.head(6)

```


## Grafikler

### Grafik 1: Türkiye'de PISA Sınavına Giren Öğrenci Sayısı

```
#Yıllara göre cinsiyet kırılımındaki öğrenci sayısı için bir dataframe oluşturma

stu_num = data.groupby(["year","gender"]).size().reset_index()
stu_num = stu_num.rename(columns={"year":"Yıl", "gender":"Cinsiyet", 0:"Kişi Sayısı"})


#Sütun grafik

fig = px.bar(stu_num, x='Yıl', y='Kişi Sayısı', color='Cinsiyet', barmode='group',
             labels={'Yıl': 'Yıl', 'Kişi Sayısı': 'Kişi Sayısı', 'Cinsiyet': 'Cinsiyet'},
             title="Türkiye'de PISA Sınavı Giren Öğrenci Sayısı (2003-2018 Yılları Arasında)",
             category_orders={'Cinsiyet': ['Kadın', 'Erkek']},
             color_discrete_sequence=px.colors.qualitative.Bold)
fig.update_layout(plot_bgcolor='white') #grafik arkaplanı düzenleme
fig.update_layout(
    font=dict(
        family="Arial",  # Yazı fontu
        size=14,         # Yazı boyutu
    ),
    xaxis=dict(
        title=dict(
            text="Yıl",  # X ekseni başlığı
            font=dict(
                family="Arial",  # Başlık fontu
                size=20        # Başlık boyutu
            )
        ),
    ),
    yaxis=dict(
        title=dict(
            text="Kişi Sayısı",  # Y ekseni başlığı
            font=dict(
                family="Arial",  # Başlık fontu
                size=20         # Başlık boyutu
            )
        ),
    ),
    title=dict(
        text="Türkiye'de PISA Sınavı Giren Öğrenci Sayısı",  # Grafik başlığı
        font=dict(
            family="Arial",  # Başlık fontu
            size=24        # Başlık boyutu
        )
    )
)
fig.update_yaxes(
    range=[0, 4000], #y eksenini ölçeklendirmek için
    showline=True, #yatay çizgilerin görünmesi için
    gridcolor='lightgrey' #yatay çizgilerin rengi
)
fig.show()

```

![]()


Türkiye, PISA sınavlarına 2003 yılı itibariyle katılmaya başlamıştır. Sınava katılacak öğrenciler rastgele seçilmektedir ve gönüllülük ilkesine dayanmaktadır. İlk yıllarda erkek öğrenci sayısının daha fazla olduğu görülürken 2015 ve 2018 yıllarında seçilen öğrencilerin cinsiyet dağılımı nispeten eşitlenmiştir. Sınava katılan kadın öğrenci sayısının da artması ise Türkiye’de eğitime katılan kadın öğrenci sayısının iyileşmesine işaret olarak yorumlanabilir.

### Grafik 2: Türkiye'deki Öğrencilerin Cinsiyet ve Sınav Türü Kırılımında Puanlarının Dağılımı

```
#Cinsiyet kırılımında sınav türünde ortalama puanlar için dataframe
avg_data = student.groupby('gender')[["math","read","science"]].mean()
avg_data

#sub plot yardımıyla görselleştirme
color_dict = {
    'math': '#4C78A8',
    'read': '#F58518',
    'science': '#E45756'
}

fig = make_subplots(rows=1, cols=3, subplot_titles=('Matematik', 'Okuma', 'Fen'))

for i, col in enumerate(avg_data.columns, start=1):
    fig.add_trace(
        go.Box(y=student[col], x=student['gender'], name=col, marker_color=color_dict[col]),
        row=1,
        col=i
    )

fig.update_layout(
    title="Türkiye'deki Öğrencilerin Cinsiyet ve Sınav Türü Kırılımında Puanlarının Dağılımı",
    height=400,
    width=1000,
    showlegend=False  
)
fig.update_layout(
    font=dict(
        family="Arial",  # Yazı fontu
        size=14,         # Yazı boyutu
    ),
    xaxis=dict(
        title=dict(
            text="Cinsiyet",  # X ekseni başlığı
            font=dict(
                family="Arial",  # Başlık fontu
                size=20        # Başlık boyutu
            )
        ),
    ),
    yaxis=dict(
        title=dict(
            text="Puan",  # Y ekseni başlığı
            font=dict(
                family="Arial",  # Başlık fontu
                size=20         # Başlık boyutu
            )
        ),
    ),
    title=dict(
        text="Türkiye'deki Öğrencilerin Cinsiyet ve Sınav Türü Kırılımında Puanlarının Dağılımı",  # Grafik başlığı
        font=dict(
            family="Arial",  # Başlık fontu
            size=22        # Başlık boyutu
        )
    )
)
fig.update_xaxes(title_text='Cinsiyet', row=1, col=2)
fig.update_yaxes(title_text='Puan', row=1, col=1)
fig.update_yaxes(
    range = [0,1100],
    showline=True,
    gridcolor='lightgrey'
)
fig.update_layout(plot_bgcolor='white')
fig.show()
```

![]()

PISA sınavları, öğrenin temelde üç alandaki muhakeme yeteneğini ölçmektedir. Sınav türü ve cinsiyet kırılımında durumu incelediğimizde kadın öğrencilerin ‘Matematik’ ve ‘Fen’ sınavlarında daha stabil bir aralıkta performans sergilediği görülmektedir.


### Grafik 3: Türkiye'de PISA Sınavlarında Cinsiyet Kırılımında Ortalama Puanlar

```
#Öğrencilerin üç sınavda aldığı puanların ortalamasının hesaplanması için ilk önce veri manipülasyonu yapılmıştır.

student['average_score'] = student[['math', 'science', 'read']].mean(axis=1)

#Cinsiyet kırılımındaki dağılımı incelemek için kutu grafikten yararlanılmıştır.

fig = px.box(student, x='year', y='average_score', color='gender',
             title="Türkiye'de PISA Sınavlarında Cinsiyet Kırılımında Ortalama Puanlar",
             labels={'value': 'Ortalama Puan', 'year': 'Yıl', 'gender': 'Cinsiyet'},
             color_discrete_sequence=px.colors.qualitative.Bold,
             category_orders={'gender': ['Kadın', 'Erkek']}) 
fig.update_layout(xaxis_tickangle=-90, legend_title_text='Cinsiyet') 
fig.update_layout(plot_bgcolor='white')
fig.update_layout(
    font=dict(
        family="Arial",  # Yazı fontu
        size=14,         # Yazı boyutu
    ),
    xaxis=dict(
        title=dict(
            text="Yıl",  # X ekseni başlığı
            font=dict(
                family="Arial",  # Başlık fontu
                size=20        # Başlık boyutu
            )
        ),
    ),
    yaxis=dict(
        title=dict(
            text="Ortalama Puan",  # Y ekseni başlığı
            font=dict(
                family="Arial",  # Başlık fontu
                size=20         # Başlık boyutu
            )
        ),
    ),
    title=dict(
        text="Türkiye'de PISA Sınavlarında Cinsiyet Kırılımında Ortalama Puanlar",  # Grafik başlığı
        font=dict(
            family="Arial",  # Başlık fontu
            size=24        # Başlık boyutu
        )
    )
)
fig.update_yaxes(
    range=[100,800],
    showline=True,
    gridcolor='lightgrey'
)
fig.show()

```

![]()

Grafikte  cinsiyet kırılımında öğrencilerin sınav
dönemlerine ait performanslarının dağılımları incelenmiştir. 2015'te her iki cinsiyet için de diğer yıllara oranla
düşüş gözlemlenirken, kadın öğrencilerin genel performansı erkek öğrencilere göre daha yüksektir
yorumu yapılabilir.


### Grafik 4: Öğrencilerin Sahip Olduğu Kitap Sayısının Okuma Sınavı Puanlarına Etkisi

```
#Learningtower kütüphanesinden 2012,2015 ve 2018 yılları için OECD ülkeleri filtresiyle veri alınmıştır.
new_data = pd.read_excel('2012_to_2018.xlsx')

#Ülke kodları ile isimlerini eşleştirmek için country setiyle birleştirme işlemi
oecd_data = new_data.merge(country, on='country', how='left')

#oecd data içerisindeki öğrencilerin sınavlardan aldığı ortalama puan hesaplaması
student['average_score'] = student[['math', 'science', 'read']].mean(axis=1)

#oecd_data içinde çok fazla sütun olduğu incelemek için sub_df oluşturulmuştur
sub_oecd = oecd_data[['year','country','student_id','mother_educ','father_educ','gender','math','read','science','book','wealth','escs','country_name','average_score']]

#Türkiye vs OECD ülkeleri karşılaştırması için ülke ismine göre veri etiketleme
sub_oecd.loc[sub_oecd['country_name'] == 'Turkey', 'Country_Label'] = 'Türkiye'
sub_oecd.loc[sub_oecd['country_name'] != 'Turkey', 'Country_Label'] = 'OECD Ülkeleri'

#Öğrencinin sahip olduğu kitap sayısı ile okuma sınavındaki performansını incelemek için kutu grafiğinden yararlanılmıştır
fig = px.violin(sub_oecd, x='book', y='read', color='Country_Label', box=True,
             title="Öğrencilerin Sahip Olduğu Kitap Sayısının Okuma Sınavı Puanlarına Etkisi",
             color_discrete_sequence=px.colors.qualitative.G10,
             labels={'book': 'Kitap Sayısı', 'read': 'PISA Okuma Sınav Puanı', 'Country_Label':'Türkiye vs OECD Ülkeleri'},
             category_orders={'book': ['0-10', '11-25', '26-100', '101-200', '201-500', 'more than 500' ]})

fig.update_layout(plot_bgcolor='white')
fig.update_layout(
    font=dict(
        family="Arial",  # Yazı fontu
        size=14,         # Yazı boyutu
    ),
    xaxis=dict(
        title=dict(
            text="Kitap Sayısı",  # X ekseni başlığı
            font=dict(
                family="Arial",  # Başlık fontu
                size=20        # Başlık boyutu
            )
        ),
    ),
    yaxis=dict(
        title=dict(
            text="PISA Okuma Sınav Puanı",  # Y ekseni başlığı
            font=dict(
                family="Arial",  # Başlık fontu
                size=20         # Başlık boyutu
            )
        ),
    ),
    title=dict(
        text="Öğrencilerin Sahip Olduğu Kitap Sayısının Okuma Sınavı Puanlarına Etkisi",  # Grafik başlığı
        font=dict(
            family="Arial",  # Başlık fontu
            size=24        # Başlık boyutu
        )
    )
)
fig.update_yaxes(
    range=[0,1000],
    showline=True,
    gridcolor='lightgrey'
)
fig.show()

```

![]()

Öğrencinin sahip olduğu kitap sayısı ile ‘Okuma’ sınavında sergilemiş olduğu performansa baktığımızda öğrencinin sahip olduğu kitap sayısı arttıkça hem OECD ülkelerindeki öğrencilerin hem de Türkiye’deki öğrencilerin sınavda aldığı puanda artış görülmektedir.





